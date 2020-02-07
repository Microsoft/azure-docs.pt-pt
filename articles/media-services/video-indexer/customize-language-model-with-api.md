---
title: Usar as APIs de Video Indexer para personalizar modelos de idioma – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de linguagem com as APIs de Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 01ea4d9ef943183f09baa86b729ec69344d4309e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049034"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personalizar um modelo de linguagem com as APIs de Video Indexer

Video Indexer permite criar modelos de linguagem personalizados para personalizar o reconhecimento de fala carregando texto de adaptação, ou seja, o texto do domínio cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas. 

Para uma visão geral detalhada e boas práticas para modelos de idioma personalizados, consulte [Personalizar um modelo de Idioma com Indexer](customize-language-model-overview.md)de Vídeo .

Você pode usar as APIs de Video Indexer para criar e editar modelos de idioma personalizados em sua conta, conforme descrito neste tópico. Também pode utilizar o website, conforme descrito no [modelo Custom Language utilizando o website do Indexer de Vídeo.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

A criação de [um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API cria um novo modelo de idioma personalizado na conta especificada. Você pode carregar arquivos para o modelo de idioma nesta chamada. Como alternativa, você pode criar o modelo de linguagem aqui e carregar arquivos para o modelo mais tarde atualizando o modelo de linguagem.

> [!NOTE]
> Você ainda deve treinar o modelo com seus arquivos habilitados para que o modelo Aprenda o conteúdo de seus arquivos. Instruções sobre como treinar um idioma estão na próxima seção.

Para carregar arquivos a serem adicionados ao modelo de linguagem, você deve carregar arquivos no corpo usando dados de formulário, além de fornecer valores para os parâmetros necessários acima. Existem duas formas de fazer isso: 

1. A chave será o nome do arquivo e o valor será o arquivo txt
2. A chave será o nome do arquivo e o valor será uma URL para o arquivo txt

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem criado recentemente junto com metadados em cada um dos arquivos do modelo, seguindo o formato da saída JSON de exemplo.

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
> Primeiro, você deve criar o modelo de linguagem e carregar seus arquivos. Você pode carregar arquivos ao criar o modelo de idioma ou ao atualizar o modelo de linguagem. 

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem recentemente treinado junto com metadados em cada um dos arquivos do modelo, seguindo o formato da saída JSON de exemplo.

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

O **id** devolvido é um id único usado para distinguir entre modelos linguísticos, enquanto **o idiomaModelId** é usado tanto para [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) APIs (também conhecido como **linguisticModelId** em Vídeo Indexer upload/re-index a APIs).

## <a name="delete-a-language-model"></a>Excluir um modelo de linguagem

O modelo de [idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API elimina um modelo de Idioma personalizado da conta especificada. Qualquer vídeo que estava usando o modelo de linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de linguagem ao vídeo. Caso contrário, Video Indexer usará seu modelo padrão para reindexar o vídeo.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o modelo de linguagem é excluído com êxito.

## <a name="update-a-language-model"></a>Atualizar um modelo de linguagem

A [atualização de um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API atualiza um modelo de pessoa de idioma personalizado na conta especificada.

> [!NOTE]
> Você já deve ter criado o modelo de linguagem. Você pode usar essa chamada para habilitar ou desabilitar todos os arquivos no modelo, atualizar o nome do modelo de idioma e carregar arquivos a serem adicionados ao modelo de linguagem.

Para carregar arquivos a serem adicionados ao modelo de linguagem, você deve carregar arquivos no corpo usando dados de formulário, além de fornecer valores para os parâmetros necessários acima. Existem duas formas de fazer isso: 

1. A chave será o nome do arquivo e o valor será o arquivo txt
2. A chave será o nome do arquivo e o valor será uma URL para o arquivo txt


### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem recentemente treinado junto com metadados em cada um dos arquivos do modelo, seguindo o formato da saída JSON de exemplo.

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

Utilize a **identificação** dos ficheiros devolvidos na resposta para descarregar o conteúdo do ficheiro.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um arquivo de um modelo de idioma

A [atualização de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) permite-lhe atualizar o nome e **ativar** o estado de um ficheiro num modelo idioma personalizado na conta especificada.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o arquivo que você atualizou seguindo o formato da saída JSON de exemplo abaixo.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Utilize o **id** do ficheiro devolvido na resposta para descarregar o conteúdo do ficheiro.

## <a name="get-a-specific-language-model"></a>Obter um modelo de linguagem específico

A API [obtém](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) informações sobre o modelo idioma especificado na conta especificada, como o idioma e os ficheiros que estão no modelo Idioma. 

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem especificado junto com os metadados em cada um dos arquivos do modelo, seguindo o formato da saída JSON de exemplo abaixo.

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

Utilize o **id** do ficheiro devolvido na resposta para descarregar o conteúdo do ficheiro.

## <a name="get-all-the-language-models"></a>Obter todos os modelos de linguagem

O [obter todos os](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API devolve todos os modelos idiomas personalizados na conta especificada numa lista.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos de linguagem em sua conta e de cada um de seus metadados e arquivos seguindo o formato da saída JSON de exemplo abaixo.

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

## <a name="delete-a-file-from-a-language-model"></a>Excluir um arquivo de um modelo de idioma

A [API elimina](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) o ficheiro especificado do modelo idioma especificado na conta especificada. 

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o arquivo é excluído do modelo de idioma com êxito.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obter metadados em um arquivo de um modelo de idioma

Os [metadados de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API devolve o conteúdo e metadados no ficheiro especificado do modelo idioma escolhido na sua conta.

### <a name="response"></a>Resposta

A resposta fornece o conteúdo e os metadados do arquivo no formato JSON, semelhante a este:

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
> O conteúdo deste arquivo de exemplo são as palavras "Olá" e mundo "em duas linhas separadas.

## <a name="download-a-file-from-a-language-model"></a>Baixar um arquivo de um modelo de idioma

O [download de um ficheiro](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API descarrega um ficheiro de texto contendo o conteúdo do ficheiro especificado do modelo idioma especificado na conta especificada. Esse arquivo de texto deve corresponder ao conteúdo do arquivo de texto que foi carregado originalmente.

### <a name="response"></a>Resposta

A resposta será o download de um arquivo de texto com o conteúdo do arquivo no formato JSON. 

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de idioma usando o site](customize-language-model-with-website.md)
