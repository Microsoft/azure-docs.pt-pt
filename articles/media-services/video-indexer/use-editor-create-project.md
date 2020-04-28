---
title: Use o editor do Indexer de Vídeo para criar projetos
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor do Indexer de Vídeo para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73839169"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Use o editor do Indexer de Vídeo para criar projetos

O site do Indexer de Vídeo, permite-lhe utilizar as informações profundas dos seus vídeos para: encontrar o conteúdo dos meios de comunicação certos, localizar as peças em que está interessado e utilizar os resultados para criar um projeto inteiramente novo. Uma vez criado, o projeto pode ser renderizado e descarregado do Indexer de Vídeo e ser usado nas suas próprias aplicações de edição ou fluxos de trabalho a jusante.

Alguns cenários onde você pode achar esta funcionalidade útil são: 

* Criar destaques de filmes para trailers.
* Usando vídeos antigos em noticiários.
* Criar conteúdos mais curtos para as redes sociais.

Este artigo mostra como criar um projeto de raiz e também como criar um projeto a partir de um vídeo na sua conta.

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerir vídeos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione o separador **Projetos.** Se já criou projetos antes, verá todos os seus outros projetos aqui.
1. Clique **em Criar novo projeto.**  

    ![Novo projeto](./media/video-indexer-view-edit/new-project.png)
1. Dê um nome ao seu projeto clicando no ícone do lápis. Substitua o texto que diz "Projeto Sem título" com o nome do seu projeto e clique no cheque.

    ![Novo projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicione vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos só podem conter vídeos indexados na mesma língua. Uma vez que selecione um vídeo numa língua, não pode adicionar os vídeos na sua conta que se encontram num idioma diferente.

1. Adicione vídeos com os quais pretende trabalhar neste projeto selecionando **vídeos Adicionar**.

    Verá todos os vídeos na sua conta e uma caixa de pesquisa que diz "Procure texto, palavras-chave ou conteúdo visual". Para pesquisar vídeos que tenham uma pessoa especificada, etiqueta, marca, palavra-chave ou ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estamos à procura de vídeos que mencionem "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Pode filtrar ainda mais os seus resultados selecionando **os resultados**do Filtro . Pode filtrar para mostrar vídeos que tenham uma determinada pessoa ou especificar que só quer ver resultados de vídeo que sejam num determinado idioma ou que tenham um proprietário específico. <br/> Também pode especificar o âmbito da sua consulta. Por exemplo, se quiser pesquisar "GitHub" no OCR, selecione **Texto Visual**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    Pode colocar vários filtros na sua consulta. Utilize **+** / **-** os botões para adicionar/remover filtros. Utilize **filtros clear** para remover todos os filtros.
1. Para adicionar vídeos, selecione-os e, em seguida, **selecione Adicionar**.
1. Agora, verá todos os vídeos que escolheu. Estes são os vídeos a partir dos quais vai selecionar clips para o seu projeto.

    Pode reorganizar a encomenda dos vídeos arrastando e largando ou selecionando o botão do menu da lista e selecionando **Mover para baixo** ou **mover-se**para cima . Do menu da lista, também poderá remover o vídeo deste projeto. 

    ![Reorganizar](./media/video-indexer-view-edit/rearrange.png)
    
    Tem a opção de adicionar mais vídeos a este projeto a qualquer momento selecionando **vídeos Adicionar**. Também pode adicionar múltiplas ocorrências do mesmo vídeo ao seu projeto. É melhor fazer isto se quiser mostrar um clip de um vídeo e depois um clip de outro e depois outro clip do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clips para usar no seu projeto

Se clicar na seta para baixo no lado direito de cada vídeo, abrirá as ideias no vídeo com base em selos de tempo (clips do vídeo). 

1. Selecione **Ver Insights** para personalizar quais os insights que deseja ver e quais não quer ver. 

    ![Ver informações](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clips específicos, utilize a caixa de pesquisa que diz "Pesquisar em transcrição, texto visual, pessoas e etiquetas".
1. Adicione filtros para especificar mais detalhes sobre as cenas que procura selecionando **as opções**do Filtro .

    ![Opções de filtragem](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, pode querer ver clips onde o GitHub é mencionado enquanto Donovan Brown está no ecrã. Para isso, é necessário adicionar um filtro "incluir" que tenha "Pessoas" como o tipo de introspeção. Em seguida, precisa escrever "Donovan Brown" na caixa de pesquisa para o filtro.
    
    ![Incluir](./media/video-indexer-view-edit/include.png)
    
    Se quiser clips onde o GitHub é mencionado enquanto Donovan Brown _não_ está no ecrã, simplesmente mudaria o filtro "incluir" num filtro "excluir" utilizando a queda. 

1. Adicione um clip ao seu projeto selecionando o segmento que pretende adicionar. Pode desseleccionar este clip clicando novamente no segmento.
    
    Adicione todos os segmentos de um vídeo clicando na opção do menu da lista ao lado do vídeo e selecione **todos os segmentos**. 

    ![Adicionar tudo](./media/video-indexer-view-edit/add-all.png)

    Pode limpar toda a sua seleção selecionando a seleção Clear.

> [!TIP]
> À medida que está a selecionar e encomendar os seus clips, pode pré-visualizar o vídeo no leitor do lado direito da página. 

![Pré-visualização](./media/video-indexer-view-edit/preview.png)

Lembre-se de salvar o seu projeto quando fizer alterações selecionando **o projeto Save**. 

### <a name="render-and-download-the-project"></a>Render e baixar o projeto

> [!NOTE]
> Para contas pagas por Indexer de Vídeo, renderizar o seu projeto tem custos de codificação. As contas de teste do Indexer de vídeo estão limitadas a 5 horas de renderização.

1. Assim que terminar, certifique-se de que o seu projeto foi salvo. Agora pode tornar este projeto. Selecione **Render e Download**. 

    ![Guardar](./media/video-indexer-view-edit/save.png)

    Haverá um popup que lhe diz que o indexante de vídeo irá renderizar um ficheiro e, em seguida, o link de descarregamento será enviado para o seu e-mail. Selecione Proceder. 
    
    Verá também uma notificação de que o projeto está a ser prestado em cima da página. Uma vez feito, verá uma nova notificação de que o projeto foi entregue com sucesso. Clique na notificação para descarregar o projeto. Irá descarregar o projeto em formato MP4.

    ![Renderização feita](./media/video-indexer-view-edit/rendering-done.png)

1. Pode aceder a projetos guardados a partir do separador **Projetos.** 

    Se selecionar este projeto, verá todos os insights e a cronologia deste projeto. Se selecionar editor de **Vídeo,** pode continuar a fazer edições para este projeto. As edificações incluem adicionar ou remover vídeos e clips ou renomear o projeto.

    ![Editor de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Crie um projeto a partir do seu vídeo

Pode criar um novo projeto diretamente a partir de um vídeo na sua conta. 

1. Vá ao separador da **Biblioteca** do site do Indexer de Vídeo.
1. Abra o vídeo que pretende utilizar para criar o seu projeto. Na página de insights e linha do tempo, selecione o botão de editor de **vídeo.**

    Isto leva-o à mesma página que usou para criar um novo projeto. Ao contrário do novo projeto, vê-se os segmentos de insights timestamped do vídeo, que já tinha começado a editar anteriormente.

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md)

