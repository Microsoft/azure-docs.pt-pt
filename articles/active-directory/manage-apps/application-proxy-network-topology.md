---
title: Considerações de topologia de rede para Proxy de aplicação AD AZure
description: Cobre considerações de topologia de rede ao utilizar o Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67505e7112c41b21b2ae5e8acc834ff047a470d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254810"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao utilizar o Azure Ative Directory Application Proxy

Este artigo explica considerações de topologia da rede ao utilizar o Azure Ative Directory (Azure AD) Application Proxy para publicar e aceder remotamente às suas aplicações.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando uma aplicação é publicada através do Azure AD Application Proxy, o tráfego dos utilizadores para as aplicações flui através de três ligações:

1. O utilizador conecta-se ao serviço público de procuração de aplicação Azure em Azure
1. O serviço De procuração de aplicação liga-se ao conector Proxy de aplicação
1. O conector Proxy de aplicação conecta-se à aplicação-alvo

![Diagrama que mostra o fluxo de tráfego do utilizador para a aplicação alvo](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Localização do inquilino e serviço de Procuração de Aplicações

Quando se inscreve para um inquilino da AD Azure, a região do seu inquilino é determinada pelo país/região que especifica. Quando ativa o Application Proxy, os casos de serviço de procuração de aplicação para o seu inquilino são escolhidos ou criados na mesma região que o seu inquilino AZure AD, ou a região mais próxima do mesmo.

Por exemplo, se o país ou região do seu inquilino Azure AD for o Reino Unido, todos os conectores de aplicação Proxy usam instâncias de serviço em centros de dados europeus. Quando os seus utilizadores acedem às aplicações publicadas, o seu tráfego passa pelas instâncias do serviço Application Proxy neste local.

## <a name="considerations-for-reducing-latency"></a>Considerações para reduzir a latência

Todas as soluções proxy introduzem latência na sua ligação de rede. Independentemente da solução proxy ou VPN que escolha como solução de acesso remoto, inclui sempre um conjunto de servidores que permitem a ligação ao interior da sua rede corporativa.

As organizações normalmente incluem pontos finais do servidor na sua rede de perímetro. No entanto, com o Azure AD Application Proxy, o tráfego flui através do serviço de procuração na nuvem enquanto os conectores residem na sua rede corporativa. Não é necessária nenhuma rede de perímetro.

As próximas secções contêm sugestões adicionais para ajudá-lo a reduzir ainda mais a latência. 

### <a name="connector-placement"></a>Colocação do conector

A Application Proxy escolhe a localização das instâncias para si, com base na localização do seu inquilino. No entanto, pode decidir onde instalar o conector, dando-lhe o poder de definir as características de latência do tráfego da sua rede.

Ao configurar o serviço Application Proxy, faça as seguintes perguntas:

- Onde está a aplicação?
- Onde estão a maioria dos utilizadores que acedem à aplicação?
- Onde está a instância Proxy application?
- Já tem uma ligação de rede dedicada aos datacenters Azure, como o Azure ExpressRoute ou uma VPN semelhante?

O conector tem de comunicar tanto com o Azure como com as suas aplicações (passos 2 e 3 no diagrama do fluxo de tráfego), pelo que a colocação do conector afeta a latência dessas duas ligações. Ao avaliar a colocação do conector, tenha em mente os seguintes pontos:

- Se quiser utilizar a delegação restrita kerberos (KCD) para uma única sinteligção, então o conector precisa de uma linha de visão para um datacenter. Além disso, o servidor de conector precisa de ser ligado ao domínio.  
- Em caso de dúvida, instale o conector mais perto da aplicação.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Pode minimizar a latência do tráfego de ponta a ponta, otimizando cada ligação de rede. Cada ligação pode ser otimizada por:

- Reduzindo a distância entre as duas extremidades do lúpulo.
- Escolhendo a rede certa para atravessar. Por exemplo, atravessar uma rede privada em vez da Internet pública pode ser mais rápido, devido a ligações dedicadas.

Se tiver uma ligação VPN ou ExpressRoute dedicada entre a Azure e a sua rede corporativa, talvez queira usá-la.

## <a name="focus-your-optimization-strategy"></a>Concentre a sua estratégia de otimização

Há pouco que possa fazer para controlar a ligação entre os seus utilizadores e o serviço Application Proxy. Os utilizadores podem aceder às suas aplicações a partir de uma rede doméstica, de um café ou de um país/região diferente. Em vez disso, pode otimizar as ligações do serviço Application Proxy para os conectores Application Proxy para as aplicações. Considere incorporar os seguintes padrões no seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão 1: Coloque o conector perto da aplicação

Coloque o conector perto da aplicação-alvo na rede de clientes. Esta configuração minimiza o passo 3 no diagrama de topografia, porque o conector e a aplicação estão próximos.

Se o seu conector precisa de uma linha de visão para o controlador de domínio, então este padrão é vantajoso. A maioria dos nossos clientes usa este padrão, porque funciona bem para a maioria dos cenários. Este padrão também pode ser combinado com o padrão 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Padrão 2: Aproveite o ExpressRoute com o espreitar da Microsoft

Se tiver o ExpressRoute configurado com o microsoft a espreitar, pode utilizar a ligação ExpressRoute mais rápida para o tráfego entre o Application Proxy e o conector. O conector ainda está na sua rede, perto da aplicação.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão 3: Aproveite o ExpressRoute com o seu olhar privado

Se tiver uma VPN ou ExpressRoute dedicada configurada com um olhar privado entre a Azure e a sua rede corporativa, tem outra opção. Nesta configuração, a rede virtual em Azure é tipicamente considerada como uma extensão da rede corporativa. Assim, pode instalar o conector no datacenter Azure e ainda satisfazer os baixos requisitos de latência da ligação connector-a-app.

A latência não está comprometida porque o tráfego está a fluir sobre uma ligação dedicada. Você também obtém uma latência de serviço proxy-para-conector de aplicação melhorada porque o conector está instalado num datacenter Azure perto da sua localização de inquilino Azure AD.

![Diagrama mostrando conector instalado dentro de um datacenter Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo seja a colocação do conector, também pode alterar a colocação da aplicação para obter melhores características de latência.

Cada vez mais, as organizações estão a mover as suas redes para ambientes hospedados. Isto permite-lhes colocar as suas apps num ambiente hospedado que também faz parte da sua rede corporativa, e ainda estar dentro do domínio. Neste caso, os padrões discutidos nas secções anteriores podem ser aplicados ao novo local de aplicação. Se estiver a considerar esta opção, consulte [os Serviços de Domínio AD AZure](../../active-directory-domain-services/overview.md).

Além disso, considere organizar os seus conectores usando [grupos de conector](application-proxy-connector-groups.md) para direcionar aplicações que estão em diferentes locais e redes.

## <a name="common-use-cases"></a>Casos de utilização comuns

Nesta secção, passamos por alguns cenários comuns. Assuma que o inquilino Azure AD (e, portanto, ponto final de serviço de procuração) está localizado nos Estados Unidos (EUA). As considerações discutidas nestes casos de utilização aplicam-se igualmente a outras regiões do mundo.

Para estes cenários, chamamos a cada ligação um "hop" e numeramos-nos para uma discussão mais fácil:

- **Hop 1**: Utilizador do serviço Application Proxy
- **Hop 2**: Serviço de procuração de aplicação ao conector Proxy de aplicação
- **Hop 3**: Conector proxy de aplicação à aplicação-alvo 

### <a name="use-case-1"></a>Use o caso 1

**Cenário:** A aplicação encontra-se numa rede de organização nos EUA, com utilizadores na mesma região. Não existe ExpressRoute ou VPN entre o datacenter Azure e a rede corporativa.

**Recomendação:** Siga o padrão 1, explicado na secção anterior. Para melhorar a latência, considere usar o ExpressRoute, se necessário.

Este é um padrão simples. Otimiza o lúpulo 3 colocando o conector perto da aplicação. Esta também é uma escolha natural, porque o conector normalmente é instalado com linha de visão para a app e para o datacenter para realizar operações de KCD.

![Diagrama que mostra utilizadores, proxy, conector e app estão todos nos EUA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Use o caso 2

**Cenário:** A aplicação está numa rede de organização nos EUA, com os utilizadores espalhados globalmente. Não existe ExpressRoute ou VPN entre o datacenter Azure e a rede corporativa.

**Recomendação:** Siga o padrão 1, explicado na secção anterior.

Mais uma vez, o padrão comum é otimizar o lúpulo 3, onde coloca o conector perto da app. O lúpulo 3 não é tipicamente caro, se é tudo dentro da mesma região. No entanto, o hop 1 pode ser mais caro dependendo do local onde o utilizador está, porque os utilizadores em todo o mundo devem aceder à instância Proxy da aplicação nos EUA. Vale a pena notar que qualquer solução de procuração tem características semelhantes em relação aos utilizadores que estão a ser espalhados globalmente.

![Os utilizadores estão espalhados globalmente, mas tudo o resto está nos EUA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Use a caixa 3

**Cenário:** A aplicação está numa rede de organização nos EUA. ExpressRoute com o espreitamento da Microsoft existe entre o Azure e a rede corporativa.

**Recomendação:** Siga os padrões 1 e 2, explicados na secção anterior.

Primeiro, coloque o conector o mais próximo possível da aplicação. Em seguida, o sistema utiliza automaticamente o ExpressRoute para o lúpulo 2.

Se o link ExpressRoute estiver a utilizar o olho da Microsoft, o tráfego entre o proxy e o conector flui sobre essa ligação. Hop 2 otimizou a latência.

![Diagrama mostrando ExpressRoute entre o proxy e o conector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Use o caso 4

**Cenário:** A aplicação está numa rede de organização nos EUA. ExpressRoute com o espreitamento privado existe entre a Azure e a rede corporativa.

**Recomendação:** Siga o padrão 3, explicado na secção anterior.

Coloque o conector no datacenter Azure que está ligado à rede corporativa através do espreitamento privado ExpressRoute.

O conector pode ser colocado no datacenter Azure. Uma vez que o conector ainda tem uma linha de visão para a aplicação e o datacenter através da rede privada, o hop 3 permanece otimizado. Além disso, o hop 2 é otimizado ainda mais.

![Conector no Centro de Dados Azure, ExpressRoute entre conector e app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Use o caso 5

**Cenário:** A aplicação encontra-se numa rede de organização na Europa, com a aplicação Proxy e a maioria dos utilizadores nos EUA.

**Recomendação:** Coloque o conector perto da aplicação. Como os utilizadores dos EUA estão a aceder a uma instância Proxy de aplicação que por acaso está na mesma região, o hop 1 não é muito caro. O hop 3 está otimizado. Considere usar o ExpressRoute para otimizar o lúpulo 2.

![Diagrama mostra utilizadores e procuração nos EUA, conector e app na Europa](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Também pode considerar a utilização de uma outra variante nesta situação. Se a maioria dos utilizadores da organização estiver nos EUA, então é provável que a sua rede se estenda também aos EUA. Coloque o conector nos EUA e use a linha de rede corporativa interna dedicada para a aplicação na Europa. Desta forma os lúpulos 2 e 3 estão otimizados.

![Diagrama mostra utilizadores, proxy e conector nos EUA, app na Europa](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar procuração de aplicação](application-proxy-add-on-premises-application.md)
- [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)
