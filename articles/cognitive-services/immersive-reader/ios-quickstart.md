---
title: 'Início rápido: Criar um aplicativo iOS que inicie o leitor de imersão (Swift)'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo iOS do zero e adicionará a funcionalidade de leitura de imersão.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899537"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Início rápido: Criar um aplicativo iOS que inicie o leitor de imersão (Swift)

O [leitor de imersão](https://www.onenote.com/learningtools) é uma ferramenta projetada de inclusivamente que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste guia de início rápido, você criará um aplicativo iOS do zero e integrará o leitor de imersão usando o SDK do leitor de imersão. Um exemplo funcional completo deste guia de início rápido está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [XCode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Um recurso de leitor de imersão configurado para autenticação do Azure Active Directory (AD do Azure). Siga [estas instruções](./azure-active-directory-authentication.md) para configurar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Crie um novo projeto no Xcode.

![Novo Projeto](./media/ios/xcode-create-project.png)

Escolha **aplicativo de modo de exibição único**.

![Novo aplicativo de exibição única](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obter o SDK do CocoaPod
A maneira mais fácil de usar o SDK do leitor de imersão é via CocoaPods. Para instalar via Cocoapods:
1. [Instalar o Cocoapods](http://guides.cocoapods.org/using/getting-started.html) -siga o guia de introdução para instalar o Cocoapods.
2. Crie um Podfile executando `pod init` no diretório raiz do projeto Xcode.
3.  Adicione o CocoaPod ao seu Podfile adicionando `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Seu Pofile deve ser semelhante ao seguinte, com o nome do destino que substitui o início rápido-Swift:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
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

Na pasta principal do projeto, que contém o arquivo ViewController. Swift, crie um arquivo de classe Swift chamado Constants. Swift. Substitua a classe pelo código a seguir, adicionando em seus valores quando aplicável. Mantenha esse arquivo como um arquivo local que existe somente em seu computador e certifique-se de não confirmar esse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser tornados públicos. É recomendável que você não mantenha os segredos em seu aplicativo. Em vez disso, é recomendável usar um serviço de back-end para obter o token, onde os segredos podem ser mantidos fora do aplicativo e fora do dispositivo. O ponto de extremidade da API de back-end deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de leitor de imersão e cobrança; Esse trabalho está além do escopo deste guia de início rápido.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurar o aplicativo para ser executado sem um storyboard

Abra AppDelegate. Swift e substitua o arquivo pelo código a seguir.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Criar o controlador de exibição de inicialização e adicionar conteúdo de exemplo

Renomeie ViewController. Swift para LaunchViewController. Swift e substitua o arquivo pelo código a seguir.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Defina o esquema de arquivo no Xcode selecionando um simulador ou um destino do dispositivo.
![Esquema de arquivo morto](./media/ios/xcode-archive-scheme.png)<br/>
![Selecionar destino](./media/ios/xcode-select-target.png)

No Xcode, pressione CTRL + R ou clique no botão reproduzir para executar o projeto e o aplicativo deverá ser iniciado no simulador ou dispositivo especificado.

Em seu aplicativo, você deve ver:

![Aplicativo de exemplo](./media/ios/sample-app-ipad.png)

Ao clicar no botão "leitor de imersão", você verá o leitor de imersão iniciado com o conteúdo no aplicativo.

![Leitura Avançada](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Passos Seguintes

* Explorar o [SDK do IOS do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) e a [referência do SDK do IOS do leitor de imersão](./ios-reference.md)
