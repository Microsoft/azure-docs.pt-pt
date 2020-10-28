---
title: 'Tutorial: Criar uma aplicação iOS que tira uma foto e a lança no Leitor Imersivo (Swift)'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá construir uma aplicação iOS de raiz e adicionar a imagem à funcionalidade Immersive Reader.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 73084f5116083b190b6fa31e504ffc5e8f6f39f6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636498"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutorial: Criar uma aplicação iOS que lança o Leitor Imersivo com conteúdo a partir de uma foto (Swift)

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente concebida que implementa técnicas comprovadas para melhorar a compreensão da leitura.

A [API de Serviços Cognitivos de Visão Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) deteta conteúdo de texto numa imagem utilizando os modelos de reconhecimento mais recentes da Microsoft e converte o texto identificado num fluxo de caracteres legível por máquina.

Neste tutorial, você vai construir uma aplicação iOS de raiz e integrar a API de leitura, e o Leitor Imersivo usando o Leitor Imersivo SDK. Uma amostra completa deste tutorial está disponível [aqui.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de amostra. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* A utilização desta amostra requer uma subscrição do Azure ao Serviço Cognitivo da Visão Computacional. [Criar um recurso de Serviço Cognitivo de Visão Computacional no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Criar um novo projeto no Xcode.

![Novo Projeto](./media/ios/xcode-create-project.png)

Escolha **a aplicação de vista única.**

![Nova aplicação single view](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obtenha o SDK CocoaPod
A maneira mais fácil de usar o SDK do leitor imersivo é através de Cacau. Para instalar através de Cacau:
1. [Instalar Cacau -](http://guides.cocoapods.org/using/getting-started.html) Siga o guia de arranque para instalar cacau.
2. Crie um Podfile executando `pod init` o diretório de raiz do seu projeto Xcode.
3.  Adicione o Cacau ao seu Podfile adicionando `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` . O seu Podfile deve parecer o seguinte, com o nome do seu alvo a substituir a imagem-a-imersiva leitor-rapidez:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. No terminal, no diretório do seu projeto Xcode, executar o comando `pod install` para instalar o pod SDK do leitor imersivo.
5. Adicione `import immersive_reader_sdk` a todos os ficheiros que necessitem de fazer referência ao SDK.
6. Certifique-se de abrir o projeto abrindo o `.xcworkspace` ficheiro e não o `.xcodeproj` ficheiro.

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação AD AZure

Necessita de alguns valores da configuração de autenticação AD Azure, antes de se aproximar desta parte. Consulte de volta o ficheiro de texto que guardou nessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Na pasta principal do projeto, que contém o ficheiro ViewController.swift, crie um ficheiro de classe Swift chamado Constants.swift. Substitua a classe pelo seguinte código, adicionando os seus valores sempre que aplicável. Guarde este ficheiro como um ficheiro local que só existe na sua máquina e certifique-se de que não compromete este ficheiro em controlo de origem, uma vez que contém segredos que não devem ser tornados públicos. Recomenda-se que não guarde segredos na sua aplicação. Em vez disso, recomendamos a utilização de um serviço de backend para obter o token, onde os segredos podem ser guardados fora da app e fora do dispositivo. O ponto final da API de backend deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor Imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Crie a app para funcionar sem um storyboard

Abrir AppDeegate.swift e substituir o ficheiro pelo seguinte código.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Adicione funcionalidade para tirar e carregar fotos

Renomear ViewController.swift para PictureLaunchViewController.swift e substituir o ficheiro pelo seguinte código.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Desaponte o esquema de arquivo no Xcode selecionando um simulador ou alvo do dispositivo.
![Esquema de arquivo](./media/ios/xcode-archive-scheme.png)<br/>
![Selecionar o Destino](./media/ios/xcode-select-target.png)

No Xcode, prima Ctrl + R ou clique no botão de reprodução para executar o projeto e a aplicação deve ser lançada no simulador ou dispositivo especificado.

Na sua aplicação, deve ver:

![Aplicativo de amostra](./media/ios/picture-to-immersive-reader-ipad-app.png)

Dentro da aplicação, tire ou carre fique com uma fotografia de texto premindo o botão 'Take Photo' ou 'Choose Photo from Library' e o Leitor Imersivo lançará então a exibição do texto a partir da fotografia.

![Leitura Avançada](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
