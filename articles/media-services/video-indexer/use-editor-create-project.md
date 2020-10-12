---
title: Use o editor do Índice de Vídeo para criar projetos
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor do Video Indexer para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 5eaf17281fef32d682a2dac17b379faf7ec23a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970081"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Use o editor do Índice de Vídeo para criar projetos

O site do Video Indexer permite-lhe utilizar as informações profundas dos seus vídeos para: encontrar o conteúdo certo dos meios de comunicação, localizar as partes que lhe interessam e utilizar os resultados para criar um projeto inteiramente novo. Uma vez criado, o projeto pode ser renderizado e descarregado a partir de Video Indexer e ser usado nas suas próprias aplicações de edição ou fluxos de trabalho a jusante.

Alguns cenários onde poderá achar este recurso útil são: 

* Criando destaques de filmes para trailers.
* Usando velhos clips de vídeos em noticiários.
* Criar conteúdo mais curto para as redes sociais.

Este artigo mostra como criar um projeto de raiz e também como criar um projeto a partir de um vídeo na sua conta.

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerir vídeos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione o **separador Projetos.** Se já criou projetos antes, verá todos os seus outros projetos aqui.
1. Clique **em Criar novo projeto.**  

    ![Novo projeto](./media/video-indexer-view-edit/new-project.png)
1. Dê um nome ao seu projeto clicando no ícone do lápis. Substitua o texto que diz "Projeto Sem Título" pelo nome do seu projeto e clique na verificação.

    ![Novo projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicione vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos só podem conter vídeos indexados no mesmo idioma. Uma vez que selecione um vídeo num idioma, não pode adicionar os vídeos na sua conta que estão num idioma diferente.

1. Adicione vídeos com os quais pretende trabalhar neste projeto selecionando **Vídeos Add**.

    Verá todos os vídeos na sua conta e uma caixa de pesquisa que diz "Procure texto, palavras-chave ou conteúdo visual". Para procurar vídeos que tenham uma pessoa especificada, etiqueta, marca, palavra-chave ou ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estamos à procura de vídeos que mencionem "GitHub".
    
    ![A screenshot mostra a procura de vídeos que mencionem GitHub com dois resultados.](./media/video-indexer-view-edit/github.png)

    Pode filtrar ainda mais os seus resultados selecionando **os resultados do Filtro.** Pode filtrar para mostrar vídeos que tenham uma determinada pessoa ou especificar que só pretende ver resultados de vídeo que são de uma determinada língua ou têm um proprietário específico. <br/> Também pode especificar o âmbito da sua consulta. Por exemplo, se pretender pesquisar "GitHub" no OCR, selecione **Texto Visual**.

    ![Filtrar](./media/video-indexer-view-edit/visual-text.png)

    Pode colocar vários filtros em camadas na sua consulta. Utilize os **+** / **-** botões para adicionar/remover filtros. Utilize **filtros limpos** para remover todos os filtros.
1. Para adicionar vídeos, selecione-os e, em seguida, **selecione Adicionar**.
1. Agora, vais ver todos os vídeos que escolheste. Estes são os vídeos a partir dos quais vai selecionar clips para o seu projeto.

    Pode reorganizar a ordem dos vídeos arrastando e baixando ou selecionando o botão do menu da lista e selecionando **Move down** ou **Move up**. A partir do menu da lista, poderá também remover o vídeo deste projeto. 

    ![O Screenshot mostra o Video Indexer com um menu contextual para um dos vídeos remover, limpar a seleção ou descer.](./media/video-indexer-view-edit/rearrange.png)
    
    Tem a opção de adicionar mais vídeos a este projeto a qualquer momento, selecionando **Vídeos Add**. Também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. É melhor fazer isto se quiser mostrar um clip de um vídeo e depois um clip de outro e depois outro clip do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clips para usar no seu projeto

Se clicar na seta descendente no lado direito de cada vídeo, abrirá as ideias no vídeo com base em selos temporais (clips do vídeo). 

1. Selecione **Ver Insights** para personalizar quais as ideias que deseja ver e que não quer ver. 

    ![Ver informações](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clips específicos, utilize a caixa de pesquisa que diz "Pesquisa na transcrição, texto visual, pessoas e etiquetas".
1. Adicione filtros para especificar mais detalhes sobre as cenas que procura selecionando **opções de Filtro**.

    ![Opções de filtragem](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, pode querer ver clips onde o GitHub é mencionado enquanto Donovan Brown está no ecrã. Para isso, é necessário adicionar um filtro "incluir" que tenha "People" como tipo de insight. Em seguida, é necessário digitar "Donovan Brown" na caixa de pesquisa para o filtro.
    
    ![A screenshot mostra o Indexer de Vídeo com Pessoas selecionadas para um filtro de incluem.](./media/video-indexer-view-edit/include.png)
    
    Se quiser clips onde o GitHub é mencionado enquanto Donovan Brown _não_ estiver no ecrã, basta mudar o filtro "incluir" num filtro de "excluir" utilizando o dropdown. 

1. Adicione um clip ao seu projeto selecionando o segmento que pretende adicionar. Pode desmarcar este clipe clicando novamente no segmento.
    
    Adicione todos os segmentos de um vídeo clicando na opção do menu da lista ao lado do vídeo e selecione **Selecione todos os segmentos**. 

    ![Adicionar tudo](./media/video-indexer-view-edit/add-all.png)

    Pode limpar toda a sua seleção selecionando a seleção Clear.

> [!TIP]
> À medida que está a selecionar e a encomendar os seus clips, pode visualizar o vídeo no leitor no lado direito da página. 

![A screenshot mostra o Video Indexer com a pré-visualização de um vídeo no lado direito da janela.](./media/video-indexer-view-edit/preview.png)

Lembre-se de guardar o seu projeto quando escoar alterações selecionando **o projeto Save**. 

### <a name="render-and-download-the-project"></a>Rendereia e descarregue o projeto

> [!NOTE]
> Para contas pagas por Video Indexer, renderizar o seu projeto tem custos de codificação. As contas de ensaio do Indexer de Vídeo estão limitadas a 5 horas de renderização.

1. Uma vez feito, certifique-se de que o seu projeto foi salvo. Agora pode fazer este projeto. Selecione **Render e Download**. 

    ![O Screenshot mostra o Video Indexer com a opção de renderizar e descarregar o seu projeto.](./media/video-indexer-view-edit/save.png)

    Haverá um popup que lhe dirá que o indexante de vídeo irá renderizar um ficheiro e, em seguida, o link de descarregamento será enviado para o seu e-mail. Selecione Proceder. 
    
    Também verá uma notificação de que o projeto está a ser prestado no topo da página. Uma vez feito, verá uma nova notificação de que o projeto foi prestado com sucesso. Clique na notificação para descarregar o projeto. Irá descarregar o projeto em formato MP4.

    ![Renderização feita](./media/video-indexer-view-edit/rendering-done.png)

1. Pode aceder a projetos guardados a partir do separador **Projetos.** 

    Se selecionar este projeto, vê todas as ideias e a cronologia deste projeto. Se selecionar **editor de vídeo,** pode continuar a fazer edições para este projeto. As edições incluem adicionar ou remover vídeos e clips ou renomear o projeto.

    ![Editor de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Crie um projeto a partir do seu vídeo

Pode criar um novo projeto diretamente a partir de um vídeo na sua conta. 

1. Aceda ao separador **Biblioteca** do site do Índice de Vídeo.
1. Abra o vídeo que pretende utilizar para criar o seu projeto. Na página de insights e linha do tempo, selecione o botão **editor de vídeo.**

    Isto leva-o à mesma página que usou para criar um novo projeto. Ao contrário do novo projeto, vê-se os segmentos de insights com tempos do vídeo, que tinha começado a editar anteriormente.

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md)

