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
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616081"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Utilize um modelo ONNX da Custom Vision com Windows ML (pré-visualização)

Saiba como utilizar um modelo ONNX exportado do Serviço de Visão Personalizada com o Windows ML (pré-visualização).

Neste guia, você vai aprender a usar um ficheiro ONNX exportado do Serviço de Visão Personalizada com o Windows ML. Você usará o exemplo da aplicação UWP com o seu próprio classificador de imagem treinado.

## <a name="prerequisites"></a>Pré-requisitos

* Versão 10 do Windows 10 ou superior
* Windows SDK para construir 17763 ou mais
* O Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho de __programação da Plataforma Universal do Windows__ ativada.
* Modo de desenvolvimento ativado no seu PC. Para obter mais informações, consulte [Ativar o seu dispositivo para o desenvolvimento.](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="about-the-example-app"></a>Sobre a aplicação de exemplo

A aplicação incluída é uma aplicação genérica do Windows UWP. Permite-lhe selecionar uma imagem do seu computador, que é processada por um modelo de classificação armazenado localmente. As etiquetas e as pontuações devolvidas pelo modelo são apresentadas junto à imagem.

## <a name="get-the-example-code"></a>Obter o código de exemplo

A aplicação exemplo está disponível no repo de Amostra de [Visão Personalizada ONNX dos Serviços Cognitivos onNX](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) no GitHub. Clone-o para a sua máquina local e abra *sampleOnnxEvaluationApp.sln* em Visual Studio.

## <a name="test-the-application"></a>Testar a aplicação

1. Utilize a tecla `F5` para iniciar a aplicação a partir do Visual Studio. Poderá ser-lhe pedido para ativar o modo de Programador.
1. Quando a aplicação for iniciada, utilize o botão para selecionar uma imagem para pontuação. O modelo ONNX padrão é treinado para classificar diferentes tipos de plâncton.

## <a name="use-your-own-model"></a>Utilizar o seu próprio modelo

Para utilizar o seu próprio modelo de classificação de imagem, siga estes passos:

1. Criar e preparar um classificador com o Serviço de Visão Personalizada. Para obter instruções sobre como fazê-lo, consulte [Criar e treinar um classificador](./getting-started-build-a-classifier.md). Utilize um dos domínios **compactos,** como **o General (compacto)**. 
   * Se tiver um classificador existente que utilize um domínio diferente, pode convertê-lo em **compacto** nas definições do projeto. Em seguida, re-treinar o seu projeto antes de continuar.
1. Exporte o seu modelo. Mude para o separador Desempenho e selecione uma iteração treinada com um domínio **compacto.** Selecione o **botão Exportação** que aparece. Em seguida, selecione **ONNX** e, em seguida, **Export**. Assim que o ficheiro estiver pronto, selecione o botão **Transferir**. Para obter mais informações sobre as opções de exportação, consulte [Export your model](./export-your-model.md).
1. Abra o ficheiro *.zip* descarregado e extraia o ficheiro *modelo.onnx* do mesmo. Este ficheiro contém o seu modelo de classificador.
1. No Solution Explorer no Estúdio Visual, clique com o botão direito na pasta **Ativos** e selecione __Add Existing Item__. Selecione o seu ficheiro ONNX.
1. No Solution Explorer, clique com o botão direito no ficheiro ONNX e selecione **Propriedades**. Altere as propriedades seguintes do ficheiro:
   * __Construir Ação__  ->  __Conteúdo__
   * __Cópia para o Diretório de__  ->  Saída __Copiar se mais recente__
1. Em _seguida,_ abra MainPage.xaml.cs e altere o valor `_ourOnnxFileName` do nome do seu ficheiro ONNX.
1. Use o `F5` para construir e executar o projeto.
1. Clique no botão para selecionar a imagem a avaliar.

## <a name="next-steps"></a>Passos seguintes

Para descobrir outras formas de exportar e utilizar um modelo de Visão Personalizada, veja os seguintes documentos:

* [Exportar o seu modelo para utilização com dispositivos móveis](./export-your-model.md)
* [Utilizar o modelo exportado do Tensorflow numa aplicação Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Utilizar o modelo exportado do CoreML numa aplicação Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Utilizar o modelo exportado do CoreML numa aplicação iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre a utilização de modelos ONNX com o Windows ML, consulte [Integrar um modelo na sua aplicação com o Windows ML](/windows/ai/windows-ml/integrate-model).