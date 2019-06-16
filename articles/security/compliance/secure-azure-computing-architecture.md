---
title: Arquitetura de Computação Protegida do Azure
description: Esta arquitetura de referência para uma arquitetura de rede de Perímetro de nível empresarial utiliza aplicações virtuais de rede e outras ferramentas. Esta arquitetura foi projetada para requisitos do departamento de defesa seguro na Cloud de informática arquitetura funcional. Ele também pode ser usado para qualquer organização. Essa referência inclui duas opções automatizadas que utilizam aplicações Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963234"
---
# <a name="secure-azure-computing-architecture"></a>Arquitetura de Computação Protegida do Azure

As Os clientes do departamento de defesa (DoD) que implementar cargas de trabalho para o Azure tem-lhe pedido para obter orientações configurar as redes virtuais seguras e configurar as ferramentas de segurança e serviços que estão estipulados pela normas Dods e prática. 

A defesa informações sistema Agency (DISA) publicado a [seguro na Cloud de informática arquitetura (SCCA) documento de requisitos funcionais (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) em 2017. SCCA descreve os objetivos funcionais para proteger a defesa informações de sistema da rede (DISN) e a cloud comercial pontos de ligação do fornecedor. SCCA também descreve como os proprietários de missão seguros na cloud a aplicações com o limite de ligação. Cada entidade do DoD que liga-se para a cloud comercial tem de seguir as diretrizes estabelecidas no FRD SCCA.
 
O SCCA tem quatro componentes:
 
- Ponto de acesso de nuvem do limite (BCAP)
- Pilha de segurança do virtual Datacenter (VDSS)
- Virtual Datacenter Managed Service (VDMS)
- Gestor de credenciais de Cloud fidedigna (TCCM) 

A Microsoft desenvolveu uma solução que satisfaça os requisitos de SCCA para IL4 e IL5 cargas de trabalho que são executadas no Azure. Esta solução específica do Azure é chamada Secure Azure computação arquitetura (SACA). Os clientes que implementem SACA estão em conformidade com o FRD SCCA. Pode permitem que os clientes do DoD mover as cargas de trabalho para o Azure depois de eles estão conectados.

Arquiteturas e documentação de orientação de SCCA são específicas para os clientes do DoD, mas as revisões mais recente para os clientes civis de ajuda SACA está em conformidade com diretrizes de ligação (TIC) de internet fidedigna. As revisões de mais recente também ajudam os clientes comerciais que deseja implementar uma rede de Perímetro segura para proteger seus ambientes do Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Componentes de arquitetura de informática na Cloud seguras

### <a name="bcap"></a>BCAP

O objetivo do BCAP é proteger o DISN contra ataques que têm origem no ambiente de cloud. BCAP executa a deteção de intrusão e prevenção. também filtra o tráfego não autorizado. Este componente pode ser localizado conjuntamente com outros componentes do SCCA. Recomendamos que implemente este componente com o hardware físico. Requisitos de segurança BCAP estão listados na tabela seguinte.

#### <a name="bcap-security-requirements"></a>Requisitos de segurança BCAP

![Matriz de requisitos de BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

O objetivo do VDSS é proteger aplicativos de missão proprietário do DoD que estão alojados no Azure. VDSS executa a maior parte das operações de segurança no SCCA. Ele realiza a inspeção de tráfego para proteger as aplicações que executar no Azure. Este componente pode ser fornecido no seu ambiente do Azure.

#### <a name="vdss-security-requirements"></a>Requisitos de segurança VDSS

![Matriz de requisitos de VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

A finalidade do VDMS é fornecer a segurança do host e serviços do Centro de dados compartilhados. As funções de VDMS podem optar por executar no hub de sua SCCA ou o proprietário de missão pode implementar partes na sua própria subscrição do Azure específica. Este componente pode ser fornecido no seu ambiente do Azure.

#### <a name="vdms-security-requirements"></a>Requisitos de segurança VDMS

![Matriz de requisitos de VDMS](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM é uma função de negócio. Essa pessoa é responsável por gerenciar o SCCA. As suas obrigações são: 

- Estabelece planos e as políticas de acesso de conta para o ambiente de cloud. 
- Certifique-se de que a gestão de identidades e acesso está a funcionar corretamente. 
- Manter o plano de gestão de credenciais de Cloud. 

Essa pessoa é indicada pelo autorização oficial. O BCAP VDSS e VDMS fornecem as funcionalidades de que o TCCM precisa de executar seu trabalho.

#### <a name="tccm-security-requirements"></a>Requisitos de segurança TCCM

![Matriz de requisitos de TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Considerações de planejamento e componentes SACA 

A arquitetura de referência SACA foi concebida para implantar os componentes VDSS e VDMS no Azure e ativar o TCCM. Esta arquitetura é modular. Todas as partes de VDSS e VDMS podem viver num hub centralizado. Alguns dos controles podem ser atendidos no espaço de missão proprietário ou até mesmo no local. A Microsoft recomenda que colocalizar os componentes VDSS e VDMS numa rede virtual central que podem ser ligado através de todos os proprietários de missão. O diagrama seguinte mostra esta arquitetura: 


![Diagrama de arquitetura de referência SACA](media/sacav2generic.png)

Quando planear a sua estratégia de conformidade SCCA e a arquitetura técnica, considere os seguintes tópicos desde o início, porque elas afetam todos os clientes. Os seguintes problemas têm surgir com os clientes do DoD e tendem a diminuir o planejamento e a execução. 

#### <a name="which-bcap-will-your-organization-use"></a>Qual BCAP utilizará sua organização?
   - DISA BCAP:
        - DISA tem dois BCAPs operacionais o pentágono e em Camp Roberts, AC. Um terceiro está previsto a ficar online em breve. 
        - BCAPs do DISA que todos os circuitos do ExpressRoute do Azure para o Azure, que pode ser utilizado por clientes do DoD para conectividade de ter. 
        - DISA tem uma sessão peering da Microsoft de nível empresarial para os clientes do DoD que pretendem subscrever o software da Microsoft como ferramentas de serviço (SaaS), como o Office 365. Ao utilizar o BCAP DISA, pode ativar a conectividade e o peering para a sua instância SACA. 
    - Crie seu próprio BCAP:
        - Esta opção requer a concessão espaço num centro de dados localizadas conjuntamente e configurar um circuito do ExpressRoute para o Azure. 
        - Esta opção requer aprovação adicional. 
        - Devido a aprovação adicional e um limite de compilação físico, esta opção demora mais tempo. 
    - Recomendamos que utilize o BCAP DISA. Esta opção está disponível, redundância incorporada e tem os clientes que operam de hoje na produção.
- Espaço IP encaminhável DoD:
    - Tem de utilizar o espaço IP encaminhável DoD extremidade. A opção para utilizar o NAT para ligar os espaços ao espaço IP privados no Azure está disponível.
    - Contacte o Centro de informações de rede (NIC) o DoD para obter o espaço de IP. Precisa como parte da sua submissão para o processo de aprovação de sistema/rede (SNAP) com DISA. 
    - Se planeja usar o NAT para ligar o espaço de endereços privados no Azure, é necessário um mínimo de um /24 sub-rede de espaço de endereço atribuído a partir do NIC para cada região em que planeja implantar SACA.
- Redundância:
    - Implemente uma instância SACA em, pelo menos, duas regiões. Na DoD cloud, implementá-la para ambas as regiões disponíveis do DoD.
    - Ligar a, pelo menos, dois BCAPs através de circuitos do ExpressRoute separados. Ambas as ligações do ExpressRoute, em seguida, podem ser associadas à instância SACA de cada região. 
- Requisitos de componente específico do DoD:
    - A sua organização tem os requisitos específicos fora os requisitos de SCCA? Algumas organizações têm requisitos de IPS específicos.
- SACA é uma arquitetura modular:
    - Utilize apenas os componentes de que necessita para o seu ambiente. 
        - Implemente aplicações virtuais de rede numa camada única ou várias camadas.
        - Utilize IPS integrada ou Traga as suas próprias IPS.
- Nível de impacto DoD das suas aplicações e dados:
    - Se houver qualquer possibilidade de aplicações em execução no Microsoft IL5 regiões, crie a sua instância SACA no IL5. A instância pode ser utilizada em frente IL4 aplicativos e IL5. Uma instância de IL4 SACA à frente de uma aplicação de IL5 provavelmente não receberá acreditação.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>O fornecedor da aplicação virtual de rede irá utilizar para VDSS?
Como mencionado anteriormente, pode criar esta referência SACA utilizando uma variedade de aplicações e serviços do Azure. Microsoft tenha automatizado os modelos de soluções para implementar a arquitetura SACA com F5 e Citrix. Estas soluções são abordadas na secção seguinte.

#### <a name="which-azure-services-will-you-use"></a>Que serviços do Azure irá utilizar?
- Existem serviços do Azure que podem atender aos requisitos para o log analytics, a proteção baseada no anfitrião e a funcionalidade de IDS. É possível que alguns serviços não disponíveis em geral em regiões do Microsoft IL5. Neste caso, precisará de utilizar as ferramentas de terceiros se estes serviços do Azure não é possível satisfazer as suas necessidades. Examinar as ferramentas que se sente confortável e a viabilidade da utilização de ferramentas nativas do Azure.
- Recomendamos que utilize tantos ferramentas nativas do Azure quanto possível. Eles são criados com segurança na cloud e integram facilmente com o restante da plataforma do Azure. Utilize as ferramentas nativas do Azure na lista seguinte para cumprir diversos requisitos de SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Cofre de Chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Porta de entrada do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Grupos de segurança do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionamento
    - Um exercício de dimensionamento deve ser concluído. Veja o número de ligações em simultâneo, que poderá ser necessário por meio da instância SACA e os requisitos de débito de rede. 
    - Este passo é fundamental. Ele ajuda a dimensionar as VMs e identificar as licenças necessárias de vários fornecedores que utilizar na sua instância SACA. 
    - Uma análise de custo boa não pode ser feita sem este exercício de dimensionamento. Também permite dimensionamento correto para um melhor desempenho. 


## <a name="most-common-deployment-scenario"></a>Cenário de implementação mais comuns

 Vários clientes da Microsoft passaram por meio da implantação completa ou em, pelo menos, fase de planejamento de seus ambientes de SACA. Suas experiências reveladas informações sobre o cenário de implementação mais comuns. O diagrama seguinte mostra a arquitetura mais comuns: 


![Diagrama de arquitetura de referência SACA](media/sacav2commonscenario.png) 


Como pode ver no diagrama, os clientes do DoD normalmente subscrevem duas do BCAPs DISA. Um dos seguintes perdura na costa oeste e a outra vida a costa leste. Um elemento de rede privado do ExpressRoute está ativado para o Azure em cada localização DISA BCAP. Estes elementos de rede do ExpressRoute, em seguida, estão ligados ao gateway de rede virtual nas regiões leste do DoD e o Azure Central do DoD. É implementada uma instância SACA nas regiões leste do DoD e o Azure Central do DoD. Todo o tráfego de entrada e de saída flui através do mesmo, de e para a ligação do ExpressRoute para o BCAP DISA.

Aplicativos de missão proprietário, em seguida, escolha regiões do Azure na qual eles planeja implantar seus aplicativos. Eles usam a rede virtual peering para ligar uma rede virtual do seu aplicativo para a rede virtual de SACA. Em seguida, eles forçar túnel de todo o tráfego através da instância VDSS.

Recomendamos esta arquitetura de uma vez que ele atende aos requisitos de SCCA. É altamente disponível e dimensionável facilmente. Também simplifica a implantação e gerenciamento.

## <a name="automated-saca-deployment-options"></a>Opções de implementação de SACA automatizadas

 Conforme mencionado anteriormente, Microsoft associou-se a dois fornecedores para criar um modelo de infraestrutura SACA automatizado. Ambos os modelos de implementam os componentes do Azure seguintes: 

- Rede virtual de SACA
    - Sub-rede de gestão
        - Esta sub-rede é onde as VMs de gestão e serviços são implementados, também conhecido como caixas de atalhos.
        - Sub-rede VDMS
            - Esta sub-rede é onde as VMs e serviços utilizados para VDMS estão implementados.
        - Sub-redes fidedignas e não fidedignas
            - Estas sub-redes estão onde as aplicações virtuais são implementadas.
        - Sub-rede de gateway
            - Esta sub-rede é onde o Gateway do ExpressRoute está implementado.
- Máquinas de virtuais de caixa de atalhos de gestão
    - Eles são usados para a gestão fora de banda do ambiente.
- Aplicações virtuais de rede
    - Utilizar qualquer um dos Citrix ou F5 com base na qual modelo implementa.
- IPs públicos
    - Eles são usados para o front-end até que o ExpressRoute é colocado online. Estes IPs traduzir-se para o espaço de endereço privado do Azure de back-end.
- Tabelas de rotas 
    - Aplicadas durante a automação, estes encaminham tabelas forçar túnel todo o tráfego através do dispositivo virtual.
- Balanceadores de carga do Azure - Standard SKU
    - Eles são usados para tráfego de balanceamento de carga nos dispositivos.
- Grupos de segurança de rede
    - Eles são usados para controlar quais tipos de tráfego podem percorrer para certos pontos de extremidade.


### <a name="citrix-saca-deployment"></a>Implementação de Citrix SACA

Um modelo de implementação da Citrix implementa duas camadas de aplicações de elevada disponibilidade Citrix ADC. Esta arquitetura cumpre os requisitos de VDSS. 

![Diagrama de Citrix SACA](media/citrixsaca.png)


Para a documentação do Citrix e o script de implementação, consulte [esta ligação do GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Implementação de F5 SACA

Dois modelos de implementação de F5 distintos abrangem duas arquiteturas diferentes. O primeiro modelo tem apenas uma camada de F5 aplicações de uma configuração de elevada disponibilidade de ativo-ativo. Esta arquitetura cumpre os requisitos para VDSS. O segundo modelo adiciona uma segunda camada de F5s ativas / ativas de elevada disponibilidade. Essa segunda camada permite aos clientes adicionar seus próprios IPS separado da F5 entre as camadas de F5. Nem todos os componentes do DoD tem IPS específicos prescrito para utilização. Se for esse o caso, a única camada de aparelhos de F5 funciona para a maioria porque essa arquitetura inclui IPS nos dispositivos F5.

![Diagrama de F5 SACA](media/f5saca.png)

Para a documentação de F5 e o script de implementação, consulte [esta ligação do GitHub](https://github.com/f5devcentral/f5-azure-saca).












