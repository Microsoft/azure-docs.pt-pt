---
title: Gestão de dados de utilizador com o aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como gerir dados de utilizador com o aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7ea0b246a16ff196a4160d9822b5db15cd39a4a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385208"
---
# <a name="managing-user-data"></a>Gestão de dados de utilizador

Esta página descreve o que o serviço de nuvem de aprendiz de conversação registra ao conduzir as caixas de diálogo com os utilizadores finais.  Também descreve como associar os registos de aprendiz de conversação com IDs de utilizador, para que possa obter ou eliminar todos os registos associados a um utilizador específico.

## <a name="overview-of-end-user-data-logging"></a>Descrição geral do registo de dados do utilizador final

Por predefinição, o serviço de nuvem de aprendiz de conversação regista as interações entre os utilizadores finais e o seu bot.  Estes registos são importantes para melhorar o seu bot, permitindo-lhe identificar casos em que o seu bot extraídos da entidade incorreta ou selecionado a ação incorreta.  Estes erros, em seguida, podem ser corrigidos ao aceder à página "Log caixas de diálogo de" da interface do Usuário, fazer correções e armazenar essa caixa de diálogo corrigida como uma nova caixa de diálogo de comboio. Para obter mais informações, consulte o tutorial sobre "Caixas de diálogo de registo."

## <a name="how-to-disable-logging"></a>Como desativar o registo

Pode controlar se conversas com os utilizadores finais estão na página "Definições" para o modelo de aprendiz de conversação.  Há uma caixa de verificação "Conversações de registo".  Ao desmarcar a opção nesta caixa, não serão registadas conversas com os utilizadores finais.

## <a name="what-is-logged"></a>O que é registado 

Nas caixas de diálogo de registo, o aprendiz de conversação armazena a entrada do usuário, os valores de entidade, ações selecionadas e carimbos de data / para cada vez.  Estes registos são armazenados durante um período de tempo e, em seguida, eliminados (consulte a página de ajuda em "limites e de valor predefinido" para obter detalhes).  

Aprendiz de conversação criará uma ID exclusiva para cada caixa de diálogo com sessão iniciada.  Aprendiz de conversação faz *não* armazenar um identificador de utilizador com caixas de diálogo com sessão iniciada.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associar com sessão iniciada a caixas de diálogo com um ID de utilizador

Muitas vezes, é importante poder associar caixas de diálogo registadas com o ID do utilizador – por exemplo, para conseguir obter ou eliminar caixas de diálogo com sessão iniciada a partir de um utilizador específico.  Uma vez que o aprendiz de conversação não armazena um identificador de utilizador, esta associação tem de ser mantidos pelo código do desenvolvedor.  

Para criar esse mapeamento, obtenha o ID da caixa de diálogo com sessão iniciada no `EntityDetectionCallback`; em seguida, no armazenamento de seu bot, armazenar a associação entre o ID de utilizador e esta caixa de diálogo com sessão iniciada.  

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

onde `<LUIS_AUTHORING_KEY>` é o chave utilizada para aceder às suas aplicações de aprendiz de conversação de criação de LUIS.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Como obter dados não processados para uma caixa de diálogo com sessão iniciada

Para obter os dados não processados para uma caixa de diálogo de registo, pode utilizar esta chamada HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Em que `<appId>` é o GUID para este modelo de aprendiz de conversação e `<logDialgoId>` é o ID da caixa de diálogo de registo que pretende recuperar.  

> [!NOTE]
> Caixas de diálogo de registo podem ser editadas pelo desenvolvedor e, em seguida, armazenadas como preparar as caixas de diálogo.  Quando isso for feito, o aprendiz de conversação armazena a identificação da caixa de diálogo de registo de "origem" com a caixa de diálogo de comboio.  Além disso, uma caixa de diálogo train pode ser ", ser também autor" na interface do Usuário; Se uma caixa de diálogo train tem um ID de caixa de diálogo de registo de origem associada, em seguida, ramificações essa caixa de diálogo train serão marcadas com o mesmo ID de caixa de diálogo de registo.

Para obter todas as caixas de diálogo de formação que foram derivadas de uma caixa de diálogo de registo, siga estes passos.

Primeiro, recuperar todas as caixas de diálogo train:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Onde `<appId>` é o GUID para este modelo de aprendiz de conversação.  

Esta ação devolve todas as caixas de diálogo de comboio.  Procurar nesta lista associada `sourceLogDialogId`e observe associada `trainDialogId`. 

Para uma única Treine a caixa de diálogo por ID de:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Em que `<appId>` é o GUID para este modelo de aprendiz de conversação e `<trainDialogId>` é o ID da caixa de diálogo train que pretende recuperar.  

## <a name="how-to-delete-a-logged-dialog"></a>Como eliminar uma caixa de diálogo com sessão iniciada

Se pretender eliminar uma caixa de diálogo de registo, função de sua ID, pode usar esta chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Em que `<appId>` é o GUID para este modelo de aprendiz de conversação e `<logDialogId>` é o ID da caixa de diálogo de registo que pretende eliminar. 

Se pretender eliminar uma caixa de diálogo train função de sua ID, pode usar esta chamada HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Em que `<appId>` é o GUID para este modelo de aprendiz de conversação e `<trainDialogId>` é o ID da caixa de diálogo train que pretende eliminar. 
