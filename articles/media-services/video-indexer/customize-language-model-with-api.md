---
title: Personalize um modelo de idioma com API indexante de vídeo
titlesuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Idioma com a API do Indexer de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127985"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Personalize um modelo de idioma com a API do Indexante de Vídeo

O Indexer de vídeo permite criar modelos idiomas personalizados para personalizar o reconhecimento da fala através do upload de texto de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, serão reconhecidas novas palavras no texto de adaptação.

Para uma visão geral detalhada e boas práticas para modelos de idioma personalizados, consulte [Personalizar um modelo de Idioma com Indexer](customize-language-model-overview.md)de Vídeo .

Pode utilizar as APIs do Indexer de Vídeo para criar e editar modelos de Idioma personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar o website, conforme descrito no [modelo Custom Language utilizando o website do Indexer de Vídeo.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Criar um modelo linguístico

A criação de [um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API cria um novo modelo de idioma personalizado na conta especificada. Pode fazer o upload de ficheiros para o modelo Idioma nesta chamada. Em alternativa, pode criar o modelo Idioma aqui e carregar ficheiros para o modelo mais tarde, atualizando o modelo Idioma.

> [!NOTE]
> Ainda tem de treinar o modelo com os seus ficheiros habilitados para que o modelo aprenda o conteúdo dos seus ficheiros. As instruções para treinar uma língua estão na próxima secção.

Para fazer upload de ficheiros a adicionar ao modelo Idioma, tem de carregar ficheiros no organismo utilizando o FormData, além de fornecer valores para os parâmetros acima referidos. Há duas maneiras de fazer esta tarefa:

* A chave será o nome e o valor do ficheiro será o ficheiro TXT.
* A chave será o nome e o valor do ficheiro será um URL para ficheiro txt.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo idioma recém-criado juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída jSON exemplo:

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

## <a name="train-a-language-model"></a>Treinar um modelo de linguagem

O modelo de [idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API treina um modelo idioma personalizado na conta especificada com os conteúdos nos ficheiros que foram carregados e ativados no modelo de idioma.

> [!NOTE]
> Primeiro, tem de criar o modelo Idioma e carregar os seus ficheiros. Pode fazer o upload de ficheiros ao criar o modelo Idioma ou atualizando o modelo Idioma.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo idioma recentemente treinado juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída jSON exemplo:

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

O `id` devolvido é um ID único usado `languageModelId` para distinguir entre modelos linguísticos, enquanto é usado tanto para [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) APIs (também conhecido como `linguisticModelId` em Video Indexer upload/REindex APIs).

## <a name="delete-a-language-model"></a>Eliminar um modelo de Linguagem

O modelo de [idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API elimina um modelo de Idioma personalizado da conta especificada. Qualquer vídeo que estivesse a usar o modelo Idioma apagado manterá o mesmo índice até reindexar o vídeo. Se reindexar o vídeo, pode atribuir um novo modelo de Idioma ao vídeo. Caso contrário, o Indexer de Vídeo utilizará o seu modelo padrão para reindexar o vídeo.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando o modelo Idioma é eliminado com sucesso.

## <a name="update-a-language-model"></a>Atualizar um modelo de Idioma

A [atualização de um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API atualiza um modelo pessoa de idioma personalizado na conta especificada.

> [!NOTE]
> Já deve ter criado o modelo Linguístico. Pode utilizar esta chamada para ativar ou desativar todos os ficheiros sob o modelo, atualizar o nome do modelo Idioma e carregar ficheiros a adicionar ao modelo idioma.

Para fazer upload de ficheiros a adicionar ao modelo Idioma, tem de carregar ficheiros no organismo utilizando o FormData, além de fornecer valores para os parâmetros acima referidos. Há duas maneiras de fazer esta tarefa:

* A chave será o nome e o valor do ficheiro será o ficheiro TXT.
* A chave será o nome e o valor do ficheiro será um URL para ficheiro txt.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo idioma recentemente treinado juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída jSON exemplo:

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

Utilize `id` os ficheiros devolvidos na resposta para descarregar o conteúdo do ficheiro.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um ficheiro a partir de um modelo idioma

A [atualização de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) `enable` permite-lhe atualizar o nome e o estado de um ficheiro num modelo idioma personalizado na conta especificada.

### <a name="response"></a>Resposta

A resposta fornece metadados no ficheiro que atualizou seguindo o formato da saída jSON exemplo abaixo.

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

A API [obtém](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) informações sobre o modelo idioma especificado na conta especificada, como o idioma e os ficheiros que estão no modelo Idioma.

### <a name="response"></a>Resposta

A resposta fornece metadados no modelo idioma especificado juntamente com metadados em cada um dos ficheiros do modelo seguindo o formato desta saída jSON exemplo:

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

## <a name="get-all-the-language-models"></a>Obtenha todos os modelos linguísticos

O [obter todos os](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API devolve todos os modelos idiomas personalizados na conta especificada numa lista.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos Idioma na sua conta e cada um dos seus metadados e ficheiros seguindo o formato desta saída jSON exemplo:

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

## <a name="delete-a-file-from-a-language-model"></a>Eliminar um ficheiro de um modelo idioma

A [API elimina](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) o ficheiro especificado do modelo idioma especificado na conta especificada.

### <a name="response"></a>Resposta

Não há conteúdo devolvido quando o ficheiro é eliminado do modelo Idioma com sucesso.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obtenha metadados num ficheiro de um modelo de Idioma

Os [metadados de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API devolve o conteúdo e metadados do ficheiro especificado do modelo idioma escolhido na sua conta.

### <a name="response"></a>Resposta

A resposta fornece o conteúdo e metadados do ficheiro em formato JSON, semelhante a este exemplo:

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
> O conteúdo deste ficheiro de exemplo são as palavras "olá" e mundo em duas linhas distintas.

## <a name="download-a-file-from-a-language-model"></a>Descarregue um ficheiro de um modelo Idioma

O [download de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API descarrega um ficheiro de texto contendo o conteúdo do ficheiro especificado do modelo idioma especificado na conta especificada. Este ficheiro de texto deve coincidir com o conteúdo do ficheiro de texto que foi originalmente carregado.

### <a name="response"></a>Resposta

A resposta será o download de um ficheiro de texto com o conteúdo do ficheiro no formato JSON.

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de idioma usando o site](customize-language-model-with-website.md)
