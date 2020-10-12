---
title: Gestor de Tráfego Azure com Recuperação do Local Azure / Microsoft Docs
description: Descreve como usar o Gestor de Tráfego Azure com recuperação do local de Azure para recuperação de desastres e migração
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134956"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Gestor de Tráfego do Azure com o Azure Site Recovery

O Azure Traffic Manager permite-lhe controlar a distribuição de tráfego através dos seus pontos finais de aplicação. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gestor de Tráfego utiliza o Sistema de Nome de Domínio (DNS) para direcionar os pedidos do cliente para o ponto final mais adequado, com base num método de encaminhamento de tráfego e na saúde dos pontos finais. O Gestor de Tráfego proporciona vários [métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md) e [opções de monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. Os clientes ligam diretamente ao ponto final selecionado. O Gestor de Tráfego não é um representante ou um portal, e não vê o trânsito a passar entre o cliente e o serviço.

Este artigo descreve como pode combinar o encaminhamento inteligente do Azure Traffic Monitor com as poderosas capacidades de recuperação e migração de desastres do Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>No local para o failover de Azure

Para o primeiro cenário, considere **a Empresa A** que tem toda a sua infraestrutura de aplicação em funcionamento no seu ambiente no local. Por razões de continuidade e conformidade do negócio, **a Empresa A** decide utilizar a Azure Site Recovery para proteger as suas aplicações.

**A Empresa A** está a executar aplicações com pontos finais públicos e quer a capacidade de redirecionar o tráfego perfeitamente para Azure num evento de desastre. O método de encaminhamento de tráfego [prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) no Azure Traffic Manager permite à Empresa A implementar facilmente este padrão de failover.

A configuração é a seguinte:
- **A Empresa A** cria um [perfil de Gestor de Tráfego.](../traffic-manager/quickstart-create-traffic-manager-profile.md)
- Utilizando o método de encaminhamento **prioritário,** a **Empresa A** cria dois pontos finais – **Primário** para as instalações e **Failover** para o Azure. **A prioridade** 1 é atribuída e **a Falha** é atribuída prioridade 2.
- Uma vez que o ponto final **principal** é hospedado fora de Azure, o ponto final é criado como um ponto final [externo.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)
- Com a Recuperação do Site Azure, o site Azure não possui nenhuma máquina virtual ou aplicações em execução antes de falhar. Assim, o ponto final **failover** também é criado como um ponto final **externo.**
- Por predefinição, o tráfego de utilizadores é direcionado para a aplicação no local porque esse ponto final tem a maior prioridade associada à sua adesão. Nenhum tráfego é direcionado para Azure se o ponto final **principal** for saudável.

![No local-a-Azure antes do failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Num evento de desastre, a Empresa A pode desencadear uma [falha](site-recovery-failover.md) na Azure e recuperar as suas aplicações no Azure. Quando o Azure Traffic Manager deteta que o ponto final **principal** já não é saudável, utiliza automaticamente o ponto de final **de Failover** na resposta ao DNS e os utilizadores ligam-se à aplicação recuperada no Azure.

![No local-para-Azure após falha](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Dependendo dos requisitos do negócio, **a Empresa A** pode escolher uma frequência de sondagem mais alta ou mais baixa para [alternar](../traffic-manager/traffic-manager-monitoring.md) entre as instalações para Azure num evento de desastre, e garantir o mínimo de tempo de inatividade para os utilizadores.

Quando o desastre é contido, **a Empresa A** pode falhar de Azure para o seu ambiente no local[(VMware](vmware-azure-failback.md) ou [Hyper-V](hyper-v-azure-failback.md)) usando a recuperação do local de Azure. Agora, quando o Gestor de Tráfego deteta que o ponto final **principal** está novamente saudável, utiliza automaticamente o ponto final **principal** nas suas respostas dns.

## <a name="on-premises-to-azure-migration"></a>No local para a migração de Azure

Além da recuperação de desastres, a Recuperação do Sítio Azure também permite [migrações para Azure.](migrate-overview.md) Utilizando as poderosas capacidades de failover de testes da Azure Site Recovery, os clientes podem avaliar o desempenho da aplicação no Azure sem afetar o seu ambiente no local. E quando os clientes estão prontos para migrar, podem optar por migrar cargas inteiras em conjunto ou optar por migrar e escalar gradualmente.

O método de encaminhamento [ponderado](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) do Azure Traffic Manager pode ser usado para direcionar uma parte do tráfego de entrada para Azure, enquanto direciona a maioria para o ambiente no local. Esta abordagem pode ajudar a avaliar o desempenho da escala, uma vez que pode continuar a aumentar o peso atribuído ao Azure à medida que migra cada vez mais das suas cargas de trabalho para o Azure.

Por exemplo, a **Empresa B** opta por migrar por fases, movendo parte do seu ambiente de aplicação, mantendo o resto no local. Durante as fases iniciais, quando a maior parte do ambiente está no local, é atribuído um peso maior ao ambiente no local. O gestor de tráfego devolve um ponto final com base nos pesos atribuídos aos pontos finais disponíveis.

![Migração no local para azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Durante a migração, ambos os pontos finais estão ativos e a maior parte do tráfego é direcionado para o ambiente no local. À medida que a migração prossegue, um peso maior pode ser atribuído ao ponto final em Azure e, finalmente, o ponto final no local pode ser desativado após a migração.

## <a name="azure-to-azure-failover"></a>Azure para Azure failover

Para este exemplo, considere **a Empresa C** que tem toda a sua infraestrutura de aplicação em funcionamento Azure. Por razões de continuidade e conformidade do negócio, **a Empresa C** decide utilizar a Azure Site Recovery para proteger as suas aplicações.

**A Empresa C** está a executar aplicações com pontos finais públicos e quer a capacidade de redirecionar o tráfego para uma região de Azure diferente num evento de desastre. O método de encaminhamento de tráfego [prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) permite à **Empresa C** implementar facilmente este padrão de failover.

A configuração é a seguinte:
- **A Empresa C** cria um [perfil de Gestor de Tráfego.](../traffic-manager/quickstart-create-traffic-manager-profile.md)
- Utilizando o método de encaminhamento **prioritário,** a **Empresa C** cria dois pontos finais – **Primários** para a região de origem (Azure East Asia) e **Failover** para a região de recuperação (Azure Southeast Asia). **A prioridade** 1 é atribuída e **a Falha** é atribuída prioridade 2.
- Uma vez que o ponto final **principal** é hospedado em Azure, o ponto final pode ser como um ponto final [Azure.](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)
- Com a Recuperação do Site Azure, o site Azure de recuperação não possui nenhuma máquina virtual ou aplicações em execução antes de falhar. Assim, o ponto final **failover** pode ser criado como um ponto final [externo.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)
- Por padrão, o tráfego de utilizadores é direcionado para a aplicação da região de origem (Ásia Oriental), uma vez que esse ponto final tem a maior prioridade associada a ela. Nenhum tráfego é direcionado para a região de recuperação se o ponto final **primário** for saudável.

![Azure-to-Azure antes do failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Num evento de desastre, **a Empresa C** pode desencadear uma [falha](azure-to-azure-tutorial-failover-failback.md) e recuperar as suas aplicações na região de Azure de recuperação. Quando o Gestor de Tráfego Azure deteta que o ponto final primário já não é saudável, utiliza automaticamente o ponto final **failover** na resposta ao DNS e os utilizadores ligam-se à aplicação recuperada na região de Azure de recuperação (Sudeste Asiático).

![Azure-to-Azure após falha](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Dependendo dos requisitos de negócio, **a Empresa C** pode escolher uma frequência de sondagem mais alta ou menor para [alternar](../traffic-manager/traffic-manager-monitoring.md) entre regiões de origem e recuperação, e garantir o mínimo de tempo de inatividade para os utilizadores.

Quando o desastre é contido, **a empresa C** pode falhar desde a recuperação da região de Azure até à região de Azure, usando a Recuperação do Local de Azure. Agora, quando o Gestor de Tráfego deteta que o ponto final **principal** está novamente saudável, utiliza automaticamente o ponto final **principal** nas suas respostas dns.

## <a name="protecting-multi-region-enterprise-applications"></a>Proteger aplicações empresariais multi-regiões

As empresas globais melhoram frequentemente a experiência do cliente, adaptando as suas aplicações para satisfazer as necessidades regionais. A localização e a redução da latência podem levar a uma divisão das infraestruturas de aplicações entre regiões. As empresas estão também vinculadas à legislação regional de dados em determinadas áreas e optam por isolar uma parte da sua infraestrutura de aplicação dentro das fronteiras regionais.  

Vamos considerar um exemplo em que **a Empresa D** dividiu os seus pontos finais de aplicação para servir separadamente a Alemanha e o resto do mundo. **A Empresa D** utiliza o método de encaminhamento [geográfico](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) do Azure Traffic Manager para configurar isto. Qualquer tráfego originário da Alemanha é direcionado para **o Ponto Final 1** e qualquer tráfego originário da Alemanha é direcionado para **o Ponto Final 2**.

O problema com esta configuração é que se **o Endpoint 1** deixar de funcionar por qualquer motivo, não há reorientação do tráfego para **o Ponto Final 2**. O tráfego originário da Alemanha continua a ser direcionado para o **Endpoint 1,** independentemente da saúde do ponto final, deixando os utilizadores alemães sem acesso à aplicação da **Empresa D.** Da mesma forma, se **o Ponto Final 2** ficar offline, não há reorientação do tráfego para **o Ponto Final 1**.

![Aplicação multi-região antes](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Para evitar problemas e garantir a resiliência da aplicação, **a Empresa D** utiliza perfis de Gestor de Tráfego [aninhados](../traffic-manager/traffic-manager-nested-profiles.md) com a Recuperação do Site Azure. Numa configuração de perfil aninhada, o tráfego não é direcionado para pontos finais individuais, mas sim para outros perfis de Gestor de Tráfego. É assim que funciona esta configuração:
- Em vez de utilizar o encaminhamento geográfico com pontos finais individuais, **a Empresa D** utiliza o encaminhamento geográfico com perfis de Gestor de Tráfego.
- Cada perfil de Gestor de Tráfego de cada criança utiliza o encaminhamento **prioritário** com um ponto final primário e de recuperação, daí nidificar **o** encaminhamento prioritário dentro do encaminhamento **geográfico.**
- Para permitir a resiliência da aplicação, cada distribuição de carga de trabalho utiliza a Recuperação do Local Azure para falhar numa região de recuperação baseada em caso de desastre.
- Quando o gestor de tráfego do progenitor recebe uma consulta de DNS, é direcionado para o gestor de tráfego infantil relevante que responde à consulta com um ponto final disponível.

![Aplicação multi-região após](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Por exemplo, se o ponto final na Alemanha Central falhar, o pedido pode ser rapidamente recuperado para o Nordeste da Alemanha. O novo ponto final trata do tráfego originário da Alemanha com tempo mínimo de inatividade para os utilizadores. Da mesma forma, uma paragem de pontos finais na Europa Ocidental pode ser tratada recuperando a carga de trabalho da aplicação para o Norte da Europa, com o Azure Traffic Manager a tratar os redirecionamentos de DNS para o ponto final disponível.

A configuração acima pode ser expandida para incluir o maior número de combinações de região e ponto final necessários. O Gestor de Tráfego permite até 10 níveis de perfis aninhados e não permite loops dentro da configuração aninhada.

## <a name="recovery-time-objective-rto-considerations"></a>Considerações do Objetivo do Tempo de Recuperação (RTO)

Na maioria das organizações, adicionar ou modificar registos DNS é tratado por uma equipa separada ou por alguém fora da organização. Isto torna a tarefa de alterar os registos dns muito desafiante. O tempo necessário para atualizar os registos dns por outras equipas ou organizações que gerem a infraestrutura de DNS varia de organização para organização, e impacta o RTO da aplicação.

Ao utilizar o Traffic Manager, pode fazer o frontload do trabalho necessário para as atualizações do DNS. Não é necessária qualquer ação manual ou escrita no momento do fracasso real. Esta abordagem ajuda a comutação rápida (e, consequentemente, a redução do RTO) bem como a evitar erros de alteração de DNS dispendiosos num evento de desastre. Com o Traffic Manager, até o passo de recuo é automatizado, o que de outra forma teria de ser gerido separadamente.

Definir o intervalo de [sondagem](../traffic-manager/traffic-manager-monitoring.md) correto através de controlos de saúde básicos ou rápidos de intervalo pode reduzir consideravelmente o RTO durante o failover e reduzir o tempo de inatividade para os utilizadores.

Pode ainda otimizar o valor DNS Time to Live (TTL) para o perfil de Gestor de Tráfego. TTL é o valor para o qual uma entrada de DNS seria em cache por um cliente. Para que conste, o DNS não seria consultado duas vezes dentro do período de 4TL. Cada registo DNS tem um TTL associado. A redução deste valor resulta em mais consultas de DNS ao Traffic Manager, mas pode reduzir o RTO descobrindo falhas mais rapidamente.

O TTL experimentado pelo cliente também não aumenta se o número de DNS resolver entre o cliente e o servidor DNS autoritário aumentar. O DNS resolve a 'contagem decrescente' do TTL e apenas passa um valor TTL que reflete o tempo decorrido desde que o registo foi em cache. Isto garante que o registo de DNS seja atualizado no cliente após o TTL, independentemente do número de DNS Resolvers na cadeia.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md)do Traffic Manager .
- Saiba mais sobre [os perfis de Gestor de Tráfego aninhado.](../traffic-manager/traffic-manager-nested-profiles.md)
- Saiba mais sobre [a monitorização do ponto final.](../traffic-manager/traffic-manager-monitoring.md)
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover da aplicação.
