---
title: O Gestor de tráfego do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Descreve como utilizar o Gestor de tráfego do Azure com o Azure Site Recovery para recuperação após desastre e migração
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282156"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Gestor de Tráfego do Azure com o Azure Site Recovery

O Gestor de tráfego do Azure permite-lhe controlar a distribuição de tráfego entre os pontos de extremidade do aplicativo. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado, com base num método de encaminhamento de tráfego e o estado de funcionamento dos pontos finais. O Gestor de Tráfego proporciona vários [métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md) e [opções de monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. Os clientes ligam diretamente para o ponto de extremidade selecionado. O Gestor de tráfego não é um proxy ou de um gateway, e não vê o tráfego que passa entre o cliente e o serviço.

Este artigo descreve como pode combinar do Monitor de tráfego do Azure encaminhamento inteligentes com capacidades de migração e de recuperação de desastres poderosa do Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>No local para a ativação pós-falha do Azure

Para o primeiro cenário, considere **empresa A** que tem todos os sua infra-estrutura de aplicativo em execução no seu ambiente no local. Por motivos de continuidade e conformidade de negócios, **empresa A** decide usar o Azure Site Recovery para proteger seus aplicativos.

**Empresa A** está a executar aplicações com pontos finais públicos e pretende a capacidade de forma totalmente integrada a redirecionar o tráfego para o Azure num evento de desastre. O [prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md) método de encaminhamento de tráfego de mensagens em fila no Gestor de tráfego do Azure permite que a empresa A facilmente implementar este padrão de ativação pós-falha.

A configuração é o seguinte:
- **Empresa** cria uma [perfil do Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Utilizando o **prioridade** método de encaminhamento **empresa A** cria dois pontos de extremidade – **primário** no local e **ativação pós-falha** para o Azure. **Primário** é atribuída prioridade 1 e **ativação pós-falha** é atribuída a prioridade 2.
- Uma vez que o **primário** ponto final está alojado fora do Azure, o ponto final é criado como um [externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) ponto final.
- Com o Azure Site Recovery, o site do Azure não tem quaisquer máquinas virtuais ou aplicações em execução antes da ativação pós-falha. Então, o **ativação pós-falha** ponto final também é criado como um **externo** ponto final.
- Por predefinição, o tráfego de utilizador é direcionado para a aplicação no local porque esse ponto final tem a prioridade mais alta associada a ele. Não existe tráfego é direcionado para o Azure se o **primário** ponto final está em bom estado.

![No local-para-Azure antes da ativação pós-falha](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Num evento de desastre, a empresa pode acionar uma [ativação pós-falha](site-recovery-failover.md) para o Azure e recuperar as suas aplicações no Azure. Quando o Gestor de tráfego do Azure Deteta que o **primário** ponto final já não está em bom estado, ele utiliza automaticamente o **ativação pós-falha** ponto final na resposta DNS e os utilizadores ligar à aplicação recuperada no Azure.

![No local-para-Azure após a ativação pós-falha](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Dependendo das necessidades de negócio, **empresa A** pode escolher um superior ou inferior [frequência de investigação](../traffic-manager/traffic-manager-monitoring.md) para alternar entre no local para o Azure num evento de desastre e certifique-se o período de indisponibilidade mínimo para os utilizadores.

Quando o desastre está contido, **empresa** pode reativação pós-falha do Azure para o seu ambiente no local ([VMware](vmware-azure-failback.md) ou [Hyper-V](hyper-v-azure-failback.md)) com o Azure Site Recovery. Agora, quando o Gestor de tráfego detetar que o **primário** ponto final está em bom estado novamente, ele utiliza automaticamente o **primário** ponto de extremidade em suas respostas DNS.

## <a name="on-premises-to-azure-migration"></a>No local para migração do Azure

Para além da recuperação após desastre, recuperação de sites do Azure também permite [migrações para o Azure](migrate-overview.md). Com capacidades de ativação pós-falha de teste poderosas do Azure Site Recovery, os clientes podem avaliar o desempenho de aplicações no Azure sem afetar o seu ambiente no local. E quando os clientes estão prontos para migrar, eles podem optar por migrar cargas de trabalho inteiras em conjunto ou optar por migrar e aumentar gradualmente.

Azure Gestor de tráfego [ponderado](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) método de encaminhamento pode ser utilizado para direcionar alguma parte do tráfego de entrada para o Azure ao direcionar a maior parte para o ambiente no local. Esta abordagem pode ajudar a avaliar o desempenho de dimensionamento à medida que pode aumentar a ponderação atribuída para o Azure, ao migrar mais e mais das suas cargas de trabalho para o Azure.

Por exemplo, **empresa B** opta por migrar em fases, mover alguns de seus ambientes de aplicativos, mantendo o rest no local. Durante as fases iniciais quando a maior parte do ambiente é no local, é atribuído um peso maior para o ambiente no local. O Gestor de tráfego devolve um ponto de extremidade com base nos pesos atribuídos aos pontos finais disponíveis.

![Migração no local-para-Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Durante a migração, os dois pontos finais estão ativos e a maioria do tráfego é direcionado para o ambiente no local. Como a migração prossegue, pode ser atribuído um peso maior para o ponto final no Azure e, finalmente, o ponto de extremidade no local pode ser desativado após migração.

## <a name="azure-to-azure-failover"></a>Ativação pós-falha do Azure para o Azure

Neste exemplo, considere **empresa C** que tem todos os sua infra-estrutura de aplicativo em execução no Azure. Por motivos de continuidade e conformidade de negócios, **empresa C** decide usar o Azure Site Recovery para proteger seus aplicativos.

**Empresa C** está a executar aplicações com pontos finais públicos e pretende a capacidade de forma totalmente integrada redirecionar o tráfego para uma região do Azure diferente num evento de desastre. O [prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md) método de encaminhamento de tráfego permite **empresa C** facilmente implementar este padrão de ativação pós-falha.

A configuração é o seguinte:
- **Empresa C** cria uma [perfil do Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Utilizando o **prioridade** método de encaminhamento **C de empresa** cria dois pontos de extremidade – **primário** para a região de origem (Azure Ásia Oriental) e **ativação pós-falha** para a região de recuperação (Azure Sudeste asiático). **Primário** é atribuída prioridade 1 e **ativação pós-falha** é atribuída a prioridade 2.
- Uma vez que o **primário** ponto final está alojado no Azure, o ponto final pode ser como uma [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) ponto final.
- Com o Azure Site Recovery, a recuperação de site do Azure não tem quaisquer máquinas virtuais ou aplicações em execução antes da ativação pós-falha. Então, o **ativação pós-falha** ponto final pode ser criado como um [externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) ponto final.
- Por predefinição, o tráfego de utilizador é direcionado para o aplicativo de região (Leste asiático) de origem como ponto de extremidade tem a prioridade mais alta associada a ele. Não existe tráfego é direcionado para a região de recuperação se a **primário** ponto final está em bom estado.

![Azure para o Azure antes da ativação pós-falha](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Num evento de desastre **empresa C** pode acionar um [ativação pós-falha](azure-to-azure-tutorial-failover-failback.md) e recuperar seus aplicativos sobre a recuperação de região do Azure. Quando o Gestor de tráfego do Azure Deteta que o ponto final primário já não está em bom estado, ele utiliza automaticamente o **ativação pós-falha** ponto final na resposta DNS e os utilizadores ligar à aplicação recuperada na região do Azure (de recuperação Região do Sudeste asiático).

![Azure para o Azure após a ativação pós-falha](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Dependendo das necessidades de negócio, **empresa C** pode escolher um superior ou inferior [frequência de pesquisa](../traffic-manager/traffic-manager-monitoring.md) para alternar entre regiões de origem e de recuperação e certifique-se de período de indisponibilidade mínimo para os utilizadores.

Quando o desastre está contido, **empresa C** pode a reativação pós-falha da recuperação região do Azure para a origem de região do Azure com o Azure Site Recovery. Agora, quando o Gestor de tráfego detetar que o **primário** ponto final está em bom estado novamente, ele utiliza automaticamente o **primário** ponto de extremidade em suas respostas DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Proteger aplicações empresariais de várias regiões

As empresas globais, muitas vezes, melhorar a experiência do cliente, adaptando seus aplicativos para atender às necessidades regionais. Localização e a redução de latência podem levar a infraestrutura de aplicativos dividida em várias regiões. As empresas também são ligados por leis de dados regionais em determinadas áreas e optar por isolar uma parte de sua infra-estrutura de aplicativo dentro dos limites regionais.  

Vamos considerar um exemplo onde **empresa 1!d** dividiu os seus pontos de extremidade do aplicativo para servir separadamente a Alemanha e o resto do mundo. **Empresa D** utiliza a Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) método de encaminhamento para configurá-la. Qualquer tráfego com origem na Alemanha é direcionado para **1 ponto de extremidade** e qualquer tráfego com origem fora da Alemanha é direcionado para **ponto final 2**.

O problema com essa configuração é que, se **1 ponto de extremidade** deixar de funcionar por algum motivo, não há nenhum redirecionamento de tráfego para **ponto final 2**. Tráfego com origem na Alemanha continua a ser direcionado para **1 ponto de extremidade** independentemente do Estado de funcionamento do ponto de extremidade, deixando alemães utilizadores sem acesso aos **1!d da empresa**do aplicativo. Da mesma forma, se **ponto final 2** ficar offline, não há nenhum redirecionamento de tráfego para **ponto final 1**.

![Aplicação de várias regiões, antes de](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Para evitar este problema e garantir a resiliência da aplicação, **empresa 1!d** utiliza [aninhados perfis do Gestor de tráfego](../traffic-manager/traffic-manager-nested-profiles.md) com o Azure Site Recovery. Numa configuração de perfis aninhados, tráfego não é direcionado para os pontos finais individuais, mas em vez disso, para outros perfis do Gestor de tráfego. Eis como funciona esta configuração:
- Em vez de usar o encaminhamento geográfico com pontos finais individuais, **empresa 1!d** utiliza o encaminhamento geográfico com perfis do Gestor de tráfego.
- Cada filho de perfil do Gestor de tráfego utiliza **prioridade** , por conseguinte, encaminhamento com um site primário e um ponto de extremidade de recuperação, aninhar **prioridade** encaminhamento dentro **Geographic** encaminhamento.
- Para ativar a resiliência da aplicação, cada distribuição de carga de trabalho utiliza o Azure Site Recovery para ativação pós-falha para uma região de recuperação com base em caso de um evento de desastre.
- Quando o Gestor de tráfego do elemento principal recebe uma consulta DNS, é direcionado para o Gestor de tráfego que responde à consulta com um ponto de extremidade disponível de filho relevante.

![Aplicação de várias regiões após](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Por exemplo, se o ponto final na Alemanha Central falhar, o aplicativo rapidamente pode ser recuperado para Nordeste da Alemanha. O novo ponto final processa o tráfego com origem na Alemanha com período de indisponibilidade mínimo para os utilizadores. Da mesma forma, uma indisponibilidade do ponto final na Europa Ocidental pode ser processada por meio da recuperação da carga de trabalho aplicação para a Europa do Norte, com a manipulação de Gestor de tráfego do Azure que DNS redireciona para o ponto de extremidade disponível.

A configuração acima pode ser expandida para incluir tantos combinações de região e o ponto final necessárias. O Gestor de tráfego permite até 10 níveis de perfis aninhados e não permite que os loops na configuração do aninhados.

## <a name="recovery-time-objective-rto-considerations"></a>Considerações de objetivo de tempo (RTO) de recuperação

Na maioria das organizações, adicionando ou modificando os registos DNS é processado por uma equipa separada ou por alguém fora da organização. Isso facilita a tarefa de alterar os registos DNS muito difíceis de fazer. O tempo necessário para atualizar os registos DNS por outras equipas ou organizações gerir a infraestrutura DNS varia de organização para organização e afeta o RTO do aplicativo.

Ao utilizar o Gestor de tráfego, pode frontload o trabalho necessário para as atualizações de DNS. É necessária nenhuma ação de script ou manual no momento da ativação pós-falha real. Esta abordagem ajuda na rápida mudança (e, por conseguinte, reduzindo o RTO), bem como evitar dispendiosos erros de alteração de DNS demorados num evento de desastre. Com o Gestor de tráfego, até mesmo o passo de reativação pós-falha é automatizado, que teria que ser gerenciada separadamente.

Definição correta [intervalo de pesquisa](../traffic-manager/traffic-manager-monitoring.md) por meio de estado de funcionamento básico ou rápido intervalo verificações podem consideravelmente prejudicar o RTO durante a ativação pós-falha e reduzir o tempo de inatividade para os utilizadores.

Além disso, pode otimizar o tempo de DNS para o valor de Live (TTL) para o perfil do Gestor de tráfego. TTL é o valor para o qual uma entrada DNS poderia ser colocado em cache por um cliente. Para um registo DNS não seria possível consultar duas vezes no período de TTL. Cada registo DNS tem um valor de TTL associado a ele. Reduzir este valor resulta em mais consultas DNS para o Gestor de tráfego, mas pode reduzir o RTO ao detetar falhas mais rapidamente.

O valor de TTL teve pelo cliente também não aumenta se aumentar o número de resoluções DNS entre o cliente e servidor DNS autoritativo. Resoluções de DNS 'count para baixo"do valor TTL e transmitir apenas um valor TTL que reflete o tempo decorrido, uma vez que o registo foi colocado em cache. Isto garante que o registo DNS atualizado no cliente depois do valor de TTL, independentemente do número de resoluções DNS na cadeia.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md).
- Saiba mais sobre [aninhada de perfis do Gestor de tráfego](../traffic-manager/traffic-manager-nested-profiles.md).
- Saiba mais sobre [monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md).
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar a ativação pós-falha do aplicativo.
