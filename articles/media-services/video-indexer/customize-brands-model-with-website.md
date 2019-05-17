---
title: Utilizar o site do indexador de vídeo para personalizar um modelo de marcas - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de marcas com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 721fde63aeae8704761b3c21f489dcad77cb89e3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799605"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizar um modelo de marcas com o site do Video Indexer

O Video Indexer suporta a deteção de marca de voz e texto visual durante a indexação e a reindexação de conteúdo de áudio e vídeo. A funcionalidade de deteção de marca identifica menções de produtos, serviços, e as empresas sugerido pela base de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionado num conteúdo de vídeo ou áudio, ou se ele aparece no texto visual num vídeo, o indexador de vídeos Deteta-o como uma marca no conteúdo. Um modelo de marcas personalizado permite-lhe selecionar se ou não que o indexador de vídeo irá detetar as marcas do Bing o banco de dados de marcas, excluir determinado marcas famosas de que está a ser detetado (essencialmente, a criação de uma lista de preta das marcas) e incluem marcas que devem fazer parte de seu modelo que podem não estar na base de dados do marcas do Bing (criar, essencialmente, uma lista de permissões de marcas).

Para obter uma descrição detalhada, consulte [descrição geral](customize-brands-model-overview.md).

Pode utilizar o site do indexador de vídeos para criar, usar e editar os modelos de marcas personalizados, detetados num vídeo, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito em [modelo de marcas de personalizar com APIs](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Editar as definições do modelo de marcas  

Tem a opção para definir se é ou não pretender que as marcas da base de dados de marcas do Bing para ser detetado. Para isso, terá de editar as definições do seu modelo de marcas.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Para personalizar um modelo na sua conta, clique nas **personalização do modelo de conteúdo** botão no canto superior direito da página.
 
   ![Personalizar o modelo de conteúdo](./media/content-model-customization/content-model-customization.png) 
3. Para editar as marcas, selecione o **marcas** separador.

    ![Personalizar o modelo de marcas](./media/customize-brand-model/customize-brand-model.png)
4. Verifique os **Mostrar marcas sugeridas pelo Bing** opção, se desejar indexador de vídeos incluir marcas sugeridas pelo Bing. Deixe a opção desmarcada se não pretender que o indexador de vídeo para detectar marcas sugeridas pelo Bing nos seus conteúdos. 

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

O **incluem marcas** secção representa marcas personalizadas que pretende para o indexador de vídeo detetar, mesmo que eles não são sugeridos pela Bing.  

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ Adicionar marca".

    ![Personalizar o modelo de marcas](./media/customize-brand-model/add-brand.png)

    Forneça um nome (obrigatório), a categoria (opcional), a descrição (opcional) e fazer referência a URL (opcional).
    O campo de categoria destina-se para o ajudar a identificar as suas marcas. Este campo mostrado como a marca *etiquetas* ao utilizar as APIs do indexador de vídeo. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

    O campo de URL de referência pode ser qualquer site de referência para a marca como um link para sua página da Wikipédia.
2. Clique em "Adicionar marca" e verá que a marca foi adicionada para o **incluem marcas** lista.

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis junto a marca que pretende editar.

    Pode atualizar a categoria, a descrição ou o URL de referência de uma marca. Não é possível alterar o nome de uma marca, porque os nomes das marcas são exclusivos. Se tiver de alterar o nome de marca, elimine a marca inteira (consulte a secção seguinte) e criar uma nova marca com o novo nome.
2. Clique nas **atualizar** botão para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Eliminar uma marca

1. Clique no ícone de caixote do lixo junto a marca que pretende eliminar.
2. Clique em "Eliminar" e a marca já não serão apresentados no seu *incluem marcas* lista.

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

O **excluir marcas** secção representa as marcas que pretende para o indexador de vídeo não detetar.

### <a name="add-a-brand"></a>Adicionar uma marca

1. Clique em "+ Adicionar marca".

    Forneça um nome (obrigatório), a categoria (opcional).
2. Clique em "Adicionar marca" e verá que a marca foi adicionada para o *excluir marcas* lista.

### <a name="edit-a-brand"></a>Editar uma marca

1. Clique no ícone de lápis junto a marca que pretende editar.

    Só é possível atualizar a categoria de uma marca. Não é possível alterar o nome de uma marca, porque os nomes das marcas são exclusivos. Se tiver de alterar o nome de marca, elimine a marca inteira (consulte a secção seguinte) e criar uma nova marca com o novo nome.
2. Clique nas **atualizar** botão para atualizar a marca com as novas informações.

### <a name="delete-a-brand"></a>Eliminar uma marca

1. Clique no ícone de caixote do lixo junto a marca que pretende eliminar.
2. Clique em "Eliminar" e a marca já não serão apresentados no seu *excluir marcas* lista.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de marcas com APIs](customize-brands-model-with-api.md)
