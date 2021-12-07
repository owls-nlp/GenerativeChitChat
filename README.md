# Deep Average Network

## Как сделать домашку
- Склонируйте этот репозиторий
- Установите зависимости
- Сделайте задание
- Пришлите [@bobazooba](https://t.me/bobazooba) в телеграм ссылку

## Описание задания
Нужно будет реализовать генеративную модель для ответов на вопросы. В качестве датасета будем использовать вопросы-ответы из сервиса [Ответы@Mail.ru](https://otvet.mail.ru/).  
Данные, которые можете использовать для обучения:
- [qa_data.jsonl.zip](https://cloud.mail.ru/public/mtXU/fhPrP7MEd)
- [train.txt](https://www.kaggle.com/c/chit-chat-encoders/data)  

Советую использовать не все данные. Вы можете их пофильтровать. Оптимально, чтобы на одна эпоха обучения для обычной LSTM у вас занимала не больше часа. 
Вам нужно будет обучать модель на GPU. Научиться работать на GPU, вам поможет [это видео](https://www.youtube.com/watch?v=pgk1zGv5lU4).

Вам очень поможет [этот туториал про языковую модель](https://github.com/BobaZooba/DeepNLP/blob/master/Tutorials/Language%20Model.ipynb).

### Что нужно сделать:
1. Написать *Dataset* для задачи seq2seq
2. Разделить данные на тренировочные и валидационные
3. Реализовать модель
4. Сделать цикл обучения
5. Обучить модель
6. Реализовать метод генерации ответа по вопросу с помощью вашей модели
7. Обернуть это в телеграм бота используя скрипт `telegram.py` (скоро будет дописан)

#### Важный совет
Для отладки возьмите небольшой объем данных. Такой, чтобы обучение не занимало больше 10—30-ти минут. Это поможет вам гораздо быстрее итерироваться.
Допустим, у вас баг в инференсе модели. Вы обучаете ее 5 часов, а затем пробуете что-нибудь сгенерировать. У вас не получается, вы понимаете где ошиблись. 
Теперь, чтобы исправить этот баг, вам нужно еще 5 часов обучать сетку. Такой подход занимает много времени, поэтому лучше выбрать небольшой кусок данных, обучиться на нем, 
написать функцию, которая генерирует ответ и проверить все ли адекватно работает для 30-ти минут обучения. Если модель будет отдавать совершенно рандомные токены в ответе, то значит есть проблема.
В другом случае этих 30-ти минут маленькой модели хватит для того, чтобы не выдавать рандом. Да, этого мало и модель будет плохо отвечать, но она будет хоть как-то отвечать и вы сможете понять что именно не так.  
Рекомендуемые действия:
- Написать пайплайн обучения (датасет, модель, трейнлуп, валидация, генерация ответа по вопросу)
- Обучать в течение 30-ти минут
- Проверить генерацию ответа по вопросу, то есть нужна такая функция, на вход которой приходит текст и результат этот функции тоже текст (ответ на вопрос)
- Проверить простые случаи (учитывайте домен данных), можно посмотреть на то как модель отвечает на вопросы из валидации и сравнить на глаз с ответами из датасета
- Если все хорошо, поставить модель учиться на пару часов, если нет, то проверить:
  1. Функцию генерации ответа
  2. `Dataset`, есть ли там `BOS` и `EOS` в данных или еще какие-нибудь баги
  3. Проверить модель
  4. Проверить трейнлуп

### Примерный список того, что можно сделать:
Необязательно реализовывать все
1. Сделать модель, основанную на lstm/gru **6 баллов**
1. Сделать модель, основанную на cnn **10 баллов**
1. Сделать модель, основанную на трансформере (реализовать все слои самому) **10 баллов**
1. Добавить в rnn/cnn модель attention **5 баллов**
1. Реализовать жадное семплирование (генерацию по самому вероятному токену, как в туториале про языковую модель) **3 балла**
1. Реализовать beam search **5 баллов**
1. Реализовать nucleus sampling **5 баллов**
1. Добавить condition (в первом датасете дается еще тема вопроса) в модель **3 балла**
1. Добавить layer norm/residual в cnn или rnn модель **2 балла**
1. Реализовать аккамуляцию градиентов **3 балл**
1. Сделать телеграм бота **1 балла**

#### Дополнительные детали:  
**Сделать модель** подразумевает весь код с обучением и генерацией ответа по аналогии с языковой моделью.  
**6-й пункт:** у нас есть категория вопроса и мы можем явным образом добавить эту информацию в модель. Затем мы сможем задавать вопрос из любой категории, а отвечать таким ответом, которой больше будет соответствовать категории (которую мы сможем сами задать в модель). То есть на этапе предсказания мы задаем сети вопрос и категорию ответа, то есть в каком стиле наша сеть должна ответить. То есть должно получиться что-то такое:
```
Вопрос пользователя:
    Что мне делать с моей девушкой? она плохо себя ведет

Задаем категорию:
    Авто
Ответ модели:
    Сдайте ее в техосмотр

Задаем категорию:
    Сад и огород
Ответ модели:
    Вам нужно лучше ее поливать
```
**7-й пункт:**  
Статья: https://arxiv.org/pdf/1904.09751.pdf  
В помощь: https://huggingface.co/blog/how-to-generate

## Установка зависимостей
Необходимые сторонние библиотеки  
`pip install -r requirements.txt`

## Оценивание
Будет проверяться корректность логики обучения, 
будут даваться комментарии что нужно исправить, 
если домашка будет сдана вовремя. 
Максимальный балл: 10.

## Дедлайн
24/12/2021  
Далее максимальный балл за работу: 7

## Важные просьбы
- Не отправляйте ссылку на `colab`
- Прежде чем отправить задание на проверку, очистите свой код от неиспользованного кода и неважных комментариев
