---
title: Personalize um modelo de linguagem com API indexante de vídeo
titlesuffix: Azure Media Services
description: Saiba como personalizar um modelo de linguagem com a API do Indexante de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047075"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Personalize um modelo de linguagem com a API do Indexante de Vídeo

O Video Indexer permite criar modelos de linguagem personalizados para personalizar o reconhecimento da fala através do upload de textos de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, serão reconhecidas novas palavras no texto de adaptação.

Para obter uma visão geral detalhada e as melhores práticas para modelos de linguagem personalizados, consulte [Personalizar um modelo de linguagem com índice de vídeo.](customize-language-model-overview.md)

Pode utilizar as APIs do Indexador de Vídeo para criar e editar modelos de linguagem personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar o website, conforme descrito no [modelo Personale Language utilizando o website do Índice de Vídeo.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

A [API criar um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) cria um novo modelo de linguagem personalizado na conta especificada. Pode fazer o upload de ficheiros para o modelo Language nesta chamada. Em alternativa, pode criar o modelo Language aqui e carregar ficheiros para o modelo mais tarde, atualizando o modelo idioma.

> [!NOTE]
> Ainda é necessário treinar o modelo com os seus ficheiros habilitados para que o modelo aprenda o conteúdo dos seus ficheiros. As instruções sobre o treino de uma língua estão na próxima secção.

Para fazer o upload de ficheiros a adicionar ao modelo idioma, tem de carregar ficheiros no corpo utilizando o FormData, além de fornecer valores para os parâmetros acima referidos. Há duas maneiras de fazer esta tarefa:

* A chave será o nome do ficheiro e o valor será o ficheiro txt.
* A chave será o nome do ficheiro e o valor será um FICHEIRO URL para o ficheiro txt.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo linguístico recém-criado, juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída JSON exemplo:

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

## <a name="train-a-language-model"></a>Treine um modelo de linguagem

O [comboio um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API treina um modelo de linguagem personalizado na conta especificada com o conteúdo nos ficheiros que foram carregados e habilitados no modelo de idioma.

> [!NOTE]
> Primeiro tem de criar o modelo Language e fazer o upload dos seus ficheiros. Pode fazer upload de ficheiros ao criar o modelo Language ou atualizando o modelo idioma.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo linguístico recém-treinado, juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída JSON exemplo:

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

O devolvido `id` é um ID único usado para distinguir entre modelos linguísticos, enquanto `languageModelId` é usado tanto para carregar um vídeo para [indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e [reindexar um APIs de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (também conhecido como em Video `linguisticModelId` Indexer upload/REINDex APIs).

## <a name="delete-a-language-model"></a>Excluir um modelo de linguagem

A [API de um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) elimina um modelo de linguagem personalizado da conta especificada. Qualquer vídeo que estivesse a usar o modelo linguagem apagado manterá o mesmo índice até que reindex o vídeo. Se voltar a usar o vídeo, pode atribuir um novo modelo de Linguagem ao vídeo. Caso contrário, o Video Indexer utilizará o seu modelo padrão para reindexar o vídeo.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando o modelo idioma é eliminado com sucesso.

## <a name="update-a-language-model"></a>Atualizar um modelo de linguagem

A [atualização de um modelo de Idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API atualiza um modelo personalizado de pessoa linguística na conta especificada.

> [!NOTE]
> Já deve ter criado o modelo language. Pode utilizar esta chamada para ativar ou desativar todos os ficheiros no modelo, atualizar o nome do modelo idioma e carregar ficheiros a serem adicionados ao modelo de idioma.

Para fazer o upload de ficheiros a adicionar ao modelo idioma, tem de carregar ficheiros no corpo utilizando o FormData, além de fornecer valores para os parâmetros acima referidos. Há duas maneiras de fazer esta tarefa:

* A chave será o nome do ficheiro e o valor será o ficheiro txt.
* A chave será o nome do ficheiro e o valor será um FICHEIRO URL para o ficheiro txt.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo linguístico recém-treinado, juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída JSON exemplo:

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

Utilize os `id` ficheiros devolvidos na resposta para descarregar o conteúdo do ficheiro.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um ficheiro a partir de um modelo de idioma

A [atualização de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) permite-lhe atualizar o nome e o estado de um ficheiro num modelo de Linguagem personalizado na conta `enable` especificada.

### <a name="response"></a>Resposta

A resposta fornece metadados no ficheiro que atualizou seguindo o formato da saída JSON abaixo.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Utilize `id` o ficheiro devolvido na resposta para descarregar o conteúdo do ficheiro.

## <a name="get-a-specific-language-model"></a>Obtenha um modelo de linguagem específico

A API [obtém](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) informações sobre o modelo linguístico especificado na conta especificada, como o idioma e os ficheiros que estão no modelo idioma.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo de Linguagem especificado, juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída JSON exemplo:

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

Utilize `id` o ficheiro devolvido na resposta para descarregar o conteúdo do ficheiro.

## <a name="get-all-the-language-models"></a>Obtenha todos os modelos de linguagem

A [API obtém todos os](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) modelos de Linguagem personalizados na conta especificada numa lista.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos idiomas na sua conta e cada um dos seus metadados e ficheiros seguindo o formato desta saída JSON exemplo:

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

## <a name="delete-a-file-from-a-language-model"></a>Excluir um ficheiro de um modelo de idioma

A API [de eliminação](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) elimina o ficheiro especificado do modelo de Linguagem especificado na conta especificada.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando o ficheiro é eliminado do modelo Language com sucesso.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obtenha metadados num ficheiro de um modelo de idioma

A [obter metadados de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API devolve o conteúdo e os metadados no ficheiro especificado a partir do modelo idioma escolhido na sua conta.

### <a name="response"></a>Resposta

A resposta fornece os conteúdos e metadados do ficheiro em formato JSON, semelhantes a este exemplo:

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
> O conteúdo deste ficheiro exemplo são as palavras "olá" e mundo em duas linhas distintas.

## <a name="download-a-file-from-a-language-model"></a>Descarregue um ficheiro de um modelo de Linguagem

O [download de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API descarrega um ficheiro de texto contendo o conteúdo do ficheiro especificado a partir do modelo de Linguagem especificado na conta especificada. Este ficheiro de texto deve corresponder ao conteúdo do ficheiro de texto que foi originalmente carregado.

### <a name="response"></a>Resposta

A resposta será o download de um ficheiro de texto com o conteúdo do ficheiro no formato JSON.

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de linguagem usando o site](customize-language-model-with-website.md)
