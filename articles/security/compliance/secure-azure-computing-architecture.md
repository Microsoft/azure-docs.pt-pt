---
title: Proteger a arquitetura de computação do Azure
description: Esta é uma arquitetura de referência para uma arquitetura de rede de Perímetro de nível empresarial, a utilização de aplicações virtuais de rede e outras ferramentas. Esta arquitetura foi projetada para requisitos do departamento de defesa seguro na Cloud de informática arquitetura funcional. No entanto, pode ser aproveitado para qualquer organização. Essa referência inclui duas opções automatizadas, recorre a aplicações Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917596"
---
# <a name="secure-azure-computing-architecture"></a>Proteger a arquitetura de computação do Azure

Aumentar rapidamente vários clientes de DoD implementar cargas de trabalho para o Azure tem vindo a pedir para obter orientações sobre como configurar redes virtuais seguras e configurar as ferramentas de segurança e serviços estipulados pela normas Dods e prática. DISA publicado a [documento de requisitos funcionais seguro na Cloud de informática arquitetura (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) em 2017. SCCA descreve os objetivos funcionais para proteger a defesa informações de sistema da rede (DISN) e pontos de ligação do fornecedor de Cloud comercial e como a atividade da empresa aplicações na cloud seguras de proprietários no limite de ligação. É exigida que todas as entidades do DoD que liga-se para a cloud comercial segue as diretrizes estabelecidas no FRD SCCA.
 
Existem quatro os componentes do SCCA. O ponto de acesso de nuvem do limite (BCAP), a pilha de segurança do Virtual Datacenter (VDSS), o Virtual Datacenter (VDMS) de serviços geridos e fidedigno o Gestor de credenciais de Cloud (TCCM). A Microsoft desenvolveu uma solução que atenderá aos requisitos SCCA IL4 e IL5 cargas de trabalho executadas no Azure. Esta solução específica do Azure é chamada Secure Azure computação arquitetura (SACA). Os clientes que implementem SACA estarão em conformidade com o FRD SCCA e permitem aos clientes do DoD mover cargas de trabalho para o Azure, uma vez conectado. 

Arquiteturas e documentação de orientação de SCCA são específicas para os clientes do DoD, as revisões mais recente para SACA também ajudará os clientes civis está em conformidade com diretrizes de ligação (TIC) de internet fidedigna, bem como os clientes comerciais que deseja implementar uma rede de Perímetro seguro para proteger seus ambientes do azure.


## <a name="secure-cloud-computing-architecture-components"></a>Componentes de arquitetura de informática na Cloud seguras

**BCAP**

O objetivo do BCAP é proteger o DISN contra ataques com origem no ambiente de cloud. BCAP irá efetuar a deteção de intrusão e prevenção, bem como filtrar o tráfego não autorizado. Este componente pode ser localizado conjuntamente com outros componentes do SCCA. É altamente recomendável que este componente é implementado com hardware físico. Veja a seguir encontrará a lista de requisitos de segurança BCAP.

***Requisitos de segurança BCAP***

![Matriz de requisitos de BCAP](media/bcapreqs.png)


**VDSS**

O objetivo do VDSS é a proteger aplicações de proprietário de missão DoD que estão alojadas no Azure. VDSS executa a maior parte das operações de segurança no SCCA. Ele vai realizar a inspeção de tráfego para proteger as aplicações em execução no Azure. Este componente pode ser fornecido no seu ambiente do Azure.

***Requisitos de segurança VDSS***

![Matriz de requisitos de VDSS](media/vdssreqs.png)

**VDMS**

A finalidade de VDMS é fornecer a segurança de anfitrião, bem como serviços do Centro de dados compartilhados. As funções de VDMS podem optar por executar no hub de sua SCCA ou o proprietário de missão pode implementar partes na sua própria subscrição do Azure específica. Este componente pode ser fornecido no seu ambiente do Azure.

***Requisitos de segurança VDMS***

![Matriz de requisitos de VDMS](media/vdmsreqs.png)


**TCCM**

TCCM é uma função de negócio. Essa pessoa será responsável por gerenciar o SCCA. As suas obrigações incluem estabelecer planos e as políticas de acesso de conta para o ambiente de cloud, garantindo a identidade e gestão de acesso está a funcionar corretamente e para manter o plano de gestão de credencial na Cloud. Essa pessoa é indicada pelo autorizar oficial. O BCAP VDSS e VDMS irá fornecer as funcionalidades necessárias para o TCCM executar a respetiva função de trabalho.

***Requisitos de segurança TCCM***

![Matriz de requisitos de TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Considerações de planejamento e componentes SACA 

A arquitetura de referência SACA foi concebida para implantar os componentes VDSS e VDMS no azure, bem como ativar o TCCM. Esta arquitetura é modular, o que significa que todas as partes de VDSS e VDMS podem viver num hub centralizado ou alguns dos controles podem ser atendidos no espaço de proprietário para a atividade ou até mesmo no local. A recomendação da nossa equipa da Microsoft é colocalizar os componentes VDSS e VDMS numa central Virtual Net que possa ser ligado através de todos os proprietários de missão. O diagrama abaixo ilustra a nossa arquitetura recomendada. 


![Diagrama de arquitetura de referência SACA](media/sacav2generic.png)

Ao planejar sua estratégia de conformidade SCCA e a arquitetura técnica, há muitos aspetos a considerar. É importante que os tópicos seguintes são levados em consideração desde o início, como todos os clientes serão necessário explicar isso. Os tópicos seguintes foram problemas que têm surgir com clientes reais do DoD e tendem a diminuir o planejamento e a execução. 

- Qual BCAP utilizará sua organização?
    - DISA BCAP
        - DISA tem dois BCAPs operacionais no pentágono e Camp Roberts AC, com um terceiro disponível online em breve. 
        - BCAPs do DISA todos têm circuitos do ExpressRoute para o Azure, que podem ser utilizados pelos clientes do DoD para conectividade. 
        - DISA já tem uma sessão de Peering da Microsoft de nível empresarial para os clientes do DoD que pretendem subscrever as ferramentas de SaaS da Microsoft, como o Office 365. Ao utilizar DISA BCAP, pode ativar a conectividade e o peering para a sua instância SACA. 
    - Criar seu próprio BCAP
        - Isso exigiria espaço num centro de dados localizadas conjuntamente da concessão e configurar um circuito do ExpressRoute para o Azure. 
        - Esta opção irá exigir a aprovação do adicional 
        - Devido à aprovação adicional e uma compilação física horizontalmente, esta opção demora mais tempo. 
    - A Microsoft recomenda utilizar a BCAP DISA. Esta opção está disponível, desenvolveu-se numa redundância e já tem clientes operar nela hoje na produção.
- Espaço IP encaminhável DoD
    - Precisará usar o espaço IP encaminhável DoD extremidade. A opção de NAT los no espaço IP privados no Azure está disponível.  
    - Entre em contato com DoD NIC para obter o espaço IP, serão necessários como parte da sua submissão SNAP com DISA. 
    - Se planeja NAT para o espaço de endereços privados no Azure, será necessário um mínimo de um /24 sub-rede de espaço de endereço atribuído a partir do NIC para cada região planeja implantar SACA. 
- Redundância 
    - A Microsoft sugere que implemente uma instância SACA em, pelo menos, duas regiões. Na cloud do DoD, isso significaria que implementá-la para ambas as regiões disponíveis do DoD. 
    - Recomenda-se também que ligar-se para, pelo menos, dois BCAPs através de circuitos do ExpressRoute separados. Ambas as rotas Express, em seguida, podem ser associadas à instância SACA de cada região. 
- Requisitos de componentes específicos do DoD
    - A sua organização tem os requisitos específicos fora os requisitos de SCCA? (Algumas organizações têm requisitos de IPS específicos)
- SACA é uma arquitetura modular  
    - Utilizar apenas os componentes que precisa para seu ambiente. 
        - Implemente as NVAs numa camada única ou várias camadas
        - Integrado IPS ou traga seu próprio IPS
- Nível de impacto DoD das suas aplicações e dados
    - Se houver qualquer possibilidade de aplicações em execução nas nossas regiões IL5, recomenda-se que crie a sua instância SACA no IL5. A instância pode ser utilizada em frente IL4 aplicativos, bem como IL5. No entanto, uma instância de IL4 SACA à frente de uma aplicação de IL5 provavelmente não irá receber acreditação. 
- O fornecedor de aplicação Virtual de rede irá utilizar para VDSS?
    - Como mencionado anteriormente, essa referência SACA pode ser criada usando uma variedade de aplicações e serviços do Azure. No entanto, temos os modelos de soluções automatizadas para implementar a arquitetura SACA com F5 e Citrix. Essas soluções serão abordadas mais detalhadamente abaixo. 
- Que serviços do Azure irá utilizar?
    - Existem serviços do Azure que podem atender aos requisitos em torno do log analytics, a proteção baseada no anfitrião e a funcionalidade de IDS. No entanto, é possível que alguns serviços não disponíveis em geral nas nossas regiões IL5. Isso pode levar à necessidade de usar algumas ferramentas de terceiros 3º se estes serviços do Azure não é possível cumprir os requisitos. Precisará examinar quais ferramentas são confortável e a viabilidade da utilização de ferramentas nativas do Azure. 
    - É recomendação da Microsoft que utilize tantos ferramentas do Azure nativas possíveis à medida que são criadas com segurança na cloud e integram facilmente com o restante da plataforma do Azure. Segue-se uma lista de ferramentas nativas do Azure que podem ser aproveitadas para cumprir diversos requisitos de SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Cofre de Chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Porta de entrada do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionamento
    - Um exercício de dimensionamento tem de ser concluída. Precisará observar o número de ligações em simultâneo, que poderá ter através da instância SACA, bem como os requisitos de débito de rede. 
    - Este é um fator crítico como ele irá ajudar a dimensionar as VMs, bem como ajudar a identificar as licenças que serão necessárias de vários fornecedores que irá utilizar na sua instância SACA. 
    - Não é possível efetuar uma análise de custo boa neste exercício de dimensionamento, também é importante certificar-se de que tudo o que é dimensionado corretamente para permitir o melhor desempenho. 


## <a name="most-common-deployment-scenario"></a>Cenário de implementação mais comuns

A Microsoft tem vários clientes que já passaram por toda a implantação ou, pelo menos, planejamento estágios de seus ambientes de SACA. Isso permitiu-nos obter informações sobre o cenário de implementação mais comuns. O diagrama abaixo ilustra a arquitetura mais comuns. 


![Diagrama de arquitetura de referência SACA](media/sacav2commonscenario.png) 


Como pode ver no diagrama, os clientes do DoD normalmente subscrevem duas do BCAPs DISA, um destes reside no costa oeste e a outra vida na costa leste. Um elemento de rede privado do ExpressRoute está ativado para o Azure em cada localização DISA BCAP. Estes elementos de rede do ExpressRoute, em seguida, estão ligados ao Gateway de rede Virtual na DoD East e regiões do DoD Central do Azure. Uma instância SACA é implementada na região Leste do DoD e o Azure Central do DoD e todos os entrada e saída fluxos de tráfego através do mesmo de e para a ligação de Expressroute para o BCAP DISA. 

Missão proprietário aplicativos, em seguida, escolher quais regiões do Azure irá implementar seus aplicativos em e utilização de Peering de rede Virtual para ligar o seu aplicativo 's rede Virtual para a rede Virtual de SACA. Impor o túnel de todo o tráfego através da instância VDSS. 

Esta arquitetura é altamente recomendada pela Microsoft, como ele atenderá aos requisitos de SCCA, é altamente disponíveis e facilmente dimensionáveis e simplifica a implantação e gerenciamento.

## <a name="automated-saca-deployment-options"></a>Opções de implementação de SACA automatizadas

 Anteriormente, mencionamos que a Microsoft fez parceria com dois fornecedores para criar um modelo de infraestrutura SACA automatizado. Os dois modelos irão implementar os seguintes componentes do Azure. 

- Rede Virtual de SACA
    - Sub-rede de gestão
        - Onde as VMs de gestão e serviços são implementados (as caixas de ligação)
        - VDMS sub-rede
            - Onde as VMs e serviços utilizados para VDMS são implementados
        - Sub-redes fidedignas e não fidedignas 
            - Em que as aplicações virtuais são implementadas
        - Sub-rede de gateway
            - Onde será implementado o Gateway do ExpressRoute
- Máquinas de virtuais de caixa de atalhos de gestão
    - Utilizado para gestão fora de banda do ambiente.
- Aplicações virtuais de rede
    - Citrix ou F5, dependendo do modelo de implementação.
- IPs Públicos
    - Utilizado para front-end até que o ExpressRoute é colocado online. Estes IPs serão convertidos para o espaço de endereço privado do Azure de back-end
- Tabelas de Rota 
    - Aplicadas durante a automação, estas tabelas rota imposição de túnel, todo o tráfego através do dispositivo virtual
- Balanceadores de carga do Azure - Standard SKU
    - Estes são utilizados para tráfego de balanceamento de carga entre os dispositivos de
- Grupos de Segurança de Rede
    - Utilizado para controlar quais tipos de tráfego podem percorrer para certos pontos de extremidade


**Implementação de Citrix SACA**

Citrix criou um modelo de implementação que implementa duas camadas de aplicações de elevada disponibilidade Citrix ADC. Esta arquitetura cumpre os requisitos de VDSS. 

![Diagrama de Citrix SACA](media/citrixsaca.png)


Documentação do Citrix e a implementação de script pode ser encontrado [aqui.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Implementação de F5 SACA**

F5 criou dois modelos de implantação à parte que abrangem duas arquiteturas diferentes. A primeira tem apenas uma camada de F5 aplicações de uma configuração de elevada disponibilidade de ativo-ativo. Esta arquitetura cumpre os requisitos para VDSS. A segunda adiciona uma segunda camada de F5s ativas / ativas de elevada disponibilidade. O objetivo essa segunda camada é permitir aos clientes adicionar seus próprios IPS separado da F5 entre as camadas de F5. Nem todos os componentes do DoD tem IPS específicos prescrito para utilização. Se for esse o caso, a única camada de aparelhos de F5 funcionará para mais desde que a arquitetura inclui IPS nos dispositivos F5.  

![Diagrama de Citrix SACA](media/f5saca.png)

Documentação de F5 e implementação de script pode ser encontrado [aqui.](https://github.com/f5devcentral/f5-azure-saca) 












