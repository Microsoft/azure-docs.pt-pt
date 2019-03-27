---
title: Durações de token configuráveis no Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar durações de tokens emitidos pelo Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b0242a8e3745a0014e5c2a1289ca2bc8c85c75
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484548"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Durações de token configuráveis no Azure Active Directory (pré-visualização)

Pode especificar o tempo de vida de um token emitido pelo Azure Active Directory (Azure AD). Pode definir durações de token para todas as aplicações na sua organização, para uma aplicação de (com várias organizações) de multi-inquilino ou para um principal de serviço específico na sua organização.

> [!IMPORTANT]
> Depois de ouvir dos clientes durante a pré-visualização, estamos a planear substituir esta funcionalidade com um novo recurso do Azure Active Directory condicional acesso.  Assim que o novo recurso estiver concluído, essa funcionalidade, eventualmente, será preterida após um período de notificação.  Se utilizar a política de duração do Token configuráveis, esteja preparado para mudar para a nova funcionalidade de acesso condicional quando estiver disponível. 

No Azure AD, um objeto de política representa um conjunto de regras são impostas em aplicativos individuais ou em todos os aplicativos numa organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que são aplicadas a objetos para o qual são atribuídos.

Pode designar uma política como a política predefinida para a sua organização. A política é aplicada a qualquer aplicação na organização, desde que não seja substituído por uma política com uma prioridade mais alta. Também pode atribuir uma política a aplicações específicas. A ordem de prioridade varia consoante o tipo de política.

> [!NOTE]
> Política de duração do token configuráveis não é suportada para o SharePoint Online.  Apesar de ter a capacidade de criar esta política através do PowerShell, SharePoint Online não reconhecerá esta política. Consulte a [blog SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre a configuração de tempos limite de sessão ociosa.
>* O tempo de vida padrão para o SharePoint Online token de acesso é de 1 hora. 
>* O padrão de tempo de inativo máx. do SharePoint Online token de atualização é 90 dias.

## <a name="token-types"></a>Tipos de token

Pode definir políticas de duração do token para tokens de atualização, os tokens de acesso, tokens de sessões e tokens de ID.

### <a name="access-tokens"></a>Tokens de acesso

Os clientes utilizam tokens de acesso para aceder a um recurso protegido. Um token de acesso pode ser utilizado apenas para uma combinação de utilizador, o cliente e o recurso. Tokens de acesso não não possível revogar e são válidos até sua expiração. Um ator mal-intencionado que foi obtido um token de acesso pode utilizá-lo para a extensão do seu ciclo de vida. Ajustar o tempo de vida de um token de acesso é uma compensação entre melhorando o desempenho do sistema e aumentar a quantidade de tempo que o cliente manterá acesso depois da conta de utilizador está desativada. Desempenho do sistema aprimorado é obtido ao reduzir o número de vezes que um cliente precisa de adquirir um token de acesso atualizada.  A predefinição é 1 hora - depois de 1 hora, o cliente tem de utilizar o token de atualização para adquirir um novo token de atualização e token de acesso (geralmente silenciosamente). 

### <a name="refresh-tokens"></a>Tokens de atualização

Quando um cliente adquire um token de acesso para aceder a um recurso protegido, o cliente também recebe um token de atualização. O token de atualização é utilizado para obter pares de token de acesso nova/atualização quando o token de acesso atual expira. Um token de atualização está vinculado a uma combinação de utilizador e de cliente. Pode ser um token de atualização [revogado em qualquer altura](access-tokens.md#token-revocation), e a validade do token é verificada sempre que o token é utilizado.  Atualizar os tokens não são revogados quando utilizados para obter novos tokens de acesso –, é melhor prática, no entanto, para eliminar em segurança o token antigo ao obter um novo. 

É importante fazer uma distinção entre clientes confidenciais e os clientes públicos, como este problema afeta o tempo que os tokens de atualização podem ser utilizados. Para obter mais informações sobre os diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durações de token com tokens de atualização de cliente confidencial
Clientes confidenciais são aplicações que podem armazenar em segurança uma palavra-passe de cliente (segredo). Eles podem se mostrar o que os pedidos são vem da aplicação cliente protegido e não a partir de um ator malicioso. Por exemplo, uma aplicação web é um cliente confidencial porque ele pode armazenar um segredo do cliente no servidor web. Não é exposto. Uma vez que estes fluxos são mais seguros, os tempos de vida do padrão de tokens de atualização emitidos para estes fluxos é `until-revoked`, não pode ser alterado utilizando a política e não vai ser revogada sobre reposições de palavra-passe voluntária.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durações de token com tokens de atualização de cliente público

Os clientes públicos não é possível armazenar com segurança uma palavra-passe de cliente (segredo). Por exemplo, uma aplicação do iOS/Android não pode ofuscar um segredo do proprietário do recurso, para que ele é considerado um cliente público. Pode definir políticas de recursos para impedir que os tokens de atualização de clientes públicos com mais de um período especificado obter um novo par de token de acesso/atualização. (Para tal, utilize a propriedade de atualizar Token inativo hora máxima (`MaxInactiveTime`).) Também pode utilizar políticas para definir um período para além dos quais os tokens de atualização já não são aceites. (Para tal, utilize a propriedade de atualizar o Token Max Age.) Pode ajustar o tempo de vida de um token de atualização para controlar quando e com que o utilizador tem de reintroduzir as credenciais, em vez de que está a ser reautenticar silenciosamente, ao utilizar uma aplicação cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são transmitidos para os Web sites e clientes nativos. Tokens de ID contêm informações de perfil sobre um utilizador. Um token de ID está vinculado a uma combinação de utilizador e de cliente. Tokens de ID são considerados válidos até sua expiração. Normalmente, um aplicativo web corresponde a um utilizador da duração da sessão na aplicação para o tempo de vida do token de ID emitido para o utilizador. Pode ajustar o tempo de vida de um token de ID para controlar a frequência com que o aplicativo web expira a sessão do aplicativo e a frequência com que ele requer que o usuário pode ser reautenticada com o Azure AD (silenciosamente ou interativamente).

### <a name="single-sign-on-session-tokens"></a>Tokens de sessões de início de sessão único
Quando um utilizador efetua a autenticação com o Azure AD, é estabelecida uma sessão de início de início de sessão única (SSO) com o navegador do usuário e o Azure AD. O token SSO, na forma de um cookie, representa esta sessão. Tenha em atenção que o token de sessão SSO não está vinculado a uma aplicação de cliente/recurso específico. Tokens de sessões SSO podem ser revogados e sua validade é verificada sempre que são utilizados.

Azure AD utiliza dois tipos de tokens de sessões SSO: nonpersistent e persistentes. Tokens de sessões persistentes são armazenadas como cookies persistentes pelo navegador. Tokens de sessões nonpersistent são armazenadas como cookies de sessão. (Cookies de sessão são destruídos quando o browser é fechado.) Normalmente, um token de sessão nonpersistent é armazenado. Mas, quando o utilizador seleciona o **manter sessão iniciada** caixa de verificação durante a autenticação, um token de sessão persistentes é armazenada.

Tokens de sessões nonpersistent têm uma duração de 24 horas. Persistentes tokens têm uma duração de 180 dias. Sempre que um token de sessão SSO é usado no período de validade, o período de validade é estendido outro 24 horas ou 180 dias, consoante o tipo de token. Se um token de sessão SSO não está a ser utilizado dentro do período de validade, ele é considerado a expirou e já não é aceite.

Pode utilizar uma política para definir o tempo após o token de sessão primeiro foi emitido para além de que o token de sessão já não é aceite. (Para tal, utilize a propriedade de idade de máxima Token de sessão). Pode ajustar o tempo de vida de um token de sessão para controlar quando e com que um utilizador tem de reintroduzir as credenciais, em vez de a ser autenticado silenciosamente, ao utilizar uma aplicação web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de duração do token
Uma política de duração do token é um tipo de objeto de política que contém as regras de duração do token. Utilize as propriedades da política para controlar as durações de token especificadas. Não se estiver definida nenhuma política, o sistema imponha o valor de duração predefinido.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de duração do token configurável
| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Duração do Token de acesso |AccessTokenLifetime |Tokens de acesso, os tokens de ID, SAML2 tokens |1 hora |10 minutos |1 dia |
| Atualizar Token máxima tempo inativo |MaxInactiveTime |Tokens de atualização |90 dias |10 minutos |90 dias |
| Idade de máxima Token de atualização de fator único |MaxAgeSingleFactor |Tokens (para todos os utilizadores) de atualização |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade máxima Token do multi-factor atualização |MaxAgeMultiFactor |Tokens (para todos os utilizadores) de atualização |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade de máxima Token de sessão de fator único |MaxAgeSessionSingleFactor<sup>2</sup> |Tokens de sessões (persistentes e nonpersistent) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade máxima Token do multi-factor sessão |MaxAgeSessionMultiFactor<sup>3</sup> |Tokens de sessões (persistentes e nonpersistent) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo de explícito que pode ser definido para esses atributos.

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Predefinição |
| --- | --- | --- |
| Atualizar o Token de máx. de idade (emitidos para utilizadores federados que tenham informações de revogação insuficiente<sup>1</sup>) |Tokens de atualização (emitidos para utilizadores federados que tenham informações de revogação insuficiente<sup>1</sup>) |12 horas |
| Atualizar Token hora máxima do inativo (emitidos para clientes confidenciais) |Tokens (emitidos para clientes confidenciais) de atualização |90 dias |
| Atualizar Token idade máxima (emitidos para clientes confidenciais) |Tokens (emitidos para clientes confidenciais) de atualização |Until-revoked |

* <sup>1</sup>utilizadores federados que tenham informações de revogação insuficiente incluem quaisquer utilizadores que não têm o atributo "LastPasswordChangeTimestamp" sincronizado. Esses usuários recebem este idade máxima curto porque AAD não conseguir verificar quando ao revogar os tokens que estão associados a uma credencial antiga (por exemplo, uma palavra-passe que foi alterada) e tem de verificar novamente no mais frequentemente para garantir que o utilizador e tokens associados são ainda na bom  permanente. Para melhorar esta experiência, administradores de inquilinos tem de garantir que estão a sincronizar o atributo "LastPasswordChangeTimestamp" (isso pode ser definido no objeto do usuário com o Powershell ou por meio de AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação da política e uma priorização
Pode criar e, em seguida, atribuir uma política de duração do token para uma aplicação específica, para a sua organização e para principais de serviço. Várias políticas podem ser aplicadas a uma aplicação específica. A política de duração do token que entra em vigor segue estas regras:

* Se uma política está atribuída explicitamente ao principal de serviço, é imposta.
* Se nenhuma política está atribuída explicitamente ao principal de serviço, será aplicada uma política explicitamente atribuída à organização principal do principal de serviço.
* Se nenhuma política está atribuída explicitamente ao principal de serviço ou para a organização, é imposta a política atribuída à aplicação.
* Se nenhuma política tiver sido atribuída para o principal de serviço, a organização ou o objeto de aplicativo, os valores predefinidos é imposta. (Consulte a tabela [propriedades de duração do token configuráveis](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre os objectos da aplicação e objetos de principal de serviço, consulte [aplicativos e objetos de principal de serviço no Azure Active Directory](app-objects-and-service-principals.md).

Validade de um token é avaliada no momento que o token é utilizado. A política com a prioridade mais alta na aplicação que está sendo acessada entra em vigor.

Todos os períodos de tempo utilizados aqui são formatados de acordo com o c# [TimeSpan](https://msdn.microsoft.com/library/system.timespan) objeto - D.HH:MM:SS.  Portanto, seria 80 dias e 30 minutos `80.00:30:00`.  O líder 1!d pode ser removido se zero, por isso, 90 minutos seria `00:90:00`.  

> [!NOTE]
> Eis um cenário de exemplo.
>
> Um usuário quiser acessar dois aplicativos web: Aplicação Web A e B. de aplicação Web
> 
> Fatores:
> * Ambas as aplicações web estão na mesma organização principal.
> * 1 token de política de duração com uma idade máxima Token de sessão do espaço de oito horas é definida como padrão da organização principal.
> * Aplicação Web A é um aplicativo web de utilização normal e não está ligada a todas as políticas.
> * B de aplicativo Web é utilizada para processos altamente confidenciais. Seu principal de serviço está associado ao Token 2 de política de ciclo de vida, que tem uma idade máxima Token de sessão de 30 minutos.
>
> Às 12:00, o usuário inicia uma nova sessão de navegador e tenta acessar a do aplicativo Web. O utilizador é redirecionado para o Azure AD e é-lhe pedido para iniciar sessão. Esta ação cria um cookie que tem um token de sessão no browser. O utilizador é redirecionado para a aplicação de Web A um token de ID, que permite ao utilizador aceder à aplicação.
>
> Em 12:15, o utilizador tenta aceder B. de aplicação Web Redireciona o navegador para o Azure AD, Deteta o cookie de sessão. Principal de serviço do B do aplicativo Web está ligada a 2 de política de duração do Token, mas também é parte da organização principal, com o padrão de 1 de política de duração do Token. 2 de política de duração do token entra em vigor porque políticas ligadas principais de serviço tem prioridade sobre as políticas de padrão de organização. O token de sessão foi emitido originalmente nos últimos 30 minutos, pelo que é considerado válido. O utilizador é redirecionado para o B de aplicação Web com um token de ID, que lhe concede acesso.
>
> Em 1:21 horas, o utilizador tenta aceder a do aplicativo Web. O utilizador é redirecionado para o Azure AD. A aplicação de Web não está ligado a todas as políticas, mas porque se trata de uma organização com o padrão de 1 de política de duração do Token, essa política entra em vigor. O cookie de sessão que foi originalmente emitiu nas últimas horas oito é detetado. O utilizador é redirecionado automaticamente para a aplicação de Web uma com um novo token de ID. O utilizador não é necessário para autenticar.
>
> Imediatamente depois disso, o utilizador tenta aceder B. de aplicação Web O utilizador é redirecionado para o Azure AD. Como antes, 2 de política de duração do Token entra em vigor. Uma vez que o token foi emitido há mais de 30 minutos, é pedido ao utilizador para introduzir as credenciais de início de sessão. Um token de sessão totalmente novo e o token de ID emitidos. O utilizador pode aceder ao B. de aplicação Web
>
>

## <a name="configurable-policy-property-details"></a>Detalhes de propriedade de diretriz configurável
### <a name="access-token-lifetime"></a>Duração do Token de acesso
**Cadeia de caracteres:** AccessTokenLifetime

**Afeta a:** Tokens de acesso, os tokens de ID

**Resumo:** Esta política controla quanto acesso e tokens de ID para este recurso são considerados válidos. Reduzindo a propriedade de duração do Token de acesso minimiza o risco de um token de acesso ou o token de ID que está a ser utilizado por um ator malicioso por um longo período de tempo. (Não não possível revogar esses tokens.) A desvantagem é que o desempenho é afetado negativamente, porque os tokens têm de ser substituído com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Atualizar Token máxima tempo inativo
**Cadeia de caracteres:** MaxInactiveTime

**Afeta a:** Tokens de atualização

**Resumo:** Esta política controla o antigo como um token de atualização pode ser antes de um cliente pode já não usá-lo a obter um novo par de token de acesso/atualização durante a tentativa de aceder a este recurso. Porque, normalmente, um novo token de atualização é retornado quando é utilizado um token de atualização, esta política impede o acesso se o cliente tenta aceder a qualquer recurso utilizando o token de atualização atual durante o período de tempo especificado.

Esta política força os usuários que não estão ativos no seu cliente para autenticar-se para obter um novo token de atualização.

A propriedade atualizar Token inativo hora máxima tem de ser definida para um valor inferior a idade de máx. de Token de fator único e as propriedades de multi-factor atualizar Token idade máxima.

### <a name="single-factor-refresh-token-max-age"></a>Idade de máxima Token de atualização de fator único
**Cadeia de caracteres:** MaxAgeSingleFactor

**Afeta a:** Tokens de atualização

**Resumo:** Este controlos de política quanto um utilizador pode utilizar um token de atualização para obter um novo par de token de acesso/atualização após a última autenticados com êxito ao utilizar apenas um único fator. Depois de um utilizador é autenticado e recebe um novo token de atualização, o utilizador pode utilizar o fluxo do token de atualização para o período de tempo especificado. (Isso é verdadeiro desde que o token de atualização atual não foi revogado e não resta não utilizadas por mais tempo do que o tempo inativo.) Nesse ponto, o utilizador é forçado a autenticar-se para receber um novo token de atualização.

Reduzindo a idade máxima força os usuários se autentiquem com mais frequência. Porque se considera que a autenticação de fator único menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que é igual ou inferior a propriedade de multi-factor atualizar Token idade máxima.

### <a name="multi-factor-refresh-token-max-age"></a>Idade máxima Token do multi-factor atualização
**Cadeia de caracteres:** MaxAgeMultiFactor

**Afeta a:** Tokens de atualização

**Resumo:** Este controlos de política quanto um utilizador pode utilizar um token de atualização para obter um novo par de token de acesso/atualização após a última autenticados com êxito através de vários fatores. Depois de um utilizador é autenticado e recebe um novo token de atualização, o utilizador pode utilizar o fluxo do token de atualização para o período de tempo especificado. (Isso é verdadeiro desde que o token de atualização atual não foi revogado e não é não utilizada por mais tempo do que o tempo inativo.) Nessa altura, os usuários são forçados a autenticar-se para receber um novo token de atualização.

Reduzindo a idade máxima força os usuários se autentiquem com mais frequência. Porque se considera que a autenticação de fator único menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que é igual ou superior à propriedade de fator único atualizar Token idade máxima.

### <a name="single-factor-session-token-max-age"></a>Idade de máxima Token de sessão de fator único
**Cadeia de caracteres:** MaxAgeSessionSingleFactor

**Afeta a:** Tokens de sessões (persistentes e nonpersistent)

**Resumo:** Este controlos de política quanto um utilizador pode utilizar um token de sessão para obter um novo ID e o token de sessão após a última autenticados com êxito ao utilizar apenas um único fator. Depois de um utilizador é autenticado e recebe um token de sessão de novo, o utilizador pode utilizar o fluxo do token de sessão para o período de tempo especificado. (Isso é verdadeiro desde que o token de sessão atual não foi revogado e não expirou.) Após o período de tempo especificado, o utilizador é forçado a autenticar-se para receber um token de sessão de novo.

Reduzindo a idade máxima força os usuários se autentiquem com mais frequência. Porque se considera que a autenticação de fator único menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que é igual ou menor que a propriedade de idade de máxima de Token do multi-factor sessão.

### <a name="multi-factor-session-token-max-age"></a>Idade máxima Token do multi-factor sessão
**Cadeia de caracteres:** MaxAgeSessionMultiFactor

**Afeta a:** Tokens de sessões (persistentes e nonpersistent)

**Resumo:** Este controlos de política quanto um utilizador pode utilizar um token de sessão para obter um novo ID e a sessão de token após a última vez que os utilizadores autenticados com êxito através de vários fatores. Depois de um utilizador é autenticado e recebe um token de sessão de novo, o utilizador pode utilizar o fluxo do token de sessão para o período de tempo especificado. (Isso é verdadeiro desde que o token de sessão atual não foi revogado e não expirou.) Após o período de tempo especificado, o utilizador é forçado a autenticar-se para receber um token de sessão de novo.

Reduzindo a idade máxima força os usuários se autentiquem com mais frequência. Porque se considera que a autenticação de fator único menos segura do que a autenticação multifator, recomendamos que defina esta propriedade para um valor que é igual ou superior à propriedade de idade de máxima de Token de sessão de fator único.

## <a name="example-token-lifetime-policies"></a>Políticas de duração do token de exemplo
Muitos cenários são possíveis no Azure AD quando pode criar e gerir durações de token para aplicações, os principais de serviço e sua organização geral. Nesta secção, vamos analisar alguns cenários comuns de política que podem ajudá-lo a impor novas regras para:

* Duração do Token
* Máx. de token de tempo inativo
* Idade máxima de token

Nos exemplos, pode saber como:

* Gerir a política predefinida de uma organização
* Criar uma política de web para início de sessão
* Criar uma política para um aplicativo nativo que chama uma API web
* Gerir uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, criar, atualizar, ligar e eliminar políticas para aplicações, os principais de serviço e sua organização geral. Se estiver familiarizado com o Azure AD, é recomendável que conheça [como obter um inquilino do Azure AD](quickstart-create-new-tenant.md) antes de continuar com estes exemplos.  

Para começar, siga os passos abaixo:

1. Baixe a versão mais recente [versão de pré-visualização do Azure AD PowerShell módulo público](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute o `Connect` comando para iniciar sessão na sua conta de administrador do Azure AD. Execute este comando sempre que iniciar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas na sua organização, execute o seguinte comando. Execute este comando após a maioria das operações nos seguintes cenários. Executar o comando também o ajuda a obter o * * * * das suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerir a política predefinida de uma organização
Neste exemplo, vai criar uma política que permita aos utilizadores iniciar sessão com menos frequência em toda a organização. Para tal, crie uma política de duração do token de fator único atualizar Tokens, que é aplicado em toda a organização. A política é aplicada a todas as aplicações na sua organização e a cada principal de serviço que já não tem uma política definida.

1. Crie uma política de duração do token.

    1.  Conjunto o Token de atualização de fator único "até-revogada." O token não expira antes do acesso é revogado. Crie a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Para criar a política, execute o seguinte comando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Para ver a nova política e para obter a política **ObjectId**, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Atualize a política.

    Pode decidir que a primeira diretiva definida neste exemplo não é tão strict à medida que necessita do seu serviço. Para definir o fator único atualizar Token para expirar dentro de dois dias, execute o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política de web para início de sessão

Neste exemplo, vai criar uma política que requer que os usuários se autentiquem com mais frequência na sua aplicação web. Esta política define o tempo de vida dos tokens de acesso/ID e a idade máxima de um token de sessão de multi-factor ao principal de serviço da sua aplicação web.

1. Crie uma política de duração do token.

    Esta política, web para início de sessão, define o acesso/ID de duração do token e a idade de token de sessão de fator único máximo de duas horas.

    1.  Para criar a política, execute este comando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e para obter a política **ObjectId**, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy
        ```

2.  Atribua a política para o seu principal de serviço. Também tem de obter o **ObjectId** do seu principal de serviço. 

    1.  Para ver os principais de serviço da sua organização, pode consultar o [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) ou o [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Além disso, pode testar isso no [do Azure AD Graph](https://graphexplorer.azurewebsites.net/)e o [de API do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) com a sua conta do Azure AD.

    2.  Quando tem o **ObjectId** do seu principal de serviço, execute o seguinte comando:

        ```powershell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para um aplicativo nativo que chama uma API web
Neste exemplo, vai criar uma política que requer que os utilizadores sejam autenticados com menos frequência. A política também lengthens a quantidade de tempo que um utilizador pode ficar inativo antes do utilizador tem de autenticar. A política é aplicada para a API web. Quando a aplicação nativa solicita a web API como um recurso, a política é aplicada.

1. Crie uma política de duração do token.

    1.  Para criar uma política estrita para uma API web, execute o seguinte comando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e para obter a política **ObjectId**, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Atribua a política para a API web. Também tem de obter o **ObjectId** da sua aplicação. A melhor forma de localizar seu aplicativo **ObjectId** consiste em utilizar o [portal do Azure](https://portal.azure.com/).

   Quando tem o **ObjectId** da sua aplicação, execute o seguinte comando:

        ```powershell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerir uma política avançada
Neste exemplo, vai criar algumas políticas, para saber como funciona o sistema de prioridades. Também pode saber como gerir múltiplas políticas que são aplicadas a vários objetos.

1. Crie uma política de duração do token.

    1.  Para criar uma política predefinida de organização que define o tempo de vida de fator único atualizar Token para 30 dias, execute o seguinte comando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e para obter a política **ObjectId**, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Atribua a política para um principal de serviço.

    Agora, tem uma política que aplica-se a toda a organização. Talvez queira preservar esta política de 30 dias para um principal de serviço específico, mas altere a política predefinida de organização para o limite superior de "até-revogado."

    1.  Para ver os principais de serviço da sua organização, pode consultar o [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) ou o [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Além disso, pode testar isso no [do Azure AD Graph](https://graphexplorer.azurewebsites.net/)e o [de API do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) com a sua conta do Azure AD.

    2.  Quando tem o **ObjectId** do seu principal de serviço, execute o seguinte comando:

            ```powershell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Definir o `IsOrganizationDefault` sinalizador como false:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Crie uma nova predefinição política da organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Tem agora a política original ligada ao seu principal de serviço e a nova política é definida como a sua política predefinida de organização. É importante lembrar-se de que as políticas aplicadas a principais de serviço têm prioridade sobre as políticas padrão de organização.

## <a name="cmdlet-reference"></a>Referência de cmdlets

### <a name="manage-policies"></a>Gerir políticas

Pode utilizar os seguintes cmdlets para gerir as políticas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Cria uma nova política.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matriz de JSON em cadeias de contentor que contém as regras da política. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |A cadeia de caracteres de nome de política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for VERDADEIRO, define a política como a política da organização padrão. Se for FALSO, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo de política. Para durações dos tokens, utilize sempre "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Define um ID alternativo para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas do Azure AD ou uma política especificada.

```powershell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcional] |**O ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todas as aplicações e os principais de serviço que estejam ligados a uma política.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** da política que pretende. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |A cadeia de caracteres de nome de política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcional] |Matriz de JSON em cadeias de contentor que contém as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcional] |Se for VERDADEIRO, define a política como a política da organização padrão. Se for FALSO, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcional] |Tipo de política. Para durações dos tokens, utilize sempre "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Define um ID alternativo para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Elimina a política especificada.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** da política que pretende. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas de aplicações
Pode utilizar os seguintes cmdlets para políticas de aplicações.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Ligações à política especificada para uma aplicação.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política que é atribuída a uma aplicação.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Remove uma política a partir de uma aplicação.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas de principal de serviço
Pode utilizar os seguintes cmdlets para políticas de principal de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Liga à política especificada para um principal de serviço.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém a qualquer política vinculada ao principal de serviço especificado.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política ao principal de serviço especificado.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**O ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |
