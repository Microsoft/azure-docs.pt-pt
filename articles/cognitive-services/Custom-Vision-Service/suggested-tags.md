---
title: Rotular imagens mais rapidamente com Smart Labeler
titleSuffix: Azure Cognitive Services
description: Neste guia, aprenderá a usar smart labeler para gerar etiquetas sugeridas para imagens. Isto permite-lhe rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647756"
---
# <a name="label-images-faster-with-smart-labeler"></a>Rotular imagens mais rapidamente com Smart Labeler

Neste guia, aprenderá a usar smart labeler para gerar etiquetas sugeridas para imagens. Isto permite-lhe rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada.

Ao marcar imagens para um modelo Custom Vision, o serviço utiliza a mais recente iteração treinada do modelo para prever os rótulos de imagens não marcadas. Em seguida, mostra estas previsões como etiquetas sugeridas, com base no limiar de confiança selecionado e na incerteza da previsão. Em seguida, pode confirmar ou alterar as sugestões, acelerando o processo de marcação manual das imagens para treino.

## <a name="when-to-use-smart-labeler"></a>Quando usar o Smart Labeler

Tenha em mente as seguintes limitações:

* Só deve solicitar etiquetas sugeridas para imagens cujo conteúdo já tenha sido treinado uma vez. Não tenhasugestões para uma nova etiqueta que está acomeçar a treinar.

> [!IMPORTANT]
> A função Smart Labeler utiliza o mesmo modelo de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) que as previsões regulares. A primeira vez que acionar etiquetas sugeridas para um conjunto de imagens, será cobrado o mesmo que para chamadas de previsão. Depois disso, o serviço armazena os resultados das imagens selecionadas numa base de dados durante 30 dias, podendo acessá-las gratuitamente dentro desse período. Após 30 dias, será cobrado se pedir as etiquetas sugeridas novamente.

## <a name="smart-labeler-workflow"></a>Fluxo de trabalho smart Labeler

Os seguintes passos mostram-lhe como usar o Smart Labeler:

1. Faça upload de todas as suas imagens de treino para o seu projeto Custom Vision.
1. Rotule parte do seu conjunto de dados, escolhendo um número igual de imagens para cada etiqueta.
    > [!TIP]
    > Certifique-se de que utiliza todas as etiquetas para as quais pretende sugestões mais tarde.
1. Inicie o processo de treino.
1. Quando o treino estiver concluído, navegue para a vista **Untagged** e selecione o botão de **etiquetas sugerido seletiva** no painel esquerdo.
    > [!div class="mx-imgBorder"]
    > ![O botão de etiquetas sugerido é mostrado sob o separador de imagens não marcados.](./media/suggested-tags/suggested-tags-button.png)
1. Na janela popup que aparece, detete o número de imagens para as quais deseja sugestões. Só deve receber sugestões iniciais de etiquetas para uma parte das imagens não marcadas. Receberá sugestões melhores à medida que itera neste processo.
1. Confirme as etiquetas sugeridas, fixando as que não estão corretas.
    > [!TIP]
    > As imagens com etiquetas sugeridas são classificadas pela sua incerteza de previsão (valores mais baixos indicam maior confiança). Pode alterar a ordem de triagem com o **Sort por opção de incerteza.** Se definir a ordem para **alto a baixo,** pode corrigir as previsões de alta incerteza primeiro e, em seguida, confirmar rapidamente as baixas incertezas.
    * Em projetos de classificação de imagem, pode selecionar e confirmar etiquetas em lotes. Filtre a vista por uma determinada etiqueta sugerida, desmarque as imagens que são marcadas incorretamente e, em seguida, confirme o resto num lote.
        > [!div class="mx-imgBorder"]
        > ![As etiquetas sugeridas são apresentadas no modo de lote para IC com filtros.](./media/suggested-tags/ic-batch-mode.png)

        Também pode utilizar etiquetas sugeridas no modo de imagem individual, selecionando uma imagem da galeria.

        ![As etiquetas sugeridas são apresentadas no modo de imagem individual para ic.](./media/suggested-tags/ic-individual-image-mode.png)
    * Em projetos de deteção de objetos, as confirmações de lote não são suportadas, mas ainda pode filtrar e classificar por etiquetas sugeridas para uma experiência de rotulagem mais organizada. As miniaturas das suas imagens não marcadas mostrarão uma sobreposição de caixas de delimitação indicando a localização das etiquetas sugeridas. Se não selecionar um filtro de etiqueta sugerido, todas as suas imagens não marcadas aparecerão sem sobrepor as caixas de delimitação.
        > [!div class="mx-imgBorder"]
        > ![As etiquetas sugeridas são apresentadas no modo de lote para OD com filtros.](./media/suggested-tags/od-batch-mode.png)

        Para confirmar as etiquetas de deteção de objetos, é necessário aplicá-las a cada imagem individual na galeria.

        ![As etiquetas sugeridas são apresentadas no modo de imagem individual para OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Reinicie o processo de treino de novo.
1. Repita os passos anteriores até ficar satisfeito com a qualidade da sugestão.

## <a name="next-steps"></a>Passos seguintes

Siga um início rápido para começar a criar e treinar um projeto de Visão Personalizada.

* [Criar um classificador](getting-started-build-a-classifier.md)
* [Criar um detetor de objetos](get-started-build-detector.md)