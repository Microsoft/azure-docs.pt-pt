---
title: Utilize um modelo ONNX com Windows ML - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como criar uma aplicação UWP do Windows que utiliza um modelo ONNX exportado dos Serviços Cognitivos do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594300"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Utilize um modelo ONNX da Custom Vision com o Windows ML (pré-visualização)

Saiba como utilizar um modelo ONNX exportado do Serviço de Visão Personalizada com o Windows ML (pré-visualização).

Neste guia, você aprenderá a usar um ficheiro ONNX exportado do Serviço de Visão Personalizada com o Windows ML. Você usará o exemplo da aplicação UWP com o seu próprio classificador de imagem treinado.

## <a name="prerequisites"></a>Pré-requisitos

* Windows 10 versão 1809 ou superior
* Windows SDK para construção 17763 ou superior
* O Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho de __programação da Plataforma Universal do Windows__ ativada.
* Modo de desenvolvimento ativado no seu PC. Para mais informações, consulte [Ativar o seu dispositivo para desenvolvimento](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Sobre a aplicação de exemplo

A aplicação incluída é uma aplicação genérica do Windows UWP. Permite-lhe selecionar uma imagem do seu computador, que é então processada por um modelo de classificação armazenado localmente. As etiquetas e as pontuações devolvidas pelo modelo são apresentadas junto à imagem.

## <a name="get-the-example-code"></a>Obter o código de exemplo

A aplicação de exemplo está disponível no repo de amostra de [visão personalizada ONNX No](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) GitHub. Clone-o para a sua máquina local e abra *sampleOnnxEvaluationApp.sln* no Estúdio Visual.

## <a name="test-the-application"></a>Testar a aplicação

1. Utilize a tecla `F5` para iniciar a aplicação a partir do Visual Studio. Poderá ser-lhe pedido para ativar o modo de Programador.
1. Quando a aplicação for iniciada, utilize o botão para selecionar uma imagem para pontuação. O modelo ONNX padrão é treinado para classificar diferentes tipos de plâncton.

## <a name="use-your-own-model"></a>Utilizar o seu próprio modelo

Para utilizar o seu próprio modelo de classificação de imagem, siga estes passos:

1. Criar e preparar um classificador com o Serviço de Visão Personalizada. Para obter instruções sobre como fazê-lo, consulte [Criar e treinar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier). Utilize um dos domínios **compactos,** como o **General (compacto)**. 
   * Se tiver um classificador existente que utilize um domínio diferente, pode convertê-lo em **compacto** nas definições do projeto. Depois, retreine o seu projeto antes de continuar.
1. Exporte o seu modelo. Mude para o separador Performance e selecione uma iteração que foi treinada com um domínio **compacto.** Selecione o botão **Export** que aparece. Em seguida, selecione **ONNX**, e depois **exportar**. Assim que o ficheiro estiver pronto, selecione o botão **Transferir**. Para obter mais informações sobre as opções de exportação, consulte [Exportar o seu modelo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).
1. Abra o ficheiro *.zip* descarregado e extrai o ficheiro *.onnx* do modelo. Este ficheiro contém o seu modelo de classificação.
1. No Solution Explorer no Estúdio Visual, clique na pasta **de ativos** e selecione __Adicionar Item Existente__. Selecione o seu ficheiro ONNX.
1. No Solution Explorer, clique no ficheiro ONNX e selecione **Propriedades**. Altere as propriedades seguintes do ficheiro:
   * __Construir__ -> __Conteúdo de__ Ação
   * __Copiar para Cópia de Diretório__ -> de Saída__se mais recente__
1. Em _MainPage.xaml.cs_ seguida, abra MainPage.xaml.cs `_ourOnnxFileName` e altere o valor do seu ficheiro ONNX.
1. Use `F5` o para construir e executar o projeto.
1. Clique no botão para selecionar a imagem a avaliar.

## <a name="next-steps"></a>Passos seguintes

Para descobrir outras formas de exportar e utilizar um modelo de Visão Personalizada, veja os seguintes documentos:

* [Exportar o seu modelo para utilização com dispositivos móveis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Utilizar o modelo exportado do Tensorflow numa aplicação Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Utilizar o modelo exportado do CoreML numa aplicação Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Utilizar o modelo exportado do CoreML numa aplicação iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre a utilização de modelos ONNX com o Windows ML, consulte [Integrar um modelo na sua aplicação com o Windows ML](/windows/ai/windows-ml/integrate-model).
