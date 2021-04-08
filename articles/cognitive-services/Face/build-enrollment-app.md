---
title: Construa um aplicativo de inscrição para Android com React
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o seu ambiente de desenvolvimento e implementar uma app de inscrição Face para obter o consentimento dos clientes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 218579176b807bbdae85646f27eaa7f301d4b9a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102428274"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Construa um aplicativo de inscrição para Android com React

Este guia irá mostrar-lhe como começar com a aplicação de inscrição facial da amostra. A aplicação demonstra as melhores práticas para obter um consentimento significativo para inscrever os utilizadores num serviço de reconhecimento facial e adquirir dados faciais de alta precisão. Um sistema integrado poderia utilizar uma aplicação de inscrição como esta para fornecer controlo de acesso sem toque, verificação de identidade, rastreio de assiduidade, quiosque de personalização ou verificação de identidade, com base nos seus dados faciais.

Quando lançada, a aplicação mostra aos utilizadores um ecrã de consentimento detalhado. Se o utilizador der o seu consentimento, a aplicação solicita um nome de utilizador e senha e, em seguida, captura uma imagem facial de alta qualidade usando a câmara do dispositivo.

A aplicação de inscrição da amostra é escrita usando o JavaScript e o quadro React Native. Atualmente pode ser implantado em dispositivos Android; mais opções de implantação estão vindo no futuro.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma subscrição Azure – [Crie uma gratuitamente.](https://azure.microsoft.com/free/cognitive-services/)  
* Assim que tiver a sua subscrição Azure, [crie um recurso Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) no portal Azure para obter a sua chave e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.  
  * Você precisará da chave e ponto final do recurso que criou para ligar a sua aplicação à Face API.  
  * Para o desenvolvimento local e testes, pode colar a chave API e o ponto final no ficheiro de configuração. Para a colocação final, guarde a chave API num local seguro e nunca no código.  

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API de Serviço Cognitivo. Não partilhem as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre da Chave Azure. Recomendamos também a regeneração regular destas teclas. Apenas uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira chave, pode utilizar a segunda chave para o acesso continuado ao serviço.

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Clone o repositório de git para a [aplicação de inscrição](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)da amostra .
1. Para configurar o seu ambiente de desenvolvimento, siga a <a href="https://reactnative.dev/docs/environment-setup"  title=" documentação react Native "  target="_blank"> documentação </a> Nativa. Selecione **React Native CLI Quickstart** como o seu sistema operativo de desenvolvimento e selecione **o Android** como o sistema operativo alvo. Complete as secções **Instalando dependências** e **ambiente de desenvolvimento Android.**
1. Abra a env.jsno ficheiro no seu editor de texto preferido, como [o Visual Studio Code,](https://code.visualstudio.com/)e adicione o seu ponto final e chave. Pode obter o seu ponto final e a sua chave no portal Azure no **separador Visão Geral** do seu recurso. Este passo é apenas para fins de teste local &mdash; não verifique a chave da API facial no seu repositório remoto.
1. Execute a aplicação utilizando o emulador de dispositivos virtuais Android do Android Studio ou o seu próprio dispositivo Android. Para testar a sua aplicação num dispositivo físico, siga a documentação nativa de <a href="https://reactnative.dev/docs/running-on-device"  title=" reagir "  target="_blank"> </a> relevante.  


## <a name="create-an-enrollment-experience"></a>Criar uma experiência de inscrição  

Agora que criou a aplicação de inscrição de amostras, pode adaptá-la às suas necessidades de experiência de inscrição.

Por exemplo, pode querer adicionar informações específicas da situação na sua página de consentimento:

> [!div class="mx-imgBorder"]
> ![página de consentimento de aplicativo](./media/enrollment-app/1-consent-1.jpg)

O serviço fornece verificações de qualidade de imagem para ajudá-lo a escolher se a imagem é de qualidade suficiente para inscrever o cliente ou tentar reconhecimento facial. Esta aplicação demonstra como aceder a quadros a partir da câmara do dispositivo, selecionar os quadros de maior qualidade e inscrever a face detetada no serviço API face. 

Muitos problemas de reconhecimento facial são causados por imagens de referência de baixa qualidade. Alguns fatores que podem degradar o desempenho do modelo são:
* Tamanho do rosto (rostos distantes da câmara)
* Orientação facial (rostos virados ou inclinados para longe da câmara)
* Más condições de iluminação (ou baixa luminosidade ou retroiluminação) onde a imagem pode estar mal exposta ou ter muito ruído
* Oclusão (rostos parcialmente ocultos ou obstruídos) incluindo acessórios como chapéus ou óculos de aro grosso)
* Borrão (por exemplo, por movimento de rosto rápido quando a fotografia foi tirada). 

> [!div class="mx-imgBorder"]
> ![página de instrução de captura de imagem de aplicativo](./media/enrollment-app/4-instruction.jpg)

Note que a aplicação também oferece funcionalidade para eliminar a inscrição do utilizador e a opção de reinscrevimento.

> [!div class="mx-imgBorder"]
> ![página de gestão de perfis](./media/enrollment-app/10-manage-2.jpg)

Para alargar a funcionalidade da aplicação para cobrir toda a experiência de inscrição, leia a [visão geral](enrollment-overview.md) para funcionalidades adicionais para implementar e as melhores práticas.

## <a name="deploy-the-enrollment-app"></a>Implementar a aplicação de inscrição

### <a name="android"></a>Android

Em primeiro lugar, certifique-se de que a sua aplicação está pronta para a implementação da produção: remova quaisquer chaves ou segredos do código da aplicação e certifique-se de que seguiu as [melhores práticas de segurança.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

Quando estiver pronto para lançar a sua app para produção, irá gerar um ficheiro APK pronto para lançamento, que é o formato de ficheiros pacote para aplicações Android. Este ficheiro APK deve ser assinado com uma chave privada. Com esta construção de lançamento, pode começar a distribuir a aplicação diretamente para os seus dispositivos. 

Siga o <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" Prepare-se para lançar "  target="_blank"> Prepare a documentação de lançamento </a> para aprender a gerar uma chave privada, assine a sua aplicação e gere uma versão APK de lançamento.  

Assim que tiver criado um APK assinado, consulte a Publicação da <a href="https://developer.android.com/studio/publish"  title=" sua app Publicar a "  target="_blank"> documentação da sua aplicação </a> para saber mais sobre como lançar a sua aplicação.

## <a name="next-steps"></a>Passos seguintes  

Neste guia, aprendeu a configurar o seu ambiente de desenvolvimento e a começar com a aplicação de inscrição da amostra. Se é novo em Reagir Nativo, pode ler os seus médicos para aprender mais informações sobre [antecedentes.](https://reactnative.dev/docs/getting-started) Também pode ser útil familiarizar-se com [a Face API.](Overview.md) Leia as outras secções sobre a documentação da aplicação de inscrição antes de começar a ser desenvolvimento.