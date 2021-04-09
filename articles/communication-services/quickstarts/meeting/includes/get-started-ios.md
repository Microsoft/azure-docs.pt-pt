---
title: Quickstart - Adicione uma reunião de equipas a uma aplicação iOS usando os Serviços de Comunicação Azure
description: Neste arranque rápido, você vai aprender a usar a biblioteca Azure Communication Services Teams Embed para iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104804073"
---
Neste arranque rápido, você vai aprender a se juntar a uma reunião de equipas usando a biblioteca Azure Communication Services Teams Embed para iOS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você precisará dos seguintes pré-requisitos:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um [Token de Acesso ao Utilizador para](../../access-tokens.md) o seu Serviço de Comunicação Azure.
- [Cacau](https://cocoapods.org/) instalado para o seu ambiente de construção.

## <a name="setting-up"></a>Configuração

### <a name="creating-the-xcode-project"></a>Criar o projeto Xcode

No Xcode, crie um novo projeto iOS e selecione o modelo **de App.** Vamos usar storyboards da UIKit. Não vais criar testes durante este arranque rápido. Sinta-se livre para desmarcar **Inclua testes.**

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Screenshot mostrando a seleção do modelo do novo projeto dentro do Xcode.":::

Diga o nome do `TeamsEmbedGettingStarted` projeto.

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Screenshot mostrando os detalhes do Novo Projeto dentro do Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e dependências com cacau

1. Crie um Podfile para a sua aplicação:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Execute `pod install`.
3. Abra o gerado `.xcworkspace` com Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Solicite acesso ao microfone, câmara, etc.

Para aceder ao hardware do dispositivo, atualize a Lista de Propriedades de Informação da sua aplicação. Defina o valor associado a um `string` que será incluído no diálogo que o sistema utiliza para solicitar o acesso do utilizador.

Clique com o botão direito na `Info.plist` entrada da árvore do projeto e selecione Open **As**  >  **Source Code**. Adicione as seguintes linhas na secção de nível superior `<dict>` e, em seguida, guarde o ficheiro.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Adicione o quadro de incorporação de equipas

1. Descarregue a estrutura.
2. Crie uma `Frameworks` pasta na raiz do projeto. Ex. `\TeamsEmbedGettingStarted\Frameworks\`
3. Copie os `TeamsAppSDK.framework` `MeetingUIClient.framework` quadros descarregados e as estruturas para esta pasta.
4. Adicione o `TeamsAppSDK.framework` e o alvo do projeto no `MeetingUIClient.framework` separador geral. Use o `Add Other`  ->  `Add Files...` para navegar nos ficheiros-quadro e adicioná-los.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Screenshot mostrando as estruturas adicionadas no Xcode.":::

5. Se ainda não estiver, adicione ao separador de `$(PROJECT_DIR)/Frameworks` `Framework Search Paths` definições de configuração de definições de projeto. Para encontrar a definição, altere o filtro `basic` de , também pode utilizar a barra de pesquisa à `all` direita.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Screenshot mostrando o caminho de pesquisa de estrutura em Xcode.":::

### <a name="turn-off-bitcode"></a>Desligue o Bitcode

Defina `Enable Bitcode` a opção para `No` as definições de construção do projeto. Para encontrar a definição, altere o filtro `basic` de , também pode utilizar a barra de pesquisa à `all` direita.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Screenshot mostrando a opção BitCode em Xcode.":::

### <a name="add-framework-signing-script"></a>Adicionar script de assinatura de quadro

Selecione o seu alvo de aplicação e escolha o `Build Phases` separador.  Em seguida, clique no `+` , seguido por `New Run Script Phase` . Certifique-se de que esta nova fase ocorre após as `Embed Frameworks` fases.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Screenshot mostrando a adição do script de construção em Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Ligue o modo de fundo Voice over IP.

Selecione o seu alvo de aplicação e clique no separador Capabilities.

Ligue `Background Modes` clicando `+ Capabilities` no topo e selecione `Background Modes` .

Selecione a caixa de verificação para `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Screenshot mostrando VOIP ativado em Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Adicione uma referência de janela ao AppDeegate

Abra o ficheiro **appDeegate .swift app do** seu projeto e adicione uma referência para 'janela'.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Adicione um botão ao ViewController

Crie um botão na `viewDidLoad` chamada no **ViewController.swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Crie uma tomada para o botão **no ViewController.swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Abra o ficheiro **ViewController.swift** do seu projeto e adicione uma `import` declaração ao topo do ficheiro para importar o e o `AzureCommunication library` `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Substitua a implementação da `ViewController` classe por um botão simples para permitir que o utilizador se junte a uma reunião. Vamos ligar a lógica de negócio ao botão neste arranque rápido.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca Azure Communication Services Teams Embed:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | O MeetingUIClient é o principal ponto de entrada para a biblioteca Teams Embed. |
| MeetingUIClientDeegate | O MeetingUIClientDeegate é usado para receber eventos, tais como alterações no estado de chamada. |
| Reuniões | Reuniões As Opções são utilizadas para opções configuráveis, como o nome do visor. | 
| Estado de Chamada | O CallState está habituado a reportar alterações do estado de chamada. As opções são as seguintes: conectar, esperar InLobby, conectado e terminado. |

## <a name="create-and-authenticate-the-client"></a>Criar e Autenticar o cliente

Inicialize um `MeetingUIClient` exemplo com um Token de Acesso ao Utilizador que nos permitirá participar em reuniões. Adicione o seguinte código à `viewDidLoad` chamada no **ViewController.swift:**

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Substitua `<USER_ACCESS_TOKEN>` por um símbolo de acesso ao utilizador válido para o seu recurso. Consulte a documentação do [token de acesso ao utilizador](../../access-tokens.md) se ainda não tiver um token disponível.

### <a name="setup-token-refreshing"></a>Configuração Token refrescante

Crie um método `fetchTokenAsync`. Em seguida, adicione a sua `fetchToken` lógica para obter o token do utilizador.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Substitua `<USER_ACCESS_TOKEN>` por um símbolo de acesso ao utilizador válido para o seu recurso.

## <a name="join-a-meeting"></a>Junte-se a uma reunião

O `joinMeeting` método é definido como a ação que será executada quando o botão *'Encontro de Reunião'* estiver premido. Atualizar a implementação para participar de uma reunião com o `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Substitua `<MEETING URL>` por um link de reunião de equipas.

### <a name="get-a-teams-meeting-link"></a>Obtenha um link de reunião de equipas

Um link de reunião de equipas pode ser recuperado usando APIs gráfico. Isto é detalhado na [documentação do Gráfico.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Os Serviços de Comunicação Que Ligam a SDK aceitam uma ligação completa de reuniões de equipas. Este link é devolvido como parte do `onlineMeeting` recurso, acessível sob a [ `joinWebUrl` propriedade](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Também pode obter as informações de reunião necessárias do URL de Encontro de **Adesão** na reunião das Equipas.

## <a name="run-the-code"></a>Executar o código

Pode construir e executar a sua aplicação no simulador iOS selecionando **Product**  >  **Run** ou utilizando o atalho de teclado (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Olhar final e sensação da app de arranque rápido":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Olhar final e sentir uma vez que o encontro foi acompanhado":::

> [!NOTE]
> A primeira vez que se juntar a uma reunião, o sistema irá pedir-lhe acesso ao microfone. Numa aplicação de produção, deverá utilizar a `AVAudioSession` API para [verificar o estado da permissão](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e atualizar graciosamente o comportamento da sua aplicação quando a permissão não for concedida.

## <a name="add-localization-support-based-on-your-app"></a>Adicione suporte de localização com base na sua app

O Microsoft Teams SDK suporta mais de 100 cordas e recursos. O pacote-quadro contém línguas base e inglesa. Os restantes estão incluídos no `Localizations.zip` ficheiro incluído no pacote.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Adicione localização ao SDK com base no que a sua aplicação suporta

1. Determine que tipo de localização a sua aplicação suporta a partir da app Xcode Project > Info > Lista de Localizaçãos
2. Desaperte o Localizations.zip incluído com o pacote
3. Copie as pastas de localização da pasta desapertada com base no que a sua aplicação suporta até à raiz do teamsAppSDK.framework

## <a name="preparation-for-app-store-upload"></a>Preparação para upload da App Store

Remova as arquiteturas i386 e x86_64 das estruturas em caso de arquivamento.

Adicione o `i386` script e `x86_64` as arquiteturas removendo o script às Fases de Construção antes da fase de conceção de código-quadro, se quiser arquivar a sua aplicação.

No Projeto Navigator, selecione o seu projeto. No painel de editores, vá para construir fases → Clique em + assinar → Criar uma nova fase de script de execução.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
