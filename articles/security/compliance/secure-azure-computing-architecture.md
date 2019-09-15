---
title: Arquitetura de Computação Protegida do Azure
description: Essa arquitetura de referência para uma arquitetura DMZ de nível empresarial usa soluções de virtualização de rede e outras ferramentas. Essa arquitetura foi projetada para atender aos requisitos funcionais da arquitetura de computação em nuvem segura do departamento de defesa. Ele também pode ser usado para qualquer organização. Essa referência inclui duas opções automatizadas que usam dispositivos Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001864"
---
# <a name="secure-azure-computing-architecture"></a>Arquitetura de Computação Protegida do Azure

As Os clientes do departamento de defesa (DoD) que implantam cargas de trabalho no Azure solicitaram orientação para configurar redes virtuais seguras e configurar as ferramentas de segurança e os serviços que são estipulados por padrões e práticas de DoD. 

A DISA (Agência de informações de defesa) publicou o [documento de requisitos funcionais (SCCA) de arquitetura de computação em nuvem seguro (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) em 2017. SCCA descreve os objetivos funcionais para proteger os pontos de conexão da rede do sistema de informações de defesa (DISN) e do provedor de nuvem comercial. SCCA também descreve como os proprietários de missão protegem os aplicativos de nuvem no limite de conexão. Toda entidade DoD que se conecta à nuvem comercial deve seguir as diretrizes estabelecidas no SCCA FRD.
 
O SCCA tem quatro componentes:
 
- Ponto de acesso à nuvem de limite (BCAP)
- VDSS (pilha de segurança de datacenter virtual)
- Serviço gerenciado de datacenter virtual (VDMS)
- Gerenciador de credenciais de nuvem confiável (TCCM) 

A Microsoft desenvolveu uma solução que atende aos requisitos de SCCA para cargas de trabalho IL4 e IL5 que são executadas no Azure. Essa solução específica do Azure é chamada de SACA (arquitetura de computação do Azure segura). Os clientes que implantam SACA estão em conformidade com o SCCA FRD. Eles podem permitir que os clientes do DoD movam cargas de trabalho para o Azure depois que estiverem conectados.

Diretrizes e arquiteturas de SCCA são específicas para clientes do DoD, mas as últimas revisões para SACA ajudam os clientes do civis a cumprir as diretrizes de jogo (conexão de Internet confiável). As revisões mais recentes também ajudam os clientes comerciais que desejam implementar uma DMZ segura para proteger seus ambientes do Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Proteger componentes da arquitetura de computação em nuvem

### <a name="bcap"></a>BCAP

A finalidade do BCAP é proteger o DISN contra ataques originados no ambiente de nuvem. O BCAP executa detecção e prevenção de intrusão. Ele também filtra o tráfego não autorizado. Esse componente pode ser colocalizado com outros componentes do SCCA. Recomendamos que você implante esse componente usando o hardware físico. Os requisitos de segurança do BCAP são listados na tabela a seguir.

#### <a name="bcap-security-requirements"></a>Requisitos de segurança do BCAP

![Matriz de requisitos do BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

A finalidade do VDSS é proteger aplicativos DoD de missão que são hospedados no Azure. VDSS executa a massa das operações de segurança no SCCA. Ele realiza a inspeção de tráfego para proteger os aplicativos que são executados no Azure. Esse componente pode ser fornecido em seu ambiente do Azure.

#### <a name="vdss-security-requirements"></a>Requisitos de segurança do VDSS

![Matriz de requisitos do VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

A finalidade do VDMS é fornecer segurança de host e serviços de data center compartilhados. As funções do VDMS podem ser executadas no Hub de seu SCCA ou o proprietário da missão pode implantar partes dela em sua própria assinatura específica do Azure. Esse componente pode ser fornecido em seu ambiente do Azure.

#### <a name="vdms-security-requirements"></a>Requisitos de segurança do VDMS

![Matriz de requisitos do VDMS](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM é uma função comercial. Essa pessoa é responsável por gerenciar o SCCA. Suas tarefas são: 

- Estabeleça planos e políticas de acesso à conta para o ambiente de nuvem. 
- Verifique se o gerenciamento de identidade e acesso está operando corretamente. 
- Mantenha o plano de gerenciamento de credenciais de nuvem. 

Essa pessoa é indicada pela autorização oficial. O BCAP, o VDSS e o VDMS fornecem os recursos que o TCCM precisa para executar seu trabalho.

#### <a name="tccm-security-requirements"></a>Requisitos de segurança do TCCM

![Matriz de requisitos do TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Componentes e considerações de planejamento do SACA 

A arquitetura de referência do SACA foi projetada para implantar os componentes VDSS e VDMS no Azure e para habilitar o TCCM. Essa arquitetura é modular. Todas as partes de VDSS e VDMS podem residir em um hub centralizado. Alguns dos controles podem ser atendidos no espaço de proprietário da missão ou mesmo localmente. A Microsoft recomenda que você coaloque os componentes VDSS e VDMS em uma rede virtual central com a qual todos os proprietários podem se conectar. O diagrama a seguir mostra essa arquitetura: 


![Diagrama da arquitetura de referência do SACA](media/sacav2generic.png)

Ao planejar sua estratégia SCCA conformidade e sua arquitetura técnica, considere os tópicos a seguir desde o início porque eles afetam todos os clientes. Os seguintes problemas surgiram com os clientes do DoD e tendem a reduzir o planejamento e a execução. 

#### <a name="which-bcap-will-your-organization-use"></a>Qual BCAP sua organização usará?
   - BCAP DISA:
        - DISA tem dois BCAPs operacionais no Pentágono e em Camp Roberts, CA. Um terceiro está planejado para ficar online em breve. 
        - DISA BCAPs All tem circuitos do Azure ExpressRoute para o Azure, que pode ser usado por clientes do DoD para conectividade. 
        - O DISA tem uma sessão de emparelhamento da Microsoft de nível empresarial para clientes do DoD que desejam assinar ferramentas de SaaS (software como serviço) da Microsoft, como o Office 365. Usando o DISA BCAP, você pode habilitar a conectividade e o emparelhamento para sua instância do SACA. 
    - Crie seu próprio BCAP:
        - Essa opção exige que você conceda espaço em um data center colocalizado e configure um circuito do ExpressRoute para o Azure. 
        - Esta opção requer aprovação adicional. 
        - Devido à aprovação adicional e à criação física, essa opção leva mais tempo. 
    - Recomendamos que você use o DISA BCAP. Essa opção está prontamente disponível, tem redundância interna e tem clientes que operam nela hoje em produção.
- Espaço IP roteável do DoD:
    - Você deve usar o espaço IP roteável do DoD em sua borda. A opção de usar o NAT para conectar esses espaços ao espaço IP privado no Azure está disponível.
    - Entre em contato com o DoD (centro de informações de rede) para obter o espaço IP. Você precisa dela como parte do envio do seu sistema/processo de aprovação de rede (SNAP) com o DISA. 
    - Se você planeja usar o NAT para conectar o espaço de endereço privado no Azure, precisará de um mínimo de uma sub-rede/24 de espaço de endereço atribuído da NIC para cada região em que você planeja implantar SACA.
- Redundância:
    - Implante uma instância do SACA em pelo menos duas regiões. Na nuvem DoD, você o implanta em ambas as regiões do DoD disponíveis.
    - Conecte-se a pelo menos dois BCAPs por meio de circuitos de ExpressRoute separados. Ambas as conexões de ExpressRoute podem então ser vinculadas à instância de SACA de cada região. 
- Requisitos específicos do componente DoD:
    - Sua organização tem quaisquer requisitos específicos fora dos requisitos do SCCA? Algumas organizações têm requisitos de IPS específicos.
- SACA é uma arquitetura modular:
    - Use apenas os componentes necessários para o seu ambiente. 
        - Implantar soluções de virtualização de rede em uma única camada ou várias camadas.
        - Use IPS integrados ou traga seus próprios IPS.
- Nível de impacto do DoD de seus aplicativos e dados:
    - Se houver qualquer possibilidade de aplicativos em execução nas regiões do Microsoft IL5, crie sua instância do SACA no IL5. A instância pode ser usada na frente de aplicativos IL4 e IL5. Uma instância IL4 SACA na frente de um aplicativo IL5 provavelmente não receberá a certificação.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Qual fornecedor de solução de virtualização de rede será usado para VDSS?
Conforme mencionado anteriormente, você pode criar essa referência de SACA usando uma variedade de dispositivos e serviços do Azure. A Microsoft tem modelos de solução automatizados para implantar a arquitetura SACA com F5 e Citrix. Essas soluções são abordadas na seção a seguir.

#### <a name="which-azure-services-will-you-use"></a>Quais serviços do Azure serão usados?
- Há serviços do Azure que podem atender aos requisitos de log Analytics, proteção baseada em host e funcionalidade de IDS. É possível que alguns serviços não estejam geralmente disponíveis nas regiões do Microsoft IL5. Nesse caso, talvez seja necessário usar ferramentas de terceiros se esses serviços do Azure não puderem atender às suas necessidades. Examine as ferramentas com as quais você está familiarizado e a viabilidade de usar as ferramentas nativas do Azure.
- Recomendamos que você use o máximo possível de ferramentas nativas do Azure. Eles são criados com segurança na nuvem em mente e se integram perfeitamente com o restante da plataforma Azure. Use as ferramentas nativas do Azure na lista a seguir para atender a vários requisitos do SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Cofre de Chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Gateway de Aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Porta frontal do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Grupos de segurança do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Sentinela do Azure](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionamento
    - Um exercício de dimensionamento deve ser concluído. Examine o número de conexões simultâneas que você pode ter por meio da instância SACA e dos requisitos de taxa de transferência de rede. 
    - Esta etapa é crítica. Ele ajuda a dimensionar as VMs e a identificar as licenças que são necessárias dos vários fornecedores que você usa em sua instância do SACA. 
    - Uma boa análise de custo não pode ser feita sem esse exercício de dimensionamento. O dimensionamento correto também permite o melhor desempenho. 


## <a name="most-common-deployment-scenario"></a>Cenário de implantação mais comum

 Vários clientes da Microsoft passaram pela implantação completa ou pelo menos pelos estágios de planejamento de seus ambientes SACA. Suas experiências revelaram informações sobre o cenário de implantação mais comum. O diagrama a seguir mostra a arquitetura mais comum: 


![Diagrama da arquitetura de referência do SACA](media/sacav2commonscenario.png) 


Como você pode ver no diagrama, os clientes do DoD normalmente assinam duas das DISA BCAPs. Uma delas reside na costa oeste e a outra reside na costa leste. Um par privado do ExpressRoute está habilitado para o Azure em cada local DISA BCAP. Esses pares de ExpressRoute são então vinculados ao gateway de rede virtual nas regiões do Azure do DoD Oriental e do DoD central. Uma instância do SACA é implantada nas regiões do Azure do DoD Oriental e do DoD central. Todo o tráfego de entrada e saída flui através dele para e da conexão do ExpressRoute para o DISA BCAP.

Aplicativos de missão e escolha as regiões do Azure nas quais eles planejam implantar seus aplicativos. Eles usam o emparelhamento de rede virtual para conectar a rede virtual do aplicativo à rede virtual SACA. Em seguida, eles forçam o túnel de todo o tráfego por meio da instância VDSS.

Recomendamos essa arquitetura, pois ela atende aos requisitos de SCCA. Ele é altamente disponível e facilmente escalonável. Ele também simplifica a implantação e o gerenciamento.

## <a name="automated-saca-deployment-options"></a>Opções de implantação automatizada do SACA

 Como mencionado anteriormente, a Microsoft estabeleceu um parceria com dois fornecedores para criar um modelo de infraestrutura de SACA automatizado. Os dois modelos implantam os seguintes componentes do Azure: 

- Rede virtual SACA
    - Sub-rede de gerenciamento
        - Essa sub-rede é onde os serviços e VMs de gerenciamento são implantados, também conhecidos como caixas de salto.
        - Sub-rede VDMS
            - Essa sub-rede é onde as VMs e os serviços usados para VDMS são implantados.
        - Sub-redes confiáveis e não confiáveis
            - Essas sub-redes são onde os dispositivos virtuais são implantados.
        - Sub-rede do gateway
            - Essa sub-rede é onde o gateway de ExpressRoute é implantado.
- Máquinas virtuais da caixa de salto de gerenciamento
    - Eles são usados para gerenciamento fora de banda do ambiente.
- Aplicações virtuais de rede
    - Você usa o Citrix ou o F5 com base em qual modelo você implanta.
- IPs Públicos
    - Eles são usados para o front-end até que o ExpressRoute seja colocado online. Esses IPs são convertidos para o espaço de endereço privado do Azure de back-end.
- Tabelas de rotas 
    - Aplicado durante a automação, essas tabelas de rotas forçam o túnel de todo o tráfego por meio do dispositivo virtual.
- Balanceadores de carga do Azure-SKU Standard
    - Eles são usados para balancear a carga do tráfego entre os dispositivos.
- Grupos de segurança de rede
    - Eles são usados para controlar quais tipos de tráfego podem atravessar determinados pontos de extremidade.


### <a name="citrix-saca-deployment"></a>Implantação do Citrix SACA

Um modelo de implantação Citrix implanta duas camadas de dispositivos Citrix ADC altamente disponíveis. Essa arquitetura atende aos requisitos do VDSS. 

![Diagrama do Citrix SACA](media/citrixsaca.png)


Para obter a documentação do Citrix e o script de implantação, consulte [este link do GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Implantação de SACA F5

Dois modelos de implantação F5 separados abrangem duas arquiteturas diferentes. O primeiro modelo tem apenas uma camada de dispositivos F5 em uma configuração de alta disponibilidade de ativo-ativo. Essa arquitetura atende aos requisitos do VDSS. O segundo modelo adiciona uma segunda camada de F5s de alta disponibilidade ativo-ativo. Essa segunda camada permite que os clientes adicionem seus próprios IPS separados de F5 entre as camadas F5. Nem todos os componentes do DoD têm IPS específicos prescritos para uso. Se esse for o caso, a única camada de dispositivos F5 funcionará para a maioria porque essa arquitetura inclui IPS nos dispositivos F5.

![Diagrama de SACA F5](media/f5saca.png)

Para a documentação do F5 e o script de implantação, consulte [este link do GitHub](https://github.com/f5devcentral/f5-azure-saca).












