# Урок 8. Протокол OAuth 2.0

## Найдите ресурс, на котором реализован протокол OAuth 2.0. Изучите реализацию протокола, ответьте на вопросы:
Для исследования был выбран ресурс https://view.sessiondesign.ru/login

1. Какая версия протокола используется (OAuth 1.0, OAuth 2.0 Implicit Grant, OAuth 2.0 Authorization Code Grant, …, или что-то свое)? Кто является клиентом, а кто провайдером?
![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/a7b8a6d3-4f6a-4951-ae3d-638c85d39f13)

Клиент - https://view.sessiondesign.ru

Провайдер - https://oauth.vk.com/oauth/authorize

Версия используемого протокола - **OAuth 2.0 Authorization Code Grant**

2. Опишите каждый шаг протокола. Приложите соответствующие URL и ответы от сервера (чувствительные данные необходимо скрыть).

  а) Стандартное окно входа 
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/c21bea85-965a-42c4-bf0a-54544419fda9)


  б) Окно входа с помощью протокола OAuth
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/f8c25678-a5f4-408d-9088-d0832289e5e1)
  отправляем запрос с логином на авторизацию у провайдера vk.com
  ```
  POST
	https://api.vk.com/method/auth.validateLogin?v=5.207&client_id=*******
  ```
  со сделующим набором данных
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/2693885c-b598-49f8-96a6-a0107e84370f)

 Получаем ответ, со следующими данными
 ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/69280966-9a7e-4026-af3a-9343408e54fa)

  Далее необходимо ввести пароль и подтвердить введённые данные
  ```
  POST
	https://api.vk.com/method/statEvents.addVKIDAnonymously?v=5.207&client_id=*******
  ```
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/cc9649fa-27d0-49e7-a317-0a3e6391c0cf)
  Получаем ответ, что запрос был корректен и форма ожидаем ввода и отправки пароля
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/f6dc5ab4-86fc-40b7-aa11-503cae57f240)

  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/f3478263-7af8-4571-a450-cb91eefe9473)
  ```
  POST
	https://login.vk.com/?act=connect_authorize
 ```
  Вводим пароль и отправляем форму со следующими данными
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/d0843840-2ce6-4deb-9610-650b3ba0ae2c)

  Получаем подтверждение и токен доступа
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/eac05595-664d-43a1-978d-94a6f8ca36c2)

  в) Окно подверждения о предоставлении сервису, доступа к личным данным пользователя vk.com
  ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/983cb389-7561-415f-8869-54d1ea1b7d5d)

  Провайдер проверяет токен
  ```
  POST
	https://login.vk.com/?act=check_silent_token
 ```
![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/7da80753-6f96-40a1-b4d8-f046bbb6356b)

получаем подтверждение и устанавливается время токена подтверждения в 600 секунд(10 минут)
![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/46ee5249-9b1d-4add-8d9c-6d2231c4812f)
![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/4baabf1a-3f2c-42ea-a8aa-5a121315d1ea)

После чего проайдер авторизуется на клиенте с помощью полученного токена и входит в личный кабинет
```
GET
	https://vk.com/login.php?slogin_h=cd06acd25...b88f54&act=slogin&role=fast&__q_hash=9b3160...caec&iuh=1695407...a1979076b4585&_openBrowser=1&to=aHR0c...4VVRuNmU2cy1xMA--
```

![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/c12c4042-6c76-4e66-9e1f-be7ff6ec3459)


  г) 

3. Найдите уязвимости и слабые места в реализации протокола OAuth 2.0. Если уязвимостей и слабых мест нет, объясните, какие механизмы защиты применили разработчики, чтобы избежать каждой из пройденных нами уязвимостей OAuth 2.0.

  а) В OAuth 2.0 отсутствуют общие обязательные функции безопасности. Сама безопасность зависит от разработчиков, использующих правильную комбинацию параметров конфигурации и реализующих свои собственные дополнительные меры безопасности.
  
    - SSRF - думаю данный вид атаки маловероятен, т.к. аутентификация проходит на сайтак именитых брэндов(VK, Mail, Google, Instagramm...)
    - можно подменить значение **redirect_uri** - и затащить ответы на сайта злоумышленника
    ![image](https://github.com/Bravo-47/IB_homeWork_8/assets/52736408/abccf5c0-a0cf-4351-904f-0ab2662600a0)
    - неправильная реализация протокола, я понимаю схему реализации протокола, но не понимаю как это можно выявить чтобы использовать для атаки((
    - небезопасные токены
    - утечка **client_secret** - заражённые системы могут сливать данные браузеров на ресурсы злоумышленника, либо злоумышленник может читать трафик пользователя и стянуть **client_secret**


4. * Изучите реализацию протокола глубже и попробуйте найти уязвимости и слабые места OAuth 2.0, которые мы не изучали на уроке. Опишите, что и как вы пытались сделать (вне зависимости от результата) и почему выбранный вами вектор атаки сработал или не сработал.
