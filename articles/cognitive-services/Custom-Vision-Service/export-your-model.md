---
title: Exporte o seu modelo para mobile - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como exportar o seu modelo para uso na criação de aplicações móveis ou executado localmente para classificação em tempo real.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "85391762"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exporte o seu modelo para uso com dispositivos móveis

O Custom Vision Service permite que os classificadores sejam exportados para ficarem offline. Pode incorporar o seu classificador exportado numa aplicação e executá-lo localmente num dispositivo para classificação em tempo real.

## <a name="export-options"></a>Opções de exportação

O Custom Vision Service suporta as seguintes exportações:

* __Tensorflow__ para __Android__.
* __CoreML__ para __iOS11__.
* __ONNX__ para __Windows ML__.
* __[Kit de desenvolvimento vision Ai](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Um __recipiente Docker__ para windows, linux ou arquitetura ARM. O recipiente inclui um modelo de tensorflow e código de serviço para utilizar a API de visão personalizada.

> [!IMPORTANT]
> O Serviço de Visão Personalizada exporta apenas domínios __compactos.__ Os modelos gerados por domínios compactos são otimizados para os constrangimentos da classificação em tempo real em dispositivos móveis. Os classificadores construídos com um domínio compacto podem ser ligeiramente menos precisos do que um domínio padrão com a mesma quantidade de dados de treino.
>
> Para obter informações sobre a melhoria dos seus classificadores, consulte o documento [de melhoramento do seu classificador.](getting-started-improving-your-classifier.md)

## <a name="convert-to-a-compact-domain"></a>Converter-se em um domínio compacto

> [!NOTE]
> Os passos desta secção só se aplicam se tiver um modelo existente que não esteja definido para domínio compacto.

Para converter o domínio de um modelo existente, tome os seguintes passos:

1. No site da [visão personalizada,](https://customvision.ai)selecione o ícone __Home__ para ver uma lista dos seus projetos.

    ![Imagem do ícone da casa e lista de projetos](./media/export-your-model/projects-list.png)

1. Selecione um projeto e, em seguida, selecione o ícone __De Engrenagem__ no canto superior direito da página.

    ![Imagem do ícone de engrenagem](./media/export-your-model/gear-icon.png)

1. Na secção __Domínios,__ selecione um dos domínios __compactos.__ Selecione __Guardar Alterações__ para guardar as alterações. 

    > [!NOTE]
    > Para o Vision AI Dev Kit, o projeto deve ser criado com o domínio __Geral (Compacto)__ e tem de especificar a opção **Vision AI Dev Kit** na secção **Capacidades de Exportação.**

    ![Imagem de seleção de domínios](./media/export-your-model/domains.png)

1. A partir do topo da página, selecione __Train__ para retreinar utilizando o novo domínio.

## <a name="export-your-model"></a>Exportar o seu modelo para utilização com dispositivos móveis

Para exportar o modelo após a reconversão, utilize os seguintes passos:

1. Vá ao **separador Performance** e selecione __Export__. 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se a entrada __exportar__ não estiver disponível, a iteração selecionada não utiliza um domínio compacto. Utilize a secção __iterações__ desta página para selecionar uma iteração que utilize um domínio compacto e, em seguida, selecione __Export__.

1. Selecione o formato de exportação pretendido e, em seguida, __selecione Export__ para descarregar o modelo.

## <a name="next-steps"></a>Passos seguintes

Integre o seu modelo exportado numa aplicação explorando um dos seguintes artigos ou amostras:

* [Use o seu modelo de tensorflow com Python](export-model-python.md)
* [Use o seu modelo ONNX com o Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Consulte a amostra do [modelo CoreML numa aplicação iOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagem em tempo real com Swift.
* Consulte a amostra do [modelo Tensorflow numa aplicação Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagem em tempo real no Android.
* Consulte a amostra do [modelo CoreML com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para a classificação de imagem em tempo real numa aplicação Xamarin iOS.
