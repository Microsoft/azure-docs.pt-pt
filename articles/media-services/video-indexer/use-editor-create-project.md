---
title: Usar o editor de Video Indexer para criar projetos
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor de Video Indexer para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839169"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Usar o editor de Video Indexer para criar projetos

Video Indexer site, permite que você use o aprofundamento dos seus vídeos para: localizar o conteúdo de mídia correto, localizar as partes das quais você está interessado e usar os resultados para criar um projeto totalmente novo. Depois de criado, o projeto pode ser renderizado e baixado de Video Indexer e ser usado em seus próprios aplicativos de edição ou fluxos de trabalho downstream.

Alguns cenários em que você pode achar esse recurso útil são: 

* Criando destaques de filmes para os trailers.
* Usando clipes antigos de vídeos em conversões de notícias.
* Criação de conteúdo mais curto para mídia social.

Este artigo mostra como criar um projeto do zero e também como criar um projeto de um vídeo em sua conta.

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerenciar vídeos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione a guia **projetos** . Se você tiver criado projetos antes, verá todos os outros projetos aqui.
1. Clique em **criar novo projeto**.  

    ![Novo projeto](./media/video-indexer-view-edit/new-project.png)
1. Dê um nome ao seu projeto clicando no ícone de lápis. Substitua o texto que diz "projeto sem título" pelo nome do projeto e clique na marca de seleção.

    ![Novo projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicionar vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos podem conter apenas vídeos indexados no mesmo idioma. Depois de selecionar um vídeo em um idioma, você não poderá adicionar os vídeos em sua conta que estejam em um idioma diferente.

1. Adicione vídeos com os quais você deseja trabalhar neste projeto selecionando **adicionar vídeos**.

    Você verá todos os vídeos em sua conta e uma caixa de pesquisa que diz "Pesquisar texto, palavras-chave ou conteúdo visual". Para pesquisar vídeos que tenham uma pessoa, rótulo, marca, palavra-chave ou ocorrência especificada na transcrição e no OCR.
    
    Por exemplo, na imagem abaixo, estamos procurando vídeos que mencionam "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Você pode filtrar seus resultados ainda mais, selecionando **filtrar resultados**. Você pode filtrar para mostrar vídeos que têm uma determinada pessoa neles ou para especificar que você deseja ver os resultados de vídeo que são um em um determinado idioma ou ter um proprietário específico. <br/> Você também pode especificar o escopo da sua consulta. Por exemplo, se você quiser Pesquisar "GitHub" no OCR, selecione **texto visual**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    Você pode encamar vários filtros para sua consulta. Use os botões **+** / **-** para adicionar/remover filtros. Use **filtros claros** para remover todos os filtros.
1. Para adicionar vídeos, selecione-os e, em seguida, selecione **Adicionar**.
1. Agora, você verá todos os vídeos que escolheu. Esses são os vídeos dos quais você vai selecionar os clipes para seu projeto.

    Você pode reorganizar a ordem dos vídeos arrastando e soltando ou selecionando o botão de menu lista e selecionando **mover para baixo** ou **mover para cima**. No menu lista, você também poderá remover o vídeo deste projeto. 

    ![Reorganizar](./media/video-indexer-view-edit/rearrange.png)
    
    Você tem a opção de adicionar mais vídeos a este projeto a qualquer momento selecionando **adicionar vídeos**. Você também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. Talvez você queira fazer isso se quiser mostrar um clipe de um vídeo e, em seguida, um clipe de outro e, em seguida, outro clipe do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione os clipes para usar em seu projeto

Se você clicar na seta para baixo no lado direito de cada vídeo, abrirá as informações no vídeo com base nos carimbos de data/hora (clipes do vídeo). 

1. Selecione **Exibir informações** para personalizar quais percepções você deseja ver e quais não deseja ver. 

    ![Exibir informações](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clipes específicos, use a caixa de pesquisa que diz "Pesquisar em transcrição, texto visual, pessoas e rótulos".
1. Adicione filtros para especificar ainda mais detalhes sobre as cenas que você está procurando selecionando **Opções de filtro**.

    ![Opções de filtragem](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, talvez você queira ver os clipes em que o GitHub é mencionado enquanto o Donovan Brown está na tela. Para isso, você precisa adicionar um filtro "include" que tenha "pessoas" como o tipo de Insight. Em seguida, você precisa digitar "Donovan Brown" na caixa de pesquisa do filtro.
    
    ![incluir](./media/video-indexer-view-edit/include.png)
    
    Se você quiser os clipes em que o GitHub é mencionado enquanto Donovan Brown _não_ estiver na tela, bastaria alterar o filtro "include" para um filtro "exclude" usando a lista suspensa. 

1. Adicione um clipe ao seu projeto selecionando o segmento que você deseja adicionar. Você pode cancelar a seleção deste clipe clicando no segmento novamente.
    
    Adicione todos os segmentos de um vídeo clicando na opção de menu lista ao lado do vídeo e selecionando **selecionar todos os segmentos**. 

    ![Adicionar tudo](./media/video-indexer-view-edit/add-all.png)

    Você pode limpar toda a sua seleção selecionando limpar seleção.

> [!TIP]
> Ao selecionar e ordenar seus clipes, você pode visualizar o vídeo no Player no lado direito da página. 

![Pré-visualização](./media/video-indexer-view-edit/preview.png)

Lembre-se de salvar seu projeto ao fazer alterações selecionando **salvar projeto**. 

### <a name="render-and-download-the-project"></a>Renderizar e baixar o projeto

> [!NOTE]
> Para contas pagas Video Indexer, a renderização do seu projeto tem custos de codificação. Video Indexer contas de avaliação são limitadas a 5 horas de renderização.

1. Quando terminar, verifique se o projeto foi salvo. Agora você pode renderizar este projeto. Selecione **renderizar e baixar**. 

    ![Guardar](./media/video-indexer-view-edit/save.png)

    Haverá um pop-up que informa que o indexador de vídeo renderizará um arquivo e, em seguida, o link de download será enviado para seu email. Selecione continuar. 
    
    Você também verá uma notificação informando que o projeto está sendo renderizado na parte superior da página. Depois de fazer a renderização, você verá uma nova notificação informando que o projeto foi renderizado com êxito. Clique na notificação para baixar o projeto. O projeto será baixado no formato MP4.

    ![Renderização concluída](./media/video-indexer-view-edit/rendering-done.png)

1. Você pode acessar projetos salvos na guia **projetos** . 

    Se você selecionar este projeto, verá todas as informações e a linha do tempo deste projeto. Se você selecionar o **Editor de vídeo**, poderá continuar fazendo edições neste projeto. As edições incluem adicionar ou remover vídeos e clipes ou renomear o projeto.

    ![Editor de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Criar um projeto do seu vídeo

Você pode criar um novo projeto diretamente de um vídeo em sua conta. 

1. Vá para a guia **biblioteca** do site video indexer.
1. Abra o vídeo que você deseja usar para criar seu projeto. Na página informações e linha do tempo, selecione o botão **Editor de vídeo** .

    Isso levará você para a mesma página que você usou para criar um novo projeto. Ao contrário do novo projeto, você vê os segmentos de informações de carimbo de data/hora do vídeo, que começou a editar anteriormente.

## <a name="see-also"></a>Consultar também

[Descrição geral do Video Indexer](video-indexer-overview.md)

