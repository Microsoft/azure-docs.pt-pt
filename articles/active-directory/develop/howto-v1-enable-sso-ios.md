---
title: Como ativar o SSO em vária aplicações em dispositivos iOS através da ADAL | Documentos da Microsoft
description: Como utilizar as funcionalidades do SDK da ADAL para ativar o início de sessão único em todas as suas aplicações.
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e79b73123b33a012c062a89fb9748fa101fabcea
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448684"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como: Ativar a SSO em vária aplicações em dispositivos iOS através da ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Início de sessão único (SSO) permite aos utilizadores apenas introduzir as respetivas credenciais de uma vez e ter essas credenciais de trabalho automaticamente em todos os aplicativos e entre plataformas que outros aplicativos podem utilizar (por exemplo, Accounts da Microsoft ou uma conta profissional do Microsoft 365) não importa o publicador.

Plataforma de identidade da Microsoft, juntamente com os SDKs, torna mais fácil ativar o SSO no seu próprio conjunto de aplicações, ou com a capacidade de Mediador Authenticator aplicativos e, em todo o dispositivo.

Nesta explicação de procedimento, aprenderá como configurar o SDK na sua aplicação para fornecer SSO aos seus clientes.

Nesta explicação de procedimento aplica-se a:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Acesso Condicional do Azure Active Directory

## <a name="prerequisites"></a>Pré-requisitos

Nesta explicação de procedimento parte do princípio de que sabe como:

* Aprovisione a sua aplicação com o portal de legado para o Azure AD. Para obter mais informações, consulte [registar uma aplicação com o ponto de final de versão 1.0 do Azure AD](quickstart-v1-add-azure-ad-app.md)
* Integrar a aplicação com o [SDK iOS do Azure AD](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Conceitos de início de sessão únicos

### <a name="identity-brokers"></a>Mediadores de identidades

A Microsoft fornece aplicações para todas as plataformas móveis que permitem o bridging de credenciais em todos os aplicativos de diferentes fornecedores e para os recursos aprimorados que necessitam de um único local seguro para a validação de credenciais. Estes são denominados **mediadores**.

No iOS e Android, mediadores são fornecidos por meio de aplicativos que podem ser baixados que os clientes instalar de forma independente ou enviado para o dispositivo por uma empresa que gere alguns ou todos, dos dispositivos aos seus empregados. Suporte de mediadores gerenciando a segurança apenas para alguns aplicativos ou todo o dispositivo com base na configuração de administrador de TI. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporado no sistema operativo, conhecido em termos técnicos, como o Mediador de autenticação Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para iniciar sessão em dispositivos móveis

Acesso às credenciais em dispositivos seguir dois padrões básicos:

* Inícios de sessão assistidos não-Mediador
* Mediador assistidos inícios de sessão

#### <a name="non-broker-assisted-logins"></a>Inícios de sessão assistidos não-Mediador

Não-mediador assistidos inícios de sessão são experiências de início de sessão que acontecem inline com a aplicação e utilizam o armazenamento local no dispositivo para essa aplicação. Este armazenamento pode ser compartilhado entre aplicativos, mas as credenciais são fortemente vinculadas a aplicação ou do conjunto de aplicações com essa credencial. Provavelmente experimentou isso em muitos aplicativos móveis ao introduzir um nome de utilizador e palavra-passe dentro da própria aplicação.

Estes inícios de sessão tem as seguintes vantagens:

* Experiência de utilizador existe inteiramente dentro do aplicativo.
* As credenciais podem ser partilhadas em todas as aplicações que estão assinadas pelo mesmo certificado, para proporcionar uma experiência única início de sessão para o seu conjunto de aplicativos.
* Controle relacionados à experiência de início de sessão é fornecido para o aplicativo antes e depois do início de sessão.

Estes inícios de sessão tem as seguintes desvantagens:

* Os utilizadores não é possível experiência de início de sessão único em todas as aplicações que utilizam uma identidade da Microsoft, apenas entre essas identidades da Microsoft que tenha configurado a sua aplicação.
* A aplicação não pode ser utilizado com funcionalidades de negócios mais avançadas, como o acesso condicional ou utilizar o conjunto de produtos do Intune.
* A aplicação não suporta a autenticação baseada em certificados para utilizadores empresariais.

Esta é uma representação de como os SDKs funcionam com o armazenamento partilhado das suas aplicações para ativar o SSO:

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

#### <a name="broker-assisted-logins"></a>Mediador assistidos inícios de sessão

Inícios de sessão assistida por mediador são experiências de início de sessão que ocorrem dentro da aplicação de Mediador e utilizam o armazenamento e a segurança de Mediador para partilhar as credenciais em todas as aplicações no dispositivo que se aplicam a plataforma de identidade. Isso significa que seus aplicativos contam com o Mediador de sessão aos utilizadores. No iOS e Android, estes mediadores são fornecidos por meio de aplicativos que podem ser baixados que os clientes instalar de forma independente ou enviado para o dispositivo por uma empresa que gere o dispositivo para o utilizador. Um exemplo deste tipo de aplicativo é a aplicação Microsoft Authenticator em dispositivos iOS. No Windows, esta funcionalidade é fornecida por um selecionador de conta incorporado no sistema operativo, conhecido em termos técnicos, como o Mediador de autenticação Web.

A experiência varia consoante a plataforma e, às vezes, pode ser perturbador para os utilizadores se não for gerida corretamente. Provavelmente está mais familiarizado com esse padrão se tiver instalado o aplicativo para Facebook e utilizar o Facebook ligar a partir de outra aplicação. A plataforma de identidade utiliza o mesmo padrão.

Para iOS, que isso nos leva a uma "transição" animação em que a sua aplicação é enviada para o plano de fundo ao mesmo tempo as aplicações Microsoft Authenticator vem em primeiro plano para o utilizador que selecione a conta que gostariam de iniciar sessão.

Para Android e Windows o selecionador de conta é apresentado na parte superior de seu aplicativo, que é menos perturbador para o utilizador.

#### <a name="how-the-broker-gets-invoked"></a>Como o mediador é invocado

Se um mediador compatível estiver instalado no dispositivo, como a aplicação Microsoft Authenticator, os SDKs fará automaticamente o trabalho de invocar o mediador para quando um utilizador a indicar que desejam iniciar sessão com qualquer conta da plataforma de identidade. Esta conta pode ser uma Account Microsoft pessoal, um trabalho ou a conta da instituição de ensino, ou uma conta que fornece e anfitrião no Azure com os nossos produtos de B2C e B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Como podemos assegurar que o aplicativo é válido

A necessidade de garantir a identidade de uma chamada de aplicativo, que o mediador é crucial para a segurança que é fornecida no Mediador assistido inícios de sessão. Nem iOS nem Android impõe identificadores exclusivos que só são válidos para um determinado aplicativo, de modo a que aplicativo mal-intencionados pode "falsificar" identificador de um aplicativo legítimo e receber tokens destinados-se o aplicativo legítimo. Para garantir que estão sempre a comunicar com o aplicativo adequado no tempo de execução, vamos pedir o desenvolvedor forneça um redirectURI personalizado ao registar a sua aplicação com a Microsoft. Como os desenvolvedores devem, crie o URI de redirecionamento é descrito detalhadamente abaixo. Este redirectURI personalizada contém o ID do pacote do aplicativo e é garantida para que seja exclusivo para o aplicativo por meio de Store de aplicação da Apple. Quando um aplicativo chama o mediador, o mediador solicita o sistema de operativo iOS fornecê-lo com o ID do pacote que chamou o mediador. O agente fornece este ID de pacote para a Microsoft na chamada para nosso sistema de identidade. Se o ID do pacote do aplicativo não coincide com o ID do pacote fornecido para nós pelo desenvolvedor durante o registo, podemos irá negar acesso para os tokens para o recurso a aplicação está a solicitar. Esta verificação garante que apenas o aplicativo registrado pelo desenvolvedor recebe tokens.

**O desenvolvedor tem a opção se o SDK chama o mediador ou utiliza o fluxo de assistido não-mediador.** No entanto se o desenvolvedor optar por não utilizar o fluxo assistida por Mediador perdem a vantagem de utilizar SSO credenciais que o utilizador possa ter já adicionado no dispositivo e impede que os seus aplicativos sejam utilizados com funcionalidades de negócios, a Microsoft fornece seu clientes, como o acesso condicional, capacidades de gestão do Intune e a autenticação baseada em certificado.

Estes inícios de sessão tem as seguintes vantagens:

* Experiências de usuário SSO em todas as suas aplicações, independentemente do fornecedor.
* A aplicação pode utilizar funcionalidades de negócios mais avançadas, como o acesso condicional ou utilizar o conjunto de produtos do Intune.
* Seu aplicativo pode suportar a autenticação baseada em certificados para utilizadores empresariais.
* Experiência de início de sessão muito mais segura como a identidade da aplicação e o utilizador são verificadas pela aplicação de Mediador com encriptação e algoritmos de segurança adicional.

Estes inícios de sessão tem as seguintes desvantagens:

* No iOS o utilizador é transferido para fora da experiência do seu aplicativo enquanto as credenciais são escolhidas.
* Perda da capacidade de gerenciar a experiência de início de sessão para os seus clientes na sua aplicação.

Esta é uma representação de como os SDKs funcionam com os aplicativos de Mediador para ativar o SSO:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Ativar a SSO em vária aplicações com ADAL

Aqui, vamos utilizar o SDK do iOS da ADAL para:

* Ativar o SSO assistido de não-mediador para seu conjunto de aplicações
* Ativar o suporte para SSO assistida por Mediador

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativar o SSO para não-mediador assistido SSO

Para não-mediador SSO assistido entre aplicativos, os SDKs gerir muito da complexidade do SSO para. Isto inclui como localizar o usuário correto no cache e manter uma lista de utilizadores com sessão iniciada para que possa consultar.

Para ativar o SSO em aplicações que são suas que precisa fazer o seguinte:

1. Certifique-se todos os seus utilizadores de aplicações, o mesmo ID de cliente ou o ID da aplicação.
2. Certifique-se de que todas as suas aplicações partilham o mesmo certificado de assinatura da Apple para que possam partilhar keychains.
3. Solicite o mesmo direito de keychain para cada uma das suas aplicações.
4. Conte-nos SDKs keychain partilhado que querem que utilizar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Usando o mesmo ID de cliente / aplicação ID para todas as aplicações no seu conjunto de aplicações

Por ordem para a plataforma de identidade saber que tem permissão para partilhar tokens em seus aplicativos, cada um dos seus aplicativos precisam de partilhar o mesmo ID de cliente ou o ID da aplicação. Este é o identificador exclusivo que lhe foi fornecido quando registou o seu primeiro aplicativo no portal.

Os URIs de redirecionamento permitem-lhe identificar diferentes aplicações ao serviço de identidade da Microsoft, se ele usa a ID da mesma aplicação. Cada aplicativo pode ter vários URIs de redirecionamento registados no portal de inclusão. Cada aplicação no seu conjunto terá um URI de redirecionamento diferente. É um exemplo de como isso é exibido abaixo:

URI de redirecionamento de App1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

URI de redirecionamento de App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI de redirecionamento de App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estas estão aninhadas sob o mesmo ID de cliente / ID de aplicação e pesquisada com base no devolver para nós na sua configuração do SDK do URI de redirecionamento.

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

O formato de redirecionamento URIs é explicado abaixo. Pode usar qualquer URI de redirecionamento, a menos que pretenda suportar o mediador, caso em que eles devem ter um aspeto semelhante a acima *

#### <a name="create-keychain-sharing-between-applications"></a>Criar a partilha entre aplicativos de keychain

Ativar a partilha de keychain está além do escopo deste documento e abrangido pela Apple no seu documento [adicionar capacidades](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O importante é que decidir o que deseja keychain a ser chamado e adicionar essa funcionalidade em todas as suas aplicações.

Quando tiver elegibilidades configuradas corretamente, deverá ver um ficheiro no seu diretório de projeto direito `entitlements.plist` que contém algo parecido com o seguinte:

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

Assim que tiver a elegibilidade de keychain ativada em cada uma das suas aplicações e está pronto para utilizar o SSO, conte-na identidade do SDK keychain utilizando o seguinte no seu `ADAuthenticationSettings` com a definição seguinte:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando partilha um porta-chaves nas suas aplicações qualquer aplicativo pode eliminar utilizadores ou pior, eliminar todos os tokens em seu aplicativo. Isso é particularmente desastroso, se tiver aplicações que dependem dos tokens de trabalho de fundo. Partilhar um porta-chaves significa que precisa ter muito cuidado em todos os remove operações através de SDKs de identidade.

Já está! O SDK irá agora partilhar as credenciais em todas as suas aplicações. A lista de utilizador também será partilhada entre instâncias da aplicação.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO para broker assistido SSO

A capacidade para uma aplicação para utilizar qualquer Mediador de que está instalado no dispositivo é **desativada por predefinição**. Para utilizar a aplicação com o agente tem de fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Os passos a seguir são:

1. Ative o modo de Mediador na chamada de seu código de aplicativo para o SDK MS.
2. Estabelecer um novo URI de redirecionamento e fornecer que a aplicação e o registo de aplicação.
3. Registrando um esquema de URL.
4. Adicione uma permissão ao seu ficheiro info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passo 1: Ativar o modo de mediador em seu aplicativo

A capacidade para a sua aplicação utilizar o mediador é ativada ao criar o "contexto" ou a configuração inicial do seu objeto de autenticação. Fazê-lo ao definir o tipo de credenciais no seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
O `AD_CREDENTIALS_AUTO` definição permitirá que o SDK tentar chamar para o mediador, `AD_CREDENTIALS_EMBEDDED` impedirá o SDK de chamar no Mediador.

#### <a name="step-2-registering-a-url-scheme"></a>Passo 2: Registrando um esquema de URL

A plataforma de identidade usa URLs para invocar o agente e, em seguida, retornar o controle para a sua aplicação. Para concluir essa ida e volta tem um esquema de URL registado para a sua aplicação que irá conhecer a plataforma de identidade. Isso pode ser, além de outros esquemas de aplicação que pode tiver registado anteriormente com a sua aplicação.

> [!WARNING]
> Recomendamos que faça com que o esquema de URL ímpar para minimizar as chances de outra aplicação com o mesmo esquema de URL. Apple não impõe a exclusividade de esquemas de URL que são registados na app store.

Segue-se um exemplo de como é apresentado na sua configuração de projeto. Pode também fazer isso no XCode também:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passo 3: Estabelecer um novo URI com o esquema de URL de redirecionamento

Para garantir que estamos sempre retornar os tokens de credencial para a aplicação correta, é necessário para se certificar de que é chamada de retorno para a sua aplicação de uma forma que pode verificar o sistema operativo iOS. O sistema operativo iOS relatórios para os aplicativos de Mediador de Microsoft o ID do pacote do aplicativo chamá-lo. Isso não pode ter sido falsificado por um aplicativo de adesão. Portanto, podemos utilizá-la, juntamente com o URI do nosso aplicativo de Mediador para garantir que os tokens são devolvidos para a aplicação correta. É necessário estabelecer este URI, tanto de redirecionamento exclusivo na sua aplicação e definido como um URI de redirecionamento no nosso portal de programador.

Seu URI de redirecionamento tem de ser no formato adequado:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este tipo de redirecionamento URI tem de ser especificado no seu registo de aplicação utilizando o [portal do Azure](https://portal.azure.com/). Para obter mais informações sobre o registo de aplicação do Azure AD, consulte [integração com o Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passo 3a: Adicionar um URI de redirecionamento no seu portal de aplicação e o desenvolvimento para suportar a autenticação baseada em certificado

A autenticação baseada em certificado de suporte um segundo "msauth" precisa de estar registado na sua aplicação e o [portal do Azure](https://portal.azure.com/) para processar a autenticação de certificado se pretender adicionar esse suporte em seu aplicativo.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Passo 4: Adicionar um parâmetro de configuração para a sua aplicação

Utiliza a ADAL – canOpenURL: para verificar se o mediador é instalado no dispositivo. No iOS 9 no, a Apple bloqueado o que podem consultar esquemas de uma aplicação. Terá de adicionar "msauth" para a secção de LSApplicationQueriesSchemes do seu `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Configurar SSO!

Agora a identidade do SDK irá automaticamente tanto partilhar as credenciais em seus aplicativos e invocar o mediador, se estiver presente no dispositivo.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [único início de sessão do protocolo SAML](single-sign-on-saml-protocol.md)
