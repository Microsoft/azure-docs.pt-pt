---
title: Como usar hubs de notificação com Java
description: Saiba como usar os hubs de notificação do Azure de um back-end do Java.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 532ffc7a7393f016f27264b67b4ee5d3e6e5888f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213201"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Como usar hubs de notificação do Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Este tópico descreve os principais recursos do novo SDK do Java do hub de notificação oficial do Azure totalmente compatível.
Este projeto é um projeto de código aberto e você pode exibir todo o código do SDK em [SDK Java].

Em geral, você pode acessar todos os recursos de hubs de notificação de um back-end Java/PHP/Python/Ruby usando a interface REST do hub de notificação, conforme descrito no tópico MSDN [APIs REST dos hubs de notificação](https://msdn.microsoft.com/library/dn223264.aspx). Este SDK do Java fornece um wrapper fino sobre essas interfaces REST em Java.

Atualmente, o SDK dá suporte a:

* CRUD em hubs de notificação
* CRUD nos registros
* Gerenciamento de instalação
* Importar/exportar registros
* Envios regulares
* Envios agendados
* Operações assíncronas via Java NIO
* Plataformas com suporte: APNS (iOS), FCM (Android), WNS (aplicativos da Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android sem serviços do Google)

## <a name="sdk-usage"></a>Uso do SDK

### <a name="compile-and-build"></a>Compilar e compilar

Usar [Maven]

Para compilar:

    mvn package

## <a name="code"></a>Código

### <a name="notification-hub-cruds"></a>CRUDs do hub de notificação

**Criar um NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Criar Hub de notificação:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 OU

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Obter Hub de notificação:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Atualizar Hub de notificação:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Excluir Hub de notificação:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>CRUDs de registro

**Criar um cliente do hub de notificação:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Criar registro do Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Criar registro do iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Da mesma forma, você pode criar registros para Android (FCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Criar registros de modelo:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Criar registros usando a ID de registro de criação + padrão Upsert:**

Remove duplicatas devido a quaisquer respostas perdidas se armazenar IDs de registro no dispositivo:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Atualizar registros:**

    ```java
    hub.updateRegistration(reg);
    ```

**Excluir registros:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Registros de consulta:**

* **Obter registro único:**

    ```java
    hub.getRegistration(regid);
    ```

* **Obter todos os registros no Hub:**

    ```java
    hub.getRegistrations();
    ```

* **Obter registros com a marca:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Obter registros por canal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Todas as consultas de coleção dão suporte a tokens de $top e de continuação.

### <a name="installation-api-usage"></a>Uso da API de instalação

A API de instalação é um mecanismo alternativo para o gerenciamento de registro. Em vez de manter vários registros, que não são triviais e podem ser facilmente executados incorretamente ou de forma ineficiente, agora é possível usar um único objeto de instalação.

A instalação contém tudo o que você precisa: canal de push (token de dispositivo), marcas, modelos, blocos secundários (para WNS e APNS). Você não precisa chamar o serviço para obter mais ID – apenas gere o GUID ou qualquer outro identificador, mantenha-o no dispositivo e envie-o ao seu back-end junto com o canal de push (token do dispositivo).

No back-end, você deve fazer apenas uma chamada para `CreateOrUpdateInstallation`; ele é totalmente idempotente, portanto, sinta-se à vontade para tentar novamente, se necessário.

Como exemplo para o Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Se você quiser atualizá-lo:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Para cenários avançados, use o recurso de atualização parcial, que permite modificar somente as propriedades específicas do objeto de instalação. A atualização parcial é um subconjunto de operações de patch JSON que você pode executar em relação ao objeto de instalação.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Excluir instalação:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch` `Get`e `Delete` são eventualmente consistentes com o. A operação solicitada apenas vai para a fila do sistema durante a chamada e é executada em segundo plano. Get não é projetado para o cenário de tempo de execução principal, mas apenas para fins de depuração e solução de problemas, ele é rigidamente limitado pelo serviço.

O fluxo de envio para instalações é o mesmo para registros. Para direcionar a notificação para a instalação específica – basta usar a marca "InstallationID: {Desired-ID}". Para esse caso, o código é:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Para um dos vários modelos:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Agendar notificações (disponível para a camada STANDARD)

O mesmo que o envio regular, mas com um parâmetro adicional-scheduletime, que informa quando a notificação deve ser entregue. O serviço aceita qualquer ponto de tempo entre agora + 5 minutos e agora + 7 dias.

**Agendar uma notificação nativa do Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importação/exportação (disponível para a camada STANDARD)

Talvez seja necessário executar a operação em massa em relação aos registros. Normalmente, é para integração com outro sistema ou uma grande correção para atualizar as marcas. Não recomendamos o uso do fluxo de get/update se milhares de registros estiverem envolvidos. A funcionalidade de importação/exportação do sistema foi projetada para cobrir o cenário. Você fornecerá acesso a um contêiner de BLOB em sua conta de armazenamento como uma fonte de dados de entrada e local para saída.

**Enviar um trabalho de exportação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Enviar um trabalho de importação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Aguarde até que um trabalho seja feito:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Obter todos os trabalhos:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI com assinatura SAS:**

 Essa URL é a URL de um arquivo de BLOB ou contêiner de BLOB, além de um conjunto de parâmetros, como permissões e tempo de expiração, além da assinatura de todas essas coisas feitas usando a chave de SAS da conta. O SDK do Java do armazenamento do Azure tem recursos avançados, incluindo a criação desses URIs. Como alternativa simples, dê uma olhada na `ImportExportE2E` classe de teste (do local do GitHub) que tem implementação básica e compacta do algoritmo de assinatura.

### <a name="send-notifications"></a>Enviar notificações

O objeto de notificação é simplesmente um corpo com cabeçalhos, alguns métodos de utilitário ajudam na criação dos objetos nativos e de notificações de modelo.

* **Windows Store e Windows Phone 8,1 (não Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8,0 e 8,1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Incêndio Kindle**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Enviar para marcas**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Enviar para expressão de marca**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Enviar notificação de modelo**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Executar o código Java agora deve produzir uma notificação que aparece em seu dispositivo de destino.

## <a name="next-steps"></a>Passos Seguintes

Este tópico mostrou como criar um cliente REST Java simples para os hubs de notificação. A partir daqui, você pode:

* Baixe o [SDK Java]completo, que contém todo o código do SDK.
* Jogue com os exemplos:
  * [Introdução aos hubs de notificação]
  * [Enviar últimas notícias]
  * [Enviar últimas notícias localizadas]
  * [Enviar notificações para usuários autenticados]
  * [Enviar notificações entre plataformas para usuários autenticados]

[SDK Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Introdução aos hubs de notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Enviar últimas notícias]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Enviar últimas notícias localizadas]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Enviar notificações para usuários autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Enviar notificações entre plataformas para usuários autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
