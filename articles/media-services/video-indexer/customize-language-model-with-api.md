---
title: Utilizar as APIs do indexador de vídeo para personalizar um modelo de idioma - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de idioma com as APIs do indexador de vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: c2c722331c95e72bae5605606564a2083e2802e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075037"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personalizar um modelo de idioma com as APIs do indexador de vídeo

O Video Indexer permite-lhe criar modelos de linguagem personalizados para personalizar o reconhecimento de voz através do carregamento de texto de adaptação, ou seja, o texto do domínio cujo vocabulário que pretende que o mecanismo para se adaptar às. Depois de preparar o seu modelo, que aparece no texto de adaptação de novas palavras serão reconhecidas. 

Para obter uma descrição detalhada e melhores práticas para modelos de linguagem personalizados, consulte [personalizar um modelo de idioma com o indexador de vídeo](customize-language-model-overview.md).

Pode utilizar as APIs do indexador de vídeo para criar e editar os modelos de linguagem personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar o Web site, conforme descrito em [modelo de idioma de personalizar através do site do Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de idioma

O comando seguinte cria um novo modelo de idioma personalizado na conta especificada. Pode carregar ficheiros para o modelo de idioma na chamada. Em alternativa, pode criar o modelo de idioma aqui e carregar ficheiros para o modelo mais tarde, atualizando o modelo de idioma.

> [!NOTE]
> Ainda deve preparar o modelo com seus arquivos de ativado para o modelo obter o conteúdo dos seus arquivos. São as direções numa linguagem de treinamento na próxima seção.

### <a name="request-url"></a>URL do Pedido

Este é um pedido POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|
|modelName|string|Sim|O nome para o modelo de idioma|
|language|string|Sim|O idioma do modelo de idioma. <br/>O **linguagem** parâmetro tem de obter o idioma no formato BCP 47 de "etiqueta de idioma-região" (por exemplo: "en-US"). Idiomas suportados são o inglês (en-US), alemão (Alemanha-DE), Espanhol (es-SP), Árabe (ar-por exemplo), francês (fr-FR), Híndi (Olá-Olá), Italiano (it-IT), japonês (ja-JP), português (pt-BR), russo (ru-RU) e chinês (zh-CN).  |

### <a name="request-body"></a>Corpo do pedido

Para carregar ficheiros a ser adicionado para o modelo de idioma, tem de carregar arquivos no corpo com dados do formulário além de fornecer valores para os parâmetros necessários acima. Existem duas formas de fazer isso: 

1. Chave será o nome de ficheiro e o valor será o ficheiro txt.
2. Chave será o nome de ficheiro e o valor será um URL para o ficheiro txt

### <a name="response"></a>Resposta

A resposta fornece os metadados sobre o modelo de idioma recém-criado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato da saída do JSON de exemplo.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Preparar um modelo de idioma

O seguinte comando prepara um modelo de idioma personalizado na conta especificada com o conteúdo dos ficheiros foram carregados e ativados no modelo de idioma. 

> [!NOTE]
> Primeiro tem de criar o modelo de idioma e carregar seus arquivos. Pode carregar ficheiros quando criar o modelo de idioma ou atualizando o modelo de idioma. 

### <a name="request-url"></a>URL do Pedido

Este é um pedido PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|O id de modelo de idioma (gerado quando é criado o modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece os metadados sobre o modelo de idioma recentemente preparado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato da saída do JSON de exemplo.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Em seguida, deve utilizar o **id** valor do modelo de idioma para o **linguisticModelId** parâmetro quando [carregar um vídeo para o índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e para o  **languageModelId** parâmetro quando [reindexação um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Eliminar um modelo de idioma

O comando seguinte elimina um modelo de idioma personalizado da conta especificada. Qualquer vídeo que estava a utilizar o modelo de idioma eliminado irá manter o mesmo índice até novamente indexar o vídeo. Se voltar a indexar o vídeo, pode atribuir um novo modelo de idioma para o vídeo. Caso contrário, o Video Indexer irá utilizar o modelo padrão para voltar a indexar o vídeo.

### <a name="request-url"></a>URL do Pedido

Este é um pedido de eliminação.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|O id de modelo de idioma (gerado quando é criado o modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o modelo de idioma é eliminado com êxito.

## <a name="update-a-language-model"></a>Atualizar um modelo de idioma

O seguinte comando atualiza um modelo personalizado para a pessoa de idioma na conta especificada.

> [!NOTE]
> Tem de já tiver criado o modelo de idioma. Pode utilizar esta chamada para ativar ou desativar todos os ficheiros de acordo com o modelo, atualize o nome do modelo de idioma e carregar ficheiros a ser adicionado para o modelo de idioma.

### <a name="request-url"></a>URL do Pedido

Este é um pedido PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Segue-se o pedido no Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|O id de modelo de idioma (gerado quando é criado o modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|
|modelName|string|Não|Novo nome que possa dar para o modelo|
|ativar|boolean|Não|Escolha se a todos os ficheiros nesse modelo estão ativado (true) ou desativado (FALSO)|

### <a name="request-body"></a>Corpo do pedido

Para carregar ficheiros a ser adicionado para o modelo de idioma, tem de carregar arquivos no corpo com dados do formulário além de fornecer valores para os parâmetros necessários acima. Existem duas formas de fazer isso: 

1. Chave será o nome de ficheiro e o valor será o ficheiro txt.
2. Chave será o nome de ficheiro e o valor será um URL para o ficheiro txt

### <a name="response"></a>Resposta

A resposta fornece os metadados sobre o modelo de idioma recentemente preparado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato da saída do JSON de exemplo.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Pode utilizar o **id** dos ficheiros devolvidos aqui para transferir o conteúdo do ficheiro.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um ficheiro a partir de um modelo de idioma

O seguinte comando permite-lhe atualizar o nome e **ativar** estado de um arquivo num modelo de idioma personalizado na conta especificada.

### <a name="request-url"></a>URL do Pedido

Este é um pedido PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Segue-se o pedido no Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountId|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|ID do modelo de idioma que contém o arquivo (gerado quando é criado o modelo de idioma)|
|fileId|string|Sim|ID do ficheiro que está a ser atualizado (gerado quando o ficheiro é carregado na criação ou atualização do modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|
|fileName|string|Não|Nome para atualizar o nome de ficheiro para|
|ativar|boolean|Não|Atualizar se este ficheiro está ativado (true) ou desativado (false) no modelo de idioma|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece os metadados sobre o ficheiro que é atualizado seguindo o formato da saída JSON de exemplo abaixo.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Pode utilizar o **id** do arquivo devolvido aqui para transferir o conteúdo do ficheiro.

## <a name="get-a-specific-language-model"></a>Obter um modelo de idioma específico

O comando seguinte devolve informações sobre o modelo de idioma especificado na conta especificada como linguagem e os ficheiros que estão no modelo de idioma. 

### <a name="request-url"></a>URL do Pedido

Este é um pedido GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16).

### <a name="request-parameters-and-request-body"></a>Parâmetros do pedido e o corpo do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|O id de modelo de idioma (gerado quando é criado o modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece os metadados sobre o modelo de idioma especificado juntamente com metadados em cada um dos arquivos do modelo seguindo o formato da saída JSON de exemplo abaixo.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Pode utilizar o **id** do arquivo devolvido aqui para transferir o conteúdo do ficheiro.

## <a name="get-all-the-language-models"></a>Obter todos os modelos de linguagem

O comando seguinte devolve todos os modelos de idioma personalizados na conta especificada numa lista.

### <a name="request-url"></a>URL do Pedido

Este é um pedido de obter.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?).

### <a name="request-parameters"></a>Parâmetros do pedido

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos de idioma na sua conta e cada um dos respetivos metadados e ficheiros seguindo o formato da saída JSON de exemplo abaixo.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Eliminar um ficheiro de um modelo de idioma

O comando seguinte elimina o ficheiro especificado do modelo de idioma especificado na conta especificada. 

### <a name="request-url"></a>URL do Pedido

Este é um pedido de eliminação.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|ID do modelo de idioma que contém o arquivo (gerado quando é criado o modelo de idioma)|
|fileId|string|Sim|ID do ficheiro que está a ser atualizado (gerado quando o ficheiro é carregado na criação ou atualização do modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

Não existe nenhum conteúdo retornado quando o ficheiro é eliminado com êxito do modelo de idioma.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obter metadados num arquivo de um modelo de idioma

Esta ação devolve o conteúdo e os metadados do ficheiro especificado do modelo de linguagem escolhido na sua conta.

### <a name="request-url"></a>URL do Pedido

Este é um pedido GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Segue-se o pedido no Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|ID do modelo de idioma que contém o arquivo (gerado quando é criado o modelo de idioma)|
|fileId|string|Sim|ID do ficheiro que está a ser atualizado (gerado quando o ficheiro é carregado na criação ou atualização do modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta fornece o conteúdo e metadados do ficheiro no formato JSON, semelhante a este:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> O conteúdo deste ficheiro de exemplo é as palavras "Olá" e o mundo"em duas linhas separadas.

## <a name="download-a-file-from-a-language-model"></a>Transferir um ficheiro de um modelo de idioma

O comando seguinte transfere um ficheiro de texto que contenha o conteúdo do ficheiro especificado do modelo de idioma especificado na conta especificada. Este ficheiro de texto deve coincidir com o conteúdo do ficheiro de texto que foi carregado originalmente.

### <a name="request-url"></a>URL do Pedido
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Segue-se o pedido no Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Consulte os parâmetros necessários e testar com o Portal de programador do indexador de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122).

### <a name="request-parameters"></a>Parâmetros do pedido 

|**Nome**|**Tipo**|**Necessário**|**Descrição**|
|---|---|---|---|
|localização|string|Sim|A região do Azure para o qual deve ser roteada, a chamada. Para obter mais informações, consulte [regiões do Azure e o Video Indexer](regions.md).|
|accountID|string|Sim|Identificador exclusivo global para a conta|
|modelId|string|Sim|ID do modelo de idioma que contém o arquivo (gerado quando é criado o modelo de idioma)|
|fileId|string|Sim|ID do ficheiro que está a ser atualizado (gerado quando o ficheiro é carregado na criação ou atualização do modelo de idioma)|
|accessToken|string|Sim|Token de acesso (tem de ser do âmbito [Token de acesso da conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) para autenticar a chamada. Tokens de acesso expiram dentro de 1 hora.|

### <a name="request-body"></a>Corpo do pedido 

Não são necessárias mais é necessário para esta chamada o corpo do pedido.

### <a name="response"></a>Resposta

A resposta será o download de um arquivo de texto com o conteúdo do ficheiro no formato JSON. 

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de idioma a utilizar o Web site](customize-language-model-with-website.md)
