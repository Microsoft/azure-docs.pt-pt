---
title: Como habilitar o SSO entre aplicativos no Android usando a ADAL | Microsoft Docs
description: Como usar os recursos do SDK do ADAL para habilitar o logon único em seus aplicativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4d247c569cdc0beff499cee191b95711a603e42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917561"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como habilitar o SSO entre aplicativos no Android usando a ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

O SSO (logon único) permite que os usuários insiram suas credenciais apenas uma vez e essas credenciais funcionem automaticamente entre aplicativos e entre plataformas que outros aplicativos possam usar (como contas da Microsoft ou uma conta corporativa de Microsoft 365) não importa o editor.

A plataforma de identidade da Microsoft, junto com os SDKs, facilita a habilitação do SSO em seu próprio pacote de aplicativos ou com a capacidade do agente e os aplicativos de autenticador, em todo o dispositivo.

Neste "como", você aprenderá a configurar o SDK em seu aplicativo para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Este "como" pressupõe que você saiba como:

- Provisione seu aplicativo usando o portal herdado para Azure Active Directory (Azure AD). Para obter mais informações, consulte [registrar um aplicativo](quickstart-register-app.md)
- Integre seu aplicativo com o [SDK do Android do Azure ad](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Conceitos de logon único

### <a name="identity-brokers"></a>Agentes de identidade

A Microsoft fornece aplicativos para cada plataforma móvel que permitem a ponte de credenciais entre aplicativos de diferentes fornecedores e recursos aprimorados que exigem um único local seguro de onde validar as credenciais. Esses são chamados de **agentes**.

No iOS e no Android, os agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou enviada por push para o dispositivo por uma empresa que gerencia alguns ou todos os dispositivos de seus funcionários. Os agentes dão suporte ao gerenciamento de segurança apenas para alguns aplicativos ou para todo o dispositivo com base na configuração do administrador de ti. No Windows, essa funcionalidade é fornecida por um seletor de conta interno ao sistema operacional, conhecido tecnicamente como o agente de autenticação da Web.

#### <a name="broker-assisted-login"></a>Logon assistido do agente

Os logons assistidos por agente são experiências de logon que ocorrem dentro do aplicativo agente e usam o armazenamento e a segurança do agente para compartilhar credenciais em todos os aplicativos no dispositivo que aplicam a plataforma de identidade. A implicação de seus aplicativos dependerá do agente para conectar os usuários. No iOS e no Android, esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push para o dispositivo por uma empresa que gerencia o dispositivo para o usuário. Um exemplo desse tipo de aplicativo é o aplicativo Microsoft Authenticator no iOS. No Windows, essa funcionalidade é fornecida por um seletor de conta interno ao sistema operacional, conhecido tecnicamente como o agente de autenticação da Web.
A experiência varia de acordo com a plataforma e, às vezes, pode sofrer interrupções para os usuários, caso não sejam gerenciados corretamente. Provavelmente você está mais familiarizado com esse padrão se tiver o aplicativo do Facebook instalado e usar o Facebook Connect de outro aplicativo. A plataforma de identidade usa o mesmo padrão.

No Android, o seletor de conta é exibido na parte superior do aplicativo, o que é menos perturbador para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o agente é invocado

Se um agente compatível estiver instalado no dispositivo, como o aplicativo Microsoft Authenticator, os SDKs de identidade farão automaticamente o trabalho de invocar o agente para você quando um usuário indicar que deseja fazer logon usando qualquer conta da plataforma de identidade.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante que o aplicativo é válido

A necessidade de garantir que a identidade de um aplicativo chame o agente seja crucial para a segurança fornecida nos logons assistidos do agente. o iOS e o Android não impõem identificadores exclusivos que são válidos somente para um determinado aplicativo, de modo que aplicativos mal-intencionados podem "falsificar" um identificador de aplicativo legítimo e receber os tokens destinados ao aplicativo legítimo. Para garantir que a Microsoft esteja sempre se comunicando com o aplicativo certo em tempo de execução, o desenvolvedor será solicitado a fornecer um redirectURI personalizado ao registrar seu aplicativo na Microsoft. **Como os desenvolvedores devem criar esse URI de redirecionamento é discutido em detalhes abaixo.** Este redirectURI personalizado contém a impressão digital do certificado do aplicativo e tem a certeza de ser exclusivo para o aplicativo pelo Google Play Store. Quando um aplicativo chama o agente, o agente solicita que o sistema operacional Android forneça a impressão digital do certificado que chamou o agente. O agente fornece essa impressão digital do certificado à Microsoft na chamada para o sistema de identidade. Se a impressão digital do certificado do aplicativo não corresponder à impressão digital do certificado fornecida a nós pelo desenvolvedor durante o registro, o acesso será negado aos tokens para o recurso que o aplicativo está solicitando. Essa verificação garante que apenas o aplicativo registrado pelo desenvolvedor receba tokens.

Os logons de SSO com agente têm os seguintes benefícios:

* O usuário experimenta o SSO em todos os seus aplicativos, independentemente do fornecedor.
* Seu aplicativo pode usar recursos de negócios mais avançados, como o acesso condicional e o suporte a cenários do Intune.
* Seu aplicativo pode dar suporte à autenticação baseada em certificado para usuários empresariais.
* Experiência de entrada mais segura como a identidade do aplicativo e o usuário são verificados pelo aplicativo agente com algoritmos de segurança e criptografia adicionais.

Aqui está uma representação de como os SDKs funcionam com os aplicativos do agente para habilitar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativando o SSO para SSO assistido por agente

A capacidade de um aplicativo de usar qualquer agente instalado no dispositivo é desativada por padrão. Para usar o aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

As etapas a serem seguidas são:

1. Habilitar o modo de agente na chamada do código do aplicativo para o SDK do MS
2. Estabelecer um novo URI de redirecionamento e fornecê-lo ao aplicativo e ao registro do aplicativo
3. Configurando as permissões corretas no manifesto do Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: habilitar o modo de agente em seu aplicativo

A capacidade de seu aplicativo de usar o agente é ativada quando você cria as "configurações" ou a configuração inicial da sua instância de autenticação. Para fazer isso em seu aplicativo:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Etapa 2: estabelecer um novo URI de redirecionamento com seu esquema de URL

Para garantir que o aplicativo correto receba os tokens de credencial retornados, é necessário certificar-se de que o retorno de chamada para seu aplicativo de forma que o sistema operacional Android possa verificar. O sistema operacional Android usa o hash do certificado no repositório de Google Play. Esse hash do certificado não pode ser falsificado por um aplicativo não autorizado. Junto com o URI do aplicativo agente, a Microsoft garante que os tokens sejam retornados para o aplicativo correto. Um URI de redirecionamento exclusivo precisa ser registrado no aplicativo.

O URI de redirecionamento deve estar na forma apropriada de:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Você pode registrar esse URI de redirecionamento em seu registro de aplicativo usando o [portal do Azure](https://portal.azure.com/). Para obter mais informações sobre o registro de aplicativo do Azure AD, consulte [integrando com Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Etapa 3: configurar as permissões corretas em seu aplicativo

O aplicativo agente no Android usa o recurso Gerenciador de contas do sistema operacional Android para gerenciar credenciais entre aplicativos. Para usar o agente no Android, seu manifesto de aplicativo deve ter permissões para usar contas do accountmanager. Essas permissões são discutidas detalhadamente na [documentação do Google para o gerente de contas aqui](https://developer.android.com/reference/android/accounts/AccountManager.html)

Em particular, essas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Você configurou o SSO!

Agora, o SDK de identidade compartilhará automaticamente as credenciais em seus aplicativos e invocará o agente se ele estiver presente em seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [protocolo SAML de logon único](single-sign-on-saml-protocol.md)
