---
title: Aplicativos curinga no Proxy de Aplicativo do AD do Azure
description: Saiba como utilizar aplicações de caráter universal no proxy de aplicações do Azure Active Directory.
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
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275035"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplicações de caráter universal no proxy de aplicações do Azure Active Directory

No Azure Active Directory (Azure AD), configurar um grande número de locais aplicativos podem rapidamente tornar-se impossível de gerenciar e apresenta os riscos desnecessários para erros de configuração se muitos deles exigirem as mesmas definições. Com o [Azure proxy de aplicativo do AD](application-proxy.md), você pode resolver esse problema usando a publicação de aplicativos curinga para publicar e gerenciar vários aplicativos de uma só vez. Esta é uma solução que lhe permite:

- Simplificar o overhead administrativo
- Reduzir o número de potenciais erros de configuração
- Permitir que os utilizadores acedam em segurança às mais recursos

Este artigo fornece as informações necessárias configurar a publicação de aplicações de caráter universal no seu ambiente.

## <a name="create-a-wildcard-application"></a>Criar uma aplicação de caráter universal

Pode criar uma aplicação de caráter universal (*), se tiver um grupo de aplicações com a mesma configuração. Possíveis candidatos para a aplicação de caráter universal são as seguintes definições de partilha de aplicações:

- O grupo de utilizadores que têm acesso aos mesmos
- O método SSO
- O protocolo de acesso (http, https)

Pode publicar aplicações com carateres universais se tanto, os URLs internos e externos são no seguinte formato:

> http (s)://*.\> de domínio\<

Por exemplo: `http(s)://*.adventure-works.com`.

Enquanto os URLs internos e externos podem utilizar domínios diferentes, como melhor prática, deve ser igual. Quando a publicação da aplicação, verá um erro se um dos URLs de não tiver um caráter universal.

Se tiver aplicações adicionais com diferentes definições de configuração, tem de publicar essas exceções como aplicações separadas para substituir as configurações padrão para o caráter universal. Aplicativos sem um caráter universal sempre têm precedência sobre aplicações de caráter universal. Da perspectiva do configuração, são aplicativos "apenas" regulares.

A criação de um aplicativo curinga é baseada no mesmo [fluxo de publicação de aplicativos](application-proxy-add-on-premises-application.md) que está disponível para todos os outros aplicativos. A única diferença é que inclua um caráter universal nos URLs e, potencialmente, a configuração de SSO.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, verifique se você atendeu a esses requisitos.

### <a name="custom-domains"></a>Domínios personalizados

Embora os [domínios personalizados](application-proxy-configure-custom-domain.md) sejam opcionais para todos os outros aplicativos, eles são pré-requisitos para aplicativos curinga. Criar domínios personalizados requer que:

1. Crie um domínio verificado no Azure.
1. Carregar um certificado SSL no formato PFX para o proxy de aplicações.

Deve considerar a utilização de um certificado de caráter universal para corresponder a aplicação que pretende criar. Em alternativa, também pode utilizar um certificado que apenas apresenta uma lista de determinados aplicativos. Neste caso, apenas as aplicações listadas no certificado estará acessíveis por meio desta aplicação de caráter universal.

Por motivos de segurança, este é um requisito rígido e iremos não suportar carateres universais para aplicações que não é possível utilizar um domínio personalizado para o URL externo.

### <a name="dns-updates"></a>Atualizações de DNS

Ao usar domínios personalizados, você precisa criar uma entrada DNS com um registro CNAME para a URL externa (por exemplo, `*.adventure-works.com`) apontando para a URL externa do ponto de extremidade do proxy de aplicativo. Para aplicativos curinga, o registro CNAME precisa apontar para as URLs externas relevantes:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Para confirmar que você configurou o CNAME corretamente, você pode usar o [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) em um dos pontos de extremidade de destino, por exemplo, `expenses.adventure-works.com`.  Sua resposta deve incluir o alias já mencionado (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Considerações

Aqui estão algumas considerações que você deve levar em conta para aplicativos curinga.

### <a name="accepted-formats"></a>Formatos aceites

Para aplicativos curinga, a **URL interna** deve ser formatada como `http(s)://*.<domain>`.

![Para URL interna, use o formato http (s)://*. > de domínio\<](./media/application-proxy-wildcard/22.png)

Ao configurar uma **URL externa**, você deve usar o seguinte formato: `https://*.<custom domain>`

![Para URL externa, use o formato https://*.\<> de domínio personalizado](./media/application-proxy-wildcard/21.png)

Outras posições do caráter universal, vários carateres universais ou outras cadeias de caracteres de regex não são suportadas e estão a causar erros.

### <a name="excluding-applications-from-the-wildcard"></a>Excluir aplicativos do caráter universal

Pode excluir um aplicativo da aplicação de caráter universal por

- Publicação da aplicação de exceção como aplicativo regular
- Ativar o caráter universal apenas para aplicações específicas, por meio de suas definições de DNS

Publicar uma aplicação como aplicativo comum é o método preferencial para excluir uma aplicação a partir de um caráter universal. Deve publicar as aplicações excluídas antes das aplicações de caráter universal para garantir que suas exceções são impostas desde o início. O aplicativo mais específico sempre terá precedência – um aplicativo publicado como `budgets.finance.adventure-works.com` tem precedência sobre o `*.finance.adventure-works.com`do aplicativo, que, por sua vez, tem precedência sobre a `*.adventure-works.com`do aplicativo.

Também pode limitar o caráter universal só funcionar para aplicações específicas através da sua gestão de DNS. Como melhor prática, deve criar uma entrada CNAME, que inclui um caráter universal e corresponde ao formato do URL externo que configurou. No entanto, em vez disso, pode apontar URLs de aplicação específica para os carateres universais. Por exemplo, em vez de `*.adventure-works.com`, aponte `hr.adventure-works.com`, `expenses.adventure-works.com` e `travel.adventure-works.com individually` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Se você usar essa opção, também precisará de outra entrada CNAME para o valor `AppId.domain`, por exemplo, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, também apontando para o mesmo local. Você pode encontrar o **AppID** na página de propriedades do aplicativo curinga:

![Localizar a ID do aplicativo na página de propriedades do aplicativo](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Definir o URL da página para o painel de My Apps

O aplicativo curinga é representado com apenas um bloco no [painel myapps](https://myapps.microsoft.com). Por predefinição, este mosaico está oculta. Para mostrar o mosaico e ter ' s land de usuários numa página específica:

1. Siga as diretrizes para [definir uma URL da Home Page](application-proxy-configure-custom-home-page.md).
1. Defina **Mostrar aplicativo** como **verdadeiro** na página de propriedades do aplicativo.

### <a name="kerberos-constrained-delegation"></a>Delegação restringida de Kerberos

Para aplicativos que usam a [KCD (delegação restrita de Kerberos) como o método SSO](application-proxy-configure-single-sign-on-with-kcd.md), o SPN listado para o método SSO também pode precisar de um curinga. Por exemplo, o SPN pode ser: `HTTP/*.adventure-works.com`. Você ainda precisa ter os SPNs individuais configurados em seus servidores de back-end (por exemplo, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Cenário 1: Aplicação de caráter universal de geral

Neste cenário, tem três diferentes aplicações que pretende publicar:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Todos os aplicativos de três:

- São utilizados por todos os seus utilizadores
- Usar a *autenticação integrada do Windows*
- Ter as mesmas propriedades

Você pode publicar o aplicativo curinga usando as etapas descritas em [publicar aplicativos usando o Azure proxy de aplicativo do AD](application-proxy-add-on-premises-application.md). Este cenário pressupõe:

- Um locatário com a seguinte ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Um domínio verificado chamado `adventure-works.com` foi configurado.
- Uma entrada **CNAME** que aponta `*.adventure-works.com` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` foi criada.

Seguindo as [etapas documentadas](application-proxy-add-on-premises-application.md), você cria um novo aplicativo de proxy de aplicativo em seu locatário. Neste exemplo, o caráter universal é nos campos a seguir:

- URL interno:

    ![Exemplo: curinga na URL interna](./media/application-proxy-wildcard/42.png)

- URL externo:

    ![Exemplo: curinga na URL externa](./media/application-proxy-wildcard/43.png)

- SPN da aplicação interna:

    ![Exemplo: curinga na configuração de SPN](./media/application-proxy-wildcard/44.png)

Ao publicar o aplicativo curinga, agora você pode acessar seus três aplicativos navegando até as URLs às quais você está acostumado (por exemplo, `travel.adventure-works.com`).

A configuração implementa a seguinte estrutura:

![Mostra a estrutura implementada pela configuração de exemplo](./media/application-proxy-wildcard/05.png)

| Cor | Descrição |
| ---   | ---         |
| Azul  | Aplicativos publicados explicitamente e visíveis no portal do Azure. |
| Cinzento  | Aplicativos que pode acessíveis através da aplicação principal. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Cenário 2: Aplicação de caráter universal de geral com exceção

Nesse cenário, você tem, além dos três aplicativos gerais, outro aplicativo, `finance.adventure-works.com`, que só deve ser acessível pela divisão de finanças. Com a estrutura de aplicação atual, o seu aplicativo de finanças estariam acessível através da aplicação de caráter universal e por todos os funcionários. Para alterar isso, excluir a aplicação da sua com carateres universais ao configurar Finanças como um aplicativo separado com permissões mais restritivas.

Você precisa certificar-se de que há registros CNAME que apontam `finance.adventure-works.com` para o ponto de extremidade específico do aplicativo, especificado na página proxy de aplicativo para o aplicativo. Para esse cenário, `finance.adventure-works.com` aponta para `https://finance-awcycles.msappproxy.net/`.

Seguindo as [etapas documentadas](application-proxy-add-on-premises-application.md), esse cenário requer as seguintes configurações:

- Na **URL interna**, você define **Finance** em vez de um curinga.

    ![Exemplo: definir finanças em vez de um curinga na URL interna](./media/application-proxy-wildcard/52.png)

- Na **URL externa**, você define **Finance** em vez de um curinga.

    ![Exemplo: definir finanças em vez de um curinga na URL externa](./media/application-proxy-wildcard/53.png)

- SPN do aplicativo interno você define **Finance** em vez de um curinga.

    ![Exemplo: definir finanças em vez de um curinga na configuração de SPN](./media/application-proxy-wildcard/54.png)

Esta configuração implementa o seguinte cenário:

![Mostra a configuração implementada pelo cenário de exemplo](./media/application-proxy-wildcard/09.png)

Como `finance.adventure-works.com` é uma URL mais específica do que `*.adventure-works.com`, ela tem precedência. Os usuários que navegam para `finance.adventure-works.com` têm a experiência especificada no aplicativo de recursos de finanças. Nesse caso, somente os funcionários de finanças podem acessar `finance.adventure-works.com`.

Se você tiver vários aplicativos publicados para finanças e tiver `finance.adventure-works.com` como um domínio verificado, poderá publicar outro aplicativo curinga `*.finance.adventure-works.com`. Como isso é mais específico do que o `*.adventure-works.com`genérico, ele terá precedência se um usuário acessar um aplicativo no domínio de finanças.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre **domínios personalizados**, confira [trabalhando com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).
- Para saber mais sobre a **publicação de aplicativos**, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](application-proxy-add-on-premises-application.md)
