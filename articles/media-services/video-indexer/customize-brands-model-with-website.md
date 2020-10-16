---
title: Personalize um modelo de Marcas com o site do Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de Marcas com o website do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 5bd88493324867dc957922a732506f5cfb8bbc20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361239"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalize um modelo de Marcas com o site do Video Indexer

O Video Indexer suporta a deteção de marcas a partir de texto sonoro e visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção de marcas identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas da Bing. Por exemplo, se a Microsoft for mencionada em conteúdo sonoro ou vídeo ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo.

Um modelo personalizado de Marcas permite-lhe:

- selecione se quiser que o Video Indexer detete marcas a partir da base de dados de marcas Bing.
- selecione se quiser que o Video Indexer exclua que certas marcas sejam detetadas (essencialmente criando uma lista de marcas de negação).
- selecione se quiser que o Video Indexer inclua marcas que devam fazer parte do seu modelo que podem não estar na base de dados de marcas da Bing (essencialmente criando uma lista de marcas aceites).

Para uma visão geral detalhada, consulte esta [visão geral.](customize-brands-model-overview.md)

Pode utilizar o website Do Indexer de Vídeo para criar, utilizar e editar modelos de Marcas personalizadas detetados num vídeo, conforme descrito neste tópico. Também pode utilizar a API, como descrito no [modelo Customize Brands usando APIs](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Editar configurações de modelos de marcas

Tem a opção de definir se quer ou não que as marcas da base de dados das marcas Bing sejam detetadas. Para definir esta opção, precisa de editar as definições do seu modelo Marcas. Siga estes passos:

1. Vá ao site do [Video Indexer](https://www.videoindexer.ai/) e inscreva-se.
2. Para personalizar um modelo na sua conta, selecione o botão de **personalização** do modelo Content no canto superior direito da página.

   ![Personalizar modelo de conteúdo no Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Para editar marcas, selecione o **separador Marcas.**

    ![O screenshot mostra o separador Marcas da caixa de diálogo de personalização do modelo de conteúdo.](./media/customize-brand-model/customize-brand-model.png)

4. Verifique as **marcas Show sugeridas pela** opção Bing se quiser que o Video Indexer detete marcas sugeridas por Bing — deixe a opção desmarcada se não o fizer.

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A secção **de marcas Incluir** representa marcas personalizadas que você quer que o Video Indexer detete, mesmo que não sejam sugeridas por Bing.  

### <a name="add-a-brand-to-include-list"></a>Adicione uma marca para incluir a lista

1. Selecione **+ Adicionar marca**.

    ![O Screenshot mostra a caixa de diálogo das marcas Include, onde pode adicionar marcas.](./media/customize-brand-model/add-brand.png)

    Fornecer um nome (obrigatório), categoria (opcional), descrição (opcional) e URL de referência (opcional).
    O campo de categorias destina-se a ajudá-lo a marcar as suas marcas. Este campo aparece como as *tags* da marca ao utilizar as APIs do Indexante de Vídeo. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Cloud".

    O campo URL de referência pode ser qualquer website de referência para a marca (como um link para a sua página da Wikipédia).

2. **Selecione a marca Add** e verá que a marca foi adicionada à lista de marcas **Incluir.**

### <a name="edit-a-brand-on-the-include-list"></a>Editar uma marca na lista de incluí-lo

1. Selecione o ícone de lápis ao lado da marca que pretende editar.

    Pode atualizar a categoria, descrição ou URL de referência de uma marca. Não se pode mudar o nome de uma marca porque os nomes das marcas são únicos. Se precisar de alterar a marca, elimine toda a marca (ver secção seguinte) e crie uma nova marca com o novo nome.

2. Selecione o botão **'Atualização'** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-include-list"></a>Excluir uma marca na lista de incluí-lo

1. Selecione o ícone do lixo ao lado da marca que pretende eliminar.
2. Selecione **Delete** e a marca deixará de aparecer na sua lista *de marcas Incluir.*

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A secção **de marcas Excluir** representa as marcas que não quer que o Video Indexer detete.

### <a name="add-a-brand-to-exclude-list"></a>Adicione uma marca para excluir a lista

1. Selecione **+ Adicionar marca.**

    Fornecer um nome (obrigatório), categoria (opcional).

2. **Selecione a marca Add** e verá que a marca foi adicionada à lista de marcas *Excluir.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Editar uma marca na lista de exclusão

1. Selecione o ícone de lápis ao lado da marca que pretende editar.

    Só é possível atualizar a categoria de uma marca. Não se pode mudar o nome de uma marca porque os nomes das marcas são únicos. Se precisar de alterar a marca, elimine toda a marca (ver secção seguinte) e crie uma nova marca com o novo nome.

2. Selecione o botão **'Atualização'** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-exclude-list"></a>Excluir uma marca na lista de exclusão

1. Selecione o ícone do lixo ao lado da marca que pretende eliminar.
2. Selecione **Delete** e a marca deixará de aparecer na sua lista *de marcas Excluir.*

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
