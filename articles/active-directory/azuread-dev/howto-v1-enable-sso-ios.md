---
title: Como ativar o SSO de aplicação cruzada no iOS utilizando o ADAL [ Microsoft Docs
description: Como utilizar as funcionalidades do ADAL SDK para ativar o Single Sign On através das suas aplicações.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154785"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como: Ativar o SSO de aplicação cruzada no iOS utilizando o ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O único sinal de inscrição (SSO) permite que os utilizadores introduzam apenas as suas credenciais uma vez e tenham essas credenciais a funcionar automaticamente através de aplicações e em plataformas que outras aplicações podem usar (como contas microsoft ou uma conta de trabalho do Microsoft 365) independentemente da editora.

A plataforma de identidade da Microsoft, juntamente com os SDKs, facilita a ativação do SSO dentro do seu próprio conjunto de aplicações, ou com a capacidade de corretor e aplicações Authenticator, em todo o dispositivo.

Neste como- vai aprender a configurar o SDK dentro da sua aplicação para fornecer SSO aos seus clientes.

Este como se aplica a:

* Diretório Ativo Azure (Diretório Ativo Azure)
* Azure Active Directory B2C
* Diretório Ativo Azure B2B
* Acesso Condicional do Diretório Ativo Azure

## <a name="prerequisites"></a>Pré-requisitos

Este como-assumir que você sabe como:

* Disponibilize a sua aplicação utilizando o portal legacy para a AD Azure. Para mais informações, consulte [Registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integre a sua aplicação com o [SDK iOS Azure AD](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Conceitos de inscrição simples

### <a name="identity-brokers"></a>Corretores de identidade

A Microsoft fornece aplicações para todas as plataformas móveis que permitem a ponte de credenciais através de aplicações de diferentes fornecedores e para funcionalidades melhoradas que requerem um único lugar seguro de onde validar credenciais. Estes são **chamados corretores.**

No iOS e Android, os corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere alguns, ou todos, dos dispositivos para os seus colaboradores. Os corretores suportam a gestão da segurança apenas para algumas aplicações ou todo o dispositivo com base na configuração de administração de TI. No Windows, esta funcionalidade é fornecida por um escolhidor de conta incorporado no sistema operativo, conhecido tecnicamente como o Corretor de Autenticação Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para iniciar sessão em dispositivos móveis

O acesso às credenciais nos dispositivos segue dois padrões básicos:

* Logins assistidos não-corretores
* Logins assistidos corretor

#### <a name="non-broker-assisted-logins"></a>Logins assistidos não-corretores

Os logins assistidos não-corretores são experiências de login que acontecem em linha com a aplicação e usam o armazenamento local no dispositivo para essa aplicação. Este armazenamento pode ser partilhado entre aplicações, mas as credenciais estão fortemente ligadas à app ou ao conjunto de aplicações que usam essa credencial. É provável que tenha experimentado isso em muitas aplicações móveis quando introduz um nome de utilizador e uma senha dentro da própria aplicação.

Estes logins têm os seguintes benefícios:

* A experiência do utilizador existe inteiramente dentro da aplicação.
* As credenciais podem ser partilhadas através de aplicações assinadas pelo mesmo certificado, proporcionando uma única experiência de inscrição ao seu conjunto de aplicações.
* O controlo em torno da experiência de iniciar sessão é fornecido à aplicação antes e depois do login.

Estes logins têm as seguintes desvantagens:

* Os utilizadores não podem experimentar um único sinal em todas as aplicações que usam uma identidade microsoft, apenas através das identidades da Microsoft que a sua aplicação configura.
* A sua aplicação não pode ser utilizada com funcionalidades de negócio mais avançadas, como o Acesso Condicional ou utilizar o conjunto de produtos Intune.
* A sua aplicação não pode suportar a autenticação baseada em certificados para utilizadores empresariais.

Aqui está uma representação de como os SDKs funcionam com o armazenamento partilhado das suas aplicações para permitir o SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Logins assistidos corretor

Os logins assistidos pelo corretor são experiências de login que ocorrem dentro da aplicação de corretor e usam o armazenamento e segurança do corretor para partilhar credenciais em todas as aplicações do dispositivo que aplicam a plataforma de identidade. Isto significa que as suas aplicações dependem do corretor para iniciar a sua inscrição nos utilizadores. No iOS e Android, estes corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere o dispositivo para o seu utilizador. Um exemplo deste tipo de aplicação é a aplicação Microsoft Authenticator no iOS. No Windows esta funcionalidade é fornecida por um escolhidor de conta incorporado no sistema operativo, conhecido tecnicamente como o Corretor de Autenticação Web.

A experiência varia por plataforma e pode por vezes ser disruptiva para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com este padrão se tiver a aplicação do Facebook instalada e utilizar o Facebook Connect a partir de outra aplicação. A plataforma de identidade usa o mesmo padrão.

Para o iOS isto leva a uma animação de "transição" onde a sua aplicação é enviada para segundo plano enquanto as aplicações do Autenticador Microsoft chegam ao primeiro plano para o utilizador selecionar com que conta gostaria de assinar.

Para Android e Windows, o escolhidor de conta é apresentado em cima da sua aplicação, o que é menos disruptivo para o utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor é invocado

Se um corretor compatível for instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs farão automaticamente o trabalho de invocar o corretor para si quando um utilizador indicar que pretende iniciar sessão utilizando qualquer conta a partir da plataforma de identidade. Esta conta pode ser uma Conta Microsoft pessoal, uma conta de trabalho ou escola, ou uma conta que fornece e hospeda em Azure usando os nossos produtos B2C e B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Como garantimos que a aplicação é válida

A necessidade de garantir a identidade de uma aplicação que chama o corretor é crucial para a segurança que fornecemos em logins assistidos pelo corretor. Nem o iOS nem o Android aplicam identificadores únicos que são válidos apenas para uma determinada aplicação, pelo que aplicações maliciosas podem "falsificar" o identificador de uma aplicação legítima e receber os tokens destinados à aplicação legítima. Para garantir que estamos sempre a comunicar com a aplicação certa no prazo de funcionamento, pedimos ao desenvolvedor que forneça um redirectURI personalizado ao registar a sua aplicação com a Microsoft. Como os desenvolvedores devem criar este URI redirecionado é discutido em detalhe abaixo. Este redirectURI personalizado contém o Bundle ID da aplicação e é garantido ser único na aplicação pela Apple App Store. Quando uma aplicação chama o corretor, o corretor pede ao sistema operativo iOS que o forneça com o Id bundle que chamou o corretor. O corretor fornece este Bundle ID à Microsoft na chamada para o nosso sistema de identidade. Se o Bundle ID da aplicação não corresponder ao Id bundle fornecido pelo desenvolvedor durante o registo, negaremos o acesso aos tokens para o recurso que a aplicação está a solicitar. Este cheque garante que apenas a aplicação registada pelo programador recebe fichas.

**O desenvolvedor tem a escolha se o SDK chama o corretor ou utiliza o fluxo assistido não-corretor.** No entanto, se o desenvolvedor optar por não utilizar o fluxo assistido pelo corretor, perde o benefício de utilizar credenciais SSO que o utilizador pode já ter adicionado no dispositivo e impede que a sua aplicação seja utilizada com funcionalidades empresariais a Microsoft fornece aos seus clientes, tais como Acesso Condicional, capacidades de gestão Intune e autenticação baseada em certificados.

Estes logins têm os seguintes benefícios:

* Experiências de utilizador SSO em todas as suas aplicações, independentemente do fornecedor.
* A sua aplicação pode utilizar funcionalidades de negócio mais avançadas, como o Acesso Condicional ou utilizar o conjunto de produtos Intune.
* A sua aplicação pode apoiar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de inscrição muito mais segura como a identidade da aplicação e o utilizador são verificados pela aplicação de corretagem com algoritmos de segurança adicionais e encriptação.

Estes logins têm as seguintes desvantagens:

* No iOS o utilizador é transitado da experiência da sua aplicação enquanto as credenciais são escolhidas.
* Perda da capacidade de gerir a experiência de login para os seus clientes dentro da sua aplicação.

Aqui está uma representação de como os SDKs trabalham com as aplicações de corretor para permitir o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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

## <a name="enabling-cross-app-sso-using-adal"></a>Habilitar o SSO transversal usando o ADAL

Aqui utilizamos o SDK adal iOS para:

* Ligue o SSO assistido não-corretor para o seu conjunto de aplicações
* Ligue o suporte para sSO assistido por corretor

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ligar o SSO para sso assistido não-corretor

Para o SSO não-corretor assistido através de aplicações, os SDKs gerem grande parte da complexidade do SSO para si. Isto inclui encontrar o utilizador certo na cache e manter uma lista de utilizadores registados nos utilizadores para que possa consultar.

Para permitir o SSO através de aplicações que possui, tem de fazer o seguinte:

1. Certifique-se de que todas as suas aplicações utilizam o mesmo ID do Cliente ou ID de Aplicação.
2. Certifique-se de que todas as suas aplicações partilham o mesmo certificado de assinatura da Apple para que possa partilhar porta-chaves.
3. Solicite o mesmo direito de porta-chaves para cada um dos seus pedidos.
4. Conte aos SDKs sobre o porta-chaves partilhado que quer que usemos.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Utilizando o mesmo ID/ID de aplicação do cliente para todas as aplicações no seu conjunto de apps

Para que a plataforma de identidade saiba que é permitido partilhar fichas nas suas aplicações, cada uma das suas aplicações terá de partilhar o mesmo ID do Cliente ou ID de aplicação. Este é o identificador único que lhe foi fornecido quando registou a sua primeira aplicação no portal.

Redirecione os URIs que lhe permitem identificar diferentes aplicações para o serviço de identidade da Microsoft se utilizar o mesmo ID de aplicação. Cada aplicação pode ter vários URIs Redirecionados registados no portal de embarque. Cada aplicação na sua suite terá um URI de redirecionamento diferente. Um exemplo de como isto parece está abaixo:

App1 Redirecione URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 Redirecione URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 Redirecione URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estes são aninhados sob o mesmo ID/id de aplicação do cliente e procurados com base no URI redirecionado que nos devolve na sua configuração SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

O formato destes URIs redirecionados é explicado abaixo. Você pode usar qualquer Redirect URI a menos que deseje apoiar o corretor, nesse caso eles devem se parecer com o acima*

#### <a name="create-keychain-sharing-between-applications"></a>Criar a partilha de porta-chaves entre aplicações

Permitir a partilha de porta-chaves está fora do âmbito deste documento e coberto pela Apple no seu documento [Adicionando Capacidades.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) O que é importante é que decida o que quer que o seu porta-chaves seja chamado e adicione essa capacidade em todas as suas aplicações.

Quando tiver direitos configurados corretamente, deve ver um ficheiro `entitlements.plist` no seu diretório de projeto intitulado que contém algo que se parece com o seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Assim que tiver o direito do porta-chaves ativado em cada uma das suas aplicações, e estiver pronto a utilizar `ADAuthenticationSettings` o SSO, informe a identidade SDK sobre o seu porta-chaves utilizando a seguinte definição no seu seguinte ajuste com a seguinte definição:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando partilha um porta-chaves nas suas aplicações, qualquer aplicação pode eliminar utilizadores ou, pior, apagar todas as fichas através da sua aplicação. Isto é particularmente desastroso se tivermos aplicações que dependem dos símbolos para fazer trabalhode fundo. Partilhar um porta-chaves significa que deve ter muito cuidado em todas as operações de remoção através dos SDKs de identidade.

Já está! O SDK irá agora partilhar credenciais em todas as suas aplicações. A lista de utilizadores também será partilhada em todas as instâncias de aplicação.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ligar sSO para corretor assistido SSO

A capacidade de uma aplicação utilizar qualquer corretor instalado no dispositivo é **desligada por defeito**. Para utilizar a sua aplicação com o corretor, tem de fazer alguma configuração adicional e adicionar algum código à sua aplicação.

Os passos a seguir são:

1. Ative o modo de corretor na chamada do código de aplicação para o MS SDK.
2. Estabeleça um novo redirecionamento URI e forneça-o tanto à app como ao registo da sua aplicação.
3. Registando um esquema de URL.
4. Adicione uma permissão ao seu ficheiro info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de corretagem na sua aplicação

A capacidade de utilização do corretor da sua aplicação é ligada quando cria o "contexto" ou a configuração inicial do objeto de Autenticação. Faça-o definindo o seu tipo de credenciais no seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` definição permitirá ao SDK tentar chamar `AD_CREDENTIALS_EMBEDDED` o corretor, impedirá o SDK de ligar para o corretor.

#### <a name="step-2-registering-a-url-scheme"></a>Passo 2: Registar um esquema de URL

A plataforma de identidade utiliza URLs para invocar o corretor e, em seguida, devolver o controlo à sua aplicação. Para terminar essa viagem de ida e volta, precisa de um esquema de URL registado para a sua aplicação que a plataforma de identidade saberá. Isto pode ser além de quaisquer outros esquemas de aplicações que possa ter registado anteriormente com a sua aplicação.

> [!WARNING]
> Recomendamos que o esquema de URL é bastante único para minimizar as chances de outra aplicação usando o mesmo esquema de URL. A Apple não aplica a singularidade dos esquemas de URL que estão registados na loja de aplicações.

Abaixo está um exemplo de como isto aparece na configuração do seu projeto. Também pode fazê-lo no XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 3: Estabelecer um novo uri redirecionamento com o seu esquema de URL

Para garantir que devolvemos sempre os tokens credenciais à aplicação correta, precisamos de nos certificar de que voltamos a ligar para a sua aplicação de forma a que o sistema operativo iOS possa verificar. O sistema operativo iOS reporta ao corretor da Microsoft aplica o Bundle ID da aplicação que lhe chama. Isto não pode ser falsificado por uma aplicação fraudulenta. Por isso, aproveitamos isso juntamente com o URI da nossa aplicação de corretor para garantir que os tokens sejam devolvidos à aplicação correta. Exigimos que estabeleça este URI único redirecionamento, tanto na sua aplicação como definido como Redirect URI no nosso portal de desenvolvimento.

O seu uri redireccional deve estar na forma adequada de:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este URI redirecionado precisa de ser especificado no registo da sua aplicação através do [portal Azure](https://portal.azure.com/). Para obter mais informações sobre o registo da aplicação Azure AD, consulte [Integração com o Diretório Ativo Azure](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passo 3a: Adicione um URI redirecionamento na sua app e portal de v para apoiar a autenticação baseada em certificados

Para apoiar a autenticação baseada em cert, um segundo "msauth" precisa de ser registado na sua aplicação e no [portal Azure](https://portal.azure.com/) para lidar com a autenticação de certificado se pretender adicionar esse suporte na sua aplicação.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Passo 4: Adicione um parâmetro de configuração à sua aplicação

O ADAL utiliza –canOpenURL: para verificar se o corretor está instalado no dispositivo. No iOS 9 on, a Apple bloqueou quais os esquemas que uma aplicação pode consultar. Terá de adicionar "msauth" à secção LSApplicationQueriesSchemes da sua `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Configuraste o SSO!

Agora, a identidade SDK partilhará automaticamente as credenciais através das suas aplicações e invocará o corretor se estiver presente no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Conheça o [protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
