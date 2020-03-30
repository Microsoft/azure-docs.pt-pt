---
title: Personalize um modelo de Marcas com o site do Indexer de Vídeo
titleSuffix: Azure Media Services
description: Saiba personalizar um modelo de Marcas com o website do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128048"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalize um modelo de Marcas com o site do Indexer de Vídeo

O Indexer de vídeo suporta a deteção da marca a partir da fala e do texto visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção da marca identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas bing. Por exemplo, se a Microsoft for mencionada em conteúdos de vídeo ou áudio ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo.

Um modelo personalizado de Marcas permite-lhe:

- selecione se quiser que o Indexer de Vídeo detete marcas a partir da base de dados das marcas Bing.
- selecione se quiser que o Indexer de Vídeo exclua certas marcas de serem detetadas (essencialmente criando uma lista de negação de marcas).
- selecione se quiser que o Indexer de Vídeo inclua marcas que devam fazer parte do seu modelo que podem não estar na base de dados de marcas da Bing (essencialmente criando uma lista de marcas aceites).

Para uma visão geral detalhada, consulte esta [visão geral](customize-brands-model-overview.md).

Pode utilizar o website do Indexer de Vídeo para criar, utilizar e editar modelos personalizados de Marcas detetados num vídeo, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito no [modelo Customize Brands utilizando APIs](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Editar definições de modelo de Marcas

Tem a opção de definir se quer ou não que sejam detetadas marcas da base de dados das marcas Bing. Para definir esta opção, precisa editar as definições do seu modelo Marcas. Siga estes passos.

1. Vá ao site do [Indexer](https://www.videoindexer.ai/) de Vídeo e inscreva-se.
2. Para personalizar um modelo na sua conta, selecione o botão de **personalização** do modelo Conteúdo no canto superior direito da página.

   ![Personalize o modelo de conteúdo no Indexer de Vídeo](./media/content-model-customization/content-model-customization.png)

3. Para editar marcas, selecione o separador **Marcas.**

    ![Personalize o modelo de marcas no Indexer de Vídeo](./media/customize-brand-model/customize-brand-model.png)

4. Consulte as **marcas Show sugeridas pela** opção Bing se quiser que o Indexer de Vídeo detete marcas sugeridas pelo Bing — deixe a opção desmarcada se não o fizer.

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A secção **De Marcas Incluir** representa marcas personalizadas que pretende que o Indexer de Vídeo detete, mesmo que não sejam sugeridas pela Bing.  

### <a name="add-a-brand-to-include-list"></a>Adicione uma marca para incluir a lista

1. Selecione **+ Adicionar marca**.

    ![Personalize o modelo de marcas no Indexer de Vídeo](./media/customize-brand-model/add-brand.png)

    Fornecer um nome (obrigatório), categoria (opcional), descrição (opcional) e URL de referência (opcional).
    O campo de categoria destina-se a ajudá-lo a marcar as suas marcas. Este campo aparece como as *tags* da marca ao utilizar as APIs do Indexer de Vídeo. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Cloud".

    O campo URL de referência pode ser qualquer site de referência para a marca (como um link para a sua página wikipédia).

2. Selecione **Adicionar marca** e verá que a marca foi adicionada à lista de **marcas Incluir.**

### <a name="edit-a-brand-on-the-include-list"></a>Editar uma marca na lista de incluir

1. Selecione o ícone do lápis ao lado da marca que pretende editar.

    Pode atualizar a categoria, descrição ou URL de referência de uma marca. Não se pode mudar o nome de uma marca porque os nomes das marcas são únicos. Se precisar de alterar a marca, elimine toda a marca (ver secção seguinte) e crie uma nova marca com o novo nome.

2. Selecione o botão **'Actualizar'** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-include-list"></a>Eliminar uma marca na lista de inclui-se

1. Selecione o ícone do lixo ao lado da marca que pretende eliminar.
2. Selecione **Delete** e a marca deixará de aparecer na sua lista *de marcas Incluir.*

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A secção **excluir marcas** representa as marcas que não quer que o Indexer de Vídeo detete.

### <a name="add-a-brand-to-exclude-list"></a>Adicione uma marca para excluir lista

1. Selecione **+ Adicionar marca.**

    Fornecer um nome (obrigatório), categoria (opcional).

2. Selecione **Adicionar marca** e verá que a marca foi adicionada à lista de *marcas Excluir.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Editar uma marca na lista de exclusão

1. Selecione o ícone do lápis ao lado da marca que pretende editar.

    Só é possível atualizar a categoria de uma marca. Não se pode mudar o nome de uma marca porque os nomes das marcas são únicos. Se precisar de alterar a marca, elimine toda a marca (ver secção seguinte) e crie uma nova marca com o novo nome.

2. Selecione o botão **'Actualizar'** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-exclude-list"></a>Eliminar uma marca na lista de exclusão

1. Selecione o ícone do lixo ao lado da marca que pretende eliminar.
2. Selecione **Delete** e a marca deixará de aparecer na sua lista *de marcas Excluir.*

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md)
