---
title: Autenticação intermediada no Android Rio Azure
titlesuffix: Microsoft identity platform
description: Uma visão geral da autenticação intermediada & autorização para Android na plataforma de identidade da Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76697902"
---
# <a name="brokered-authentication-in-android"></a>Autenticação intermediada no Android

Deve utilizar um dos corretores de autenticação da Microsoft para participar em Sign-On Único (SSO) em todo o dispositivo e para cumprir as políticas organizacionais de acesso condicional. A integração com um corretor proporciona os seguintes benefícios:

- Dispositivo único de inscrição
- Acesso condicional para:
  - Intune App Protection
  - Registo do dispositivo (Workplace Join)
  - Gestão de Dispositivos Móveis
- Gestão de conta em todo o dispositivo
  -  através de Android AccountManager & Definições de Conta
  - "Conta de Trabalho" - tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente que está incluído na [Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [no Portal da Empresa Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Apenas uma aplicação que hospeda o corretor estará ativa como corretor de cada vez. Qual aplicação está ativa como corretor é determinada por ordem de instalação no dispositivo. O primeiro a ser instalado, ou o último presente no dispositivo, torna-se o corretor ativo.

O diagrama que se segue ilustra a relação entre a sua aplicação, a Microsoft Authentication Library (MSAL) e os corretores de autenticação da Microsoft.

![Diagrama de implementação de corretor](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalação de aplicativos que acolhem um corretor

As aplicações de alojamento de corretores podem ser instaladas pelo proprietário do dispositivo a partir da sua loja de aplicações (normalmente a Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) estão protegidas por Políticas de Acesso Condicional que exigem que os dispositivos sejam:

- Registado (local de trabalho aderido) e/ou
- Inscrito na Gestão de Dispositivos ou
- Inscrito na Intune App Protection

Se um dispositivo ainda não tiver uma aplicação de corretagem instalada, a MSAL instrui o utilizador a instalar uma assim que a aplicação tentar obter um símbolo interativamente. A aplicação terá então de liderar o utilizador através das etapas para tornar o dispositivo conforme com a política exigida.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um corretor

### <a name="when-a-broker-is-installed"></a>Quando um corretor é instalado

Quando um corretor é instalado num dispositivo, todos os pedidos de fichas interativos subsequentes (chamadas `acquireToken()` para) são tratados pelo corretor e não localmente pela MSAL. Qualquer estado SSO anteriormente disponível para a MSAL não está disponível para o corretor. Como resultado, o utilizador terá de autenticar novamente ou selecionar uma conta a partir da lista de contas existentes conhecidas pelo dispositivo.

A instalação de um corretor não requer que o utilizador volte a iniciar sedu. Só quando o utilizador precisar de resolver um `MsalUiRequiredException` será que o próximo pedido irá para o corretor. `MsalUiRequiredException`é jogado por uma série de razões, e precisa ser resolvido interativamente. Estas são algumas razões comuns:

- O utilizador alterou a palavra-passe associada à sua conta.
- A conta do utilizador já não cumpre uma política de Acesso Condicional.
- O utilizador revogou o seu consentimento para que a app fosse associada à sua conta.

### <a name="when-a-broker-is-uninstalled"></a>Quando um corretor é desinstalado

Se houver apenas uma aplicação de hospedagem de corretor instalada e removida, então o utilizador terá de iniciar novamente o sôm. Desinstalar o corretor ativo remove a conta e as fichas associadas do dispositivo.

Se o Portal da Empresa Intune estiver instalado e estiver a funcionar como corretor ativo, e o Microsoft Authenticator também estiver instalado, então se o Portal da Empresa Intune (corretor ativo) for desinstalado, o utilizador terá de iniciar novamente o súmis. Assim que voltarem a entrar, a aplicação Microsoft Authenticator torna-se o corretor ativo.

## <a name="integrating-with-a-broker"></a>Integração com um corretor

### <a name="generating-a-redirect-uri-for-a-broker"></a>Gerando um URI de redirecionamento para um corretor

Tem de registar um URI de redirecionamento que seja compatível com o corretor. O URI de redirecionamento para o corretor precisa de incluir o nome do pacote da sua aplicação, bem como a representação codificada base64 da assinatura da sua aplicação.

O formato do URI de redirecionamento é:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Gere a sua assinatura codificada de url Base64 utilizando as chaves de assinatura da sua aplicação. Aqui estão alguns comandos de exemplo que usam as suas chaves de sinalização de depuração:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Consulte [a sua aplicação](https://developer.android.com/studio/publish/app-signing) para obter informações sobre a assinatura da sua aplicação.

> [!IMPORTANT]
> Use a sua chave de assinatura de produção para a versão de produção da sua aplicação.

### <a name="configure-msal-to-use-a-broker"></a>Configure a MSAL para usar um corretor

Para utilizar um corretor na sua aplicação, tem de atestar que configuraste o seu redirecionamento de corretor. Por exemplo, inclua ambos os seus corretores ativados redirecionamento URI -- e indicar que o registou -- incluindo o seguinte no seu ficheiro de configuração MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> O novo registo de aplicações do portal Azure ajuda-o a gerar o corretor redirecionando o URI. Se registou a sua aplicação utilizando a experiência mais antiga, ou o fez usando o portal de registo de aplicações da Microsoft, poderá ter de gerar o URI de redirecionamento e atualizar manualmente a lista de URIs de redirecionamento no portal.

### <a name="broker-related-exceptions"></a>Exceções relacionadas com corretores

A MSAL comunica com o corretor de duas formas:

- Serviço ligado a corretor
- Conta AndroidManager

A MSAL utiliza primeiro o serviço de corretagem porque ligar para este serviço não requer permissões Android. Se a ligação ao serviço vinculado falhar, a MSAL utilizará a API do Android AccountManager. A MSAL só o faz se a sua aplicação já tiver recebido a `"READ_CONTACTS"` permissão.

Se tiver um `MsalClientException` código de `"BROKER_BIND_FAILURE"` erro, então existem duas opções:

- Peça ao utilizador para desativar a otimização de energia para a aplicação Microsoft Authenticator e para o Portal da Empresa Intune.
- Peça ao utilizador que conceda a `"READ_CONTACTS"` permissão
