---
title: Aplicações wildcard no Proxy de Aplicação AD Azure
description: Saiba como utilizar aplicações Wildcard no proxy de aplicação do Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5861e802f39adecb5661bc17c22b432f137d59
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770293"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplicações wildcard no proxy de aplicação de diretório ativo Azure

No Azure Ative Directory (Azure AD), configurar um grande número de aplicações no local pode rapidamente tornar-se incontrolável e introduzir riscos desnecessários para erros de configuração se muitos deles necessitarem das mesmas configurações. Com o Procurador de [Aplicação AD Azure,](application-proxy.md)pode abordar este problema utilizando a publicação de aplicações wildcard para publicar e gerir muitas aplicações ao mesmo tempo. Esta é uma solução que lhe permite:

- Simplifique a sua despesa administrativa
- Reduzir o número de potenciais erros de configuração
- Permitir aos seus utilizadores aceder em segurança a mais recursos

Este artigo fornece-lhe a informação de que precisa para configurar a publicação de aplicações wildcard no seu ambiente.

## <a name="create-a-wildcard-application"></a>Criar uma aplicação wildcard

Pode criar uma aplicação wildcard (*) se tiver um grupo de aplicações com a mesma configuração. Os potenciais candidatos a uma aplicação wildcard são aplicações que partilham as seguintes definições:

- O grupo de utilizadores que têm acesso aos mesmos
- O método SSO
- O protocolo de acesso (http, https)

Pode publicar aplicações com wildcards se ambos, os URLs internos e externos estiverem no seguinte formato:

> http(s)://*. \<domínio\>

Por exemplo: `http(s)://*.adventure-works.com`.

Enquanto os URLs internos e externos podem usar diferentes domínios, como uma melhor prática, devem ser os mesmos. Ao publicar a aplicação, vê um erro se um dos URLs não tiver um wildcard.

A criação de uma aplicação wildcard baseia-se no mesmo fluxo de publicação de [aplicações](application-proxy-add-on-premises-application.md) que está disponível para todas as outras aplicações. A única diferença é que você inclui um wildcard nos URLs e potencialmente a configuração SSO.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que cumpriu estes requisitos.

### <a name="custom-domains"></a>Domínios personalizados

Embora os [domínios personalizados](application-proxy-configure-custom-domain.md) sejam opcionais para todas as outras aplicações, são um pré-requisito para aplicações wildcard. Criar domínios personalizados requer que:

1. Crie um domínio verificado dentro do Azure.
1. Faça o upload de um certificado TLS/SSL no formato PFX para o seu proxy de aplicação.

Deve considerar usar um certificado wildcard para corresponder à aplicação que pretende criar. Em alternativa, também pode utilizar um certificado que apenas lista aplicações específicas. Neste caso, apenas os pedidos listados no certificado estarão acessíveis através desta aplicação wildcard.

Por razões de segurança, este é um requisito difícil e não apoiaremos wildcards para aplicações que não podem usar um domínio personalizado para o URL externo.

### <a name="dns-updates"></a>Atualizações do DNS

Ao utilizar domínios personalizados, é necessário criar uma entrada DNS com um `*.adventure-works.com`registo CNAME para o URL externo (por exemplo, ) apontando para o URL externo do ponto final do proxy da aplicação. Para aplicações wildcard, o registo CNAME deve apontar para os URLs externos relevantes:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Para confirmar que configura corretamente o seu CNAME, pode utilizar o [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) num `expenses.adventure-works.com`dos pontos finais do alvo, por exemplo, .  A sua resposta deve incluir`<yourAADTenantId>.tenant.runtime.msappproxy.net`o pseudónimo já mencionado .

## <a name="considerations"></a>Considerações

Aqui ficam algumas considerações que deve ter em conta as aplicações wildcard.

### <a name="accepted-formats"></a>Formatos aceites

Para aplicações wildcard, o **URL** Interno `http(s)://*.<domain>`deve ser formatado como .

![Para URL interno, utilize o formato http(s)://*. \<> de domínio](./media/application-proxy-wildcard/22.png)

Quando configurar um **URL Externo,** deve utilizar o seguinte formato:`https://*.<custom domain>`

![Para URL externo, utilize o formato https://*. \<> de domínio personalizado](./media/application-proxy-wildcard/21.png)

Outras posições do wildcard, vários wildcards ou outras cordas regex não são suportadas e estão a causar erros.

### <a name="excluding-applications-from-the-wildcard"></a>Excluindo as aplicações do wildcard

Pode excluir uma aplicação da aplicação wildcard por

- Publicação do pedido de exceção como aplicação regular
- Ativar o wildcard apenas para aplicações específicas através das definições do DNS

Publicar uma aplicação como aplicação regular é o método preferido para excluir uma aplicação de um wildcard. Deve publicar as aplicações excluídas antes das aplicações wildcard para garantir que as suas exceções são aplicadas desde o início. A aplicação mais específica terá sempre precedência – uma aplicação publicada como `budgets.finance.adventure-works.com` tem precedência sobre a aplicação `*.finance.adventure-works.com`, que por sua vez tem precedência sobre a aplicação `*.adventure-works.com`.

Também pode limitar o wildcard a trabalhar apenas para aplicações específicas através da sua gestão DNS. Como uma boa prática, deve criar uma entrada CNAME que inclua um wildcard e corresponda ao formato do URL externo que configurado. No entanto, pode, em vez disso, apontar URLs de aplicação específicas para os wildcards. Por exemplo, `*.adventure-works.com`em `hr.adventure-works.com`vez `expenses.adventure-works.com` `travel.adventure-works.com individually` de `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`, apontar, e para .

Se utilizar esta opção, também precisa de `AppId.domain`outra entrada `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`CNAME para o valor , por exemplo, também apontando para a mesma localização. Pode encontrar o **AppId** na página de propriedades da aplicação da aplicação wildcard:

![Encontre o ID da aplicação na página de propriedade da aplicação](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Definição do URL da página inicial para o painel MyApps

A aplicação wildcard está representada com apenas um azulejo no [painel MyApps](https://myapps.microsoft.com). Por defeito, este azulejo está escondido. Para mostrar o azulejo e fazer com que os utilizadores aterrem numa página específica:

1. Siga as diretrizes para [definir um URL](application-proxy-configure-custom-home-page.md)de página inicial .
1. Definir **Mostrar Aplicação** **verdadeiramente** na página de propriedades da aplicação.

### <a name="kerberos-constrained-delegation"></a>Delegação restrita de Kerberos

Para aplicações que utilizem [a delegação limitada de Kerberos (KCD) como método SSO,](application-proxy-configure-single-sign-on-with-kcd.md)o SPN listado para o método SSO também pode necessitar de um wildcard. Por exemplo, o SPN `HTTP/*.adventure-works.com`pode ser: . Ainda precisa de ter as SPNs individuais configuradas nos `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`seus servidores de backend (por exemplo, ).

## <a name="scenario-1-general-wildcard-application"></a>Cenário 1: Aplicação geral wildcard

Neste cenário, tem três aplicações diferentes que pretende publicar:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Todas as três aplicações:

- São utilizados por todos os seus utilizadores
- Utilizar *autenticação integrada do Windows*
- Ter as mesmas propriedades

Pode publicar a aplicação wildcard utilizando os [passos descritos nas aplicações Publicar utilizando o Proxy de Aplicação AD Azure](application-proxy-add-on-premises-application.md). Este cenário pressupõe:

- Um inquilino com a seguinte identificação:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Um domínio verificado `adventure-works.com` chamado foi configurado.
- Foi criada uma `*.adventure-works.com` entrada `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` **CNAME** que aponta para a criação.

Seguindo as [etapas documentadas,](application-proxy-add-on-premises-application.md)cria uma nova aplicação de procuração de aplicação no seu inquilino. Neste exemplo, o wildcard está nos seguintes campos:

- URL interno:

    ![Exemplo: Wildcard em URL interno](./media/application-proxy-wildcard/42.png)

- URL externo:

    ![Exemplo: Wildcard em URL externo](./media/application-proxy-wildcard/43.png)

- Aplicação interna SPN:

    ![Exemplo: Wildcard na configuração SPN](./media/application-proxy-wildcard/44.png)

Ao publicar a aplicação wildcard, pode agora aceder às suas três aplicações navegando para `travel.adventure-works.com`os URLs a que está habituado (por exemplo, ).

A configuração implementa a seguinte estrutura:

![Mostra a estrutura implementada pela configuração do exemplo](./media/application-proxy-wildcard/05.png)

| Cor | Descrição |
| ---   | ---         |
| Azul  | Aplicações explicitamente publicadas e visíveis no portal Azure. |
| Cinzento  | Aplicações que pode ser acessível através da aplicação dos pais. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Cenário 2: Aplicação geral wildcard com exceção

Neste cenário, tem além das três aplicações `finance.adventure-works.com`gerais outra aplicação, que só deverá ser acessível pela divisão de Finanças. Com a estrutura de candidatura atual, a sua aplicação financeira estaria acessível através da aplicação wildcard e por todos os colaboradores. Para alterar isto, exclui a sua aplicação do seu wildcard configurando o Financiamento como uma aplicação separada com permissões mais restritivas.

Tem de se certificar de que existem registos CNAME que apontam `finance.adventure-works.com` para o ponto final específico da aplicação, especificado na página proxy de aplicação para a aplicação. Para este `finance.adventure-works.com` cenário, `https://finance-awcycles.msappproxy.net/`aponta para .

Seguindo as [etapas documentadas,](application-proxy-add-on-premises-application.md)este cenário requer as seguintes definições:

- No **URL Interno,** você define **finanças** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard em URL interno](./media/application-proxy-wildcard/52.png)

- No **URL Externo,** você define **finanças** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard em URL externo](./media/application-proxy-wildcard/53.png)

- Aplicação Interna SPN você define **financiamento** em vez de um wildcard.

    ![Exemplo: Definir financiamento em vez de um wildcard na configuração SPN](./media/application-proxy-wildcard/54.png)

Esta configuração implementa o seguinte cenário:

![Mostra a configuração implementada pelo cenário da amostra](./media/application-proxy-wildcard/09.png)

Porque `finance.adventure-works.com` é um URL `*.adventure-works.com`mais específico do que, tem precedência. Os utilizadores `finance.adventure-works.com` que navegam para ter a experiência especificada na aplicação Recursos Financeiros. Neste caso, só os trabalhadores `finance.adventure-works.com`das finanças podem aceder.

Se tiver várias aplicações publicadas `finance.adventure-works.com` para financiamento e tiver como domínio `*.finance.adventure-works.com`verificado, poderá publicar outra aplicação wildcard . Como isto é mais `*.adventure-works.com`específico do que o genérico, tem precedência se um utilizador aceder a uma aplicação no domínio financeiro.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre **domínios personalizados,** consulte [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).
- Para saber mais sobre **aplicações de publicação,** consulte [Publicar aplicações usando procuração de aplicação ad azure](application-proxy-add-on-premises-application.md)
