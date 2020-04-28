---
title: Considerações de topologia de rede para procuração de aplicação da AD Azure
description: Cobre considerações de topologia de rede ao utilizar o Proxy de Aplicação AD Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295126"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao usar o Proxy de Aplicação de Diretório Ativo Azure

Este artigo explica considerações de topode de rede ao utilizar o Proxy de Aplicação de Aplicação Azure Ative Directory (Azure AD) para a publicação e acesso remotamente às suas aplicações.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando uma aplicação é publicada através do Procurador de Aplicação AD Azure, o tráfego dos utilizadores para as aplicações flui através de três ligações:

1. O utilizador liga-se ao serviço de proxy de aplicação Azure AD no Azure
1. O serviço Proxy de Aplicação liga-se ao conector Proxy de Aplicação
1. O conector Proxy de Aplicação liga-se à aplicação-alvo

![Diagrama mostrando fluxo de tráfego do utilizador para aplicação alvo](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Localização do inquilino e serviço de procuração de aplicações

Quando se inscreve num inquilino da AD Azure, a região do seu inquilino é determinada pelo país/região que especifica. Quando ativa o Application Proxy, as instâncias de serviço de Procuração de Aplicação para o seu inquilino são escolhidas ou criadas na mesma região que o seu inquilino Azure AD, ou a região mais próxima do mesmo.

Por exemplo, se o país ou região do seu inquilino Azure AD for o Reino Unido, todos os seus conectores De Procuração de Aplicação utilizam instâncias de serviço em centros de dados europeus. Quando os seus utilizadores acedem a aplicações publicadas, o seu tráfego passa pelas instâncias do serviço Proxy de Aplicação neste local.

## <a name="considerations-for-reducing-latency"></a>Considerações para reduzir a latência

Todas as soluções proxy introduzem latência na sua ligação de rede. Independentemente da solução proxy ou VPN que escolha como solução de acesso remoto, inclui sempre um conjunto de servidores que permitem a ligação dentro da sua rede corporativa.

As organizações normalmente incluem pontos finais do servidor na sua rede de perímetro. No entanto, com a Aplicação AD Proxy azure, o tráfego flui através do serviço proxy na nuvem enquanto os conectores residem na sua rede corporativa. Não é necessária uma rede de perímetro.

As secções seguintes contêm sugestões adicionais para ajudá-lo a reduzir ainda mais a latência. 

### <a name="connector-placement"></a>Colocação do conector

Application Proxy escolhe a localização dos casos para si, com base na localização do seu inquilino. No entanto, pode decidir onde instalar o conector, dando-lhe o poder de definir as características de latência do tráfego de rede.

Ao configurar o serviço proxy de aplicação, faça as seguintes perguntas:

- Onde está a aplicação localizada?
- Onde está a maioria dos utilizadores que acedem à aplicação localizada?
- Onde está a instância de Procuração de Aplicação localizada?
- Já tem uma ligação de rede dedicada aos centros de dados Azure, como o Azure ExpressRoute ou uma VPN semelhante?

O conector tem de comunicar tanto com o Azure como com as suas aplicações (passos 2 e 3 no diagrama de fluxo de tráfego), pelo que a colocação do conector afeta a latência dessas duas ligações. Ao avaliar a colocação do conector, tenha em conta os seguintes pontos:

- Se quiser utilizar a delegação limitada kerberos (KCD) para um único sinal, então o conector precisa de uma linha de visão para um datacenter. Além disso, o servidor do conector precisa de ser unido ao domínio.  
- Em caso de dúvida, instale o conector mais perto da aplicação.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Pode minimizar a latência do tráfego de ponta a ponta, otimizando cada ligação de rede. Cada ligação pode ser otimizada por:

- Reduzindo a distância entre as duas extremidades do lúpulo.
- Escolher a rede certa para atravessar. Por exemplo, atravessar uma rede privada em vez da Internet pública pode ser mais rápido, devido a ligações dedicadas.

Se tiver uma ligação VPN ou ExpressRoute dedicada entre o Azure e a sua rede corporativa, poderá querer usá-lo.

## <a name="focus-your-optimization-strategy"></a>Concentre a sua estratégia de otimização

Pouco há que possa fazer para controlar a ligação entre os seus utilizadores e o serviço de Procuração de Aplicações. Os utilizadores podem aceder às suas aplicações a partir de uma rede doméstica, de um café ou de um país/região diferente. Em vez disso, pode otimizar as ligações do serviço Proxy de Aplicação aos conectores proxy de aplicação às aplicações. Considere incorporar os seguintes padrões no seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão 1: Coloque o conector perto da aplicação

Coloque o conector perto da aplicação-alvo na rede de clientes. Esta configuração minimiza o passo 3 no diagrama de topografia, porque o conector e a aplicação estão próximos.

Se o seu conector precisa de uma linha de visão para o controlador de domínio, então este padrão é vantajoso. A maioria dos nossos clientes usa este padrão, porque funciona bem para a maioria dos cenários. Este padrão também pode ser combinado com o padrão 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Padrão 2: Aproveite a ExpressRoute com o peering da Microsoft

Se tiver o ExpressRoute configurado com o peering da Microsoft, pode utilizar a ligação ExpressRoute mais rápida para o tráfego entre o Proxy da Aplicação e o conector. O conector ainda se encontra na sua rede, perto da aplicação.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão 3: Aproveite a Rota Expresso com o peering privado

Se tiver uma VPN ou ExpressRoute dedicada configurada com um peering privado entre o Azure e a sua rede corporativa, tem outra opção. Nesta configuração, a rede virtual em Azure é tipicamente considerada como uma extensão da rede corporativa. Assim, pode instalar o conector no centro de dados Azure e ainda satisfazer os baixos requisitos de latência da ligação conector-app.

A latência não está comprometida porque o tráfego está a fluir sobre uma ligação dedicada. Você também obtém uma melhor latência de serviço proxy de aplicação para conector porque o conector está instalado em um centro de dados Azure perto da sua localização de inquilino Azure AD.

![Diagrama mostrando conector instalado dentro de um datacenter Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo seja a colocação do conector, também pode alterar a colocação da aplicação para obter melhores características de latência.

Cada vez mais, as organizações estão a mover as suas redes para ambientes alojados. Isto permite-lhes colocar as suas apps num ambiente hospedado que também faz parte da sua rede corporativa, e ainda estar dentro do domínio. Neste caso, os padrões discutidos nas secções anteriores podem ser aplicados ao novo local de aplicação. Se está a considerar esta opção, consulte [o Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Além disso, considere organizar os seus conectores usando grupos de [conectores](application-proxy-connector-groups.md) para direcionar aplicações que se encontram em diferentes locais e redes.

## <a name="common-use-cases"></a>Casos de utilização comuns

Nesta secção, percorremos alguns cenários comuns. Assuma que o inquilino da AD Azure (e, portanto, o ponto final do serviço proxy) está localizado nos Estados Unidos (EUA). As considerações discutidas nestes casos de utilização aplicam-se também a outras regiões do mundo.

Para estes cenários, chamamos a cada ligação um "salto" e numera-los para uma discussão mais fácil:

- **Hop 1**: Utilizador do serviço Proxy de Aplicação
- **Hop 2**: Serviço de procuração de aplicações para o conector Proxy de aplicação
- **Hop 3**: Conector proxy de aplicação à aplicação alvo 

### <a name="use-case-1"></a>Use o caso 1

**Cenário:** A aplicação está na rede de uma organização nos EUA, com utilizadores na mesma região. Não existe nenhuma ExpressRoute ou VPN entre o centro de dados Azure e a rede corporativa.

**Recomendação:** Siga o padrão 1, explicado na secção anterior. Para uma latência melhorada, considere utilizar o ExpressRoute, se necessário.

Este é um padrão simples. Otimiza o lúpulo 3 colocando o conector perto da aplicação. Esta também é uma escolha natural, porque o conector normalmente é instalado com linha de visão para a app e para o datacenter para realizar operações de KCD.

![Diagrama que mostra utilizadores, proxy, conector e app estão todos nos EUA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Use o caso 2

**Cenário:** A aplicação está na rede de uma organização nos EUA, com os utilizadores espalhados globalmente. Não existe nenhuma ExpressRoute ou VPN entre o centro de dados Azure e a rede corporativa.

**Recomendação:** Siga o padrão 1, explicado na secção anterior.

Mais uma vez, o padrão comum é otimizar o lúpulo 3, onde você colocar o conector perto da app. O hop 3 não é tipicamente caro, se é tudo dentro da mesma região. No entanto, o hop 1 pode ser mais caro dependendo de onde o utilizador está, porque os utilizadores em todo o mundo devem aceder à instância de Procuração de Aplicações nos EUA. Vale a pena notar que qualquer solução proxy tem características semelhantes em relação aos utilizadores que estão a ser espalhados globalmente.

![Os utilizadores estão espalhados globalmente, mas todo o resto está nos EUA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Use o caso 3

**Cenário:** A aplicação está numa rede de uma organização nos EUA. O ExpressRoute com a Microsoft a espreitar existe entre o Azure e a rede corporativa.

**Recomendação:** Siga os padrões 1 e 2, explicados na secção anterior.

Em primeiro lugar, coloque o conector o mais próximo possível da aplicação. Em seguida, o sistema utiliza automaticamente o ExpressRoute para o lúpulo 2.

Se a ligação ExpressRoute estiver a utilizar o peering da Microsoft, o tráfego entre o proxy e o conector flui sobre essa ligação. O Hop 2 otimizou a latência.

![Diagrama mostrando ExpressRoute entre o proxy e o conector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Utilização do caso 4

**Cenário:** A aplicação está numa rede de uma organização nos EUA. A ExpressRoute com o peering privado existe entre o Azure e a rede corporativa.

**Recomendação:** Siga o padrão 3, explicado na secção anterior.

Coloque o conector no centro de dados Azure que está ligado à rede corporativa através do peering privado ExpressRoute.

O conector pode ser colocado no centro de dados Azure. Uma vez que o conector ainda tem uma linha de visão para a aplicação e para o datacenter através da rede privada, o hop 3 permanece otimizado. Além disso, o hop 2 está otimizado ainda mais.

![Conector no centro de dados Azure, ExpressRoute entre conector e app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Use o caso 5

**Cenário:** A aplicação está na rede de uma organização na Europa, com a instância proxy de aplicação e a maioria dos utilizadores nos EUA.

**Recomendação:** Coloque o conector perto da aplicação. Como os utilizadores dos EUA estão a aceder a uma instância de Procuração de Aplicações que por acaso está na mesma região, o hop 1 não é muito caro. O hop 3 está otimizado. Considere usar o ExpressRoute para otimizar o lúpulo 2.

![Diagrama mostra utilizadores e procuração nos EUA, conector e app na Europa](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Também pode considerar usar uma outra variante nesta situação. Se a maioria dos utilizadores da organização estiver nos EUA, então é provável que a sua rede se estenda também aos EUA. Coloque o conector nos EUA e utilize a linha de rede corporativa interna dedicada à aplicação na Europa. Desta forma, os lúpulos 2 e 3 estão otimizados.

![Diagrama mostra utilizadores, proxy e conector nos EUA, app na Europa](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar proxy de aplicação](application-proxy-add-on-premises-application.md)
- [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)
