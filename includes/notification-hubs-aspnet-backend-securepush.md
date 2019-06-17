---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111526"
---
## <a name="webapi-project"></a>Projeto WebAPI
1. No Visual Studio, abra a **AppBackend** projeto que criou no **notificar utilizadores** tutorial.
2. Em Notifications.cs, substitua o todo **notificações** classe com o código a seguir. Certifique-se de que substitua os marcadores de posição pela cadeia de ligação (com acesso total) para o hub de notificação e o nome do hub. Pode obter estes valores a partir da [portal do Azure](http://portal.azure.com). Este módulo agora representa as notificações de segurança diferentes que serão enviadas. Uma implementação completa, as notificações serão armazenadas numa base de dados; Para simplificar, neste caso, armazená-las na memória.
   
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
                var notification = new Notification() {
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

1. No NotificationsController.cs, substitua o código dentro do **NotificationsController** definição com o seguinte código de classe. Este componente implementa uma forma do dispositivo obtenha a notificação de forma segura e também fornece uma forma (para efeitos deste tutorial) para acionar um envio seguro para os seus dispositivos. Tenha em atenção que ao enviar a notificação para o hub de notificação, estamos apenas enviar uma notificação não processada com o ID de notificação (e nenhuma mensagem real):
   
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


Tenha em atenção que o `Post` método agora não envia uma notificação de alerta. Envia uma notificação não processada que contém apenas o ID de notificação e não qualquer conteúdo confidencial. Além disso, lembre-se de que a operação de envio para as plataformas para o qual não tem credenciais configuradas no seu hub de notificação, pois eles irão resultar em erros de comentário.

1. Agora, vamos voltar implementar esta aplicação num website do Azure para que seja acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
2. Selecione o Web site do Azure como o destino da publicação. Inicie sessão com a sua conta do Azure, selecione um site novo ou existente e anote o **URL de destino** propriedade no **ligação** separador. Vamos referir-nos a este URL como o *ponto final do seu back-end* mais adiante neste tutorial. Clique em **Publicar**.

