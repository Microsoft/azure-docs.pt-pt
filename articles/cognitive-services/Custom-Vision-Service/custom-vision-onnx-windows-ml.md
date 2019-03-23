---
title: 'Tutorial: Utilizar um modelo ONNX com o Windows ML - serviço de visão personalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como criar uma aplicação UWP do Windows que utiliza um modelo ONNX exportado dos Serviços Cognitivos do Azure.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: larryfr
ms.openlocfilehash: af1b96b4ab47053a6737893832b484372ed37e99
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351869"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Tutorial: Utilizar um modelo ONNX de visão personalizada com o Windows ML (pré-visualização)

Saiba como utilizar um modelo ONNX exportado do Serviço de Visão Personalizada com o Windows ML (pré-visualização).

As informações neste documento mostram como utilizar um ficheiro ONNX exportado do Serviço de Visão Personalizada com o Windows ML. É fornecida uma aplicação UWP do Windows de exemplo. É incluído no exemplo um modelo preparado com capacidade de reconhecimento. São também fornecidos passos sobre como pode utilizar o seu próprio modelo com este exemplo.

> [!div class="checklist"]
> * Sobre a aplicação de exemplo
> * Obter o código de exemplo
> * Executar o exemplo
> * Utilizar o seu próprio modelo

## <a name="prerequisites"></a>Pré-requisitos

* Windows 10 versão 17738 ou superior

* Windows SDK para a versão 17738 ou superior

* O Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho de __programação da Plataforma Universal do Windows__ ativada.

* O modo de Programador ativado. Para obter mais informações, veja o documento [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Ativar o seu dispositivo para programação).

## <a name="about-the-example-app"></a>Sobre a aplicação de exemplo

A aplicação é uma aplicação UWP do Windows genérica. Permite-lhe selecionar uma imagem do computador, a qual é enviada em seguida para o modelo. As etiquetas e as pontuações devolvidas pelo modelo são apresentadas junto à imagem.

## <a name="get-the-example-code"></a>Obter o código de exemplo

A aplicação de exemplo está disponível em [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Executar o exemplo

1. Utilize a tecla `F5` para iniciar a aplicação a partir do Visual Studio. Poderá ser-lhe pedido para ativar o modo de Programador. Para obter mais informações, veja o documento [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Ativar o seu dispositivo para programação).

1. Quando a aplicação for iniciada, utilize o botão para selecionar uma imagem para pontuação.

## <a name="use-your-own-model"></a>Utilizar o seu próprio modelo

Para utilizar o seu próprio modelo, execute os seguintes passos:

1. [Criar e preparar](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) um classificador com o Serviço de Visão Personalizada. Para exportar o modelo, selecione um domínio __compacto__, como **Geral (compacto)**. Para exportar um classificador existente, converta o domínio a compactar ao selecionar o ícone de engrenagem na parte superior direita. Em __Definições__, escolha um modelo compacto, guarde e prepare o seu projeto.  

1. [Exporte o modelo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) através do separador Desempenho. Selecione uma iteração preparada com um domínio compacto e será apresentado um botão "Exportar". Selecione *Exportar*, *ONNX*, *ONNX1.2* e, em seguida *Exportar*. Assim que o ficheiro estiver pronto, selecione o botão *Transferir*.

1. Coloque o ficheiro ONNX na pasta __Assets__ (Recursos) do projeto. 

1. No Explorador de Soluções, clique com o botão direito do rato na pasta Assets (Recursos) e selecione __Adicionar Item Existente__. Selecione o ficheiro ONNX.

1. No Explorador de Soluções, selecione o ficheiro ONNX na pasta Assets (Recursos). Altere as propriedades seguintes do ficheiro:

    * __Ação de Compilação__ -> __Conteúdo__
    * __Copiar para Diretório de Saída__ -> __Copiar se for mais recente__

1. Altere a variável `_onnxFileNames` para o nome do ficheiro ONNX. Modifique também `ClassLabel` para o número de etiquetas que o modelo contém.

1. Compile e execute.

1. Clique no botão para selecionar a imagem a avaliar.

## <a name="next-steps"></a>Passos Seguintes

Para descobrir outras formas de exportar e utilizar um modelo de Visão Personalizada, veja os seguintes documentos:

* [Exportar o seu modelo para utilização com dispositivos móveis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Utilizar o modelo exportado do Tensorflow numa aplicação Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Utilizar o modelo exportado do CoreML numa aplicação Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Utilizar o modelo exportado do CoreML numa aplicação iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre como utilizar modelos ONNX com o Windows ML, veja o documento [Integrate a model into your app with Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) (Integrar um modelo na sua aplicação com o Windows ML).
