---
title: Como permitir o SSO cross-app no Android usando a ADAL Microsoft Docs
description: Como utilizar as funcionalidades do ADAL SDK para permitir um único sinal de s-on em todas as suas aplicações.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87313596"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como: Ativar sSO cross-app no Android usando ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O único sinform (SSO) permite que os utilizadores introduzam as suas credenciais apenas uma vez e que essas credenciais funcionem automaticamente através de aplicações e plataformas que outras aplicações podem usar (como as Contas microsoft ou uma conta de trabalho da Microsoft 365) independentemente do editor.

A plataforma de identidade da Microsoft, juntamente com os SDKs, facilita a ativação do SSO dentro do seu próprio conjunto de aplicações, ou com a capacidade de corretor e aplicações Authenticator, em todo o dispositivo.

Neste como fazer, aprenderá a configurar o SDK dentro da sua aplicação para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Este como-assumir que sabe como:

- Forcam a sua aplicação utilizando o portal legacy para O Diretório Ativo Azure (Azure AD). Para mais informações, consulte [Registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integre a sua aplicação com o [Azure AD Android SDK.](https://github.com/AzureAD/azure-activedirectory-library-for-android)

## <a name="single-sign-on-concepts"></a>Conceitos de início de sessão único

### <a name="identity-brokers"></a>Corretores de identidade

A Microsoft fornece aplicações para todas as plataformas móveis que permitem a ponte de credenciais através de aplicações de diferentes fornecedores e para funcionalidades melhoradas que requerem um único lugar seguro de onde validar credenciais. Estes são **chamados corretores.**

No iOS e Android, os corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere alguns, ou todos, dos dispositivos para os seus colaboradores. Os corretores suportam a gestão da segurança apenas para algumas aplicações ou todo o dispositivo com base na configuração de administração de TI. No Windows, esta funcionalidade é fornecida por um chooser de conta incorporado no sistema operativo, conhecido tecnicamente como O Corretor de Autenticação Web.

#### <a name="broker-assisted-login"></a>Login assistido por corretor

Logins assistidos por corretor são experiências de login que ocorrem dentro da aplicação do corretor e usam o armazenamento e segurança do corretor para partilhar credenciais em todas as aplicações no dispositivo que aplicam a plataforma de identidade. A implicação de que as suas aplicações dependerão do corretor para iniciar seduções. No iOS e Android, estes corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou podem ser empurrados para o dispositivo por uma empresa que gere o dispositivo para o seu utilizador. Um exemplo deste tipo de aplicação é a aplicação Microsoft Authenticator no iOS. No Windows, esta funcionalidade é fornecida por um chooser de conta incorporado no sistema operativo, conhecido tecnicamente como O Corretor de Autenticação Web.
A experiência varia de acordo com a plataforma e pode por vezes ser disruptiva para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com este padrão se tiver a aplicação do Facebook instalada e utilizar o Facebook Connect a partir de outra aplicação. A plataforma de identidade usa o mesmo padrão.

No Android, o escolha da conta é apresentado em cima da sua aplicação, o que é menos disruptivo para o utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor é invocado

Se um corretor compatível for instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs de identidade farão automaticamente o trabalho de invocar o corretor para si quando um utilizador indicar que pretende iniciar sessão utilizando qualquer conta a partir da plataforma de identidade.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante que a aplicação é válida

A necessidade de garantir a identidade de uma aplicação que chame o corretor é crucial para a segurança fornecida em logins assistidos por corretor. O iOS e o Android não aplicam identificadores exclusivos que sejam válidos apenas para uma determinada aplicação, pelo que aplicações maliciosas podem "falsificar" o identificador de uma aplicação legítima e receber os tokens destinados à aplicação legítima. Para garantir que a Microsoft está sempre a comunicar com a aplicação certa no tempo de execução, o desenvolvedor é solicitado a fornecer um reorientador personalizadoURI ao registar a sua aplicação com a Microsoft. **Como os desenvolvedores devem criar este URI redirecionado é discutido em detalhe abaixo.** Este reorientador personalizadoURI contém a impressão digital de certificado da aplicação e é garantido ser exclusivo da aplicação pela Google Play Store. Quando uma aplicação chama o corretor, o corretor pede ao sistema operativo Android que lhe forneça a impressão digital de certificado que chamou o corretor. O corretor fornece esta impressão digital de certificado à Microsoft na chamada para o sistema de identidade. Se a impressão digital do certificado do pedido não corresponder à impressão digital do certificado que nos foi fornecida pelo desenvolvedor durante o registo, o acesso é negado aos tokens para o recurso que o pedido está solicitando. Esta verificação garante que apenas a aplicação registada pelo desenvolvedor recebe fichas.

Os logins intermediados-SSO têm os seguintes benefícios:

* O utilizador experimenta sSO em todas as suas aplicações independentemente do fornecedor.
* A sua aplicação pode utilizar funcionalidades de negócio mais avançadas, como o Acesso Condicionado e apoiar cenários Intune.
* A sua aplicação pode suportar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de inscrição mais segura como a identidade da aplicação e o utilizador são verificados pela aplicação do corretor com algoritmos de segurança adicionais e encriptação.

Aqui está uma representação de como os SDKs trabalham com as aplicações de corretor para permitir sSO:

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

A capacidade de uma aplicação utilizar qualquer corretor instalado no dispositivo é descolou por predefinição. Para utilizar a sua aplicação com o corretor, tem de fazer alguma configuração adicional e adicionar algum código à sua aplicação.

Os passos a seguir são:

1. Ativar o modo de corretor na chamada do seu código de aplicação para o MS SDK
2. Estabeleça um novo URI de redirecionamento e forneça-o tanto à app como ao registo da sua aplicação
3. Configurar as permissões corretas no manifesto Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de corretagem na sua aplicação

A capacidade da sua aplicação de utilizar o corretor é ligada quando cria as "definições" ou a configuração inicial da sua instância de Autenticação. Para isso na sua aplicação:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 2: Estabelecer um novo URI de redirecionamento com o seu esquema de URL

Para garantir que a aplicação certa recebe os tokens credenciais devolvidos, é necessário garantir que a chamada de volta para a sua aplicação de forma a que o sistema operativo Android possa verificar. O sistema operativo Android utiliza o haxixe do certificado na loja Google Play. Este haxixe do certificado não pode ser falsificado por um pedido fraudulento. Juntamente com o URI da aplicação de corretagem, a Microsoft garante que os tokens são devolvidos à aplicação correta. Um URI de redirecionamento único é necessário para ser registado no pedido.

O seu URI de redirecionamento deve estar na forma adequada de:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Pode registar este URI de redirecionamento no registo da sua aplicação através do [portal Azure.](https://portal.azure.com/) Para obter mais informações sobre o registo de aplicações AZure AD, consulte [Integração com Diretório Ativo Azure](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passo 3: Configurar as permissões corretas na sua aplicação

A aplicação de corretor no Android utiliza a funcionalidade Gestor de Contas do Sistema operativo Android para gerir credenciais em todas as aplicações. Para utilizar o corretor no Android, o seu manifesto de aplicações deve ter permissões para utilizar as contas Do Gestor de Contas. Estas permissões são discutidas detalhadamente na [documentação](https://developer.android.com/reference/android/accounts/AccountManager.html) do Google para Gestor de Contas aqui

Em particular, estas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Configuraste o SSO!

Agora, a identidade SDK partilhará automaticamente credenciais através das suas aplicações e invocará o corretor se estiver presente no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o protocolo SAML de assinatura única](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
