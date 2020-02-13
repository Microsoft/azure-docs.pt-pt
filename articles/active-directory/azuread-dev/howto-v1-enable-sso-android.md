---
title: Como permitir o SSO cross-app no Android utilizando o ADAL  Microsoft Docs
description: Como utilizar as funcionalidades do ADAL SDK para permitir um único sinal através das suas aplicações.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 37055d9b59d49091261109e3553f99bcc03d8e14
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164581"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como: Ativar o SSO cross-app no Android usando o ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O único sinal de inscrição (SSO) permite que os utilizadores introduzam apenas as suas credenciais uma vez e tenham essas credenciais a funcionar automaticamente através de aplicações e em plataformas que outras aplicações podem usar (como contas microsoft ou uma conta de trabalho do Microsoft 365) não importa o editor.

A plataforma de identidade da Microsoft, juntamente com os SDKs, facilita a ativação do SSO dentro do seu próprio conjunto de aplicações, ou com a capacidade de corretor e aplicações Authenticator, em todo o dispositivo.

Neste como- vai aprender a configurar o SDK dentro da sua aplicação para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Este como-assumir que você sabe como:

- Disponibilize a sua aplicação utilizando o portal legacy para o Azure Ative Directory (Azure AD). Para mais informações, consulte [Registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integre a sua aplicação com o [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Conceitos de inscrição simples

### <a name="identity-brokers"></a>Corretores de identidade

A Microsoft fornece aplicações para todas as plataformas móveis que permitem a ponte de credenciais através de aplicações de diferentes fornecedores e para funcionalidades melhoradas que requerem um único lugar seguro de onde validar credenciais. Estes são **chamados corretores.**

No iOS e Android, os corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere alguns, ou todos, dos dispositivos para os seus colaboradores. Os corretores suportam a gestão da segurança apenas para algumas aplicações ou todo o dispositivo com base na configuração de administração de TI. No Windows, esta funcionalidade é fornecida por um escolhidor de conta incorporado no sistema operativo, conhecido tecnicamente como o Corretor de Autenticação Web.

#### <a name="broker-assisted-login"></a>Login assistido corretor

Os logins assistidos pelo corretor são experiências de login que ocorrem dentro da aplicação de corretor e usam o armazenamento e segurança do corretor para partilhar credenciais em todas as aplicações do dispositivo que aplicam a plataforma de identidade. A implicação é que as suas aplicações dependerão do corretor para iniciar a inscrição dos utilizadores. No iOS e Android, estes corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou podem ser empurrados para o dispositivo por uma empresa que gere o dispositivo para o seu utilizador. Um exemplo deste tipo de aplicação é a aplicação Microsoft Authenticator no iOS. No Windows, esta funcionalidade é fornecida por um escolhidor de conta incorporado no sistema operativo, conhecido tecnicamente como o Corretor de Autenticação Web.
A experiência varia por plataforma e pode por vezes ser disruptiva para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com este padrão se tiver a aplicação do Facebook instalada e utilizar o Facebook Connect a partir de outra aplicação. A plataforma de identidade usa o mesmo padrão.

No Android, o escolhidor de conta é apresentado em cima da sua aplicação, o que é menos disruptivo para o utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor é invocado

Se um corretor compatível for instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs de identidade farão automaticamente o trabalho de invocar o corretor para si quando um utilizador indicar que pretende iniciar sessão utilizando qualquer conta a partir da plataforma de identidade.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante que a aplicação é válida

A necessidade de garantir a identidade de uma chamada de aplicação o corretor é crucial para a segurança fornecida em logins assistidos pelo corretor. O iOS e o Android não aplicam identificadores únicos que sejam válidos apenas para uma determinada aplicação, pelo que aplicações maliciosas podem "falsificar" o identificador de uma aplicação legítima e receber os tokens destinados à aplicação legítima. Para garantir que a Microsoft está sempre a comunicar com a aplicação certa no prazo de execução, o desenvolvedor é solicitado a fornecer um redirectURI personalizado ao registar a sua aplicação com a Microsoft. **Como os desenvolvedores devem criar este URI redirecionado é discutido em detalhe abaixo.** Este redirecionamento personalizado contém a impressão digital do certificado da aplicação e é garantido ser único na aplicação pela Google Play Store. Quando uma aplicação chama o corretor, o corretor pede ao sistema operativo Android que lhe forneça a impressão digital do certificado que chamou o corretor. O corretor fornece este certificado de impressão digital à Microsoft na chamada para o sistema de identidade. Se a impressão digital do certificado da aplicação não corresponder à impressão digital do certificado que nos foi fornecida pelo desenvolvedor durante o registo, o acesso é negado às fichas para o recurso que a aplicação está a solicitar. Este cheque garante que apenas a aplicação registada pelo programador recebe fichas.

Os logins brokered-SSO têm os seguintes benefícios:

* Experiências de utilizador SSO em todas as suas aplicações, independentemente do fornecedor.
* A sua aplicação pode utilizar funcionalidades de negócio mais avançadas, como acesso condicional e suporte a cenários Intune.
* A sua aplicação pode apoiar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de inscrição mais segura como a identidade da aplicação e o utilizador são verificados pela aplicação de corretagem com algoritmos de segurança adicionais e encriptação.

Aqui está uma representação de como os SDKs trabalham com as aplicações de corretor para permitir o SSO:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ligar sSO para corretor assistido SSO

A capacidade de uma aplicação utilizar qualquer corretor instalado no dispositivo é desligada por defeito. Para utilizar a sua aplicação com o corretor, tem de fazer alguma configuração adicional e adicionar algum código à sua aplicação.

Os passos a seguir são:

1. Ativar o modo de corretagem no código de aplicação está a ligar para o MS SDK
2. Estabeleça um novo redirecionamento URI e forneça isso tanto para a app como para o registo da sua aplicação
3. Configurar as permissões corretas no manifesto Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de corretagem na sua aplicação

A capacidade de utilização da sua aplicação é ligada quando cria as "definições" ou configuração inicial da sua instância de Autenticação. Para fazer isso na sua aplicação:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 2: Estabelecer um novo uri redirecionamento com o seu esquema de URL

De forma a garantir que a aplicação certa recebe os tokens credenciais devolvidos, é necessário garantir que a chamada volte à sua aplicação de forma a que o sistema operativo Android possa verificar. O sistema operativo Android utiliza o hash do certificado na loja Google Play. Este hash do certificado não pode ser falsificado por um pedido fraudulento. Juntamente com o URI da aplicação broker, a Microsoft garante que os tokens são devolvidos à aplicação correta. É necessário que seja registado um URI único de redirecionamento no pedido.

O seu uri redireccional deve estar na forma adequada de:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Pode registar este URI redirecionado na sua inscrição na aplicação através do [portal Azure](https://portal.azure.com/). Para obter mais informações sobre o registo da aplicação Azure AD, consulte [Integração com o Diretório Ativo Azure](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passo 3: Configurar as permissões corretas na sua aplicação

A aplicação de corretor no Android utiliza a funcionalidade Accounts Manager do Sistema Operativo Android para gerir credenciais através de aplicações. Para utilizar o corretor no Android, o manifesto da aplicação deve ter permissões para utilizar contas Do AccountManager. Estas permissões são discutidas detalhadamente na [documentação](https://developer.android.com/reference/android/accounts/AccountManager.html) do Google para o Gestor de Conta aqui

Em particular, estas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Configuraste o SSO!

Agora, a identidade SDK partilhará automaticamente as credenciais através das suas aplicações e invocará o corretor se estiver presente no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Conheça o [protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
