---
title: Quickstart - Adicionar chamadas a uma aplicação iOS usando os Serviços de Comunicação Azure
description: Neste arranque rápido, aprende-se a usar a biblioteca de clientes Azure Communication Services Call para iOS.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 36ec27f3a0e69126a91b52bed26dc645ec89e46e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656650"
---
Neste arranque rápido, você vai aprender a iniciar uma chamada usando a biblioteca de clientes Azure Communication Services Call para iOS.

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.8 da biblioteca do cliente chamador.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um [Token de Acesso ao Utilizador para](../../access-tokens.md) o seu Serviço de Comunicação Azure.

## <a name="setting-up"></a>Configuração

### <a name="creating-the-xcode-project"></a>Criar o projeto Xcode

No Xcode, crie um novo projeto iOS e selecione o modelo **de Aplicação Single View.** Este tutorial utiliza a [estrutura SwiftUI,](https://developer.apple.com/xcode/swiftui/)por isso deve definir o **Idioma** para **Swift** e a Interface de **Utilizador** para **SwiftUI**. Não vais criar testes durante este início rápido. Sinta-se livre para desmarcar **Inclua testes.**

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot mostrando a janela do Novo Projeto dentro do Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e dependências com cacau

1. Crie um Podfile para a sua aplicação, assim:

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Execute o `pod install`.
3. Abra o `.xcworkspace` com Xcode.

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para aceder ao microfone do dispositivo, é necessário atualizar a Lista de Propriedades de Informação da sua aplicação com um `NSMicrophoneUsageDescription` . Defina o valor associado a um `string` que será incluído no diálogo que o sistema utiliza para solicitar o acesso do utilizador.

Clique com o botão direito na `Info.plist` entrada da árvore do projeto e selecione Open **As**  >  **Source Code**. Adicione as seguintes linhas na secção de nível superior `<dict>` e, em seguida, guarde o ficheiro.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Abra o ficheiro **ContentView.swift** do seu projeto e adicione uma `import` declaração ao topo do ficheiro para importar o ficheiro `AzureCommunicationCalling library` . Além disso, `AVFoundation` importa, vamos precisar deste para pedido de permissão áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Substitua a implementação da `ContentView` estrutura por alguns controlos de UI simples que permitam ao utilizador iniciar e terminar uma chamada. Vamos anexar a lógica de negócios a estes controlos neste arranque rápido.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.|
| Callagent | O CallAgent é usado para iniciar e gerir chamadas. |
| ComunicadoTokenCredential | O CommunicationTokenCredential é usado como credencial simbólica para instantaneaizar o CallAgent.| 
| ComunicadoUserIdentifier | O CommunicationUserIdentifier é utilizado para representar a identidade do utilizador que pode ser uma das seguintes: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicialize um `CallAgent` exemplo com um Token de Acesso ao Utilizador que nos permitirá efetuar e receber chamadas. Adicione o seguinte código à `onAppear` chamada no **ContentView.swift**:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Tem de ser substituído `<USER ACCESS TOKEN>` por um token de acesso válido do utilizador para o seu recurso. Consulte a documentação do [token de acesso ao utilizador](../../access-tokens.md) se ainda não tiver um token disponível.

## <a name="start-a-call"></a>Inicie uma ligação

O `startCall` método é definido como a ação que será executada quando o botão *'Chamada de Início'* estiver premido. Atualize a implementação para iniciar uma chamada `ASACallAgent` com:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

Também pode utilizar as propriedades `StartCallOptions` para definir as opções iniciais para a chamada (isto é, permite iniciar a chamada com o microfone silenciado).

## <a name="end-a-call"></a>Termine uma chamada

Implemente o `endCall` método para terminar a chamada atual quando o botão *'Chamada' terminar* estiver premido.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

Pode construir e executar a sua aplicação no simulador iOS selecionando **Product**  >  **Run** ou utilizando o atalho de teclado (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Olhar final e sensação da app de arranque rápido":::

Pode fazer uma chamada VOIP de saída, fornecendo um ID do utilizador no campo de texto e tocando no botão **'Chamada'.** Ligar `8:echo123` liga-o a um robô echo, isto é ótimo para começar e verificar se os seus dispositivos de áudio estão a funcionar. 

> [!NOTE]
> A primeira vez que fizer uma chamada, o sistema irá pedir-lhe acesso ao microfone. Numa aplicação de produção, deverá utilizar a `AVAudioSession` API para [verificar o estado da permissão](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e atualizar graciosamente o comportamento da sua aplicação quando a permissão não for concedida.

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
