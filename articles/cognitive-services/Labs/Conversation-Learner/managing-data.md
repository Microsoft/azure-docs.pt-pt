---
title: Gerenciando dados do usuário com o Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como gerenciar dados do usuário com o Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705203"
---
# <a name="managing-user-data"></a>Gerenciando dados do usuário

Esta página descreve o que o Conversation Learner serviço de nuvem registra ao conduzir caixas de diálogo com usuários finais.  Ele também descreve como associar logs de Conversation Learner com IDs de usuário, para que você possa recuperar ou excluir todos os logs associados a um usuário específico.

## <a name="overview-of-end-user-data-logging"></a>Visão geral do log de dados do usuário final

Por padrão, o serviço de nuvem Conversation Learner registra as interações entre os usuários finais e o bot.  Esses logs são importantes para melhorar o bot, permitindo que você identifique os casos em que o bot extraiu a entidade incorreta ou selecionou a ação incorreta.  Esses erros podem ser corrigidos acessando a página "registrar caixas de diálogo" da interface do usuário, fazendo correções e armazenando essa caixa de diálogo corrigida como uma nova caixa de diálogo de treinamento. Para obter mais informações, consulte o tutorial sobre "caixas de diálogo de log".

## <a name="how-to-disable-logging"></a>Como desabilitar o registro em log

Você pode controlar se as conversas com usuários finais estão na página "configurações" do seu modelo de Conversation Learner.  Há uma caixa de seleção para "conversas de log".  Ao desmarcar essa caixa, as conversas com usuários finais não serão registradas em log.

## <a name="what-is-logged"></a>O que é registrado em log 

Em caixas de diálogo de log, Conversation Learner armazena a entrada do usuário, os valores de entidade, as ações selecionadas e os carimbos de data/hora de cada vez.  Esses logs são armazenados por um período de tempo e, em seguida, descartados (consulte a página de ajuda em "valor padrão e limites" para obter detalhes).  

Conversation Learner cria uma ID exclusiva para cada caixa de diálogo registrada.  Conversation Learner não *armazena um* identificador de usuário com caixas de diálogo registradas.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associando caixas de diálogo registradas a uma ID de usuário

Geralmente, é importante ser capaz de associar caixas de diálogo registradas com a ID do usuário – por exemplo, para poder recuperar ou excluir caixas de diálogo registradas de um usuário específico.  Como Conversation Learner não armazena um identificador de usuário, essa associação precisa ser mantida pelo código do desenvolvedor.  

Para criar esse mapeamento, obtenha a ID da caixa de diálogo registrada em `EntityDetectionCallback`; em seguida, no armazenamento do bot, armazene a associação entre a ID de usuário e essa caixa de diálogo registrada.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Cabeçalhos para chamadas HTTP

Em cada uma das chamadas HTTP abaixo, adicione o seguinte cabeçalho:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

em `<LUIS_AUTHORING_KEY>` que é a chave de criação Luis usada para acessar seus aplicativos Conversation Learner.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Como obter dados brutos para uma caixa de diálogo registrada

Para obter os dados brutos de uma caixa de diálogo de log, você pode usar essa chamada HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Em `<appId>` que é o GUID para esse modelo de Conversation Learner `<logDialgoId>` e é a ID da caixa de diálogo de log que você deseja recuperar.  

> [!NOTE]
> As caixas de diálogo de log podem ser editadas pelo desenvolvedor e, em seguida, armazenadas como caixas de diálogo de treinamento.  Quando isso for feito, Conversation Learner armazenará a ID da caixa de diálogo de log "origem" com a caixa de diálogo de treinamento.  Além disso, uma caixa de diálogo de treinamento pode ser "ramificada" na interface do usuário; se uma caixa de diálogo de treinamento tiver uma ID de caixa de diálogo de log de origem associada, as ramificações desse diálogo de treinamento serão marcadas com a mesma ID de caixa de diálogo de log.

Para obter todas as caixas de diálogo de treinamento que foram derivadas de uma caixa de diálogo de log, siga estas etapas.

Primeiro, recupere todas as caixas de diálogo de treinamento:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Em `<appId>` que é o GUID deste modelo de Conversation Learner.  

Isso retorna todas as caixas de diálogo de treinamento.  Pesquise esta lista para o associado `sourceLogDialogId`e observe o associado. `trainDialogId` 

Para uma única caixa de diálogo de treinamento por ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Em `<appId>` que é o GUID para este modelo de Conversation Learner `<trainDialogId>` e é a ID da caixa de diálogo de treinamento que você deseja recuperar.  

## <a name="how-to-delete-a-logged-dialog"></a>Como excluir uma caixa de diálogo registrada

Se você quiser excluir uma caixa de diálogo de log de acordo com sua ID, poderá usar essa chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Em `<appId>` que é o GUID para esse modelo de Conversation Learner `<logDialogId>` e é a ID da caixa de diálogo de log que você deseja excluir. 

Se você quiser excluir uma caixa de diálogo de treinamento de acordo com sua ID, poderá usar essa chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Em `<appId>` que é o GUID para este modelo de Conversation Learner `<trainDialogId>` e é a ID da caixa de diálogo de treinamento que você deseja excluir. 
