---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 3086d15ba541aa7f08f983dac4bc363f43248a9e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062918"
---
## <a name="webapi-project"></a>Projeto WebAPI
1. No Visual Studio, abra o projeto **AppBackend** que você criou no tutorial **notificar usuários** .
2. No Notifications.cs, substitua toda a classe **notificações** pelo código a seguir. Certifique-se de substituir os espaços reservados pela sua cadeia de conexão (com acesso completo) para o Hub de notificação e o nome do Hub. Você pode obter esses valores da [portal do Azure](https://portal.azure.com). Este módulo agora representa as diferentes notificações seguras que serão enviadas. Em uma implementação completa, as notificações serão armazenadas em um banco de dados; para simplificar, nesse caso, os armazenamos na memória.
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification
                {
                    Id = notifications.Count,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. No NotificationsController.cs, substitua o código dentro da definição da classe **NotificationsController** pelo código a seguir. Esse componente implementa uma maneira de o dispositivo recuperar a notificação com segurança e também fornece uma maneira (para fins deste tutorial) disparar um envio seguro para seus dispositivos. Observe que, ao enviar a notificação para o Hub de notificação, enviamos apenas uma notificação bruta com a ID da notificação (e nenhuma mensagem real):
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


Observe que o método `Post` agora não envia uma notificação do sistema. Ele envia uma notificação bruta que contém apenas a ID de notificação e não qualquer conteúdo confidencial. Além disso, lembre-se de comentar a operação de envio para as plataformas para as quais você não tem credenciais configuradas em seu hub de notificação, pois elas resultarão em erros.

1. Agora vamos implantar novamente esse aplicativo em um site do Azure para torná-lo acessível de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
2. Selecione site do Azure como seu destino de publicação. Entre com sua conta do Azure e selecione um site novo ou existente e anote a propriedade **URL de destino** na guia **conexão** . Iremos nos referir a essa URL como seu *ponto de extremidade de back-end* posteriormente neste tutorial. Clique em **Publicar**.

