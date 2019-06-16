---
title: ExpressRoute para fornecedores de soluções de Cloud - Azure | Documentos da Microsoft
description: Este artigo fornece informações para fornecedores de soluções de Cloud que pretende incorporar serviços do Azure e ExpressRoute nas suas ofertas.
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: richcar
ms.custom: seodec18
ms.openlocfilehash: a03ab7bbdadad2728f54127583583c22bd2ec07a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367596"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute para Fornecedores de Soluções em Nuvem (CSP)
A Microsoft fornece serviços de hiper escala para revendedores e distribuidores (CSP) tradicionais de forma a aprovisionar novos serviços e soluções para os seus clientes sem a necessidade de investir no desenvolvimento destes novos serviços. Para permitir que o Fornecedor de Soluções em Nuvem (CSP) possa gerir diretamente estes novos serviços, a Microsoft disponibiliza programas e APIs que permitem ao CSP gerir os recursos do Microsoft Azure em nome dos seus clientes. Um destes recursos é o ExpressRoute. O ExpressRoute permite ao CSP ligar os recursos existentes do cliente aos serviços do Azure. O ExpressRoute é uma ligação privada de comunicações de alta velocidade para serviços no Azure. 

O ExpressRoute é composto por um par de circuitos para uma elevada disponibilidade e está anexado a uma ou várias subscrições de cliente único e não pode ser partilhado por vários clientes. Cada circuito deve ser terminado num router diferente para manter a elevada disponibilidade.

> [!NOTE]
> Existem limites de ligação e largura de banda no ExpressRoute, o que significa que as implementações grandes/complexas necessitarão de vários circuitos ExpressRoute para um único cliente.
> 
> 

O Microsoft Azure fornece um crescente número de serviços que pode oferecer aos seus clientes. O ExpressRoute ajuda-o a si e aos seus clientes a tirar partido destes serviços fornecendo um acesso de alta velocidade com baixa latência ao ambiente do Microsoft Azure.

## <a name="microsoft-azure-management"></a>Gestão do Microsoft Azure
A Microsoft fornece CSPs com APIs para gerir as subscrições de cliente do Azure, permitindo uma integração programática com os seus próprios sistemas de gestão de serviços. As capacidades de gestão suportadas podem ser encontradas [aqui](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Gestão dos recursos do Microsoft Azure
O contrato celebrado com o seu cliente, este determinará a forma como a subscrição será gerida. O CSP pode gerir diretamente a criação e manutenção de recursos ou o cliente pode manter o controlo da subscrição do Microsoft Azure e criar os recursos Azure à medida que são necessários. Se o cliente está a gerir a criação de recursos na respetiva subscrição do Microsoft Azure irá de utilizar um dos dois modelos: "*Connect-Through*" modelo, ou "*Direct-To*" modelo. Estes modelos são descritos em detalhe nas secções seguintes.  

### <a name="connect-through-model"></a>Modelo Connect-through
![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

No modelo connect-through, o CSP cria uma ligação direta entre o seu centro de dados e a subscrição do Azure do seu cliente. A ligação direta é estabelecida com o ExpressRoute, estabelecendo uma ligação entre a sua rede e o Azure. Em seguida, o cliente liga-se à sua rede. Este cenário requer que o cliente passe através da rede do CSP para aceder aos serviços do Azure. 

Se o cliente tiver outras subscrições do Azure não geridas por si, estas utilizariam a Internet pública ou as suas próprias ligações privadas para se ligar a esses serviços aprovisionados sob a subscrição não CSP. 

Para o CSP gerir os serviços do Azure, pressupõe-se que este tem um arquivo de identidade do cliente anteriormente estabelecido que, depois, seria replicado no Azure Active Directory para a gestão da respetiva subscrição do CSP através de Administrar Em Nome De (AOBO). Fatores determinantes para este cenário incluem um determinado parceiro ou fornecedor de serviços com uma relação estabelecida com o cliente, o cliente está atualmente a utilizar os serviços do fornecedor ou o parceiro pretende fornecer uma combinação de soluções alojadas pelo fornecedor e o Azure para fornecer flexibilidade e abordar os desafios do cliente que o CSP não consegue satisfazer individualmente. Este modelo é ilustrado na **Figura**, abaixo.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Modelo Connect-to
![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to.png)

No modelo Connect-To, o fornecedor de serviços cria uma ligação direta entre o centro de dados do seu cliente e a subscrição do Azure aprovisionado pelo CSP com o ExpressRoute na rede do cliente (cliente).

> [!NOTE]
> Para o ExpressRoute, o cliente iria precisar de criar e manter o circuito ExpressRoute.  
> 
> 

Este cenário de conectividade exige que o cliente se ligue diretamente através de uma rede de clientes para aceder à subscrição do Azure gerida pelo CSP, com uma ligação de rede direta criada, detida e gerida, em parte ou totalmente, pelo cliente. Para estes clientes, pressupõe-se que o fornecedor não tem atualmente um arquivo de identidade do cliente estabelecido e o fornecedor iria ajudar o cliente a replicar o respetivo arquivo de identidade atual no Azure Active Directory para a gestão da subscrição através de AOBO. Fatores determinantes para este cenário incluem um determinado parceiro ou fornecedor de serviços com uma relação estabelecida com o cliente, o cliente está atualmente a utilizar os serviços do fornecedor ou o parceiro pretende fornecer serviços baseados somente em soluções alojadas pelo Azure sem a necessidade de uma infraestrutura ou centro de dados existente do fornecedor.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

A escolha entre estas duas opções baseia-se nas necessidades do seu cliente e suas necessidades atuais para fornecer serviços do Azure Os detalhes destes modelos e o controlo de acesso baseado na função associada, funcionamento em rede e padrões de estrutura de identidade são abordados em detalhe nas seguintes ligações:

* **Controlo de acesso baseado na função (RBAC)** – O RBAC baseia-se no Azure Active Directory.  Para obter mais informações sobre o Azure RBAC, veja [aqui](../role-based-access-control/role-assignments-portal.md).
* **Funcionamento em Rede** – Aborda os vários tópicos do funcionamento em rede no Microsoft Azure.
* **Azure Active Directory (Azure AD)** – Azure AD fornece a gestão de identidade para o Microsoft Azure e aplicações SaaS de terceiros. Para obter mais informações sobre o Azure AD, veja [aqui](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Velocidades de rede
O ExpressRoute suporta velocidades de rede de 50 Mb/s a 10 Gb/s. Isto permite aos clientes comprar a quantidade de largura de banda de rede necessária para o seu respetivo ambiente exclusivo.

> [!NOTE]
> A largura de banda de rede pode ser aumentada, conforme necessário, sem perturbar as comunicações. Contudo, para reduzir a velocidade de rede, é necessário remover o circuito e recriá-lo na menor velocidade de rede.  
> 
> 

O ExpressRoute suporta a ligação de várias vNets para um único circuito ExpressRoute para uma melhor utilização das ligações de maior velocidade. Um único circuito ExpressRoute pode ser partilhado entre várias subscrições do Azure pertencentes ao mesmo cliente.

## <a name="configuring-expressroute"></a>Configurar o ExpressRoute
O ExpressRoute pode ser configurado para suportar três tipos de tráfego ([domínios de encaminhamento](#expressroute-routing-domains)) num circuito ExpressRoute único. Este tráfego é segregado para o peering da Microsoft, peering público e peering privado do Azure. Pode escolher um ou todos os tipos de tráfego a serem enviados através de um circuito ExpressRoute único ou utilizar vários circuitos ExpressRoute, dependendo do tamanho do circuito ExpressRoute e do isolamento necessários pelo seu cliente. A postura de segurança do seu cliente pode não permitir a passagem de tráfego público e privado no mesmo circuito.

### <a name="connect-through-model"></a>Modelo Connect-through
Numa configuração connect-through, será responsável por todos os apoios de funcionamento de rede para ligar os seus recursos do centro de dados dos clientes às subscrições alojadas no Azure. Cada um dos seus clientes que pretenda utilizar as capacidades do Azure irá necessitar da sua própria ligação do ExpressRoute, que será gerida pela si. Depois, utilizará os mesmos métodos que o cliente utilizaria para obter o circuito ExpressRoute. Deve seguir os mesmos passos descritos no artigo [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md) para o aprovisionamento do circuito e estados de circuitos. Em seguida, irá configurar as rotas do Protocolo BGP (Border Gateway Protocol) para controlar o tráfego que flui entre a rede no local e a Azure vNet.

### <a name="connect-to-model"></a>Modelo Connect-to
Numa configuração connect-to, o seu cliente já tem uma ligação existente para o Azure ou iniciará uma ligação ao fornecedor de serviços de Internet ligando o ExpressRoute a partir do respetivo centro de dados do seu cliente diretamente para o Azure, ao invés do seu centro de dados. Para iniciar o processo de aprovisionamento, o cliente seguirá os passos, conforme descrito no modelo Connect-Through, acima. Depois de o circuito ter sido estabelecido, o seu cliente terá de configurar os routers no local para conseguir aceder à sua rede e às Azure vNets.

Pode ajudá-lo com a configuração da ligação e das rotas para permitir que os recursos no(s) seu(s) centro(s) de dados comuniquem com os recursos do cliente no seu centro de dados ou com os recursos alojados no Azure.

## <a name="expressroute-routing-domains"></a>Domínios de encaminhamento do ExpressRoute
O ExpressRoute oferece três domínios de encaminhamento: peering público, privado e Microsoft. Cada um dos domínios de encaminhamento está configurado com routers idênticos numa configuração de ativo-ativo para uma elevada disponibilidade. Para obter mais detalhes sobre domínios de encaminhamento ExpressRoute, veja [aqui](expressroute-circuit-peerings.md).

Pode definir filtros de rotas personalizadas para permitir apenas a(s) rota(s) que pretende permitir ou precisa. Para obter mais informações ou para ver como realizar estas alterações, veja o artigo: [Criar e modificar o encaminhamento de um circuito do ExpressRoute com o PowerShell](expressroute-howto-routing-classic.md) para obter mais detalhes sobre os filtros de encaminhamento.

> [!NOTE]
> Para o Peering Público e da Microsoft, a conectividade tem de ser realizada através de um endereço IP público detido pelo cliente ou CSP e tem de cumprir todas as regras definidas. Para obter mais informações, veja a página [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).  
> 
> 

## <a name="routing"></a>Encaminhamento
O ExpressRoute liga-se às redes do Azure através do Gateway da Virtual Network do Azure. Os gateways de rede fornecem encaminhamento para redes virtuais do Azure.

Criar Virtual Networks do Azure também cria uma tabela de encaminhamento predefinido para a vNet para direcionar o tráfego de/para as sub-redes da vNet. Se a tabela de rotas predefinidas for insuficiente para a solução, podem ser criadas rotas personalizadas para encaminhar o tráfego de saída para aplicações personalizadas ou para bloquear rotas para sub-redes específicas ou redes externas.

### <a name="default-routing"></a>Encaminhamento predefinido
A tabela de rotas predefinidas inclui as rotas seguintes:

* Encaminhamento numa sub-rede
* Sub-rede a sub-rede na rede virtual
* Para a Internet
* Rede virtual a rede virtual com gateway de VPN
* Rede virtual a rede no local com uma VPN ou um gateway do ExpressRoute

![texto alternativo](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Encaminhamento definido pelo utilizador (UDR)
As rotas definidas pelo utilizador permitem o controlo de tráfego de saída da sub-rede atribuída a outras sub-redes na rede virtual ou ao longo de um dos outros gateways predefinidos (ExpressRoute; Internet ou VPN). A tabela de encaminhamento do sistema predefinido pode ser substituída por uma tabela de encaminhamento definida pelo utilizador que substitui a tabela de encaminhamento predefinido com rotas personalizadas. Com o encaminhamento definido pelo utilizador, os clientes podem criar rotas específicas para aparelhos, tais como firewalls ou aplicações de deteção de intrusões ou bloquear o acesso a sub-redes específicas da sub-rede que aloja a rota definida pelo utilizador. Para obter uma descrição geral das Rotas Definidas pelo Utilizador, veja [aqui](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Segurança
Consoante o modelo em utilização, Connect-To ou Connect-Through, o seu cliente define as políticas de segurança na respetiva vNet ou fornece os requisitos da política de segurança para CSP a definir para as respetivas vNets. Os seguintes critérios de segurança podem ser definidos:

1. **Isolamento de Cliente** — A plataforma do Azure fornece isolamento de cliente ao armazenar o ID de Cliente e informações da vNet numa base de dados segura, que serve para encapsular o tráfego de cada cliente num túnel GRE.
2. As regras do **Grupo de Segurança de Rede (NSG)** servem para definir tráfego permitido para dentro e fora das sub-redes nas vNets no Azure. Por predefinição, o NSG contêm regras de Bloqueio para bloquear o tráfego da Internet para a vNet e Permitir regras para o tráfego numa vNet. Para obter mais informações sobre os Grupos de Segurança, veja [aqui](https://azure.microsoft.com/blog/network-security-groups/).
3. **Forçar o túnel** — Esta é uma opção para redirecionar o tráfego vinculado à Internet com origem no Azure ser redirecionado através da ligação ExpressRoute ao centro de dados no local. Para obter mais informações sobre Forçar o túnel, veja [aqui](expressroute-routing.md#advertising-default-routes).  
4. **Encriptação** — Apesar dos circuitos ExpressRoute serem dedicados a um cliente específico, existe a possibilidade de o fornecedor de rede sofrer uma falha de segurança, permitindo a um intruso examinar o tráfego de pacotes. Para resolver este potencial problema, um cliente ou CSP pode encriptar tráfego através da ligação com a definição de políticas de modo de túnel IPSec para todo o tráfego que flui entre os recursos no local e do Azure (referência para o modo de túnel IPSec opcional para cliente 1 na figura de recursos 5: Segurança do ExpressRoute, acima). A segunda opção seria utilizar uma aplicação de firewall em cada ponto final do circuito ExpressRoute. Isto irá necessitar da instalação de VMs/Aplicações de firewall de terceiros adicionais em ambas as extremidades para encriptar o tráfego através de circuito ExpressRoute.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Passos Seguintes
O serviço do Fornecedor de Soluções em Nuvem permite-lhe aumentar o seu valor para os seus clientes sem a necessidade de comprar capacidades e infraestruturas dispendiosas, enquanto mantém a sua posição como o fornecedor principal de outsourcing. A integração totalmente integrada com o Microsoft Azure pode ser conseguida através da API de CSP, permitindo-lhe integrar a gestão do Microsoft Azure nas suas estruturas de gestão existentes.  

Pode encontrar informações adicionais nas seguintes ligações:

[Programa do Fornecedor de Soluções do Azure na Cloud](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Preparar-se para transacionar como um Fornecedor de Solução em Nuvem](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Recursos do Fornecedor de Soluções do Microsoft Cloud](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
