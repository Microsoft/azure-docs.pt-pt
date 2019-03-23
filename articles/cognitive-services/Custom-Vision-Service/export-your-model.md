---
title: Exporte o seu modelo para o mobile - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como exportar o seu modelo para utilização na criação de aplicativos móveis.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 405b6ebd06091536749751a94362d8c4a6495dbc
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351304"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exporte o seu modelo para utilização com dispositivos móveis

Serviço de visão personalizada permite classificadores exportada seja executado offline. Pode incorporar o seu classificador exportado numa aplicação e executá-lo localmente num dispositivo para a classificação em tempo real.

Serviço de visão personalizada suporta as seguintes exportações:

* __Tensorflow__ para __Android__.
* __CoreML__ para __iOS11__.
* __ONNX__ para __Windows ML__.
* Um Windows ou Linux __contentor__. O contêiner inclui um Tensorflow modelar e código para utilizar a API de serviço de visão personalizada de serviço. 

> [!IMPORTANT]
> Serviço de visão personalizada apenas exporta __compact__ domínios. Os modelos de gerados por domínios compactos estão otimizados para as restrições de classificação em tempo real em dispositivos móveis. Classificadores criadas com um domínio compact podem ser um pouco menos precisas do que um domínio padrão com a mesma quantidade de dados de treinamento.
>
> Para informações sobre como melhorar sua classificadores, consulte a [melhorar o classificador](getting-started-improving-your-classifier.md) documento.

## <a name="convert-to-a-compact-domain"></a>Converter a um domínio compact

> [!NOTE]
> Os passos nesta secção aplicam-se apenas se tiver um classificador existente que não está definido para compactar o domínio.

Para converter o domínio de um classificador existente, utilize os seguintes passos:

1. Partir do [página de visão personalizada](https://customvision.ai), selecione a __home page__ ícone para ver uma lista dos seus projetos. Também pode utilizar o [ https://customvision.ai/projects ](https://customvision.ai/projects) para ver os seus projetos.

    ![Imagem da lista de projetos e ícone de raiz](./media/export-your-model/projects-list.png)

2. Selecione um projeto e, em seguida, selecione o __engrenagem__ ícone no canto superior direito da página.

    ![Imagem do ícone de engrenagem](./media/export-your-model/gear-icon.png)

3. Na __domínios__ secção, selecione um __compact__ domínio. Selecione __guardar alterações__ para guardar as alterações.

    ![Imagem da seleção de domínios](./media/export-your-model/domains.png)

4. Na parte superior da página, selecione __Train__ para voltar a preparar através do novo domínio.

## <a name="export-your-model"></a>Exporte o seu modelo

Para exportar o modelo depois de reparametrização, utilize os seguintes passos:

1. Vá para o **desempenho** separador e selecione __exportar__. 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se o __exportar__ entrada não está disponível, em seguida, a iteração selecionada não utiliza um domínio compact. Utilize o __iterações__ seção desta página para selecionar uma iteração que utiliza um domínio compact e, em seguida, selecione __exportar__.

2. Selecione o formato de exportação e, em seguida, selecione __exportar__ para transferir o modelo.

## <a name="next-steps"></a>Passos Seguintes

Integre o seu modelo exportado num aplicativo. Vários aplicativos de exemplo estão disponíveis:

* Um exemplo para o [com o seu modelo exportado do CoreML num aplicativo iOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagens em tempo real com Swift
* Exemplo de aplicação do iOS para [com o seu modelo de CoreML exportado com o Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para classificação de imagens em tempo real 
* Exemplo para [com o seu modelo exportado do Tensorflow num aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagens em tempo real 
* [Com o seu modelo de Tensorflow com Windows](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-model-python)
* Exemplo para [com o seu modelo ONNX exportado com o Windows Machine Learning](https://azure.microsoft.com/resources/samples/cognitive-services-onnx-customvision-sample/)
