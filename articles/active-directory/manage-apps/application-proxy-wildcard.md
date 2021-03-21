---
title: Aplicações Wildcard no Azure AD Application Proxy
description: Saiba como utilizar as aplicações Wildcard no proxy de aplicação do Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2f3d1d47bd26167253296f06af5470818760850
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257985"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplicações Wildcard no proxy de aplicação do Azure Ative Directory

No Azure Ative Directory (Azure AD), configurar um grande número de aplicações no local pode rapidamente tornar-se incontrolável e introduz riscos desnecessários para erros de configuração se muitas delas exigirem as mesmas definições. Com [o Azure AD Application Proxy,](application-proxy.md)pode resolver este problema utilizando a publicação de aplicações wildcard para publicar e gerir muitas aplicações ao mesmo tempo. Esta é uma solução que lhe permite:

- Simplifique as suas despesas administrativas
- Reduzir o número de erros de configuração potenciais
- Capacitar os seus utilizadores a acederem de forma segura a mais recursos

Este artigo fornece-lhe as informações necessárias para configurar a publicação de aplicações wildcard no seu ambiente.

## <a name="create-a-wildcard-application"></a>Criar uma aplicação wildcard

Pode criar uma aplicação wildcard (*) se tiver um grupo de aplicações com a mesma configuração. Os potenciais candidatos a uma aplicação wildcard são as aplicações que partilham as seguintes definições:

- O grupo de utilizadores que têm acesso a eles
- O método SSO
- O protocolo de acesso (https)

Pode publicar aplicações com wildcards se ambos, os URLs internos e externos estiverem no seguinte formato:

> http://*.\<domain\>

Por exemplo: `http(s)://*.adventure-works.com`.

Embora os URLs internos e externos possam utilizar diferentes domínios, como uma boa prática, devem ser os mesmos. Ao publicar a aplicação, vê-se um erro se um dos URLs não tiver um wildcard.

A criação de uma aplicação wildcard baseia-se no mesmo [fluxo de publicação de aplicações](application-proxy-add-on-premises-application.md) que está disponível para todas as outras aplicações. A única diferença é que você inclui um wildcard nos URLs e potencialmente a configuração SSO.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de ter cumprido estes requisitos.

### <a name="custom-domains"></a>Domínios personalizados

Embora [os domínios personalizados](application-proxy-configure-custom-domain.md) sejam opcionais para todas as outras aplicações, são um pré-requisito para aplicações wildcard. A criação de domínios personalizados requer que:

1. Crie um domínio verificado dentro do Azure.
1. Faça o upload de um certificado TLS/SSL no formato PFX para o seu representante de aplicação.

Deve considerar a utilização de um certificado wildcard para corresponder à aplicação que pretende criar. 

Por razões de segurança, este é um requisito difícil e não apoiaremos wildcards para aplicações que não podem usar um domínio personalizado para o URL externo.

### <a name="dns-updates"></a>Atualizações de DNS

Ao utilizar domínios personalizados, é necessário criar uma entrada de DNS com um registo CNAME para o URL externo (por exemplo,  `*.adventure-works.com` ) que indique o URL externo do ponto final do proxy de aplicação. Para aplicações wildcard, o registo CNAME precisa de apontar para os URLs externos relevantes:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Para confirmar que configura o seu CNAME corretamente, pode utilizar [o nslookup](/windows-server/administration/windows-commands/nslookup) num dos pontos finais do alvo, por exemplo, `expenses.adventure-works.com` .  A sua resposta deve incluir o pseudónimo já mencionado `<yourAADTenantId>.tenant.runtime.msappproxy.net` .

## <a name="considerations"></a>Considerações

Aqui estão algumas considerações que deve ter em conta para aplicações wildcard.

### <a name="accepted-formats"></a>Formatos aceites

Para aplicações wildcard, o **URL Interno** deve ser formatado como `http(s)://*.<domain>` .

![Para URL interno, utilize o formato http(s)://*. \<> de domínio](./media/application-proxy-wildcard/22.png)

Ao configurar um **URL externo,** deve utilizar o seguinte formato: `https://*.<custom domain>`

![Para URL externo, utilize o formato https://*. \<> de domínio personalizado](./media/application-proxy-wildcard/21.png)

Outras posições do wildcard, vários wildcards ou outras cordas regex não são suportadas e estão a causar erros.

### <a name="excluding-applications-from-the-wildcard"></a>Excluindo as aplicações do wildcard

Pode excluir uma aplicação da aplicação wildcard por

- Publicar o pedido de exceção como aplicação regular
- Ativar o wildcard apenas para aplicações específicas através das definições de DNS

Publicar uma aplicação como aplicação regular é o método preferido para excluir uma aplicação de um wildcard. Deve publicar as aplicações excluídas antes das aplicações wildcard para garantir que as suas exceções sejam aplicadas desde o início. A aplicação mais específica terá sempre precedência – uma aplicação publicada como `budgets.finance.adventure-works.com` prevalece sobre a aplicação , que por `*.finance.adventure-works.com` sua vez tem precedência sobre a aplicação `*.adventure-works.com` .

Também pode limitar o wildcard a apenas trabalhar para aplicações específicas através da sua gestão de DNS. Como uma boa prática, deve criar uma entrada CNAME que inclua um wildcard e corresponda ao formato do URL externo que configuraste. No entanto, em vez disso, pode apontar URLs de aplicação específicos para os wildcards. Por exemplo, em vez de `*.adventure-works.com` , apontar , e para `hr.adventure-works.com` `expenses.adventure-works.com` `travel.adventure-works.com individually` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` .

Se utilizar esta opção, também precisa de outra entrada CNAME para o valor `AppId.domain` , por `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com` exemplo, também apontando para o mesmo local. Pode encontrar o **AppId** na página de propriedades da aplicação wildcard:

![Encontre o ID da aplicação na página de propriedade da aplicação](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Definição do URL de página inicial para o painel MyApps

A aplicação wildcard está representada com apenas um azulejo no [painel MyApps.](https://myapps.microsoft.com) Por defeito, este azulejo está escondido. Para mostrar o azulejo e fazer com que os utilizadores aterrem numa página específica:

1. Siga as diretrizes para [a definição de URL de página inicial](application-proxy-configure-custom-home-page.md).
1. Definir **a Aplicação do Show** para ser **verdadeira** na página de propriedades da aplicação.

### <a name="kerberos-constrained-delegation"></a>Kerberos constrangido delegação

Para aplicações que utilizem [kerberos delegação restrita (KCD) como o método SSO,](application-proxy-configure-single-sign-on-with-kcd.md)o SPN listado para o método SSO também pode precisar de um wildcard. Por exemplo, o SPN pode ser: `HTTP/*.adventure-works.com` . Ainda precisa de ter as SPNs individuais configuradas nos seus servidores de backend (por exemplo, `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com` ).

## <a name="scenario-1-general-wildcard-application"></a>Cenário 1: Aplicação geral wildcard

Neste cenário, tem três aplicações diferentes que pretende publicar:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Todas as três aplicações:

- São usados por todos os seus utilizadores
- Utilizar *autenticação integrada do Windows*
- Ter as mesmas propriedades

Pode publicar a aplicação wildcard utilizando os passos descritos nas [aplicações de publicação utilizando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md). Este cenário pressupõe:

- Um inquilino com a seguinte iD: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Um domínio verificado chamado `adventure-works.com` foi configurado.
- Foi criada uma entrada **CNAME** que aponta `*.adventure-works.com` para a `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` criação.

Seguindo os [passos documentados,](application-proxy-add-on-premises-application.md)cria um novo pedido de procuração de candidatura no seu inquilino. Neste exemplo, o wildcard está nos seguintes campos:

- URL interno:

    ![Exemplo: Wildcard em URL interno](./media/application-proxy-wildcard/42.png)

- URL externo:

    ![Exemplo: Wildcard em URL externo](./media/application-proxy-wildcard/43.png)

- Aplicação Interna SPN:

    ![Exemplo: Wildcard na configuração SPN](./media/application-proxy-wildcard/44.png)

Ao publicar a aplicação wildcard, pode agora aceder às suas três aplicações navegando para os URLs a que está habituado (por exemplo, `travel.adventure-works.com` ).

A configuração implementa a seguinte estrutura:

![Mostra a estrutura implementada pela configuração do exemplo](./media/application-proxy-wildcard/05.png)

| Cor | Description |
| ---   | ---         |
| Blue  | Aplicações explicitamente publicadas e visíveis no portal Azure. |
| Cinzento  | Aplicações que pode ser acessível através da aplicação dos pais. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Cenário 2: Aplicação geral wildcard com exceção

Neste cenário, tem para além das três candidaturas gerais outra candidatura, `finance.adventure-works.com` que só deverá ser acessível pela Divisão de Finanças. Com a atual estrutura de candidatura, a sua aplicação financeira seria acessível através da aplicação wildcard e de todos os colaboradores. Para alterar isto, exclui a sua aplicação do seu wildcard configurando o Finance como uma aplicação separada com permissões mais restritivas.

É necessário certificar-se de que existem registos CNAME que apontam `finance.adventure-works.com` para o ponto final específico da aplicação, especificado na página de Procuração de Aplicação para a aplicação. Para este cenário, `finance.adventure-works.com` aponta para `https://finance-awcycles.msappproxy.net/` .

Seguindo as [etapas documentadas,](application-proxy-add-on-premises-application.md)este cenário requer as seguintes definições:

- Na **URL Interna,** define-se **o financiamento** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard em URL interno](./media/application-proxy-wildcard/52.png)

- Na **URL Externa,** define-se **o financiamento** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard em URL externo](./media/application-proxy-wildcard/53.png)

- Aplicação Interna SPN você define **financiamento** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard na configuração SPN](./media/application-proxy-wildcard/54.png)

Esta configuração implementa o seguinte cenário:

![Mostra a configuração implementada pelo cenário da amostra](./media/application-proxy-wildcard/09.png)

Porque `finance.adventure-works.com` é uma URL mais específica do `*.adventure-works.com` que, tem precedência. Os utilizadores que navegam para `finance.adventure-works.com` ter a experiência especificada na aplicação Recursos Financeiros. Neste caso, apenas os funcionários das finanças podem `finance.adventure-works.com` aceder.

Se tiver várias aplicações publicadas para financiamento e tiver `finance.adventure-works.com` como domínio verificado, poderá publicar outra aplicação wildcard `*.finance.adventure-works.com` . Uma vez que isto é mais específico do que o `*.adventure-works.com` genérico, tem precedência se um utilizador acede a uma aplicação no domínio financeiro.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre **domínios personalizados,** consulte [Trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Para saber mais sobre **as aplicações de publicação,** consulte [publicar aplicações usando a Azure AD Application Proxy](application-proxy-add-on-premises-application.md)