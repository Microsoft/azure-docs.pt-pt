---
title: v1 para v2 Migração API
titleSuffix: Azure Cognitive Services
description: A versão 1 endpoint e a autoria de APIs de compreensão linguística são depreciadas. Utilize este guia para entender como migrar para o ponto final da versão 2 e autoria de APIs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932681"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 para v2 Guia de migração para apps LUIS
O [ponto final](https://aka.ms/v1-endpoint-api-docs) da versão 1 e [a autoria](https://aka.ms/v1-authoring-api-docs) de APIs são depreciados. Utilize este guia para entender como migrar para o [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) da versão 2 e [autoria](https://go.microsoft.com/fwlink/?linkid=2092087) de APIs. 

## <a name="new-azure-regions"></a>Novas regiões de Azure
A LUIS tem novas [regiões previstas](https://aka.ms/LUIS-regions) para as APIs luis. O LUIS fornece um portal diferente para grupos de região. O pedido deve ser da autoria na mesma região que espera consultar. As aplicações não migram automaticamente as regiões. Exporta-se a app de uma região e depois importa-se para outra para estar disponível numa nova região.

## <a name="authoring-route-changes"></a>Alterações na rota de autoria
A rota de autoria da API passou de usar a rota **prog** para a utilização da rota **api.**


| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Alterações na rota do ponto final
O ponto final API tem novos parâmetros de corda de consulta, bem como uma resposta diferente. Se a bandeira verbosa for verdadeira, todas as intenções, independentemente da pontuação, são devolvidas numa matriz chamada intenção, além do topScoringIntent.

| versão | Rota GET |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&encenação][&bing-spell-check-subscrição-key][&log]|


resposta de sucesso de ponto final v1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

resposta de sucesso de ponto final v2:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Gestão chave já não na API
As APIs de ponto final de subscrição são depreciadas, devolvendo 410 GONE.

| versão | rota |
|--|--|
|1|/luis/v1.0/prog/subscrições|
|1|/luis/v1.0/prog/subscrições/{subscriçãoKey}|

As [chaves de ponta](luis-how-to-azure-subscription.md) azure são geradas no portal Azure. Atribui a chave a uma aplicação LUIS na página **[Publicar.](luis-how-to-azure-subscription.md)** Não precisa saber o valor-chave. O LUIS usa o nome da subscrição para fazer a atribuição. 

## <a name="new-versioning-route"></a>Nova rota de versão
O modelo v2 está agora contido numa [versão](luis-how-to-manage-versions.md). Um nome de versão é de 10 caracteres na rota. A versão predefinida é "0.1".

| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entidades|
|2|/luis/**api**/v2.0/apps/{appId}/**versões**/{versionId}/entidades|

## <a name="metadata-renamed"></a>Metadados renomeados
Várias APIs que devolvem metadados LUIS têm novos nomes.

| nome da rota v1 | nome da rota v2 |
|--|--|
|PersonalAssistantApps |assistentes|
|culturas de aplicações|culturas|
|domínios de aplicações|domínios|
|cenários de aplicação|cenários de utilização|


## <a name="sample-renamed-to-suggest"></a>"Amostra" renomeada para "sugerir"
Luis sugere expressões de [expressões de ponto final existentes](luis-how-to-review-endpoint-utterances.md) que podem melhorar o modelo. Na versão anterior, esta foi nomeada **amostra.** Na nova versão, o nome é alterado de amostra para **sugestão**. Isto chama-se **[Comentários finais](luis-how-to-review-endpoint-utterances.md)** no site da LUIS.

| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entidades/{entityId}/**amostra**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**amostra**|
|2|/luis/**api**/v2.0/apps/{appId}/**versões**/{versionId}/entities/{entityId}/**sugerem**|
|2|/luis/**api**/v2.0/apps/{appId}/**versões**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>Criar app a partir de domínios pré-construídos
[Os domínios pré-construídos](luis-how-to-use-prebuilt-domains.md) fornecem um modelo de domínio predefinido. Os domínios pré-construídos permitem-lhe desenvolver rapidamente a sua aplicação LUIS para domínios comuns. Esta API permite-lhe criar uma nova aplicação com base num domínio pré-construído. A resposta é a nova appID.

|rota v2|verbo|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomínios  |obter, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importação de 1.x app em 2.x
O JSON da aplicação exportada 1.x tem algumas áreas que precisa de mudar antes de importar para [LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Entidades pré-criadas 
As [entidades pré-construídas](luis-prebuilt-entities.md) mudaram. Certifique-se de que está a utilizar as entidades pré-construídas V2. Isto inclui a utilização da [dataV2,](luis-reference-prebuilt-datetimev2.md)em vez da data. 

### <a name="actions"></a>Ações
A propriedade de ações já não é válida. Deve ser um vazio. 

### <a name="labeled-utterances"></a>Expressões rotuladas
V1 permitiu que as expressões rotuladas incluam espaços no início ou no fim da palavra ou frase. Retirou os espaços. 

## <a name="common-reasons-for-http-response-status-codes"></a>Razões comuns para códigos de estado de resposta HTTP
Consulte os códigos de [resposta da API LUIS](luis-reference-response-codes.md).

## <a name="next-steps"></a>Passos seguintes

Utilize a documentação v2 DaPI para atualizar as chamadas REST existentes para o [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) luis e [autoria](https://go.microsoft.com/fwlink/?linkid=2092087) de APIs. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
