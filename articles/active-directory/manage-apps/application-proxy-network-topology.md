---
title: Considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory | Documentos da Microsoft
description: Aborda considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure AD.
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
ms.date: 07/28/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9ee8800d84d1090f97fb72980123f59d5aa0375
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783485"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory

Este artigo explica as considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory (Azure AD) para publicar e acedam remotamente às suas aplicações.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando um aplicativo é publicado através do Proxy de aplicações do Azure AD, fluxos de tráfego dos utilizadores para os aplicativos por meio de conexões de três:

1. O utilizador liga-se para o Proxy de aplicações do Azure AD serviço ponto final público no Azure
2. O serviço de Proxy de aplicações se liga ao conector do Proxy de aplicações
3. O conector do Proxy de aplicação liga-se para o aplicativo de destino

![Diagrama que mostra o fluxo de tráfego de utilizador para o aplicativo de destino](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Localização do inquilino e o serviço de Proxy de aplicações

Quando se inscreve para um inquilino do Azure AD, a região do seu inquilino é determinada por país/região especificado. Quando ativar o Proxy de aplicações, as instâncias do serviço de Proxy de aplicações para o seu inquilino são escolhidas ou criadas na mesma região que o inquilino do Azure AD ou a região mais próxima a ele.

Por exemplo, se o país ou região do seu inquilino do Azure AD é o Reino Unido, todos os seus conectores de Proxy da aplicação utilizam instâncias de serviço nos centros de dados da UE. Quando o acesso de utilizadores a aplicações publicadas, o tráfego passa pelas instâncias do serviço de Proxy de aplicações nesta localização.

## <a name="considerations-for-reducing-latency"></a>Considerações para reduzir a latência

Todas as soluções de proxy introduzem latência em sua ligação de rede. Não importa qual proxy ou de uma solução VPN que escolher como a sua solução de acesso remoto, ele sempre inclui um conjunto de servidores, permitindo a conexão com no interior da rede empresarial.

As organizações incluem, geralmente, os pontos finais do servidor na sua rede de perímetro. Com o Proxy de aplicações do Azure AD, no entanto, o tráfego flui através do serviço de proxy na cloud, enquanto os conectores residem na sua rede empresarial. Não é necessária nenhuma rede de perímetro.

As secções seguintes contêm as sugestões adicionais para o ajudar a reduzir a latência ainda mais. 

### <a name="connector-placement"></a>Colocação de conector

Proxy da aplicação escolhe a localização de instâncias para, com base na sua localização do inquilino. No entanto, possa decidir onde instalar o conector, que lhe dá a capacidade para definir as características de latência de seu tráfego de rede.

Quando configurar o serviço de Proxy de aplicações, faça as seguintes perguntas:

* Onde está localizada a aplicação?
* Onde estão localizadas a maioria dos utilizadores que aceder à aplicação?
* Onde está localizada a instância do Proxy de aplicações?
* Já tem uma ligação de rede dedicada aos datacenters do Azure, configurar, como o Azure ExpressRoute ou uma VPN semelhante?

O conector tem para se comunicar com o Azure e seus aplicativos (passos 2 e 3 no diagrama de fluxo de tráfego), por isso, a colocação de afeta o conector a latência das duas conexões. Ao avaliar a colocação do conector, tenha em atenção os seguintes pontos:

* Se pretender utilizar a delegação restrita de Kerberos (KCD) para início de sessão único, em seguida, o conector tem de uma linha Visual para um datacenter. Além disso, o servidor do conector tem de ser associados a um domínio.  
* Em caso de dúvida, instale o conector do próximo ao aplicativo.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Pode minimizar a latência do tráfego ponto a ponto ao otimizar cada ligação de rede. Cada ligação pode ser otimizada ao:

* Reduzindo a distância entre as duas extremidades do salto.
* Escolha a rede certa para atravessar. Por exemplo, a atravessar uma rede privada, em vez de Internet pública pode ser mais rápida, devido a ligações dedicadas.

Se tiver uma ligação VPN ou ExpressRoute dedicada entre o Azure e a sua rede corporativa, talvez queira usá-lo.

## <a name="focus-your-optimization-strategy"></a>Concentre-se a sua estratégia de otimização

Não há muito que pode fazer para controlar a ligação entre os seus utilizadores e o serviço de Proxy de aplicações. Os usuários podem acessar as suas aplicações a partir de uma rede doméstica, num café ou país/região diferente. Em vez disso, pode otimizar as ligações a partir do serviço de Proxy de aplicações para os conectores de Proxy de aplicações para as aplicações. Considere a incorporar os padrões seguintes no seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão de 1: Colocar o conector perto do aplicativo

Coloca o conector perto do aplicativo de destino na rede de cliente. Esta configuração minimiza o passo 3 no diagrama topografia, porque o conector e a aplicação fechar. 

Se o conector tem de uma linha Visual para o controlador de domínio, este padrão é vantajoso. A maioria dos nossos clientes usa esse padrão, pois funciona bem para a maioria dos cenários. Este padrão também pode ser combinado com o padrão de 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Padrão de 2: Tire partido do ExpressRoute com peering da Microsoft

Se tiver o ExpressRoute configurados com peering da Microsoft, pode utilizar a ligação de ExpressRoute mais rápida para o tráfego entre o Proxy de aplicações e o conector. O conector é ainda na sua rede, próximo a aplicação.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão de 3: Tire partido do ExpressRoute com peering privado

Se tiver uma dedicado VPN ou ExpressRoute integrados com o peering privado entre o Azure e a sua rede empresarial, tem outra opção. Nesta configuração, a rede virtual no Azure, normalmente, é considerada como uma extensão da rede empresarial. Portanto, pode instalar o conector no datacenter do Azure e, ainda satisfazer os requisitos de baixa latência da ligação do conector para o aplicativo.

Latência não é comprometida, porque o tráfego estiver fluindo através de uma ligação dedicada. Também obtém melhor latência de conector do serviço de Proxy da aplicação porque o conector é instalado num datacenter do Azure perto de sua localização de inquilino do Azure AD.

![Diagrama que mostra o conector instalado num datacenter do Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo é o posicionamento de conector, também pode alterar a colocação da aplicação para obter melhores características de latência.

Cada vez mais, as organizações estão movendo suas redes para ambientes hospedados. Isto permite-lhes colocar seus aplicativos num ambiente de hospedagem que também faz parte da respetiva rede empresarial e ainda estar dentro do domínio. Neste caso, os padrões discutidos nas seções anteriores podem ser aplicados para o novo local de aplicativo. Se estiver Considerando esta opção, veja [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md).

Além disso, considere a organizar seus conectores usando [grupos de conectores](application-proxy-connector-groups.md) para aplicações de destino que estão em diferentes locais e redes. 

## <a name="common-use-cases"></a>Casos de utilização comuns

Nesta secção, vamos analisar alguns cenários comuns. Partem do princípio de que o inquilino do Azure AD (e, portanto, ponto final de serviço de proxy) está localizado nos Estados Unidos (EUA). As considerações discutidas estes uso casos também se aplicam a outras regiões em todo o mundo.

Nestes cenários, chamamos um "salto" de cada ligação e numbê-los para discussão mais fácil:

- **Salto 1**: Utilizador para o serviço de Proxy de aplicações
- **Salto 2**: Serviço de Proxy de aplicações para o conector do Proxy de aplicações
- **Salto 3**: Conector de Proxy de aplicações para o aplicativo de destino 

### <a name="use-case-1"></a>Caso de utilização 1

**Cenário:** A aplicação está na rede da organização nos E.U.A., com utilizadores na mesma região. Nenhuma VPN ou ExpressRoute existe entre o datacenter do Azure e da rede empresarial.

**Recomendação:** Siga o padrão de 1, explicado na seção anterior. Para melhor latência, considere utilizar o ExpressRoute, se necessário.

Este é um padrão simple. Otimizar o salto 3 ao colocar o conector de perto a aplicação. Isso também é a escolha natural, porque o conector, normalmente, é instalado com a linha de visão para a aplicação e para o Centro de dados para realizar operações de KCD.

![Diagrama que mostra que os utilizadores, proxy, conector e aplicação são todos nos EUA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso 2 de uso

**Cenário:** A aplicação está na rede da organização nos E.U.A., com utilizadores distribuídos globalmente. Nenhuma VPN ou ExpressRoute existe entre o datacenter do Azure e da rede empresarial.

**Recomendação:** Siga o padrão de 1, explicado na seção anterior. 

Novamente, o padrão comum é otimizar o salto 3, onde colocar o conector de perto a aplicação. Salto 3 não é normalmente Caro, se tudo está dentro da mesma região. No entanto, salto 1 pode ser mais dispendioso, dependendo de onde está o utilizador, uma vez que os utilizadores por todo o mundo tem de aceder a instância de Proxy de aplicações nos E.U.A. Vale a pena observar que qualquer solução de proxy tem características semelhantes em relação a usuários que está a ser distribuídos globalmente.

![Diagrama que mostra que os utilizadores são distribuídos globalmente, mas o proxy, o conector e a aplicação estiver nos EUA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de utilização 3

**Cenário:** A aplicação está na rede da organização nos EUA. ExpressRoute com peering da Microsoft existe entre o Azure e da rede empresarial.

**Recomendação:** Siga os padrões de 1 e 2, explicado na seção anterior.

Em primeiro lugar, coloca o conector mais próximo possível para a aplicação. Em seguida, o sistema utilizará automaticamente o ExpressRoute para o salto 2. 

Se a ligação do ExpressRoute estiver a utilizar o peering da Microsoft, o tráfego entre o proxy e o conector flui através dessa ligação. Salto 2 otimizou latência.

![Diagrama que mostra o ExpressRoute entre o proxy e o conector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de utilização 4

**Cenário:** A aplicação está na rede da organização nos EUA. ExpressRoute com peering privado existe entre o Azure e da rede empresarial.

**Recomendação:** Siga o padrão de 3, explicado na seção anterior.

Coloca o conector no datacenter do Azure que está ligado à rede empresarial através do peering privado do ExpressRoute. 

O conector pode ser colocado no datacenter do Azure. Uma vez que o conector ainda tem uma linha Visual para o aplicativo e o Centro de dados através da rede privada, salto 3 continua a ser otimizado. Além disso, o salto 2 é ainda mais otimizado.

![Diagrama que mostra o conector no datacenter do Azure e ExpressRoute entre o conector e a aplicação](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de utilização 5

**Cenário:** A aplicação está na rede de uma organização diz respeito à UE, com a instância do Proxy de aplicações e a maioria dos usuários nos EUA.

**Recomendação:** Coloca o conector de perto a aplicação. Porque os utilizadores dos EUA estão a aceder a uma instância do Proxy de aplicações que estivesse na mesma região, salto 1 não é demasiado caro. Salto 3 está otimizado. Considere utilizar o ExpressRoute para otimizar o salto 2. 

![Diagrama que mostra os utilizadores e o proxy nos E.U.A., com o conector e a aplicação na UE](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Também pode considerar usando uma outra variante nessa situação. Se a maioria dos utilizadores na organização estiverem nos EUA, em seguida, é provável que expande a sua rede para EUA também. Colocar o conector nos EUA e utilize a linha de rede empresarial interna dedicada para a aplicação na UE. Este saltos de forma 2 e 3 são otimizados.

![Diagrama que mostra os utilizadores, o proxy e o conector nos EUA, a aplicação na UE](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passos Seguintes

- [Ativar o Proxy de aplicações](application-proxy-add-on-premises-application.md)
- [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Resolver problemas que possa ter com o Proxy de aplicações](application-proxy-troubleshoot.md)
