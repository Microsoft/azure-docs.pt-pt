---
title: 'Tutorial: Crie uma aplicação iOS que tire uma foto e a lança no Leitor Imersivo (Swift)'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá construir uma aplicação iOS de raiz e adicionar a Imagem à funcionalidade Imersiva do Leitor.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841851"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutorial: Criar uma aplicação iOS que lança o Leitor Imersivo com conteúdo a partir de uma foto (Swift)

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente projetada que implementa técnicas comprovadas para melhorar a compreensão da leitura.

O [Computer Vision Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) deteta conteúdo de texto numa imagem utilizando os mais recentes modelos de reconhecimento da Microsoft e converte o texto identificado num fluxo de caracteres legível por máquina.

Neste tutorial, irá construir uma aplicação iOS de raiz e integrar a API read, e o Leitor Imersivo utilizando o SDK de Leitor Imersivo. Uma amostra completa deste tutorial está disponível [aqui.](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Um recurso Imersivo do Leitor configurado para autenticação de Diretório Ativo Azure. Siga [estas instruções](./how-to-create-immersive-reader.md) para ser configurado. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de amostra. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* A utilização desta amostra requer uma subscrição azure ao Serviço Cognitivo da Visão Computacional. Crie um recurso do Serviço Cognitivo de [Visão Computacional no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Crie um novo projeto em Xcode.

![Novo Projeto](./media/ios/xcode-create-project.png)

Escolha **a aplicação de visão única**.

![Nova app de visão única](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obtenha o CocoaPod SDK
A maneira mais fácil de usar o SDK de leitor imersivo é através de CocoaPods. Para instalar via Cocoapods:
1. [Instale CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - Siga o guia de arranque para instalar Cocoapods.
2. Crie um Podfile executando `pod init` o diretório raiz do seu projeto Xcode.
3.  Adicione o CocoaPod ao seu `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`Podfile adicionando . O seu Podfile deve parecer o seguinte, com o nome do seu alvo a substituir o leitor-a-imersivo:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. No terminal, no diretório do seu projeto Xcode, execute o comando `pod install` para instalar o pod SDK Imersivo do Leitor.
5. Adicione `import immersive_reader_sdk` a todos os ficheiros que precisam de fazer referência ao SDK.
6. Certifique-se de abrir `.xcworkspace` o projeto `.xcodeproj` abrindo o ficheiro e não o ficheiro.

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um símbolo de autenticação Azure AD

Precisa de alguns valores da configuração de autenticação AD Azure, passo acima necessário para esta parte. Volte a consultar o ficheiro de texto que guardou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Na pasta principal do projeto, que contém o ficheiro ViewController.swift, crie um ficheiro de classe Swift chamado Constants.swift. Substitua a classe pelo seguinte código, adicionando os seus valores quando aplicável. Mantenha este ficheiro como um ficheiro local que só existe na sua máquina e certifique-se de não comprometer este ficheiro no controlo de origem, uma vez que contém segredos que não devem ser tornados públicos. Recomenda-se que não guarde segredos na sua aplicação. Em vez disso, recomendamos a utilização de um serviço backend para obter o símbolo, onde os segredos podem ser mantidos fora da app e fora do dispositivo. O ponto final da API deve ser protegido por detrás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor Imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurar a app para executar sem um storyboard

Abra appDelegate.swift e substitua o ficheiro pelo seguinte código.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Adicione funcionalidade para tirar e carregar fotos

Mude o nome viewController.swift para PictureLaunchViewController.swift e substitua o ficheiro pelo seguinte código.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Desloque o sistema de arquivo em Xcode selecionando um simulador ou alvo de dispositivo.
![Esquema de arquivo](./media/ios/xcode-archive-scheme.png)<br/>
![Selecionar o Destino](./media/ios/xcode-select-target.png)

No Xcode, prima Ctrl + R ou clique no botão de reprodução para executar o projeto e a aplicação deve ser lançada no simulador ou dispositivo especificado.

Na sua aplicação, deve ver:

![App de amostras](./media/ios/picture-to-immersive-reader-ipad-app.png)

Dentro da aplicação, tire ou carregue uma foto de texto premindo o botão 'Tirar foto' ou 'Escolha a Foto da Biblioteca' e o Leitor Imersivo lançará então exibindo o texto a partir da foto.

![Leitura Avançada](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
