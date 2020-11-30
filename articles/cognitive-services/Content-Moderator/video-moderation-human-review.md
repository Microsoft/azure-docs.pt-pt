---
title: Moderação de vídeo com a ferramenta Review - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize moderação de vídeo assistida por máquina e a ferramenta Review para moderar conteúdo inadequado
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 079d5ea0259a436100b81489043b71a81ec1a330
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327224"
---
# <a name="video-moderation-with-the-review-tool"></a>Moderação de vídeo com a ferramenta Review

Utilize a ferramenta de [moderação](video-moderation-api.md) e [revisão](Review-Tool-User-Guide/human-in-the-loop.md) de vídeo assistida por moderadores de conteúdo para moderar vídeos e transcrições para conteúdos adultos (explícitos) e picantes (sugestivos) para obter os melhores resultados para o seu negócio.

## <a name="view-videos-under-review"></a>Ver vídeos sob análise

No painel de instrumentos, selecione qualquer uma das filas de revisão dentro do tipo de conteúdo de vídeo. Isto iniciará uma revisão e abrirá a página de moderação de conteúdos de vídeo.

> [!div class="mx-imgBorder"]
> ![No Moderador de Conteúdo (Preview), o slider é realçado e definido para 4 avaliações. Os interruptores Blur All e Black e White estão em destaque, e ambos estão definidos.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Rever contagem

Utilize o slider no canto superior direito para definir o número de avaliações que gostaria de visualizar na página.

### <a name="view-type"></a>Tipo de visualização

Pode ver as diferentes entradas de conteúdo como azulejos ou numa visão detalhada. A **visualização De Pormenor** permitir-lhe-á ver quadros-chave e outras informações sobre o vídeo selecionado. 

> [!NOTE]
> Em vez de fazer fotogramas de saída a intervalos regulares, o serviço de moderação de vídeo identifica e produz apenas quadros (bons) potencialmente completos. Esta funcionalidade permite uma geração de quadros eficiente para análise adulta e picante ao nível do quadro.

A vista **Tiled** mostrará cada vídeo como um único azulejo. Selecione o botão de expansão acima de uma moldura de vídeo para ampliar o vídeo e esconder os outros.

### <a name="content-obscuring-effects"></a>Efeitos de ocultação de conteúdos

Utilize o **Borrão tudo** e os alternadores **preto e branco** para definir estes efeitos de ocultação de conteúdos. Estão ligados por defeito. Na vista **Tiled,** pode alternar os efeitos individualmente para cada vídeo.

## <a name="check-video-details"></a>Ver detalhes de vídeo

Na vista **Detalhe,** o painel direito mostrará vários separadores que lhe dão detalhes sobre o vídeo.

* Selecione o separador **Notas** para adicionar notas personalizadas a vídeos.
* Selecione o **separador Transcrição** para ver a transcrição de vídeo &mdash; o serviço extrai automaticamente uma transcrição de qualquer discurso no vídeo. Quando selecionar uma secção de texto, o leitor de vídeo saltará para essa parte do vídeo.
* Selecione o **separador de metadados de dados de** vídeo para visualizar metadados de ficheiros de vídeo.
* Selecione o separador **Histórico** para ver o histórico da revisão, como quando foi criado e como foi modificado.

> [!div class="mx-imgBorder"]
> ![O painel direito é realçado e o separador Notas é selecionado. Existe uma área de teste rotulada Adicionar uma nota.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Aplicar etiquetas de moderação

A principal tarefa de uma revisão de vídeo é aplicar ou remover etiquetas de moderação em vídeos ou partes de vídeos.

### <a name="bulk-tagging"></a>Marcação a granel

A **barra de ferramentas Bulk Tags** permite adicionar tags a vários vídeos selecionados ao mesmo tempo. Selecione um ou mais vídeos e, em seguida, selecione as etiquetas que pretende aplicar e clique **em submeter**. 

> [!div class="mx-imgBorder"]
> ![O botão + é realçado no painel de marcas a granel.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Marcação do quadro de chaves

Também pode adicionar etiquetas de moderação a quadros-chave específicos. Selecione os quadros do painel de azulejos do quadro da chave e, em seguida, selecione **tags Keyframe +** para aplicar as etiquetas desejadas.

> [!NOTE]
> Se o serviço não conseguir extrair quadros-chave, o painel de azulejos do quadro da chave não mostrará **nenhum quadro disponível** e a opção de selecionar quadros-chave será acinzentada. Neste caso, só é possível aplicar tags no vídeo como um todo (utilizando as **etiquetas de vídeo +** botão).

> [!div class="mx-imgBorder"]
> ![O painel de azulejos, o leitor de vídeo, o painel de tags keyframe e as vidraças de etiquetas de vídeo são todas mostradas. Destacam-se as tags Keyframe + e video-tags +](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Coloque uma revisão em espera

O botão **Hold** na parte inferior do painel de vídeo permite-lhe colocar uma revisão em espera para que possa recuperá-lo e completá-lo mais tarde. Pode fazê-lo para uma revisão que requer uma consulta de outro membro da equipa ou gestor que não esteja disponível atualmente. 

Pode ver os vídeos em espera clicando no botão **Hold** na parte superior do ecrã. O painel de espera aparece à direita. A partir daqui, pode selecionar várias avaliações em espera e libertá-las de volta na fila ou definir o seu tempo de validade. Após o tempo pré-configurado, as avaliações em espera são libertadas de volta à fila. **Selecione Guardar** para começar a contagem rebationá-lo a partir do tempo de validade atualmente selecionado.

> [!div class="mx-imgBorder"]
> ![No painel de vídeo, destaca-se o botão Hold. Na parte inferior do painel, destaca-se uma caixa de combinação Hold Time, juntamente com os botões Release e Save.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Submeter uma revisão

Depois de aplicar as suas etiquetas, selecione o botão **Enviar** na parte inferior do painel de vídeo. Se tiver marcado vários vídeos, pode submetê-los sob uma única revisão ou como avaliações separadas.

## <a name="limbo-state"></a>Estado do limbo

Depois de submeter uma revisão, o vídeo é movido para o estado **limbo,** que pode ver selecionando o botão **Limbo** na parte superior do ecrã. Os vídeos permanecem no estado do Limbo durante um período de tempo pré-configurado (que pode alterar no menu na parte inferior), ou até que sejam revistos novamente ou submetidos manualmente.

Uma vez que os vídeos expirem do limbo, as suas avaliações são marcadas como completas.

## <a name="next-steps"></a>Passos seguintes

- Começa com o arranque rápido da [moderação de vídeo](video-moderation-api.md).
- Aprenda a gerar [comentários de vídeo](video-reviews-quickstart-dotnet.md) para os seus revisores humanos a partir da sua saída moderada.
- Adicione [comentários de transcrição de vídeo](video-transcript-reviews-quickstart-dotnet.md) às suas avaliações de vídeo.
- Confira o tutorial detalhado sobre como desenvolver uma solução completa de [moderação de vídeo.](video-transcript-moderation-review-tutorial-dotnet.md)