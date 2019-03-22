---
title: Como utilizar os Hubs de notificação com Java
description: Saiba como utilizar Notification Hubs do Azure a partir de um back-end de Java.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 993eafd5a0b74be706d13fe8e06483c223f81eb6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901190"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Como utilizar os Hubs de notificação de Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Este tópico descreve os principais recursos do novo suporte total oficial Notification Hub SDK Java do Azure.
Este projeto é um projeto de código-fonte aberto e pode ver todo o código SDK em [SDK Java].

Em geral, pode acessar todas as funcionalidades dos Hubs de notificação de um back-end Java/PHP/Python/Ruby com a interface REST do Hub de notificação, conforme descrito no tópico do MSDN [APIs de REST dos Hubs de notificação](https://msdn.microsoft.com/library/dn223264.aspx). Esse SDK de Java fornece um wrapper estreito em relação a essas interfaces REST em Java.

O SDK suporta atualmente:

* CRUD nos Hubs de notificação
* CRUD registarem
* Gestão de instalação
* Importar/Exportar registos
* Envia regular
* Envia agendada
* Operações assíncronas via NIO de Java
* Plataformas suportadas: APNS (iOS), o FCM (Android), o WNS (aplicativos da Windows Store), o MPNS (Windows Phone), o ADM (Amazon Kindle Fire), o Baidu (Android sem o Google services)

## <a name="sdk-usage"></a>Utilização do SDK

### <a name="compile-and-build"></a>Compilação e compilação

Utilize [Maven]

Para criar:

    mvn package

## <a name="code"></a>Código

### <a name="notification-hub-cruds"></a>CRUDs do Hub de notificação

**Crie um NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Crie Hub de notificação:**

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

**Hub de notificação de atualização:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Elimine Hub de notificação:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Registo CRUDs

**Crie um Hub de notificação de cliente:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Crie o registo do Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Crie registo de iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Da mesma forma pode criar registos para Android (FCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Crie registos de modelos:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Criar registos ao utilizar a criar o ID de registo + upsert padrão:**

Remove duplicatas devido a todas as respostas perdidas se armazenar o ID de registo do dispositivo:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Registos de atualização:**

    ```java
    hub.updateRegistration(reg);
    ```

**Elimine registos:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Registos de consulta:**

* **Obter registo único:**

    ```java
    hub.getRegistration(regid);
    ```

* **Obter todos os registos de hub:**

    ```java
    hub.getRegistrations();
    ```

* **Obter registros com marca:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Obter registos de canal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Todas as consultas de coleção suportam tokens $top e continuação.

### <a name="installation-api-usage"></a>Utilização da API de instalação

API de instalação é um mecanismo alternativo para a gestão de registo. Em vez de manter vários registros, o que não são simples e podem ser feitos facilmente incorretamente ou de forma ineficiente, agora é possível usar um objeto de instalação única.

Instalação contém tudo o que precisa: enviar por push o canal (token de dispositivo), etiquetas, modelos, blocos secundários (para o WNS e APNS). Não precisa chamar o serviço para obter o ID já - apenas gerar GUID ou qualquer outro identificador, mantê-la no dispositivo e enviar para o seu back-end, juntamente com o canal de push (token de dispositivo).

Back-end, deve fazer apenas uma única chamada para `CreateOrUpdateInstallation`; ele é totalmente idempotentes, fique à vontade repetir se for necessário.

Como exemplo para o Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Se quiser atualizá-lo:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Para cenários avançados, utilize a capacidade de atualização parcial, o que permite modificar apenas as propriedades específicas do objeto de instalação. Atualização parcial é o subconjunto de operações de JSON Patch, que pode executar o objeto de instalação.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Elimine a instalação:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`, e `Delete` são eventualmente consistente com `Get`. A operação pedida só vai para a fila de sistema durante a chamada e é executada em segundo plano. Get não foi concebido para o cenário de tempo de execução principal, mas apenas para depuração e fins de resolução de problemas, ele está fortemente limitado pelo serviço.

Envie o fluxo para instalações de é igual de registos. Notificação para a instalação particular de destino-basta usar a marca "InstallationId: {desired-id}". Para esse caso, o código é:

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

### <a name="schedule-notifications-available-for-standard-tier"></a>Agende notificações (disponíveis para o escalão STANDARD)

O mesmo como envio regular mas com um parâmetro adicional - scheduledTime, que indica quando deve ser entregue notificação. Serviço aceita qualquer ponto no tempo entre agora + 5 minutos e agora + 7 dias.

**Agende uma notificação de nativa do Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importar/exportar (disponível para o escalão STANDARD)

Terá de efetuar a operação em massa em relação a registos. Normalmente, é para integração com o outro sistema ou uma correção em massa para atualizar as etiquetas. Não recomendamos utilizar o fluxo de obter/atualizar, se estiverem envolvidos milhares de registos. Capacidade de importação/exportação do sistema foi projetada para cobrir o cenário. Irá fornecer acesso a um contentor de BLOBs na sua conta de armazenamento como uma origem de dados de entrada e localização de saída.

**Submeta uma tarefa de exportação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Submeta uma tarefa de importação:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Aguarde até que uma tarefa está concluída:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Obter todas as tarefas:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI com assinatura SAS:**

 Este URL é o URL de um arquivo de blob ou contentor de BLOBs e um conjunto de parâmetros, como as permissões e a hora de expiração e assinatura de todas essas coisas efetuada através da chave SAS da conta. SDK de Java de armazenamento do Azure tem capacidades avançadas, incluindo a criação destes URIs. Como alternativa simple, dê uma olhada a `ImportExportE2E` classe (a partir da localização de GitHub) que tem uma implementação básica e compact do algoritmo de assinatura de teste.

### <a name="send-notifications"></a>Enviar notificações

O objeto de notificação é simplesmente um corpo com cabeçalhos, alguns métodos de utilitário ajudam na criação de objetos de notificações nativos e do modelo.

* **Windows Store e Windows Phone 8.1 (não Silverlight)**

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

* **Windows Phone 8.0 e 8.1 Silverlight**

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

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Enviar para etiquetas**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Enviar para expressão de etiqueta**

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

Execução do seu código Java agora deve produzir uma notificação que aparece no seu dispositivo de destino.

## <a name="next-steps"></a>Passos Seguintes

Este tópico mostrou como criar um cliente de Java REST simple para os Hubs de notificação. A partir daqui, pode:

* Transferir o completo [SDK Java], que contém todo o código do SDK.
* Brincar com os exemplos:
  * [Introdução aos Hubs de notificação]
  * [Enviar notícias de última hora]
  * [Enviar notícias de última hora localizada]
  * [Enviar notificações para utilizadores autenticados]
  * [Enviar notificações multiplataformas para utilizadores autenticados]

[SDK Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Introdução aos Hubs de notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Enviar notícias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Enviar notícias de última hora localizada]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Enviar notificações para utilizadores autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Enviar notificações multiplataformas para utilizadores autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
