---
title: v1 para v2 Migração API
titleSuffix: Azure Cognitive Services
description: O ponto final da versão 1 e a autoria de APIs de compreensão linguística são depreciados. Utilize este guia para entender como migrar para a versão 2 e autorizar APIs.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018757"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Guia de migração da API v1 para v2 Migração para apps LUIS
O ponto [final da](https://aka.ms/v1-endpoint-api-docs) versão 1 e as APIs [de autoria](https://aka.ms/v1-authoring-api-docs) são depreciadas. Utilize este guia para entender como migrar para a versão [2](https://go.microsoft.com/fwlink/?linkid=2092356) e [autorizar](https://go.microsoft.com/fwlink/?linkid=2092087) APIs.

## <a name="new-azure-regions"></a>Novas regiões de Azure
A LUIS tem novas [regiões previstas](./luis-reference-regions.md) para as APIs luis. O LUIS disponibiliza um portal diferente para grupos regionais. O pedido deve ser da autoria da mesma região que espera consultar. As aplicações não migram automaticamente para as regiões. Exporta a app de uma região e depois importa para outra para que esteja disponível numa nova região.

## <a name="authoring-route-changes"></a>Alterações de rota de autoria
A rota da autoria da API passou de usar a rota **prog** para usar a rota **api.**


| versão | route |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Alterações na rota do ponto final
A API de ponta final tem novos parâmetros de cadeia de consulta, bem como uma resposta diferente. Se a bandeira verbosa for verdadeira, todas as intenções, independentemente da pontuação, são devolvidas numa matriz chamada intenção, além do topScoringInt.

| versão | Rota GET |
|--|--|
|1|/luis/v1/application?ID={appId}}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log] [&log][&log][&log][&bing-spell-check-subscription-key][&log]|


v1 resposta ao sucesso do ponto final:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 resposta ao sucesso do ponto final:
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
As APIs chave de ponto final de subscrição são depreciadas, devolvendo 410 GONE.

| versão | route |
|--|--|
|1|/luis/v1.0/prog/subscrições|
|1|/luis/v1.0/prog/subscrições/{subscriçãoKey}|

As [teclas de ponto final](luis-how-to-azure-subscription.md) Azure são geradas no portal Azure. Atribua a chave a uma aplicação LUIS na página **[Publicar.](luis-how-to-azure-subscription.md)** Não precisa saber o valor real da chave. Luis usa o nome de assinatura para fazer a atribuição.

## <a name="new-versioning-route"></a>Nova rota de versão
O modelo V2 está agora contido numa [versão](luis-how-to-manage-versions.md). Um nome de versão é de 10 caracteres na rota. A versão predefinida é "0.1".

| versão | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versões**/{versionId}/entities|

## <a name="metadata-renamed"></a>Metadados renomeados
Várias APIs que devolvem metadados LUIS têm novos nomes.

| v1 nome da rota | v2 nome da rota |
|--|--|
|PersonalAssistantApps |assistentes|
|aplicações|culturas|
|candidaturas|domínios|
|aplicaçãousagescenarios|usescenarios|


## <a name="sample-renamed-to-suggest"></a>"Amostra" renomeada para "sugerir"
LUIS sugere declarações de [frases](luis-how-to-review-endpoint-utterances.md) de ponta existentes que podem melhorar o modelo. Na versão anterior, esta foi nomeada **amostra.** Na nova versão, o nome é alterado de amostra para **sugestão**. Isto chama-se **[Rever as expressões](luis-how-to-review-endpoint-utterances.md)** de ponto final no site da LUIS.

| versão | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**amostra**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**amostra**|
|2|/luis/**api**/v2.0/apps/{appD}/**versões**/{versionId}/entities/{entityId}/**sugerem**|
|2|/luis/**api**/v2.0/apps/{appId}/**versões**/{versionId}/intents/{intentId}/**sugerem**|


## <a name="create-app-from-prebuilt-domains"></a>Criar aplicativo a partir de domínios pré-construídos
[Os domínios pré-construídos](./howto-add-prebuilt-models.md) fornecem um modelo de domínio predefinido. Os domínios pré-construídos permitem-lhe desenvolver rapidamente a sua aplicação LUIS para domínios comuns. Esta API permite criar uma nova aplicação baseada num domínio pré-construído. A resposta é a nova appID.

|v2 rota|verbo|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |obter, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importar app 1.x em 2.x
O JSON da app 1.x exportado tem algumas áreas que precisa de alterar antes de importar para [LUIS][LUIS] 2.0.

### <a name="prebuilt-entities"></a>Entidades pré-criadas
As [entidades pré-construídas](./howto-add-prebuilt-models.md) mudaram. Certifique-se de que está a utilizar as entidades pré-construídas V2. Isto inclui a utilização [do tempo de dataV2,](luis-reference-prebuilt-datetimev2.md)em vez de hora de data.

### <a name="actions"></a>Ações
A propriedade de ações já não é válida. Deve ser um vazio.

### <a name="labeled-utterances"></a>Expressões rotuladas
V1 permitiu que as expressões rotuladas incluíssem espaços no início ou no fim da palavra ou frase. Retirou os espaços.

## <a name="common-reasons-for-http-response-status-codes"></a>Razões comuns para códigos de estado de resposta HTTP
Consulte [os códigos de resposta da API LUIS](luis-reference-response-codes.md).

## <a name="next-steps"></a>Próximos passos

Utilize a documentação v2 API para atualizar as chamadas REST existentes para [o ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) da LUIS e para as APIs de [autoria.](https://go.microsoft.com/fwlink/?linkid=2092087)

[LUIS]: ./luis-reference-regions.md