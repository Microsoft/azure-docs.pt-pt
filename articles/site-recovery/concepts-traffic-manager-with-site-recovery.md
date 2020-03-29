---
title: Gestor de Tráfego Azure com recuperação do site Azure Microsoft Docs
description: Descreve como usar o Gestor de Tráfego Azure com a Recuperação do Site Azure para recuperação e migração de desastres
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947805"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Gestor de Tráfego do Azure com o Azure Site Recovery

O Gestor de Tráfego Azure permite controlar a distribuição do tráfego através dos pontos finais da sua aplicação. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Traffic Manager utiliza o Sistema de Nome de Domínio (DNS) para direcionar os pedidos dos clientes para o ponto final mais adequado, com base num método de encaminhamento de tráfego e na saúde dos pontos finais. O Gestor de Tráfego proporciona vários [métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md) e [opções de monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. Os clientes ligam diretamente ao ponto final selecionado. O Traffic Manager não é um representante ou um portal, e não vê o tráfego a passar entre o cliente e o serviço.

Este artigo descreve como pode combinar o encaminhamento inteligente do Azure Traffic Monitor com as poderosas capacidades de recuperação e migração de desastres do Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>No local para o fracasso de Azure

Para o primeiro cenário, considere a **Empresa A** que tem toda a sua infraestrutura de aplicação em funcionamento no seu ambiente no local. Por razões de continuidade e conformidade do negócio, a **Empresa A** decide utilizar a Recuperação do Site Azure para proteger as suas aplicações.

**A Empresa A** está a executar aplicações com pontos finais públicos e quer a capacidade de redirecionar o tráfego para Azure num evento de desastre. O método [prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) de encaminhamento de tráfego em Azure Traffic Manager permite à Empresa A implementar facilmente este padrão de failover.

A configuração é a seguinte:
- **Empresa A** cria um perfil de Gestor de [Tráfego.](../traffic-manager/traffic-manager-create-profile.md)
- Utilizando o método de encaminhamento **prioritário,** a **Empresa A** cria dois pontos finais – **Primário** para instalações e **Failover** para Azure. **A Primária** é atribuída prioridade 1 e **a Failover** é atribuída prioridade 2.
- Uma **vez** que o ponto final primário está hospedado fora de Azure, o ponto final é criado como um ponto final [externo.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)
- Com a Recuperação do Site Azure, o site Azure não dispõe de máquinas ou aplicações virtuais em funcionamento antes da falha. Assim, o ponto final **failover** também é criado como um ponto final **externo.**
- Por predefinição, o tráfego do utilizador é direcionado para a aplicação no local porque esse ponto final tem a maior prioridade associada a ela. Nenhum tráfego é direcionado para Azure se o ponto final **primário** for saudável.

![No local-para-Azure antes do fracasso](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Num evento de desastre, a Empresa A pode desencadear uma [falha](site-recovery-failover.md) no Azure e recuperar as suas aplicações no Azure. Quando o Gestor de Tráfego Azure deteta que o ponto final **primário** já não é saudável, utiliza automaticamente o ponto final **failover** na resposta DNS e os utilizadores ligam-se à aplicação recuperada no Azure.

![No local-para-Azure após falha](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Dependendo dos requisitos empresariais, a **Empresa A** pode escolher uma frequência de [sondagem](../traffic-manager/traffic-manager-monitoring.md) mais alta ou menor para mudar entre as instalações para o Azure num evento de desastre, e garantir o mínimo de tempo de inatividade para os utilizadores.

Quando o desastre está contido, a **Empresa A** pode falhar de Azure para o seu ambiente no local ([VMware](vmware-azure-failback.md) ou [Hyper-V](hyper-v-azure-failback.md)) usando a Recuperação do Site Azure. Agora, quando o Gestor de Tráfego deteta que o ponto final **primário** está novamente saudável, utiliza automaticamente o ponto final **primário** nas suas respostas DNS.

## <a name="on-premises-to-azure-migration"></a>No local para a migração de Azure

Além da recuperação de desastres, a Recuperação do Sítio Azure também permite [migrações para Azure.](migrate-overview.md) Utilizando as poderosas capacidades de failover de teste do Azure Site Recovery, os clientes podem avaliar o desempenho da aplicação no Azure sem afetar o seu ambiente no local. E quando os clientes estão prontos para migrar, podem optar por migrar cargas inteiras em conjunto ou optar por migrar e escalar gradualmente.

O método de encaminhamento [ponderado](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) do Gestor de Tráfego Azure pode ser usado para direcionar parte do tráfego de entrada para Azure enquanto direciona a maioria para o ambiente no local. Esta abordagem pode ajudar a avaliar o desempenho da escala, pois pode continuar a aumentar o peso atribuído ao Azure à medida que migra cada vez mais das suas cargas de trabalho para o Azure.

Por exemplo, a **Empresa B** opta por migrar por fases, movendo parte do seu ambiente de aplicação, mantendo o resto no local. Durante as fases iniciais em que a maior parte do ambiente está no local, um peso maior é atribuído ao ambiente no local. O gestor de tráfego devolve um ponto final com base nos pesos atribuídos aos pontos finais disponíveis.

![Migração no local-para-Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Durante a migração, ambos os pontos finais estão ativos e a maior parte do tráfego é direcionado para o ambiente no local. À medida que a migração prossegue, um peso maior pode ser atribuído ao ponto final em Azure e, finalmente, o ponto final no local pode ser desativado após a migração.

## <a name="azure-to-azure-failover"></a>Azure para Azure falha

Para este exemplo, considere a **Empresa C** que tem toda a sua infraestrutura de aplicação a funcionar azure. Por razões de continuidade e conformidade do negócio, a **Empresa C** decide utilizar a Recuperação do Site Azure para proteger as suas aplicações.

**A empresa C** está a executar aplicações com pontos finais públicos e quer a capacidade de redirecionar o tráfego para uma região azure diferente num evento de desastre. O método [prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) de encaminhamento de tráfego permite à **Empresa C** implementar facilmente este padrão de failover.

A configuração é a seguinte:
- **A empresa C** cria um [perfil de Gestor de Tráfego.](../traffic-manager/traffic-manager-create-profile.md)
- Utilizando o método de encaminhamento **prioritário,** a **Empresa C** cria dois pontos finais – **Primário** para a região de origem (Azure East Asia) e **Failover** para a região de recuperação (Azure Southeast Asia). **A Primária** é atribuída prioridade 1 e **a Failover** é atribuída prioridade 2.
- Uma vez que o ponto final **primário** está hospedado em Azure, o ponto final pode ser como um ponto final [Azure.](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)
- Com a Recuperação do Site Azure, o site de recuperação Azure não tem nenhuma máquina virtual ou aplicações em execução antes do failover. Assim, o ponto final **failover** pode ser criado como um ponto final [externo.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)
- Por padrão, o tráfego de utilizadores é direcionado para a aplicação da região de origem (Ásia Oriental), uma vez que esse ponto final tem a maior prioridade associada a ela. Nenhum tráfego é direcionado para a região de recuperação se o ponto final **primário** for saudável.

![Azure-to-Azure antes do fracasso](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Num evento de desastre, a **Empresa C** pode desencadear uma [falha](azure-to-azure-tutorial-failover-failback.md) e recuperar as suas aplicações na região de recuperação do Azure. Quando o Gestor de Tráfego Azure deteta que o ponto final primário já não é saudável, utiliza automaticamente o ponto final **failover** na resposta DNS e os utilizadores ligam-se à aplicação recuperada na região de recovery Azure (Sudeste Asiático).

![Azure-to-Azure após falha](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Dependendo dos requisitos empresariais, a **Empresa C** pode escolher uma frequência de [sondagem](../traffic-manager/traffic-manager-monitoring.md) mais alta ou menor para alternar entre as regiões de origem e recuperação, e garantir o mínimo de tempo de inatividade para os utilizadores.

Quando o desastre estiver contido, a **Empresa C** pode falhar desde a região de recuperação do Azure até à nascente da região azure utilizando a Recuperação do Sítio Azure. Agora, quando o Gestor de Tráfego deteta que o ponto final **primário** está novamente saudável, utiliza automaticamente o ponto final **primário** nas suas respostas DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Proteger aplicações empresariais multi-regiões

As empresas globais muitas vezes melhoram a experiência do cliente adaptando as suas aplicações para atender às necessidades regionais. A localização e a redução da latência podem levar à divisão de infraestruturas de aplicações entre regiões. As empresas estão também vinculadas à legislação regional em determinados domínios e optam por isolar uma parte das suas infraestruturas de aplicação dentro das fronteiras regionais.  

Vamos considerar um exemplo em que a **Empresa D** dividiu os seus pontos finais de aplicação para servir separadamente a Alemanha e o resto do mundo. **A Empresa D** utiliza o método de encaminhamento [Geográfico](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) do Gestor de Tráfego azure para configurar isto. Qualquer tráfego originário da Alemanha é direcionado para **endpoint 1** e qualquer tráfego originário fora da Alemanha é direcionado para **endpoint 2**.

O problema desta configuração é que, se o **Endpoint 1** deixar de funcionar por qualquer motivo, não há reorientação do tráfego para **o Ponto Final 2**. O tráfego originário da Alemanha continua a ser direcionado para **endpoint 1,** independentemente da saúde do ponto final, deixando os utilizadores alemães sem acesso à aplicação da **Empresa D.** Da mesma forma, se o **Ponto Final 2** ficar offline, não há reorientação do tráfego para **endpoint 1**.

![Aplicação multi-região antes](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Para evitar encontrar este problema e garantir a resiliência da aplicação, a **Empresa D** utiliza [perfis aninhados do Gestor de Tráfego](../traffic-manager/traffic-manager-nested-profiles.md) com a Recuperação do Site Azure. Numa configuração de perfil aninhada, o tráfego não é direcionado para pontos finais individuais, mas sim para outros perfis do Gestor de Tráfego. É assim que esta configuração funciona:
- Em vez de utilizar o encaminhamento geográfico com pontos finais individuais, a **Empresa D** utiliza o encaminhamento geográfico com perfis do Traffic Manager.
- Cada perfil do Gestor de Tráfego infantil utiliza o encaminhamento **prioritário** com um ponto final primário e de recuperação, nidiferindo assim o encaminhamento **prioritário** dentro do encaminhamento **geográfico.**
- Para permitir a resiliência da aplicação, cada distribuição de carga de trabalho utiliza a Recuperação do Site Azure para falhar numa região de recuperação baseada em caso de desastre.
- Quando o Gestor de Tráfego dos Pais recebe uma consulta de DNS, é direcionado para o gestor de tráfego infantil relevante que responde à consulta com um ponto final disponível.

![Aplicação multi-região após](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Por exemplo, se o ponto final na Alemanha Central falhar, o pedido pode ser rapidamente recuperado para o Nordeste alemão. O novo ponto final trata do tráfego originário da Alemanha com o mínimo de tempo de paragem para os utilizadores. Da mesma forma, uma paragem de pontos finais na Europa Ocidental pode ser resolvida recuperando a carga de trabalho de aplicação para o Norte da Europa, com o Gestor de Tráfego Azure a tratar os redirecionamentos do DNS para o ponto final disponível.

A configuração acima pode ser expandida para incluir o maior número de combinações de região e ponto final necessários. O Gestor de Tráfego permite até 10 níveis de perfis aninhados e não permite loops dentro da configuração aninhada.

## <a name="recovery-time-objective-rto-considerations"></a>Considerações do Objetivo do Tempo de Recuperação (RTO)

Na maioria das organizações, adicionar ou modificar registos de DNS é tratado por uma equipa separada ou por alguém fora da organização. Isto torna a tarefa de alterar os registos de DNS muito desafiante. O tempo necessário para atualizar os registos de DNS por outras equipas ou organizações que gerem a infraestrutura de DNS varia de organização para organização, e impacta o RTO da aplicação.

Utilizando o Traffic Manager, pode carregar frontalmente o trabalho necessário para as atualizações de DNS. Não é necessária qualquer ação manual ou escrita no momento da falha real. Esta abordagem ajuda na rápida troca (e, consequentemente, na redução do RTO), bem como na prevenção de erros de mudança de DNS dispendiosos e morosos num evento de catástrofe. Com o Gestor de Tráfego, até o passo de retrocesso é automatizado, o que de outra forma teria de ser gerido separadamente.

A definição do intervalo correto de [sondagem](../traffic-manager/traffic-manager-monitoring.md) através de controlos de saúde básicos ou rápidos pode reduzir consideravelmente o RTO durante a falha e reduzir o tempo de inatividade para os utilizadores.

Pode ainda otimizar o valor dNS Time to Live (TTL) para o perfil do Gestor de Tráfego. TTL é o valor pelo qual uma entrada dNS seria cached por um cliente. Para um registo, o DNS não seria interrogado duas vezes no espaço da TTL. Cada registo dNS tem um TTL associado a ele. A redução deste valor resulta em mais consultas de DNS ao Traffic Manager, mas pode reduzir o RTO através da descoberta de interrupções mais rapidamente.

O TTL experimentado pelo cliente também não aumenta se o número de DNS resolver entre o cliente e o servidor DNS autoritário aumenta. DNS resolve 'contagem regressiva' do TTL e só transmitem um valor TTL que reflita o tempo decorrido desde que o registo foi cache. Isto garante que o registo DNS seja atualizado no cliente após o TTL, independentemente do número de DNS Resolvers na cadeia.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os métodos de [encaminhamento](../traffic-manager/traffic-manager-routing-methods.md)do Gestor de Tráfego.
- Saiba mais sobre [os perfis do Gestor de Tráfego.](../traffic-manager/traffic-manager-nested-profiles.md)
- Saiba mais sobre [a monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md).
- Saiba mais sobre [os planos](site-recovery-create-recovery-plans.md) de recuperação para automatizar falha na aplicação.
