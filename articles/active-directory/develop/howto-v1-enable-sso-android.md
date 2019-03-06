---
title: Como ativar o SSO em vária aplicações em dispositivos Android através da ADAL | Documentos da Microsoft
description: Como utilizar as funcionalidades do SDK da ADAL para ativar o início de sessão único em todas as suas aplicações.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5085acad8a82394340892a3a67a3d1e5d85384b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442176"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como: Ativar a SSO em vária aplicações em dispositivos Android através da ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Início de sessão único (SSO) permite aos utilizadores apenas introduzir as respetivas credenciais de uma vez e ter essas credenciais de trabalho automaticamente em todos os aplicativos e entre plataformas que outros aplicativos podem utilizar (por exemplo, Accounts da Microsoft ou uma conta profissional do Microsoft 365) não importa o publicador.

Plataforma de identidade da Microsoft, juntamente com os SDKs, torna mais fácil ativar o SSO no seu próprio conjunto de aplicações, ou com a capacidade de Mediador Authenticator aplicativos e, em todo o dispositivo.

Nesta explicação de procedimento, aprenderá como configurar o SDK na sua aplicação para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Nesta explicação de procedimento parte do princípio de que sabe como:

- Aprovisione a sua aplicação com o portal de legado para o Azure Active Directory (Azure AD). Para obter mais informações, consulte [registar uma aplicação com o ponto de final de versão 1.0 do Azure AD](quickstart-v1-add-azure-ad-app.md)
- Integrar a aplicação com o [do Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Conceitos de início de sessão únicos

### <a name="identity-brokers"></a>Mediadores de identidades

A Microsoft fornece aplicações para todas as plataformas móveis que permitem o bridging de credenciais em todos os aplicativos de diferentes fornecedores e para os recursos aprimorados que necessitam de um único local seguro para a validação de credenciais. Estes são denominados **mediadores**.

No iOS e Android, mediadores são fornecidos por meio de aplicativos que podem ser baixados que os clientes instalar de forma independente ou enviado para o dispositivo por uma empresa que gere alguns ou todos, dos dispositivos aos seus empregados. Suporte de mediadores gerenciando a segurança apenas para alguns aplicativos ou todo o dispositivo com base na configuração de administrador de TI. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporado no sistema operativo, conhecido em termos técnicos, como o Mediador de autenticação Web.

#### <a name="broker-assisted-login"></a>Início de sessão assistido Mediador

Inícios de sessão assistida por mediador são experiências de início de sessão que ocorrem dentro da aplicação de Mediador e utilizam o armazenamento e a segurança de Mediador para partilhar as credenciais em todas as aplicações no dispositivo que se aplicam a plataforma de identidade. A implicação a ser seus aplicativos dependerá o Mediador de sessão aos utilizadores. No iOS e Android, estes mediadores são fornecidos por meio de aplicativos que podem ser baixados que os clientes instalar de forma independente ou podem ser enviados para o dispositivo por uma empresa que gere o dispositivo para o utilizador. Um exemplo deste tipo de aplicativo é a aplicação Microsoft Authenticator em dispositivos iOS. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporado no sistema operativo, conhecido em termos técnicos, como o Mediador de autenticação Web.
A experiência varia consoante a plataforma e, às vezes, pode ser perturbador para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com esse padrão se tiver instalado o aplicativo para Facebook e utilizar o Facebook ligar a partir de outra aplicação. A plataforma de identidade utiliza o mesmo padrão.

No Android, o selecionador de conta é apresentado na parte superior de seu aplicativo, que é menos nociva, para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o mediador é invocado

Se um mediador compatível estiver instalado no dispositivo, como a aplicação Microsoft Authenticator, a identidade SDKs automaticamente fará o trabalho de invocar o mediador para quando um utilizador a indicar que desejam iniciar sessão com qualquer conta da plataforma de identidade.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante a aplicação é válido

A necessidade de garantir a identidade de uma chamada de aplicativo o mediador é fundamental da segurança já fornecida nos inícios de sessão de Mediador assistido. iOS e Android não impõem identificadores exclusivos que só são válidos para um determinado aplicativo, de modo a que aplicativo mal-intencionados pode "falsificar" identificador de um aplicativo legítimo e receber tokens destinados-se o aplicativo legítimo. Para garantir que a Microsoft sempre está a comunicar com o aplicativo adequado no tempo de execução, o desenvolvedor é-lhe pedido para fornecer um redirectURI personalizado ao registar a sua aplicação com a Microsoft. **Como os desenvolvedores devem, crie o URI de redirecionamento é descrito detalhadamente abaixo.** Este redirectURI personalizada contém o thumbprint do certificado da aplicação e é garantida para que seja exclusivo para o aplicativo por meio de Store de Play Google. Quando um aplicativo chama o mediador, o mediador solicita o sistema operativo Android fornecê-lo com o thumbprint do certificado que o Mediador de chamada. O mediador fornece este thumbprint de certificado para a Microsoft na chamada para o sistema de identidade. Se o thumbprint do certificado da aplicação não coincide com o thumbprint do certificado fornecido para nós pelo desenvolvedor durante o registo, o acesso é negado para os tokens para o recurso que a aplicação está a solicitar. Esta verificação garante que apenas o aplicativo registrado pelo desenvolvedor recebe tokens.

Inícios de sessão de SSO mediada têm as seguintes vantagens:

* Experiências de usuário SSO em todas as suas aplicações, independentemente do fornecedor.
* A aplicação pode utilizar funcionalidades de negócios mais avançadas, como o acesso condicional e oferecer suporte a cenários do Intune.
* Seu aplicativo pode suportar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de início de sessão mais segura como a identidade da aplicação e o utilizador são verificadas pela aplicação de Mediador com encriptação e algoritmos de segurança adicional.

Esta é uma representação de como os SDKs funcionam com os aplicativos de Mediador para ativar o SSO:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO para broker assistido SSO

A capacidade para uma aplicação para utilizar qualquer Mediador de que está instalado no dispositivo está desativada por predefinição. Para utilizar a aplicação com o agente, tem de fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Os passos a seguir são:

1. Ativar o modo de Mediador chamar o código da aplicação para o SDK MS
2. Estabelecer um novo URI de redirecionamento e indique que a aplicação e o registo de aplicação
3. Como configurar as permissões corretas no manifesto do Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de mediador em seu aplicativo

A capacidade para a sua aplicação utilizar o mediador é ativada quando cria a "definições" ou a configuração inicial da sua instância de autenticação. Para fazê-lo na sua aplicação:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 2: Estabelecer um novo URI com o esquema de URL de redirecionamento

Para garantir que o aplicativo certo recebe retornado a credencial de tokens, existe uma necessidade de fazer-se de que a chamada para a sua aplicação de uma forma que pode verificar o sistema operativo Android. O sistema operativo Android utiliza o hash do certificado na Google Play store. Este hash do certificado não pode ser falsificada por um aplicativo de adesão. Juntamente com o URI da aplicação de Mediador, a Microsoft garante que os tokens são devolvidos para a aplicação correta. Um redirecionamento exclusivo URI é necessário a registar no aplicativo.

Seu URI de redirecionamento tem de ser no formato adequado:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Pode registrar esse URI de redirecionamento no seu registo de aplicação utilizando o [portal do Azure](https://portal.azure.com/). Para obter mais informações sobre o registo de aplicação do Azure AD, consulte [integração com o Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passo 3: Configurar as permissões corretas na sua aplicação

A aplicação de Mediador no Android utiliza a funcionalidade do Gestor de contas do sistema operativo Android para gerir as credenciais em todos os aplicativos. Para usar o mediador no Android o manifesto da aplicação tem de ter permissões para utilizar contas de AccountManager. Estas permissões são discutidas detalhadamente no [aqui Google documentação para o Gestor de conta](https://developer.android.com/reference/android/accounts/AccountManager.html)

Em particular, estas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Configurar SSO!

Agora a identidade do SDK irá automaticamente tanto partilhar as credenciais em seus aplicativos e invocar o mediador, se estiver presente no dispositivo.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [único início de sessão do protocolo SAML](single-sign-on-saml-protocol.md)
