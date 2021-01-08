---
title: Como permitir o SSO cross-app no Android usando o MSAL Rio Azure
titleSuffix: Microsoft identity platform
description: Como utilizar a Microsoft Authentication Library (MSAL) para Android para permitir um único sinal de sação em todas as suas aplicações.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: c4c98ad377100c35b0c364607bfd3803d07a95a7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015934"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Como: Ativar sSO cross-app no Android usando o MSAL

O único sign-on (SSO) permite que os utilizadores só introduzam as suas credenciais uma vez e tenham essas credenciais automaticamente a trabalhar em todas as aplicações.

A [plataforma de identidade da Microsoft](./index.yml) e a Microsoft Authentication Library (MSAL) ajudam-no a ativar o SSO através do seu próprio conjunto de aplicações. Com a capacidade do corretor e aplicações Authenticator, pode estender SSO por todo o dispositivo.

Neste como fazer, aprenderá a configurar os SDKs utilizados pela sua aplicação para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Este como assumir que sabe como:

- Provisão da sua aplicação utilizando o portal Azure. Para mais informações sobre este tema, consulte as instruções para criar uma aplicação [no tutorial Android](./tutorial-v2-android.md#create-a-project)
- Integre a sua aplicação na [Biblioteca de Autenticação da Microsoft para Android.](https://github.com/AzureAD/microsoft-authentication-library-for-android)

## <a name="methods-for-single-sign-on"></a>Métodos para um único sinal

Existem duas formas de aplicações que usam o MSAL para Android alcançar o SSO:

* Através de uma [aplicação de corretor](#sso-through-brokered-authentication)
* Através do navegador do [sistema](#sso-through-system-browser)


   Recomenda-se a utilização de uma aplicação de corretor para benefícios como SSO em todo o dispositivo, gestão de conta e acesso condicional. No entanto, exige que os seus utilizadores descarreguem aplicações adicionais.

## <a name="sso-through-brokered-authentication"></a>SSO através da autenticação intermediada

Recomendamos que utilize um dos corretores de autenticação da Microsoft para participar em um único sign-on (SSO) em todo o dispositivo e para cumprir as políticas organizacionais de acesso condicional. A integração com um corretor proporciona os seguintes benefícios:

- Dispositivo único de inscrição
- Acesso condicional para:
  - Intune App Protection
  - Registo do dispositivo (Workplace Join)
  - Gestão de Dispositivos Móveis
- Gestão de conta em todo o dispositivo
  -  através de Android AccountManager & Definições de Conta
  - "Conta de Trabalho" - tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente que está incluído nas aplicações Microsoft [Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [Intune Company Portal.](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

O diagrama que se segue ilustra a relação entre a sua aplicação, a Microsoft Authentication Library (MSAL) e os corretores de autenticação da Microsoft.

![Diagrama mostrando como uma aplicação se relaciona com MSAL, aplicações de corretor e o gestor de conta Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalação de aplicativos que acolhem um corretor

As aplicações de alojamento de corretores podem ser instaladas pelo proprietário do dispositivo a partir da sua loja de aplicações (normalmente a Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) estão protegidas por Políticas de Acesso Condicional que exigem que os dispositivos sejam:

- Registado (local de trabalho aderido) e/ou
- Inscrito na Gestão de Dispositivos ou
- Inscrito na Intune App Protection

Se um dispositivo ainda não tiver uma aplicação de corretagem instalada, a MSAL instrui o utilizador a instalar uma assim que a aplicação tentar obter um símbolo interativamente. A aplicação terá então de liderar o utilizador através das etapas para tornar o dispositivo conforme com a política exigida.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um corretor

#### <a name="when-a-broker-is-installed"></a>Quando um corretor é instalado

Quando um corretor é instalado num dispositivo, todos os pedidos de fichas interativos subsequentes (chamadas `acquireToken()` para) são tratados pelo corretor e não localmente pela MSAL. Qualquer estado SSO anteriormente disponível para a MSAL não está disponível para o corretor. Como resultado, o utilizador terá de autenticar novamente ou selecionar uma conta a partir da lista de contas existentes conhecidas pelo dispositivo.

A instalação de um corretor não requer que o utilizador volte a iniciar sedutil. Só quando o utilizador precisar de resolver um `MsalUiRequiredException` será que o próximo pedido irá para o corretor. `MsalUiRequiredException` pode ser jogado por várias razões, e precisa ser resolvido interativamente. Por exemplo:

- O utilizador alterou a palavra-passe associada à sua conta.
- A conta do utilizador já não cumpre uma política de Acesso Condicional.
- O utilizador revogou o seu consentimento para que a app fosse associada à sua conta.

**Vários corretores** - Se vários corretores forem instalados num dispositivo, o corretor que foi instalado primeiro é sempre o corretor ativo. Apenas um único corretor pode estar ativo num dispositivo.

#### <a name="when-a-broker-is-uninstalled"></a>Quando um corretor é desinstalado

Se houver apenas uma aplicação de hospedagem de corretor instalada e removida, então o utilizador terá de iniciar novamente o sôm. Desinstalar o corretor ativo remove a conta e as fichas associadas do dispositivo.

Se o Portal da Empresa Intune estiver instalado e estiver a funcionar como corretor ativo, e o Microsoft Authenticator também estiver instalado, então se o Portal da Empresa Intune (corretor ativo) for desinstalado, o utilizador terá de iniciar novamente o súmis. Assim que voltarem a entrar, a aplicação Microsoft Authenticator torna-se o corretor ativo.

### <a name="integrating-with-a-broker"></a>Integração com um corretor

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Gere um URI de redirecionamento para um corretor

Tem de registar um URI de redirecionamento que seja compatível com o corretor. O URI de redirecionamento para o corretor deve incluir o nome do pacote da sua aplicação e a representação codificada base64 da assinatura da sua aplicação.

O formato do URI de redirecionamento é: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Pode utilizar [o keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) para gerar um hash de assinatura codificado base64 utilizando as teclas de assinatura da sua aplicação e, em seguida, usar o portal Azure para gerar o seu URI de redirecionamento usando esse haxixe.

Linux e macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Depois de gerar um hash de assinatura com *tecla,* use o portal Azure para gerar o URI de redirecionamento:

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a> e selecione a sua aplicação Android em **registos de Aplicações.**
1. Selecione **Autenticação**  >  **Adicione uma plataforma**  >  **Android**.
1. No **painel de configuração** da sua aplicação Android que abre, insira o **hash Signature** que gerou anteriormente e um nome **pacote.**
1. Selecione o **botão Configurar.**

O portal Azure gera o URI de redirecionamento para si e exibe-o no campo URI de **redirecionamento** de **configuração** Android.

Para obter mais informações sobre a assinatura da sua aplicação, consulte [a sua aplicação](https://developer.android.com/studio/publish/app-signing) no Android Studio User Guide.

> [!IMPORTANT]
> Use a sua chave de assinatura de produção para a versão de produção da sua aplicação.

#### <a name="configure-msal-to-use-a-broker"></a>Configure a MSAL para usar um corretor

Para utilizar um corretor na sua aplicação, tem de atestar que configuraste o seu redirecionamento de corretor. Por exemplo, inclua ambos os seus corretores ativados redirecionamento URI -- e indicar que o registou -- incluindo as seguintes definições no seu ficheiro de configuração MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Exceções relacionadas com corretores

A MSAL comunica com o corretor de duas formas:

- Serviço ligado a corretor
- Conta AndroidManager

A MSAL utiliza primeiro o serviço com ligação a corretores porque ligar para este serviço não requer permissões Android. Se a ligação ao serviço vinculado falhar, a MSAL utilizará a API do Android AccountManager. A MSAL só o faz se a sua aplicação já tiver recebido a `"READ_CONTACTS"` permissão.

Se tiver um `MsalClientException` código de `"BROKER_BIND_FAILURE"` erro, então existem duas opções:

- Peça ao utilizador para desativar a otimização de energia para a aplicação Microsoft Authenticator e para o Portal da Empresa Intune.
- Peça ao utilizador que conceda a `"READ_CONTACTS"` permissão

### <a name="verify-broker-integration"></a>Verificar a integração de corretores

Pode não ser imediatamente claro que a integração do corretor está a funcionar, mas pode usar os seguintes passos para verificar:

1. No seu dispositivo Android, preencha um pedido utilizando o corretor.
1. Nas definições do seu dispositivo Android, procure uma conta recém-criada correspondente à conta que autenticou. A conta deve ser do tipo *De Trabalho.*

Pode remover a conta das definições se quiser repetir o teste.

## <a name="sso-through-system-browser"></a>SSO através do navegador do sistema

As aplicações Android têm a opção de utilizar o WebView, o navegador do sistema ou os Separadores Personalizados do Chrome para a experiência do utilizador de autenticação. Se a aplicação não estiver a utilizar a autenticação intermediada, terá de utilizar o navegador do sistema em vez do webview nativo para obter SSO.

### <a name="authorization-agents"></a>Agentes de autorização

Escolher uma estratégia específica para os agentes de autorização é opcional e representa aplicações de funcionalidade adicionais que podem personalizar. A maioria das aplicações utilizará os padrão MSAL (ver [Consulte o ficheiro de configuração DO Android MSAL](msal-configuration.md) para ver as várias padrão).

A MSAL suporta a autorização utilizando um `WebView` , ou o navegador do sistema.  A imagem abaixo mostra como parece usando o `WebView` , ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de login da MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicações únicas de inscrição

Por predefinição, as aplicações integradas com o MSAL utilizam os Separadores Personalizados do navegador do sistema para autorizar. Ao contrário do WebViews, os Separadores Personalizados partilham um frasco de cookies com o navegador padrão do sistema, permitindo menos insusões com web ou outras aplicações nativas que se integraram com Separadores Personalizados.

Se a aplicação utilizar uma estratégia sem integrar o `WebView` suporte do Microsoft Authenticator ou do Portal da Empresa na sua aplicação, os utilizadores não terão uma única experiência de entrada no dispositivo ou entre aplicações nativas e aplicações web.

Se a aplicação utilizar o MSAL com um corretor como o Microsoft Authenticator ou o Portal da Empresa Intune, então os utilizadores podem ter uma experiência SSO em todas as aplicações se tiverem um s-in ativo com uma das aplicações.

### <a name="webview"></a>WebView

Para utilizar o WebView in-app, coloque a seguinte linha na configuração da aplicação JSON que é passada para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao utilizar a aplicação `WebView` in-app, o utilizador assina diretamente na aplicação. Os tokens são mantidos dentro da caixa de areia da app e não estão disponíveis fora do pote de cookies da aplicação. Como resultado, o utilizador não pode ter uma experiência SSO em todas as aplicações, a menos que as aplicações se integrem com o Autenticador ou Portal da Empresa.

No entanto, `WebView` fornece a capacidade de personalizar o visual e a sensação de UI de inscrição. Consulte [o Android WebViews](https://developer.android.com/reference/android/webkit/WebView) para saber mais sobre como fazer esta personalização.

### <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais separadores personalizados

Por padrão, o MSAL utiliza o navegador e uma estratégia [de separadores personalizados.](https://developer.chrome.com/multidevice/android/customtabs) Pode indicar explicitamente esta estratégia para evitar alterações em futuras versões `DEFAULT` utilizando a seguinte configuração JSON no ficheiro de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Utilize esta abordagem para proporcionar uma experiência SSO através do navegador do dispositivo. A MSAL utiliza um frasco de cookies partilhado, que permite que outras aplicações nativas ou aplicações web atinjam SSO no dispositivo utilizando o cookie de sessão de persistência definido pelo MSAL.

### <a name="browser-selection-heuristic"></a>Heurística de seleção de navegador

Como é impossível para a MSAL especificar o pacote de navegador exato para usar em cada um dos amplos conjuntos de telefones Android, o MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO de dispositivos cruzados.

A MSAL recupera principalmente o navegador predefinido do gestor do pacote e verifica se está numa lista testada de navegadores seguros. Caso contrário, o MSAL volta a usar o Webview em vez de lançar outro navegador não padrão a partir da lista de segurança. O navegador predefinido será escolhido independentemente de suportar separadores personalizados. Se o navegador suportar Separadores Personalizados, o MSAL lançará o Separador `WebView` Personalizado. Consulte [separadores personalizados no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver pacotes de navegador no dispositivo, o MSAL utiliza a aplicação `WebView` in-app . Se a definição por defeito do dispositivo não for alterada, o mesmo navegador deve ser lançado para cada sinal para garantir uma experiência SSO.

#### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver se redirecionam corretamente para o `"redirect_uri"` especificado no ficheiro de configuração:

| Dispositivo | Navegador Incorporado | Chrome | Ópera  | Microsoft Edge | Navegador UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passar | passar |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | passe<sup>1</sup> | passar | passar | passar | falhar |passar |
| Huawei (API 26) |passe<sup>2</sup> | passar | falhar | passar | passar |passar |
| Vivo (API 26) |passar|passar|passar|passar|passar|falhar|
| Pixel 2 (API 26) |passar | passar | passar | passar | falhar |passar |
| Rio Oppo | passar | não aplicável<sup>3</sup>|não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passar | passar | passar | passar | falhar |passar |
| Nexus (API 28) |passar | passar | passar | passar | falhar |passar |
|MI | passar | passar | passar | passar | falhar |passar |

<sup>1</sup> O navegador incorporado da Samsung é a Samsung Internet.<br/>
<sup>2</sup> O navegador incorporado da Huawei é o Navegador Huawei.<br/>
<sup>3</sup> O navegador predefinido não pode ser alterado dentro da definição do dispositivo Oppo.

## <a name="next-steps"></a>Próximos passos

[O modo de dispositivo partilhado para dispositivos Android](msal-android-shared-devices.md) permite configurar um dispositivo Android para que possa ser facilmente partilhado por vários colaboradores.