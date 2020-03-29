---
title: Autenticação intermediada no Android / Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697902"
---
# <a name="brokered-authentication-in-android"></a>Autenticação intermediada no Android

Deve utilizar um dos corretores de autenticação da Microsoft para participar no Single Sign-On (SSO) em todo o dispositivo e para cumprir as políticas de acesso condicional organizacional. Integrar-se com um corretor proporciona os seguintes benefícios:

- Inscrição única do dispositivo
- Acesso condicional para:
  - Intune App Protection
  - Registo do Dispositivo (Adesão ao Local de Trabalho)
  - Gestão de Dispositivos Móveis
- Gestão de Conta em todo o dispositivo
  -  através de Definições de conta do Gestor de Contas Android &
  - "Conta de Trabalho" - tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente que está incluído com a [Aplicação autenticadora microsoft](https://play.google.com/store/apps/details?id=com.azure.authenticator) e o [Portal da Empresa Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Apenas uma aplicação que acolhe o corretor estará ativa como corretor de cada vez. Qual aplicação é ativa como corretor é determinada por ordem de instalação no dispositivo. O primeiro a ser instalado, ou o último presente no dispositivo, torna-se o corretor ativo.

O diagrama que se segue ilustra a relação entre a sua aplicação, a Microsoft Authentication Library (MSAL) e os corretores de autenticação da Microsoft.

![Diagrama de implementação de corretor](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalação de apps que acolhem um corretor

As aplicações de hospedagem de corretores podem ser instaladas pelo proprietário do dispositivo a partir da sua loja de aplicações (normalmente Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) estão protegidas por Políticas de Acesso Condicional que exigem que os dispositivos sejam:

- Registado (local de trabalho aderiu) e/ou
- Matriculado em Gestão de Dispositivos ou
- Matriculado na Proteção de Aplicações Intune

Se um dispositivo ainda não tiver uma aplicação de corretagem instalada, a MSAL instrui o utilizador a instalar uma assim que a aplicação tente obter um sinal interativo. A aplicação terá então de levar o utilizador através das etapas para tornar o dispositivo conforme com a política exigida.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um corretor

### <a name="when-a-broker-is-installed"></a>Quando um corretor é instalado

Quando um corretor é instalado num dispositivo, todos os pedidos `acquireToken()`de fichainter interativa subsequentes (chamadas para ) são tratados pelo corretor e não localmente pela MSAL. Qualquer estado SSO anteriormente disponível para a MSAL não está disponível para o corretor. Como resultado, o utilizador terá de autenticar novamente ou selecionar uma conta da lista existente de contas conhecidas pelo dispositivo.

Instalar um corretor não requer que o utilizador volte a fazer o seu insto. Só quando o utilizador `MsalUiRequiredException` precisar de resolver um irá o próximo pedido ir para o corretor. `MsalUiRequiredException`é lançado por uma série de razões, e precisa ser resolvido interactivamente. Estas são algumas razões comuns:

- O utilizador alterou a palavra-passe associada à sua conta.
- A conta do utilizador já não cumpre uma política de Acesso Condicional.
- O utilizador revogou o seu consentimento para que a app fosse associada à sua conta.

### <a name="when-a-broker-is-uninstalled"></a>Quando um corretor é desinstalado

Se houver apenas uma aplicação de hospedagem de corretor instalada, e for removida, então o utilizador terá de iniciar sessão novamente. Desinstalar o corretor ativo remove a conta e fichas associadas do dispositivo.

Se o Portal da Empresa Intune estiver instalado e estiver a funcionar como corretor ativo, e o Microsoft Authenticator também estiver instalado, então se o Portal da Empresa Intune (corretor ativo) estiver desinstalado, o utilizador terá de voltar a iniciar o seu contrato. Uma vez que eles voltam a entrar, a aplicação Microsoft Authenticator torna-se o corretor ativo.

## <a name="integrating-with-a-broker"></a>Integrando-se com um corretor

### <a name="generating-a-redirect-uri-for-a-broker"></a>Gerar um URI redirecionado para um corretor

Deve registar um URI redirecionado que seja compatível com o corretor. O redirecionamento uri para o corretor precisa incluir o nome de pacote da sua aplicação, bem como a representação codificada base64 da assinatura da sua app.

O formato do URI redirecionado é:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Gere a sua assinatura codificada pelo url Base64 utilizando as chaves de assinatura da sua aplicação. Aqui estão alguns comandos de exemplo que usam as suas teclas de depuração:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Consulte [assine a sua aplicação](https://developer.android.com/studio/publish/app-signing) para obter informações sobre a assinatura da sua aplicação.

> [!IMPORTANT]
> Utilize a sua chave de assinatura de produção para a versão de produção da sua aplicação.

### <a name="configure-msal-to-use-a-broker"></a>Configure mSAL para usar um corretor

Para utilizar um corretor na sua aplicação, deve atestar que configurao o seu redirecionamento do corretor. Por exemplo, inclua tanto o seu corretor ativado URI -- e indique que o registou -- incluindo o seguinte no seu ficheiro de configuração MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> O novo UI de registo de aplicativos do portal Azure ajuda-o a gerar o redirecionamento uri do corretor. Se registou a sua aplicação utilizando a experiência mais antiga, ou o fez utilizando o portal de registo de aplicações da Microsoft, poderá ter de gerar o REdirect URI e atualizar manualmente a lista de URIs redirecionados no portal.

### <a name="broker-related-exceptions"></a>Exceções relacionadas com corretores

A MSAL comunica com o corretor de duas formas:

- Serviço vinculado a corretor
- Gestor de contas Android

A MSAL usa primeiro o serviço de corretor ligado porque ligar para este serviço não requer nenhuma permissão Android. Se a ligação ao serviço vinculado falhar, a MSAL utilizará o API do Gestor de Contas Android. A MSAL só o faz se `"READ_CONTACTS"` a sua aplicação já tiver recebido a permissão.

Se obtém `MsalClientException` um `"BROKER_BIND_FAILURE"`código de erro, então existem duas opções:

- Peça ao utilizador para desativar a otimização de energia para a aplicação Microsoft Authenticator e para o Portal da Empresa Intune.
- Peça ao utilizador `"READ_CONTACTS"` que conceda a permissão
