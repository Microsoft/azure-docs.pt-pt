---
title: Utilize o editor do indexador de vídeo para criar projetos
titlesuffix: Azure Media Services
description: Este tópico demonstra como utilizar o editor de indexador de vídeo para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896650"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Utilize o editor do indexador de vídeo para criar projetos

Site do indexador de vídeo, permite-lhe utilizar aprofundados dos seus vídeos para: encontrar o conteúdo do suporte de dados correta, localize as partes que está interessado e utilizar os resultados para criar um projeto inteiramente novo. Depois de criado, o projeto pode ser processado e transferido a partir do Video Indexer e ser utilizados na sua própria edição de aplicações ou fluxos de trabalho de downstream.

Seguem-se alguns cenários em que pode ser útil para: 

* Criação de filme destaca para trailers.
* Usando o antigos clips de vídeos em conversões de notícias.
* Criação de conteúdo mais curto para mídia social.

Este artigo mostra como criar um projeto a partir do zero e também como criar um projeto a partir de um vídeo na sua conta.

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerir vídeos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione o **projetos** separador. Se tiver criado projetos antes, verá todos os outros projetos aqui.
1. Clique em **criar novo projeto**.  

    ![Novo projeto](./media/video-indexer-view-edit/new-project.png)
1. Dê um nome ao seu projeto clicando no ícone de lápis. Substitua o texto que indica "Sem título project" com o nome do projeto e clique no cheque.

    ![Novo projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicionar vídeos ao projeto

> [!NOTE]
> Atualmente, projetos só podem conter vídeos indexados no mesmo idioma. Depois de selecionar um vídeo num idioma, não é possível adicionar os vídeos na sua conta de que estão num idioma diferente.

1. Adicionar vídeos que pretende trabalhar com neste projeto, selecionando **adicionar vídeos**.

    Verá todos os vídeos na sua conta e uma caixa de pesquisa que diz "Pesquisa de texto, palavras-chave ou conteúdo visual". Para procurar vídeos que tenham uma pessoa específica, a etiqueta, a marca, a palavra-chave ou o ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estamos procurando vídeos que mencionam "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Pode filtrar ainda mais os resultados, selecionando **filtrar resultados**. Pode filtrar para mostrar os vídeos que tenham uma determinada pessoa nos mesmos, ou para especificar que apenas pretende ver os resultados de vídeo que são uma num determinado idioma ou um proprietário específico. <br/> Também pode especificar o âmbito da sua consulta. Por exemplo, se pretende procurar "GitHub" o OCR, selecione **texto Visual**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    Pode acrescentar vários filtros à sua consulta. Utilize o **+** / **-** botões para adicionar ou remover filtros. Uso **Limpar filtros** para remover todos os filtros.
1. Para adicionar vídeos, selecioná-los e, em seguida, selecione **adicionar**.
1. Agora, irá ver todos os vídeos que escolheu. Estes são os vídeos a partir do qual vai selecionar clips para o seu projeto.

    Pode reorganizar a ordem dos vídeos arrastando e soltando ou ao selecionar o botão de menu de lista e selecionar **mover para baixo** ou **mover para cima**. No menu de lista, também poderá remover o vídeo deste projeto. 

    ![Reorganizar](./media/video-indexer-view-edit/rearrange.png)
    
    Tem a opção para adicionar mais vídeos a este projeto em qualquer altura, selecionando **adicionar vídeos**. Também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. Pode querer fazer isso, se quiser mostrar um clip de vídeo de um e, em seguida, um clip de outro e, em seguida, outra clip do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clips para utilizar no seu projeto

Se clicar na seta para baixo no lado direito de cada vídeo, abrirá as informações de vídeo com base nos carimbos de data / hora (clips de vídeo). 

1. Selecione **ver informações** para personalizar as informações que pretende ver e o que não deseja ver. 

    ![Ver informações](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clips específicas, utilize a caixa de pesquisa que diz "Pesquisar na transcrição, texto visual, pessoas e as etiquetas".
1. Adicionar filtros para especificar ainda mais detalhes sobre o plano que está procurando, selecionando **opções de filtro**.

    ![Opções de filtragem](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, pode querer ver clipes de onde o GitHub é mencionado enquanto Donovan Brown é na tela. Para isso, precisa adicionar um filtro de "inclusão" que tenha "Pessoas" como o tipo de informações. Em seguida, terá de digitar "Donovan Brown" na caixa de pesquisa para o filtro.
    
    ![Incluir](./media/video-indexer-view-edit/include.png)
    
    Se pretender que os clips em que o GitHub é mencionado enquanto Donovan Brown é _não_ na tela, bastaria alterar o filtro de "inclusão" num filtro de "excluir" através da lista pendente. 

1. Adicione um clip ao seu projeto, selecionando o segmento que pretende adicionar. Pode desmarcar este clip ao clicar no segmento novamente.
    
    Adicionar todos os segmentos de um vídeo ao clicar na opção de menu de lista ao lado de vídeo e selecionar **selecionar todos os segmentos**. 

    ![Adicionar todos](./media/video-indexer-view-edit/add-all.png)

    Pode limpar todos os de sua seleção, selecionando limpar seleção.

> [!TIP]
> À medida que é seleção e ordenação sua clips, pode visualizar o vídeo no player de no lado direito da página. 

![Pré-visualização](./media/video-indexer-view-edit/preview.png)

Lembre-se de guardar o seu projeto quando fizer alterações, selecionando **Save project**. 

### <a name="render-and-download-the-project"></a>Compor e transfira o projeto

> [!NOTE]
> Indexador de vídeos pago contas, a composição de seu projeto tem custos de codificação. Vídeo contas de avaliação de indexador estão limitadas a 5 horas de processamento.

1. Quando tiver terminado, certifique-se de que o projeto foi guardado. Agora pode processar este projeto. Selecione **processar e transferir**. 

    ![Guardar](./media/video-indexer-view-edit/save.png)

    Haverá um pop-up que diz a que o indexador de vídeo renderizará um ficheiro e, em seguida, o link de download será envio para o seu e-mail. Selecione avançar. 
    
    Também verá uma notificação que o projeto está a ser processado na parte superior da página. Quando estiver pronta a ser processado, verá uma nova notificação que o projeto tenha sido processado com êxito. Clique na notificação para transferir o projeto. Ele irá transferir o projeto no formato mp4.

    ![Processamento concluído](./media/video-indexer-view-edit/rendering-done.png)

1. Pode acessar projetos guardados a partir da **projetos** separador. 

    Se selecionar este projeto, verá todas as informações e a linha de tempo deste projeto. Se selecionou **editor de vídeo**, pode continuar a fazer edições a este projeto. As edições incluem adicionar ou remover vídeos e clips ou mudar o nome do projeto.

    ![Edição de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Criar um projeto do seu vídeo

Pode criar um novo projeto diretamente a partir de um vídeo na sua conta. 

1. Vá para o **biblioteca** separador do site do Video Indexer.
1. Abra o vídeo que pretende utilizar para criar o seu projeto. Na página de linha cronológica e insights, selecione o **editor de vídeo** botão.

    Isto leva-o para a mesma página que utilizou para criar um novo projeto. Ao contrário do novo projeto, verá os segmentos de insights timestamped do vídeo, o que tinha iniciou anteriormente a edição.

## <a name="see-also"></a>Consulte também

[Descrição geral do indexador vídeo](video-indexer-overview.md)

