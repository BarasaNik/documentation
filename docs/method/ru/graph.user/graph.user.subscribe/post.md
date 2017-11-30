graph.user.subscribe

$description
Подписка на получение сообщений о событиях в чатах на Webhook

$payload
{% highlight json %}
{
  "url": String  /* http: или https: URL, куда будут доставляться сообщения о событиях в чатах */
}
{% endhighlight %}

$additional
{% format_code **Внимание!** POST-запросы должны выполняться с заголовком Content-Type: application/json;charset=utf-8 %}

При подписке на все Webhook из списка подписанных приходят сообщения о событиях в чате. 

В качестве сообщений выступают данные о следующих событиях:
* изменение состояния чата (изменение названия, иконки чата);
* новое сообщение в чате (только сообщения, написанные не владельцем чата, который подписан на Webhook);
* действия над участниками чата (добавление нового участника, выход участника из чата)

Сообщения в чате могут содержать приложения (изображение, видео, аудиозапись, share ссылки), это также отображается в сообщении, 
приходящем на Webhook.

{% format_code https://api.ok.ru/graph/me/subscribe?access_token=tkn18YdUJZe:CQABPOJKAKEKEKEKE %}

**Пример POST-payload**

{% highlight json %}
{
  "url": "http://sample.com/webhook.php"
}
{% endhighlight %}

**Обработка запроса от API**

На зарегистрированные Webhook URL'ы будут отправляться уведомления о входящих сообщениях методом POST в формате JSON.

На каждый такой запрос скрипт должен в течение максимум 5 секунд вернуть HTTP ответ со статусом 200 OK.
Если отправка уведомления не удалась, или если скрипт вернул ответ со статусом, отличным от 200, через некоторое время 
будет предпринята попытка повторной отправки уведомления. Если в течение 8 часов от скрипта не было получено положительного 
ответа, регистрация указанного Webhook может быть автоматически отменена.

Запросы могут производиться только с определенного списка IP-адресов:

* 217.20.145.192/28
* 217.20.151.160/28
* 217.20.153.48/28


**Ответ**

{% highlight json %}
{
  "success": true       /* Индикатор того, что подписка произошла успешно */
}
{% endhighlight %}

**Примеры сообщений**

При появлении нового события в чате / диалоге на зарегистрированный Webhook приходит сообщение с информацией об этом событии.

Сообщения аналогичны тем, которые можно получить при вызове метода /me/messages.

Новое сообщение (без приложения):

{% highlight json %}
{
  "sender": {
    "user_id": "user:123456789012"
  },
  "recipient": {
    "chat_id": "chat:C3ecb9d02a600"
  },
  "message": {
    "text": "text",
    "seq": 98211023614189660,
    "mid": "mid:C3ecb9d02a600.15cea67d78d2059"
  },
  "timestamp": 1498581292941
}
{% endhighlight %}

Новое сообщение (с приложением):

{% highlight json %}
{
  "sender": {
    "user_id": "user:123456789012"
  },
  "recipient": {
    "chat_id": "chat:C3ecb9d02a600"
  },
  "message": {
    "text": "https://ya.ru/",
    "seq": 98211018056672380,
    "attachments": [
      {
        "type": "SHARE",
        "payload": {
          "url": "https://ya.ru/"
        }
      }
    ],
    "mid": "mid:C3ecb9d02a600.15cea668c4c2481"
  },
  "timestamp": 1498581208140
}
{% endhighlight %}