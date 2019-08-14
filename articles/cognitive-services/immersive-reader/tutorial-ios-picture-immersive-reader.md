---
title: 'Tutorial: Criar um aplicativo iOS que usa uma foto e a inicia no leitor de imersão (Swift)'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo iOS do zero e adicionará a imagem à funcionalidade de leitura de imersão.
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: d0e6cf05043c82faa04d530571ad7cacb3a02854
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991072"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutorial: Criar um aplicativo iOS que inicia o leitor de imersão com o conteúdo de uma foto (Swift)

O [leitor de imersão](https://www.onenote.com/learningtools) é uma ferramenta projetada de inclusivamente que implementa técnicas comprovadas para melhorar a compreensão da leitura.

O [Pesquisa Visual computacional API de leitura de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) detecta o conteúdo de texto em uma imagem usando os modelos de reconhecimento mais recentes da Microsoft e converte o texto identificado em um fluxo de caracteres legível por máquina.

Neste tutorial, você criará um aplicativo iOS do zero e integrará a API de leitura e o leitor de imersão usando o SDK do leitor de imersão. Um exemplo funcional completo deste tutorial está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [XCode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Um recurso de leitor de imersão configurado para autenticação do Azure Active Directory (AD do Azure). Siga [estas instruções](./azure-active-directory-authentication.md) para configurar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.
* O uso deste exemplo requer uma assinatura do Azure para o serviço Pesquisa Visual Computacional cognitiva. [Crie um pesquisa Visual computacional recurso de serviço cognitiva no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Crie um novo projeto no Xcode.

![Novo Projeto](./media/ios/xcode-create-project.png)

Escolha **aplicativo de modo de exibição único**.

![Novo aplicativo de exibição única](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obter o SDK do CocoaPod
A maneira mais fácil de usar o SDK do leitor de imersão é via CocoaPods. Para instalar via Cocoapods:
1. [Instalar o CocoaPods](http://guides.cocoapods.org/using/getting-started.html) -siga o guia de introdução para instalar o CocoaPods.
2. Crie um Podfile executando `pod init` no diretório raiz do projeto Xcode.
3.  Adicione o CocoaPod ao seu Podfile adicionando `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Seu Podfile deve ser semelhante ao seguinte, com o nome do seu destino substituindo Picture-to-imersiva-Reader-Swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. No terminal, no diretório do seu projeto do Xcode, execute o comando `pod install` para instalar o Pod do SDK do leitor de imersão.
5. Adicione `import immersive_reader_sdk` a todos os arquivos que precisam referenciar o SDK.
6. Certifique-se de abrir o projeto abrindo `.xcworkspace` o arquivo e não `.xcodeproj` o arquivo.

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Você precisa de alguns valores da etapa de pré-requisito de configuração de autenticação do Azure AD acima para esta parte. Consulte novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Na pasta principal do projeto, que contém o arquivo ViewController. Swift, crie um arquivo de classe Swift chamado Constants. Swift. Substitua a classe pelo código a seguir, adicionando em seus valores quando aplicável. Mantenha esse arquivo como um arquivo local que existe somente em seu computador e certifique-se de não confirmar esse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser tornados públicos. É recomendável que você não mantenha os segredos em seu aplicativo. Em vez disso, é recomendável usar um serviço de back-end para obter o token, onde os segredos podem ser mantidos fora do aplicativo e fora do dispositivo. O ponto de extremidade da API de back-end deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de leitor de imersão e cobrança; Esse trabalho está além do escopo deste tutorial.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurar o aplicativo para ser executado sem um storyboard

Abra AppDelegate. Swift e substitua o arquivo pelo código a seguir.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/AppDelegate.swift)]

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Adicionar funcionalidade para fazer e carregar fotos

Renomeie ViewController. Swift para PictureLaunchViewController. Swift e substitua o arquivo pelo código a seguir.

[!code-swift[PictureLaunchViewController](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/PictureLaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Defina o esquema de arquivo no Xcode selecionando um simulador ou um destino do dispositivo.
![Esquema de arquivo morto](./media/ios/xcode-archive-scheme.png)<br/>
![Selecionar destino](./media/ios/xcode-select-target.png)

No Xcode, pressione CTRL + R ou clique no botão reproduzir para executar o projeto e o aplicativo deverá ser iniciado no simulador ou dispositivo especificado.

Em seu aplicativo, você deve ver:

![Aplicativo de exemplo](./media/ios/picture-to-immersive-reader-ipad-app.png)

Dentro do aplicativo, faça ou carregue uma foto de texto pressionando o botão ' tirar foto ' ou ' escolher foto da biblioteca ' e o leitor de imersão será iniciado exibindo o texto da foto.

![Leitura Avançada](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Passos Seguintes

* Explorar o [SDK do IOS do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) e a [referência do SDK do IOS do leitor de imersão](./ios-reference.md)
