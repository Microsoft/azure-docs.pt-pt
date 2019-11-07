---
title: Exportar seu modelo para celular-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como exportar seu modelo para uso na criação de aplicativos móveis ou execução local para classificação em tempo real.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718945"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportar seu modelo para uso com dispositivos móveis

Serviço de Visão Personalizada permite que os classificadores sejam exportados para serem executados offline. Você pode inserir o classificador exportado em um aplicativo e executá-lo localmente em um dispositivo para classificação em tempo real.

## <a name="export-options"></a>Opções de exportação

O Serviço de Visão Personalizada dá suporte às seguintes exportações:

* __Tensorflow__ para __Android__.
* __Do coreml__ para __iOS11__.
* __ONNX__ para o __Windows ml__.
* __[Kit de desenvolvedor de ia de visão](https://azure.github.io/Vision-AI-DevKit-Pages/)__ .
* Um __contêiner do Docker__ para arquitetura Windows, Linux ou ARM. O contêiner inclui um modelo Tensorflow e um código de serviço para usar a API Visão Personalizada.

> [!IMPORTANT]
> Serviço de Visão Personalizada apenas exporta domínios __compacta__ . Os modelos gerados por domínios de compactação são otimizados para as restrições de classificação em tempo real em dispositivos móveis. Os classificadores criados com um domínio compacto podem ser um pouco menos precisos do que um domínio padrão com a mesma quantidade de dados de treinamento.
>
> Para obter informações sobre como melhorar seus classificadores, consulte o documento [melhorando o classificador](getting-started-improving-your-classifier.md) .

## <a name="convert-to-a-compact-domain"></a>Converter em um domínio compacto

> [!NOTE]
> As etapas nesta seção se aplicam somente se você tiver um modelo existente que não esteja definido para o domínio compacto.

Para converter o domínio de um modelo existente, execute as seguintes etapas:

1. No [site da visão personalizada](https://customvision.ai), selecione o ícone __página inicial__ para exibir uma lista de seus projetos.

    ![Imagem do ícone de página inicial e da lista de projetos](./media/export-your-model/projects-list.png)

1. Selecione um projeto e, em seguida, selecione o ícone de __engrenagem__ no canto superior direito da página.

    ![Imagem do ícone de engrenagem](./media/export-your-model/gear-icon.png)

1. Na seção __domínios__ , selecione um dos domínios do __Compact__ . Selecione __salvar alterações__ para salvar as alterações. 

    > [!NOTE]
    > Para o kit de desenvolvimento de ia de visão, o projeto deve ser criado com o domínio __geral (compacto)__ e você deve especificar a opção **visão de kit de desenvolvimento de ia** na seção recursos de **exportação** .

    ![Imagem da seleção de domínios](./media/export-your-model/domains.png)

1. Na parte superior da página, selecione __treinar__ para treinar novamente usando o novo domínio.

## <a name="export-your-model"></a>Exportar seu modelo

Para exportar o modelo após o novo treinamento, use as seguintes etapas:

1. Vá para a guia **desempenho** e selecione __Exportar__. 

    ![Imagem do ícone de exportação](./media/export-your-model/export.png)

    > [!TIP]
    > Se a entrada de __exportação__ não estiver disponível, a iteração selecionada não usará um domínio compacto. Use a seção __iterações__ desta página para selecionar uma iteração que usa um domínio compacto e, em seguida, selecione __Exportar__.

1. Selecione o formato de exportação desejado e, em seguida, selecione __Exportar__ para baixar o modelo.

## <a name="next-steps"></a>Passos seguintes

Integre seu modelo exportado a um aplicativo explorando um dos seguintes artigos ou exemplos:

* [Usar seu modelo Tensorflow com Python](export-model-python.md)
* [Usar seu modelo ONNX com o Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Consulte o exemplo para o [modelo do coreml em um aplicativo IOS](https://go.microsoft.com/fwlink/?linkid=857726) para classificação de imagem em tempo real com Swift.
* Consulte o exemplo para o [modelo Tensorflow em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para classificação de imagem em tempo real no Android.
* Consulte o exemplo de [modelo do coreml com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para classificação de imagem em tempo real em um aplicativo Xamarin Ios.
