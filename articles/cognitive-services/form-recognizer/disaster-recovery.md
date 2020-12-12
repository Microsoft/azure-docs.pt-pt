---
title: Orientação de recuperação de desastres para O Reconhecimento de FormulárioS Azure
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o modelo de cópia API para apoiar os recursos do Form Recogniser.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 168dcf994d00ba1cb2070665ada5a55cf86cfa4a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359817"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Recue e recupere os seus modelos de Reconhecimento de Formulários

Quando cria um recurso de Reconhecimento de Formulários no portal Azure, especifica uma região. A partir daí, o seu recurso e todas as suas operações permanecem associados a essa região de servidores Azure em particular. É raro, mas não impossível, encontrar um problema de rede que atinge toda uma região. Se a sua solução precisar de estar sempre disponível, então deve desenhá-la para falhar noutra região ou dividir a carga de trabalho entre duas ou mais regiões. Ambas as abordagens requerem pelo menos dois recursos de Reconhecimento de Formulários em diferentes regiões e a capacidade de sincronizar [modelos personalizados](./quickstarts/curl-train-extract.md) entre regiões.

A API copy permite este cenário permitindo-lhe copiar modelos personalizados a partir de uma conta de Reconhecimento de Formulário ou em outros, que podem existir em qualquer região geográfica suportada. Este guia mostra-lhe como utilizar a API Copy REST com cURL. Também pode utilizar um serviço de pedido HTTP como o Carteiro para emitir os pedidos.

## <a name="business-scenarios"></a>Cenários de negócio

Se a sua aplicação ou negócio depender da utilização de um modelo personalizado do Form Recogniser, recomendamos que copie o seu modelo para outra conta do Form Recogniser noutra região. Se ocorrer uma paralisação regional, pode então aceder ao seu modelo na região onde foi copiado.

##  <a name="prerequisites"></a>Pré-requisitos

1. Dois recursos de Reconhecimento de Forma Azure em diferentes regiões de Azure. Se não os tiver, vá ao portal Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" crie um novo recurso de Reconhecimento de " target="_blank"> Formulários crie um novo recurso de Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários .
1. A chave de subscrição, URL de ponto final e ID de subscrição do seu recurso Desemboador de Formulários. Pode encontrar estes valores no **separador Visão Geral** do recurso no portal Azure.


## <a name="copy-api-overview"></a>Visão geral da API de cópia

O processo de cópia de um modelo personalizado consiste nos seguintes passos:

1. Primeiro emita um pedido de autorização de cópia ao recurso-alvo, &mdash; ou seja, o recurso que receberá o modelo copiado. Você recebe de volta o URL do modelo-alvo recém-criado, que receberá os dados copiados.
1. Em seguida, envia o pedido de cópia para o recurso de origem &mdash; o recurso que contém o modelo a ser copiado. Receberá de volta um URL que pode consultar para acompanhar o progresso da operação.
1. Você usará as suas credenciais de recurso de origem para consultar o URL de progresso até que a operação seja um sucesso. Também pode consultar o novo ID do modelo no recurso-alvo para obter o estado do novo modelo.

> [!CAUTION]
> A API copy não suporta atualmente iDs de modelo para [modelos personalizados compostos.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose) O composição do modelo é uma funcionalidade de pré-visualização em pré-visualização v2.1-preview.2. 

## <a name="generate-copy-authorization-request"></a>Gerar pedido de autorização de cópia

O seguinte pedido HTTP obtém a autorização de cópia do seu recurso-alvo. Terá de introduzir o ponto final e a chave do seu recurso alvo como cabeçalhos.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Terá uma `201\Created` resposta com um valor no `modelId` corpo. Esta corda é o ID do modelo recém-criado (em branco). O `accessToken` é necessário para que a API copie dados para este recurso, e o valor é a `expirationDateTimeTicks` expiração do token. Guarde os três valores para um local seguro.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Iniciar operação de cópia

O seguinte pedido HTTP inicia a operação Copy no recurso de origem. Terá de introduzir o ponto final e a chave do seu recurso de origem como cabeçalhos. Note que o URL de pedido contém o ID do modelo de origem que pretende copiar.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

O corpo do seu pedido precisa de ter o seguinte formato. Terá de introduzir o ID de recurso e o nome da região do seu recurso alvo. Pode encontrar o seu ID de recurso no **separador Propriedades** do seu recurso no portal Azure, e pode encontrar o nome da região no separador **Chaves e ponto final.** Também vai precisar do ID do modelo, do token de acesso e do valor de expiração que copiou do passo anterior.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> A API copy suporta de forma transparente a função [AEK/CMK.](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) Isto não requer qualquer tratamento especial, mas note que se estiver a copiar entre um recurso não encriptado para um recurso encriptado, tem de incluir o cabeçalho de pedido `x-ms-forms-copy-degrade: true` . Se este cabeçalho não estiver incluído, a operação de cópia falhará e devolverá a `DataProtectionTransformServiceError` .

Terá uma `202\Accepted` resposta com um cabeceamento Operation-Location. Este valor é o URL que você usará para acompanhar o progresso da operação. Copie-o para um local temporário para o próximo passo.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Erros comuns

|Erro|Resolução|
|:--|:--|
| 400 / Mau Pedido com `"code:" "1002"` | Indica erro de validação ou pedido de cópia mal formado. As questões comuns incluem: a) Carga útil inválida ou `copyAuthorization` modificada. b) Valor expirado `expirationDateTimeTicks` para token `copyAuhtorization` (a carga útil é válida por 24 horas). c) Inválido ou não suportado `targetResourceRegion` . d) Cadeia inválida ou mal `targetResourceId` formada.
|

## <a name="track-copy-progress"></a>Rastreio do progresso da cópia

Acompanhe o seu progresso consultando a API **do Resultado do Modelo de Cópia** de Cópia contra o ponto final do recurso de origem.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

A sua resposta variará consoante o estado da operação. Procure o `"status"` campo no corpo json. Se estiver a automatizar esta chamada da API num script, recomendamos que se questione a operação de uma vez a cada segundo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Erros comuns

|Erro|Resolução|
|:--|:--|
|"Erros":[{"código":"AutorizaçãoError",<br>"mensagem":"Falha de autorização devido a <br>pedidos de autorização em falta ou inválidos."}]   | Ocorre quando a `copyAuthorization` carga útil ou o conteúdo são modificados a partir do que foi devolvido pela `copyAuthorization` API. Certifique-se de que a carga útil é o mesmo conteúdo exato que foi devolvido da `copyAuthorization` chamada anterior.|
|"Erros":[{"código":"AutorizaçãoError",<br>"mensagem":"Não foi possível recuperar a autorização <br>Metadados. Se esta questão persistir, use um diferente <br>modelo-alvo para copiar em."}] | Indica que a `copyAuthorization` carga está a ser reutilizada com um pedido de cópia. Um pedido de cópia que tenha sucesso não permitirá mais pedidos que utilizem a mesma `copyAuthorization` carga útil. Se levantar um erro separado (como os abaixo notados) e posteriormente voltar a tentar a cópia com a mesma carga útil de autorização, este erro é levantado. A resolução é gerar uma nova `copyAuthorization` carga útil e, em seguida, reeditar o pedido de cópia.|
|"erros":[{"código":"DataProtectionTransformServiceError",<br>"mensagem":"Pedido de transferência de dados não é permitido <br>à medida que se reduz para um sistema de proteção de dados menos seguro. Consulte a documentação ou contacte o seu administrador de serviço <br>para detalhes."}]    | Ocorre ao copiar entre um `AEK` recurso ativado para um recurso não `AEK` ativado. Para permitir a cópia do modelo encriptado para o alvo como cabeçalho de especificação não encriptado `x-ms-forms-copy-degrade: true` com o pedido de cópia.|
|"Erros":[{"código":"ResourceResolverError",<br>"Mensagem":"Não consegui obter informação para recurso cognitivo com identificação '...'. Certifique-se de que o recurso é válido e existe na região especificada 'westus2'.."}] | Indica que o recurso Azure indicado pelo `targetResourceId` não é um recurso cognitivo válido ou não existe. Verifique e reedite o pedido de cópia para resolver este problema.|


### <a name="optional-track-the-target-model-id"></a>[Opcional] Acompanhe o ID do modelo alvo 

Também pode utilizar a **API do Modelo Personalizado** para acompanhar o estado da operação consultando o modelo-alvo. Chame esta API usando o ID do modelo-alvo que copiou no primeiro passo.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

No corpo de resposta, verá informações sobre o modelo. Verifique o `"status"` estado do modelo no campo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>código de amostra cURL

Os seguintes fragmentos de código utilizam o CURL para esboçar as chamadas API descritas nos passos acima. Você ainda precisa preencher os IDs do modelo e informações de subscrição específicas dos seus próprios recursos.

### <a name="generate-copy-authorization-request"></a>Gerar pedido de autorização de cópia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Iniciar operação de cópia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Rastreio do progresso da cópia

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a usar a API copy para fazer cópias dos seus modelos personalizados a um recurso secundário do Reconhecimento de Formulários. Em seguida, explore os docs de referência da API para ver o que mais pode fazer com o Form Recogniser.
* [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
