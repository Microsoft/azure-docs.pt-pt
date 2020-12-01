---
title: Use o editor do Índice de Vídeo para criar projetos e adicionar videoclips
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor do Video Indexer para criar projetos e adicionar videoclips.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 3a3ac3f2db4e23f03f83a98bee0aceaddef9f889
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433392"
---
# <a name="add-video-clips-to-your-projects"></a>Adicione videoclips aos seus projetos

O site do Video Indexer permite-lhe utilizar as informações profundas dos seus vídeos para: encontrar o conteúdo certo dos meios de comunicação, localizar as partes que lhe interessam e utilizar os resultados para criar um projeto inteiramente novo. 

Uma vez criado, o projeto pode ser renderizado e descarregado a partir de Video Indexer e ser usado nas suas próprias aplicações de edição ou fluxos de trabalho a jusante.

Alguns cenários onde poderá achar este recurso útil são: 

* Criando destaques de filmes para trailers.
* Usando velhos clips de vídeos em noticiários.
* Criar conteúdo mais curto para as redes sociais.

Este artigo mostra como criar um projeto e adicionar clips selecionados dos vídeos ao projeto. 

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerir vídeos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione o **separador Projetos.** Se já criou projetos antes, verá todos os seus outros projetos aqui.
1. Clique **em Criar novo projeto.**  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Criar um novo projeto":::
1. Dê um nome ao seu projeto clicando no ícone do lápis. Substitua o texto que diz "Projeto Sem Título" pelo nome do seu projeto e clique na verificação.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Um novo projeto":::
    
### <a name="add-videos-to-the-project"></a>Adicione vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos só podem conter vídeos indexados no mesmo idioma. </br>Uma vez selecionado um vídeo num idioma, não é possível adicionar os vídeos na sua conta que se encontrem num idioma diferente, os vídeos que têm outras línguas serão acinzentados/desativados.

1. Adicione vídeos com os quais pretende trabalhar neste projeto selecionando **Vídeos Add**.

    Verá todos os vídeos na sua conta e uma caixa de pesquisa que diz "Procure texto, palavras-chave ou conteúdo visual". Pode pesquisar vídeos que tenham uma pessoa especificada, etiqueta, marca, palavra-chave ou ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estávamos à procura de vídeos que mencionassem apenas "visão personalizada" na transcrição (utilize **o Filtro** se quiser filtrar os resultados da sua pesquisa).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Screenshot mostra procura de vídeos que mencionam visão personalizada":::
1. Clique **em Adicionar** para adicionar vídeos ao projeto.
1. Agora, vais ver todos os vídeos que escolheste. Estes são os vídeos a partir dos quais vai selecionar clips para o seu projeto.

    Pode reorganizar a ordem dos vídeos arrastando e baixando ou selecionando o botão do menu da lista e selecionando **Move down** ou **Move up**. A partir do menu da lista, poderá também remover o vídeo deste projeto. 
    
    Pode adicionar mais vídeos a este projeto a qualquer momento selecionando **Vídeos Add**. Também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. É melhor fazer isto se quiser mostrar um clip de um vídeo e depois um clip de outro e depois outro clip do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clips para usar no seu projeto

Se clicar na seta descendente no lado direito de cada vídeo, abrirá as ideias no vídeo com base em selos temporais (clips do vídeo). 

1. Para criar consultas para clips específicos, utilize a caixa de pesquisa que diz "Pesquisa na transcrição, texto visual, pessoas e etiquetas".
1. Selecione **Ver Insights** para personalizar quais as ideias que deseja ver e que não quer ver. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Screenshot mostra procura de vídeos que dizem Tente serviços cognitivos":::
1. Adicione filtros para especificar mais detalhes sobre as cenas que procura selecionando **opções de Filtro**.

    Pode adicionar vários filtros. 
1. Assim que estiver satisfeito com os seus resultados, selecione os clips que pretende adicionar a este projeto selecionando o segmento que pretende adicionar. Pode desmarcar este clipe clicando novamente no segmento.
    
    Adicione todos os segmentos de um vídeo (ou, tudo o que foi devolvido após a sua pesquisa) clicando na opção do menu da lista ao lado do vídeo e selecionando **Selecione todos**. 

À medida que está a selecionar e a encomendar os seus clips, pode visualizar o vídeo no leitor no lado direito da página. 

> [!IMPORTANT]
> Lembre-se de guardar o seu projeto quando escoar alterações selecionando o **projeto Save** no topo da página. 

### <a name="render-and-download-the-project"></a>Rendereia e descarregue o projeto

> [!NOTE]
> Para contas pagas por Video Indexer, renderizar o seu projeto tem custos de codificação. As contas de ensaio do Indexer de Vídeo estão limitadas a 5 horas de renderização.

1. Uma vez feito, certifique-se de que o seu projeto foi salvo. Agora pode fazer este projeto. Clique em **Render**, surge um diálogo popup que lhe diz que o indexante de Vídeo irá renderizar um ficheiro e, em seguida, o link de descarregamento será enviado para o seu e-mail. Selecione Proceder. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Screenshot mostra Video Indexer com a opção de renderizar e descarregar o seu projeto":::
    
    Também verá uma notificação de que o projeto está a ser prestado no topo da página. Uma vez feito, verá uma nova notificação de que o projeto foi prestado com sucesso. Clique na notificação para descarregar o projeto. Irá descarregar o projeto em formato MP4.
1. Pode aceder a projetos guardados a partir do separador **Projetos.** 

    Se selecionar este projeto, vê todas as ideias e a cronologia deste projeto. Se selecionar **editor de vídeo,** pode continuar a fazer edições para este projeto. As edições incluem adicionar ou remover vídeos e clips ou renomear o projeto.
    
## <a name="create-a-project-from-your-video"></a>Crie um projeto a partir do seu vídeo

Pode criar um novo projeto diretamente a partir de um vídeo na sua conta. 

1. Aceda ao separador **Biblioteca** do site do Índice de Vídeo.
1. Abra o vídeo que pretende utilizar para criar o seu projeto. Na página de insights e linha do tempo, selecione o botão **editor de vídeo.**

    Isto leva-o à mesma página que usou para criar um novo projeto. Ao contrário do novo projeto, vê-se os segmentos de insights com tempos do vídeo, que tinha começado a editar anteriormente.

## <a name="see-also"></a>Ver também

[Descrição geral do Video Indexer](video-indexer-overview.md)

