---
title: Exporte o seu modelo para mobile - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como exportar o seu modelo para uso na criação de aplicações móveis ou correr localmente para classificação em tempo real.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718945"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exporte o seu modelo para uso com dispositivos móveis

O Custom Vision Service permite que os classificadores sejam exportados para serem desativados. Pode incorporar o seu classificador exportado numa aplicação e executá-lo localmente num dispositivo para classificação em tempo real.

## <a name="export-options"></a>Opções de exportação

O Serviço de Visão Personalizada suporta as seguintes exportações:

* __Tensorflow__ para __Android__.
* __CoreML__ para __iOS11__.
* __ONNX__ para __Windows ML__.
* Kit de __[desenvolvimento vision AI](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Um __recipiente Docker__ para windows, linux ou arquitetura ARM. O recipiente inclui um modelo tensorflow e um código de serviço para utilizar a API de Visão Personalizada.

> [!IMPORTANT]
> O Custom Vision Service exporta apenas domínios __compactos.__ Os modelos gerados por domínios compactos são otimizados para os constrangimentos da classificação em tempo real em dispositivos móveis. Os classificadores construídos com um domínio compacto podem ser ligeiramente menos precisos do que um domínio padrão com a mesma quantidade de dados de treino.
>
> Para obter informações sobre a melhoria dos seus classificadores, consulte o documento De melhoramento do [seu classificador.](getting-started-improving-your-classifier.md)

## <a name="convert-to-a-compact-domain"></a>Converter em um domínio compacto

> [!NOTE]
> Os passos nesta secção só se aplicam se tiver um modelo existente que não esteja definido para domínio compacto.

Para converter o domínio de um modelo existente, tome os seguintes passos:

1. No site da [visão Personalizada,](https://customvision.ai)selecione o ícone __Home__ para ver uma lista dos seus projetos.

    ![Imagem do ícone da casa e lista de projetos](./media/export-your-model/projects-list.png)

1. Selecione um projeto e, em seguida, selecione o ícone __Engrenagem__ na parte superior direita da página.

    ![Imagem do ícone do equipamento](./media/export-your-model/gear-icon.png)

1. Na secção __Domínios,__ selecione um dos domínios __compactos.__ Selecione __Guardar Alterações__ para guardar as alterações. 

    > [!NOTE]
    > Para o Vision AI Dev Kit, o projeto deve ser criado com o domínio __Geral (Compacto)__ e deve especificar a opção **Vision AI Dev Kit** no âmbito da secção Capacidades de **Exportação.**

    ![Imagem de seleção de domínios](./media/export-your-model/domains.png)

1. A partir do topo da página, selecione __Train__ para retreinar utilizando o novo domínio.

## <a name="export-your-model"></a>Exportar o seu modelo para utilização com dispositivos móveis

Para exportar o modelo após a reconversão, utilize os seguintes passos:

1. Vá ao separador **Performance** e selecione __Export .__ 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se a entrada __exportação__ não estiver disponível, a iteração selecionada não utiliza um domínio compacto. Utilize a secção __Iterações__ desta página para selecionar uma iteração que utiliza um domínio compacto e, em seguida, __selecione Export .__

1. Selecione o formato de exportação pretendido e, em seguida, selecione __Exportar__ para descarregar o modelo.

## <a name="next-steps"></a>Passos seguintes

Integre o seu modelo exportado numa aplicação explorando um dos seguintes artigos ou amostras:

* [Use o seu modelo Tensorflow com Python](export-model-python.md)
* [Use o seu modelo ONNX com Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Consulte a amostra do [modelo CoreML numa aplicação iOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagem em tempo real com Swift.
* Veja a amostra do [modelo Tensorflow numa aplicação Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagem em tempo real no Android.
* Veja a amostra do [modelo CoreML com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para classificação de imagem em tempo real numa aplicação xamarin iOS.
