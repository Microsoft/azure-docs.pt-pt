---
title: Use o site Video Indexer para personalizar modelos de marcas – Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de marcas com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513903"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizar um modelo de marcas com o site Video Indexer

O Video Indexer dá suporte à detecção de marcas de fala e texto visual durante a indexação e a reindexação de conteúdo de vídeo e áudio. O recurso de detecção de marca identifica as mencionações de produtos, serviços e empresas sugeridas pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em um conteúdo de vídeo ou áudio ou se aparecer no texto visual em um vídeo, Video Indexer o detectará como uma marca no conteúdo. Um modelo de marcas personalizadas permite que você selecione se você Video Indexer detectará marcas do banco de dados de marcas do Bing, excluirá a detecção de determinadas marcas (essencialmente criando uma lista negra de marcas) e incluirá marcas que devem ser parte do seu modelo Isso pode não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de permissões de marcas).

Para obter uma visão geral detalhada, consulte [visão geral](customize-brands-model-overview.md).

Você pode usar o site Video Indexer para criar, usar e editar modelos de marcas personalizadas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar o modelo de marcas usando APIs](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Editar as configurações do modelo de marcas  

Você tem a opção de definir se deseja ou não que as marcas do banco de dados de marcas do Bing sejam detectadas. Para isso, você precisa editar as configurações do modelo de marcas.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Para personalizar um modelo em sua conta, clique no botão **personalização do modelo de conteúdo** no canto superior direito da página.
 
   ![Personalizar modelo de conteúdo](./media/content-model-customization/content-model-customization.png) 
3. Para editar marcas, selecione a guia **marcas** .

    ![Personalizar modelo de marcas](./media/customize-brand-model/customize-brand-model.png)
4. Marque a opção **Mostrar marcas sugeridas pelo Bing** , se desejar que o video indexer inclua as marcas sugeridas pelo Bing. Deixe a opção desmarcada se você não quiser que Video Indexer detecte marcas sugeridas pelo Bing em seu conteúdo. 

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A seção **incluir marcas** representa marcas personalizadas que você deseja Video indexer para detectar, mesmo que não sejam sugeridas pelo Bing.  

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ adicionar marca".

    ![Personalizar modelo de marcas](./media/customize-brand-model/add-brand.png)

    Forneça um nome (obrigatório), categoria (opcional), descrição (opcional) e URL de referência (opcional).
    O campo Categoria destina-se a ajudá-lo a marcar suas marcas. Esse campo aparece como as *marcas* da marca ao usar as APIs de video indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "nuvem".

    O campo URL de referência pode ser qualquer site de referência para a marca, como um link para sua página da Wikipédia.
2. Clique em "adicionar marca" e você verá que a marca foi adicionada à lista **incluir marcas** .

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis ao lado da marca que você deseja editar.

    Você pode atualizar a categoria, a descrição ou a URL de referência de uma marca. Não é possível alterar o nome de uma marca porque os nomes das marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.
2. Clique no botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Excluir uma marca

1. Clique no ícone de Lixeira ao lado da marca que você deseja excluir.
2. Clique em "excluir" e a marca não será mais exibida na lista de *marcas de inclusão* .

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A seção **Excluir marcas** representa as marcas que você deseja para video indexer não detectar.

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ adicionar marca".

    Forneça um nome (obrigatório), categoria (opcional).
2. Clique em "adicionar marca" e você verá que a marca foi adicionada à lista *Excluir marcas* .

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis ao lado da marca que você deseja editar.

    Você só pode atualizar a categoria de uma marca. Não é possível alterar o nome de uma marca porque os nomes das marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.
2. Clique no botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Excluir uma marca

1. Clique no ícone de Lixeira ao lado da marca que você deseja excluir.
2. Clique em "excluir" e a marca não será mais exibida na sua lista de *marcas de exclusão* .

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo de marcas usando APIs](customize-brands-model-with-api.md)
