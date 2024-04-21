# CHATBOT2
E-COMMERCE CHATBOT
import random
from newspaper import Article
import string
import nltk
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np
import warnings
warnings.filterwarnings('ignore')
nltk.download('punkt', quiet=True)
file_path = "C:\\Users\\Dhruvi\\CHATBOT\\chatbot\\data2.txt"

# Read the contents of the local text file
with open(file_path, "r", encoding="utf-8") as file:
    file_content = file.read()

# Create an Article object
article = Article(url="C:\\Users\\Dhruvi\\CHATBOT\\chatbot\\data2.txt")
article.download(input_html=file_content)

# Set the text content of the article
#article.set_text(file_content)
#article=Article('./data2.txt')
#article.download()
article.parse()
article.nlp()
corpus=article.text
text=corpus 
sentence_list=nltk.sent_tokenize(text)
def greeting_response(text):
    text=text.lower()
    bot_greetings=['howdy','hi','hey','hello','hola']
    user_greetings=['hi','hey','hello','hola','greetings','wassup']
    for word in text.split():
        if word in user_greetings:
            return random.choice(bot_greetings)
def index_sort(list_var):
    length=len(list_var)
    list_index=list(range(0,length))
    x=list_var
    for i in range(length):
        for j in range(length):
            if x[list_index[i]]>x[list_index[j]]:
                temp=list_index[i]
                list_index[i]=list_index[j]
                list_index[j]=temp
    return list_index
def bot_response(user_input):
    user_input=user_input.lower()
    sentence_list.append(user_input)
    bot_response=''
    cm=CountVectorizer().fit_transform(sentence_list)
    similarity_scores=cosine_similarity(cm[-1],cm)
    similarity_scores_list=similarity_scores.flatten()
    index=index_sort(similarity_scores_list)
    index=index[1:]
    response_flag=0
    j=0
    for i in range(len(index)):
        if similarity_scores_list[index[i]]>0.0:
            bot_response=bot_response+' '+sentence_list[index[i]]
            response_flag=1
            j=j+1
        if j>2:
            break
    if response_flag==0:
        bot_response=bot_response+' '+"I apologize, I don't understand."
    sentence_list.remove(user_input)
    return bot_response
print('Bot: I am a bot. I will answer your queries about Chatbots. If you want to exit, type bye.')
exit_list=['exit','bye','quit']
# while(True):
#     user_input=input()
#     if user_input.lower() in exit_list:
#         print('Bot: Chat with you later!')
#         break
#     else:
#         if greeting_response(user_input)!=None:
#             print('Bot: '+greeting_response(user_input))
#         else:
#             print('Bot: '+bot_response(user_input))

from flask import Flask, request, jsonify
from flask_cors import CORS
app = Flask(__name__)
CORS(app)
# Your chatbot logic function
def chatbot(input_text):
    input_text=input()
    if input_text.lower() in exit_list:
       # print('Bot: Chat with you later!')
        return 'Chat with you later!'
    else:
        if greeting_response(input_text)!=None:
           # print('Bot: '+greeting_response(input_text))
            return greeting_response(input_text)
        else:
            return bot_response(input_text)
           # print('Bot: '+bot_response(input_text))

    #return response


@app.route('/chatbot', methods=['GET'])
def handle_message():
    
    input_text = request.args['message']

    
    response = chatbot(input_text)

    # Return the response to the frontend
    return response

if __name__ == '__main__':
    app.run(debug=True)
