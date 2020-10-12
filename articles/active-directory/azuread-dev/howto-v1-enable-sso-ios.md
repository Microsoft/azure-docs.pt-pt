---
title: Como permitir o SSO de aplicações cruzadas no iOS usando a ADAL Microsoft Docs
description: Como utilizar as funcionalidades do ADAL SDK para ativar o Signo Único em todas as suas aplicações.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383704"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como: Permitir o SSO de aplicações cruzadas no iOS utilizando o ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O único sinform (SSO) permite que os utilizadores introduzam as suas credenciais apenas uma vez e que essas credenciais funcionem automaticamente através de aplicações e plataformas que outras aplicações podem usar (como as Contas microsoft ou uma conta de trabalho da Microsoft 365) independentemente do editor.

A plataforma de identidade da Microsoft, juntamente com os SDKs, facilita a ativação do SSO dentro do seu próprio conjunto de aplicações, ou com a capacidade de corretor e aplicações Authenticator, em todo o dispositivo.

Neste como fazer, aprenderá a configurar o SDK dentro da sua aplicação para fornecer SSO aos seus clientes.

Este "como"aplicar-se a:

* Diretório Ativo Azure (Diretório Ativo Azure)
* Azure Active Directory B2C
* Diretório Ativo Azure B2B
* Acesso Condicional do Diretório Ativo Azure

## <a name="prerequisites"></a>Pré-requisitos

Este como-assumir que sabe como:

* Forcam a sua aplicação utilizando o portal legado para Azure AD. Para mais informações, consulte [Registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integre a sua aplicação com o [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Conceitos de início de sessão único

### <a name="identity-brokers"></a>Corretores de identidade

A Microsoft fornece aplicações para todas as plataformas móveis que permitem a ponte de credenciais através de aplicações de diferentes fornecedores e para funcionalidades melhoradas que requerem um único lugar seguro de onde validar credenciais. Estes são **chamados corretores.**

No iOS e Android, os corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere alguns, ou todos, dos dispositivos para os seus colaboradores. Os corretores suportam a gestão da segurança apenas para algumas aplicações ou todo o dispositivo com base na configuração de administração de TI. No Windows, esta funcionalidade é fornecida por um chooser de conta incorporado no sistema operativo, conhecido tecnicamente como O Corretor de Autenticação Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para iniciar sessão em dispositivos móveis

O acesso a credenciais em dispositivos segue dois padrões básicos:

* Logins assistidos não corretores
* Logins assistidos por corretor

#### <a name="non-broker-assisted-logins"></a>Logins assistidos não corretores

Os logins assistidos não corretores são experiências de login que acontecem de acordo com a aplicação e usam o armazenamento local no dispositivo para essa aplicação. Este armazenamento pode ser partilhado através de aplicações, mas as credenciais estão fortemente ligadas à app ou conjunto de aplicações que usam essa credencial. É provável que tenha experimentado isto em muitas aplicações móveis quando introduz um nome de utilizador e senha dentro da própria aplicação.

Estes logins têm os seguintes benefícios:

* A experiência do utilizador existe inteiramente dentro da aplicação.
* As credenciais podem ser partilhadas através de aplicações assinadas pelo mesmo certificado, proporcionando uma única experiência de inscrição no seu conjunto de aplicações.
* O controlo em torno da experiência de login é fornecido à aplicação antes e depois do login.

Estes logins têm as seguintes desvantagens:

* Os utilizadores não podem experimentar um único sinal em todas as aplicações que usam uma identidade da Microsoft, apenas através das identidades da Microsoft que a sua aplicação configura.
* A sua aplicação não pode ser utilizada com funcionalidades de negócio mais avançadas, como o Acesso Condicional ou a utilização do conjunto de produtos Intune.
* A sua aplicação não pode suportar a autenticação baseada em certificados para utilizadores empresariais.

Aqui está uma representação de como os SDKs funcionam com o armazenamento partilhado das suas aplicações para permitir sSO:

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

#### <a name="broker-assisted-logins"></a>Logins assistidos por corretor

Logins assistidos por corretor são experiências de login que ocorrem dentro da aplicação do corretor e usam o armazenamento e segurança do corretor para partilhar credenciais em todas as aplicações no dispositivo que aplicam a plataforma de identidade. Isto significa que as suas aplicações dependem do corretor para iniciar seduções. No iOS e Android, estes corretores são fornecidos através de aplicações transferíveis que os clientes instalam de forma independente ou empurradas para o dispositivo por uma empresa que gere o dispositivo para o seu utilizador. Um exemplo deste tipo de aplicação é a aplicação Microsoft Authenticator no iOS. No Windows esta funcionalidade é fornecida por um chooser de conta incorporado no sistema operativo, conhecido tecnicamente como O Corretor de Autenticação Web.

A experiência varia de acordo com a plataforma e pode por vezes ser disruptiva para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com este padrão se tiver a aplicação do Facebook instalada e utilizar o Facebook Connect a partir de outra aplicação. A plataforma de identidade usa o mesmo padrão.

Para o iOS isto leva a uma animação de "transição" onde a sua aplicação é enviada para o fundo enquanto as aplicações Microsoft Authenticator surgem em primeiro plano para o utilizador selecionar com que conta gostaria de iniciar scontabilidade.

Para Android e Windows o escolha da conta é apresentado em cima da sua aplicação, o que é menos perturbador para o utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor é invocado

Se um corretor compatível for instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs farão automaticamente o trabalho de invocar o corretor para si quando um utilizador indicar que pretende iniciar sessão utilizando qualquer conta a partir da plataforma de identidade. Esta conta pode ser uma Conta Microsoft pessoal, uma conta de trabalho ou escola, ou uma conta que você fornece e hospeda em Azure usando os nossos produtos B2C e B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Como garantimos que a aplicação é válida

A necessidade de garantir a identidade de uma aplicação que ligue para o corretor é crucial para a segurança que fornecemos em logins assistidos por corretor. Nem o iOS nem o Android aplicam identificadores únicos que sejam válidos apenas para uma determinada aplicação, pelo que aplicações maliciosas podem "falsificar" o identificador de uma aplicação legítima e receber os tokens destinados à aplicação legítima. Para garantir que estamos sempre a comunicar com a aplicação certa no tempo de execução, pedimos ao desenvolvedor que forneça um reorientador personalizado NO momento de registar a sua aplicação com a Microsoft. Como os desenvolvedores devem criar este URI redirecionado é discutido em detalhe abaixo. Este reorientação personalizadoURI contém o ID do Bundle da aplicação e é garantido ser exclusivo da aplicação pela Apple App Store. Quando uma aplicação chama o corretor, o corretor pede ao sistema operativo iOS que lhe forneça o Bundle ID que chamou o corretor. O corretor fornece este Bundle ID à Microsoft na chamada para o nosso sistema de identidade. Se o ID do Pacote da aplicação não corresponder ao ID do Bundle fornecido pelo desenvolvedor durante o registo, negaremos o acesso aos tokens para o recurso que a aplicação está solicitando. Esta verificação garante que apenas a aplicação registada pelo desenvolvedor recebe fichas.

**O desenvolvedor tem a escolha se o SDK chama o corretor ou utiliza o fluxo assistido não corretor.** No entanto, se o desenvolvedor optar por não utilizar o fluxo assistido pelo corretor, perde o benefício de usar credenciais SSO que o utilizador pode já ter adicionado no dispositivo e impede que a sua aplicação seja usada com funcionalidades empresariais a Microsoft fornece aos seus clientes, tais como Acesso Condicional, capacidades de gestão intune e autenticação baseada em certificados.

Estes logins têm os seguintes benefícios:

* O utilizador experimenta sSO em todas as suas aplicações independentemente do fornecedor.
* A sua aplicação pode utilizar funcionalidades de negócio mais avançadas, como o Acesso Condicionado ou utilizar o conjunto de produtos Intune.
* A sua aplicação pode suportar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de inscrição muito mais segura como a identidade da aplicação e o utilizador são verificados pela aplicação do corretor com algoritmos de segurança adicionais e encriptação.

Estes logins têm as seguintes desvantagens:

* No iOS, o utilizador é transitado da experiência da sua aplicação enquanto as credenciais são escolhidas.
* Perda da capacidade de gerir a experiência de login para os seus clientes dentro da sua aplicação.

Aqui está uma representação de como os SDKs trabalham com as aplicações de corretor para permitir sSO:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Habilitar o SSO de cross-app usando a ADAL

Aqui usamos o ADAL iOS SDK para:

* Ligue sSO assistido não corretor para o seu conjunto de aplicações
* Ligue o suporte para SSO assistido por corretor

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ligar sSO para SSO assistido não corretor

Para sSO assistido não corretor em todas as aplicações, os SDKs gerem grande parte da complexidade do SSO para si. Isto inclui encontrar o utilizador certo na cache e manter uma lista de utilizadores registados para que possa consultar.

Para ativar o SSO através das aplicações que possui, tem de fazer o seguinte:

1. Certifique-se de que todas as suas aplicações utilizam o mesmo ID do Cliente ou ID de aplicação.
2. Certifique-se de que todas as suas aplicações partilham o mesmo certificado de assinatura da Apple para que possa partilhar porta-chaves.
3. Solicite o mesmo direito a cada uma das suas candidaturas.
4. Conte aos SDKs sobre o chaveiro partilhado que quer que usemos.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Utilização do mesmo ID / ID de aplicação do cliente para todas as aplicações no seu conjunto de aplicações

Para que a plataforma de identidade saiba que é permitido partilhar fichas através das suas aplicações, cada uma das suas aplicações terá de partilhar o mesmo ID ou ID do Cliente. Este é o identificador único que lhe foi fornecido quando registou a sua primeira aplicação no portal.

Redirecionar URIs permite identificar diferentes aplicações para o serviço de identidade da Microsoft se utilizar o mesmo ID de aplicação. Cada aplicação pode ter várias URIs de redirecionamento registadas no portal de embarque. Cada aplicação na sua suite terá um URI de redirecionamento diferente. Um exemplo de como isto parece abaixo:

App1 Redirecionar URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 Redirecionar URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 Redirecionamento URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estes estão aninhados no mesmo ID/ID de aplicação do cliente e analisados com base no redirecionamento URI que você retorna a nós na sua configuração SDK.

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

O formato destes URIs de redirecionamento é explicado abaixo. Pode utilizar qualquer URI de redirecionamento a menos que deseje apoiar o corretor, caso em que eles devem parecer algo como o acima*

#### <a name="create-keychain-sharing-between-applications"></a>Criar partilha de chave entre aplicações

Permitir a partilha de chaveiros está fora do âmbito deste documento e coberto pela Apple no seu documento [Adicionar Capacidades.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) O que é importante é que decida o que quer que o seu chaveiro seja chamado e adicione essa capacidade em todas as suas aplicações.

Quando tiver direitos configurado corretamente, deve ver um ficheiro no seu diretório de projeto intitulado `entitlements.plist` que contém algo que se parece com o seguinte:

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

Uma vez que tenha o direito de chaveiro ativo em cada uma das suas aplicações, e está pronto para usar SSO, informe a identidade SDK sobre o seu chaveiro utilizando a seguinte definição na sua `ADAuthenticationSettings` definição com a seguinte definição:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando partilha um chaveiro em todas as suas aplicações qualquer aplicação pode eliminar utilizadores ou, pior, eliminar todos os tokens da sua aplicação. Isto é particularmente desastroso se tiver aplicações que dependem dos tokens para fazer trabalhos de fundo. Partilhar um porta-chaves significa que deve ter muito cuidado em todas as operações de remoção através dos SDKs de identidade.

Já está. O SDK irá agora partilhar credenciais em todas as suas aplicações. A lista de utilizadores também será partilhada em todas as instâncias de aplicação.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ligar sSO para corretor assistido SSO

A capacidade de uma aplicação utilizar qualquer corretor instalado no dispositivo é **desligada por defeito**. Para utilizar a sua aplicação com o corretor tem de fazer alguma configuração adicional e adicionar algum código à sua aplicação.

Os passos a seguir são:

1. Ativar o modo de corretor na chamada do seu código de aplicação para o MS SDK.
2. Estabeleça um novo URI de redirecionamento e forneça-o tanto à app como ao registo da sua aplicação.
3. Registo de um esquema de URL.
4. Adicione uma permissão ao seu ficheiro info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de corretagem na sua aplicação

A capacidade da sua aplicação de utilizar o corretor é ligada quando cria o "contexto" ou configuração inicial do seu objeto de Autenticação. Fá-lo definindo o seu tipo de credenciais no seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A `AD_CREDENTIALS_AUTO` definição permitirá que o SDK tente chamar o corretor, `AD_CREDENTIALS_EMBEDDED` impedirá o SDK de ligar para o corretor.

#### <a name="step-2-registering-a-url-scheme"></a>Passo 2: Registo de um esquema de URL

A plataforma de identidade utiliza URLs para invocar o corretor e, em seguida, devolver o controlo à sua aplicação. Para terminar essa viagem de ida e volta, precisa de um esquema de URL registado para a sua aplicação que a plataforma de identidade saberá. Isto pode ser além de quaisquer outros esquemas de aplicações que possa ter registado anteriormente na sua aplicação.

> [!WARNING]
> Recomendamos tornar o esquema de URL bastante exclusivo para minimizar as chances de outra aplicação usando o mesmo esquema de URL. A Apple não aplica a singularidade dos esquemas de URL que estão registados na loja de aplicações.

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 3: Estabelecer um novo URI de redirecionamento com o seu esquema de URL

Para garantir que devolvemos sempre os tokens credenciais à aplicação correta, precisamos de ter a certeza de que voltamos à sua aplicação de forma a que o sistema operativo iOS possa verificar. O sistema operativo iOS informa às aplicações do corretor da Microsoft o ID do Pacote da aplicação que lhe chama. Isto não pode ser falsificado por uma aplicação fraudulenta. Por isso, aproveitamos isto juntamente com o URI da nossa aplicação de corretor para garantir que os tokens são devolvidos à aplicação correta. Exigimos que estabeleça este URI de redirecionamento único tanto na sua aplicação como definido como um URI redirecionado no nosso portal de desenvolvedores.

O seu URI de redirecionamento deve estar na forma adequada de:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este reorientação URI precisa de ser especificado no registo da sua aplicação utilizando o [portal Azure.](https://portal.azure.com/) Para obter mais informações sobre o registo de aplicações AZure AD, consulte [Integração com Diretório Ativo Azure](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passo 3a: Adicione um URI de redirecionamento na sua app e portal dev para suportar a autenticação baseada em certificados

Para suportar a autenticação baseada em cert, um segundo "msauth" precisa de ser registado na sua candidatura e no [portal Azure](https://portal.azure.com/) para lidar com a autenticação de certificados se pretender adicionar esse suporte na sua aplicação.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Passo 4: Adicione um parâmetro de configuração à sua aplicação

A ADAL utiliza o canOpenURL: para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou quais os esquemas que uma aplicação pode consultar. Terá de adicionar "msauth" à secção LSApplicationQueriesSchemes da sua `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Configuraste o SSO!

Agora, a identidade SDK partilhará automaticamente credenciais através das suas aplicações e invocará o corretor se estiver presente no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o protocolo SAML de assinatura única](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
