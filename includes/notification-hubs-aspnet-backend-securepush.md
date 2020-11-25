---
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/14/2020
ms.author: sethm
ms.openlocfilehash: fb3c95b74128f1da7b29a290e17fefe21987dd76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019429"
---
## <a name="webapi-project"></a>Projeto WebAPI

1. No Visual Studio, abra o projeto **AppBackend** que criou no tutorial **de Notifica users.**
2. Em Notifications.cs, substitua toda a classe **notificações** pelo seguinte código. Certifique-se de que substitui os espaços reservados pela sua cadeia de ligação (com acesso total) para o seu centro de notificação e o nome do hub. Pode obter estes valores a partir do [portal Azure](https://portal.azure.com). Este módulo representa agora as diferentes notificações seguras que serão enviadas. Numa implementação completa, as notificações serão armazenadas numa base de dados; para a simplicidade, neste caso armazenamos na memória.

   ```csharp
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
    ```

3. Em NotificationsController.cs, substitua o código dentro da definição da classe **NotificationsController** pelo seguinte código. Este componente implementa uma forma de o dispositivo recuperar a notificação de forma segura, e também fornece uma forma (para efeitos deste tutorial) de desencadear um impulso seguro para os seus dispositivos. Note que ao enviar a notificação para o centro de notificação, apenas enviamos uma notificação em bruto com o ID da notificação (e nenhuma mensagem real):

   ```csharp
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
    ```

Note que o `Post` método agora não envia uma notificação de torradas. Envia uma notificação em bruto que contém apenas o ID de notificação, e não qualquer conteúdo sensível. Além disso, certifique-se de comentar a operação de envio para as plataformas para as quais não possui credenciais configuradas no seu centro de notificações, uma vez que resultarão em erros.

1. Agora vamos re-implantar esta aplicação para um Website Azure de forma a torná-la acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
2. Selecione o Website Azure como o seu alvo de publicação. Faça sôr-se com a sua conta Azure e selecione um Website existente ou novo, e tome nota da propriedade URL de **destino** no separador **'Ligação'.** Vamos referir-nos a este URL como o seu ponto final de *backend* mais tarde neste tutorial. Clique em **Publish** (Publicar).
