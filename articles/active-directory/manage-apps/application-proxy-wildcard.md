---
title: Aplicações de caráter universal no proxy de aplicações do Azure Active Directory | Documentos da Microsoft
description: Saiba como utilizar aplicações de caráter universal no proxy de aplicações do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fb57052cc550a659dcd8a514fbb71ad0f7d8df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442494"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplicações de caráter universal no proxy de aplicações do Azure Active Directory 

No Azure Active Directory (Azure AD), configurar um grande número de locais aplicativos podem rapidamente tornar-se impossível de gerenciar e apresenta os riscos desnecessários para erros de configuração se muitos deles exigirem as mesmas definições. Com o [Proxy de aplicações do Azure AD](application-proxy.md), pode solucionar este problema através de publicação de aplicações de caráter universal para publicar e gerir vários aplicativos ao mesmo tempo. Esta é uma solução que lhe permite:

-   Simplificar o overhead administrativo
-   Reduzir o número de potenciais erros de configuração
-   Permitir que os utilizadores acedam em segurança às mais recursos

Este artigo fornece as informações necessárias configurar a publicação de aplicações de caráter universal no seu ambiente.


## <a name="create-a-wildcard-application"></a>Criar uma aplicação de caráter universal 

Pode criar uma aplicação de caráter universal (*), se tiver um grupo de aplicações com a mesma configuração. Possíveis candidatos para a aplicação de caráter universal são as seguintes definições de partilha de aplicações:

- O grupo de utilizadores que têm acesso aos mesmos
- O método SSO
- O protocolo de acesso (http, https)

Pode publicar aplicações com carateres universais se tanto, os URLs internos e externos são no seguinte formato:

> http(s)://*.\<domain\>

Por exemplo: `http(s)://*.adventure-works.com`. Enquanto os URLs internos e externos podem utilizar domínios diferentes, como melhor prática, deve ser igual. Quando a publicação da aplicação, verá um erro se um dos URLs de não tiver um caráter universal.

Se tiver aplicações adicionais com diferentes definições de configuração, tem de publicar essas exceções como aplicações separadas para substituir as configurações padrão para o caráter universal. Aplicativos sem um caráter universal sempre têm precedência sobre aplicações de caráter universal. Da perspectiva do configuração, são aplicativos "apenas" regulares.

Criar uma aplicação de caráter universal baseia-se no mesmo [fluxo de publicação de aplicativo](application-proxy-add-on-premises-application.md) que está disponível para todas as outras aplicações. A única diferença é que inclua um caráter universal nos URLs e, potencialmente, a configuração de SSO.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="custom-domains"></a>Domínios personalizados

Embora [domínios personalizados](application-proxy-configure-custom-domain.md) são opcionais para todas as outras aplicações, são um pré-requisito para aplicações de caráter universal. Criar domínios personalizados requer que:

1. Criar um domínio verificado no Azure 
2. Carregar um certificado SSL no formato PFX para o proxy de aplicações.

Deve considerar a utilização de um certificado de caráter universal para corresponder a aplicação que pretende criar. Em alternativa, também pode utilizar um certificado que apenas apresenta uma lista de determinados aplicativos. Neste caso, apenas as aplicações listadas no certificado estará acessíveis por meio desta aplicação de caráter universal.

Por motivos de segurança, este é um requisito rígido e iremos não suportar carateres universais para aplicações que não é possível utilizar um domínio personalizado para o URL externo.

### <a name="dns-updates"></a>Atualizações de DNS

Ao utilizar domínios personalizados, precisa criar uma entrada DNS com um registo CNAME para o URL externo (por exemplo, `*.adventure-works.com`) que aponta para o URL externo do ponto de extremidade do proxy de aplicação. Para aplicações de caráter universal, o registo CNAME tem de apontar para os URLs externos relevantes:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Para confirmar que tiver configurado o CNAME corretamente, pode usar [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) dos pontos finais de destino, por exemplo, `expenses.adventure-works.com`.  A resposta deve incluir o alias já foi mencionado (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Considerações


### <a name="accepted-formats"></a>Formatos aceites

Para aplicações de caráter universal, o **URL interno** têm de ser formatados como `http(s)://*.<domain>`. 

![appId](./media/application-proxy-wildcard/22.png)


Quando configura uma **URL externo**, tem de utilizar o seguinte formato: `https://*.<custom domain>` 

![appId](./media/application-proxy-wildcard/21.png)

Outras posições do caráter universal, vários carateres universais ou outras cadeias de caracteres de regex não são suportadas e estão a causar erros.


### <a name="excluding-applications-from-the-wildcard"></a>Excluir aplicativos do caráter universal

Pode excluir um aplicativo da aplicação de caráter universal por

- Publicação da aplicação de exceção como aplicativo regular 
- Ativar o caráter universal apenas para aplicações específicas, por meio de suas definições de DNS  


Publicar uma aplicação como aplicativo comum é o método preferencial para excluir uma aplicação a partir de um caráter universal. Deve publicar as aplicações excluídas antes das aplicações de caráter universal para garantir que suas exceções são impostas desde o início. O aplicativo mais específico sempre irá ter precedência – uma aplicação publicada como `budgets.finance.adventure-works.com` terão precedência sobre o aplicativo `*.finance.adventure-works.com`, que por sua vez, tem precedência sobre o aplicativo `*.adventure-works.com`. 

Também pode limitar o caráter universal só funcionar para aplicações específicas através da sua gestão de DNS. Como melhor prática, deve criar uma entrada CNAME, que inclui um caráter universal e corresponde ao formato do URL externo que configurou. No entanto, em vez disso, pode apontar URLs de aplicação específica para os carateres universais. Por exemplo, em vez de `*.adventure-works.com`, aponte `hr.adventure-works.com`, `expenses.adventure-works.com` e `travel.adventure-works.com individually` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Se utilizar esta opção, também precisa de outra entrada CNAME para o valor `AppId.domain`, por exemplo, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, também apontando para a mesma localização. Pode encontrar os **AppId** na página de propriedades do aplicativo do aplicativo com carateres universais:

![appId](./media/application-proxy-wildcard/01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Definir o URL da página para o painel de My Apps

A aplicação de caráter universal é representada com apenas um mosaico no [MyApps painel](https://myapps.microsoft.com). Por predefinição, este mosaico está oculta. Para mostrar o mosaico e ter ' s land de usuários numa página específica:

1. Siga as diretrizes para [definir um URL da home page](application-proxy-configure-custom-home-page.md).
2. Definir **Mostrar aplicativo** para **verdadeiro** na página de propriedades do aplicativo.

### <a name="kerberos-constrained-delegation"></a>Delegação restringida de Kerberos

Para aplicações utilizando [restrita de kerberos (KCD) de delegação como o método SSO](application-proxy-configure-single-sign-on-with-kcd.md), o SPN listados para o método SSO também poderá ser necessário um caráter universal. Por exemplo, o SPN pode ser: `HTTP/*.adventure-works.com`. Ainda tem de ter os SPNs individuais configurados nos seus servidores de back-end (por exemplo, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Cenário 1: Aplicação de caráter universal de geral

Neste cenário, tem três diferentes aplicações que pretende publicar:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Todos os aplicativos de três:

- São utilizados por todos os seus utilizadores
- Utilize *autenticação Windows integrada* 
- Ter as mesmas propriedades


Pode publicar a aplicação de caráter universal com os passos descritos em [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md). Este cenário pressupõe:

- Um inquilino com o seguinte ID: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Chamada de um domínio verificado `adventure-works.com` foi configurado.

- R **CNAME** entrada que aponta `*.adventure-works.com` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` foi criado.

A seguir a [documentado passos](application-proxy-add-on-premises-application.md), criar uma nova aplicação de proxy de aplicação no seu inquilino. Neste exemplo, o caráter universal é nos campos a seguir:

- URL interno:

    ![URL interno](./media/application-proxy-wildcard/42.png)


- URL externo:

    ![URL externo](./media/application-proxy-wildcard/43.png)

 
- SPN da aplicação interna: 

    ![Configuração do SPN](./media/application-proxy-wildcard/44.png)


Ao publicar a aplicação de caráter universal, agora pode aceder a seus aplicativos de três ao navegar para os URLs que está habituado (por exemplo, `travel.adventure-works.com`).

A configuração implementa a seguinte estrutura:

![appId](./media/application-proxy-wildcard/05.png)

| Cor | Descrição |
| ---   | ---         |
| Azul  | Aplicações explicitamente publicadas e visíveis no Portal do Azure. |
| Cinzento  | Aplicativos que pode acessíveis através da aplicação principal. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Cenário 2: Aplicação de caráter universal de geral com exceção

Neste cenário, precisa além do mais três aplicações de gerais outro aplicativo, `finance.adventure-works.com`, que só deve ser acessível a divisão de finanças. Com a estrutura de aplicação atual, o seu aplicativo de finanças estariam acessível através da aplicação de caráter universal e por todos os funcionários. Para alterar isso, excluir a aplicação da sua com carateres universais ao configurar Finanças como um aplicativo separado com permissões mais restritivas.



Tem de certificar-se de que os registos CNAME existem qual aponta `finance.adventure-works.com` para o aplicativo ponto final específico, especificado na página de Proxy de aplicações para a aplicação. Para este cenário `finance.adventure-works.com` aponta para `https://finance-awcycles.msappproxy.net/`. 

A seguir a [documentado passos](application-proxy-add-on-premises-application.md), este cenário requer as seguintes definições:


- Na **URL interno**, defina **Finanças** em vez de um caráter universal. 

    ![URL interno](./media/application-proxy-wildcard/52.png)

- Na **URL externo**, defina **Finanças** em vez de um caráter universal. 

    ![URL externo](./media/application-proxy-wildcard/53.png)

- Definir o SPN da aplicação interna **Finanças** em vez de um caráter universal.

    ![Configuração do SPN](./media/application-proxy-wildcard/54.png)


Esta configuração implementa o seguinte cenário:

![Cenário](./media/application-proxy-wildcard/09.png)

Uma vez `finance.adventure-works.com` é um URL mais específico que `*.adventure-works.com`, ela terá precedência. Os usuários a navegar para `finance.adventure-works.com` ter a experiência especificada no aplicativo de recursos de finanças. Neste caso, apenas os funcionários do departamento financeiro conseguem aceder `finance.adventure-works.com`.

Se tiver várias aplicações publicadas para finanças e tiver `finance.adventure-works.com` como um domínio verificado, conseguiu publicar outra aplicação de caráter universal `*.finance.adventure-works.com`. Como se trata mais específica que genérica `*.adventure-works.com`, ela terá precedência, se um utilizador acede a uma aplicação no domínio financeiro.


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre:

- **Domínios personalizados**, consulte [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md).

- **Publicação de aplicações**, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md)


