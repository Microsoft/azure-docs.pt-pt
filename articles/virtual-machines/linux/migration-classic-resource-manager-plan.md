---
title: Planeamento para migração do clássico para o Gestor de Recursos Azure
description: Planeamento para migração de recursos IaaS do clássico para OGestor de Recursos Azure
services: virtual-machines-linux
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 0b9c5b17b993afdd64cd2cbd8a15cbd6dd53f5ca
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944656"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planeamento para migração de recursos IaaS do clássico para OGestor de Recursos Azure

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs iaas estão usando O Gestor de [Recursos Azure.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VMs clássicos foram depreciados e serão totalmente aposentados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Embora o Azure Resource Manager ofereça um monte de funcionalidades incríveis, é fundamental planear a sua viagem de migração para garantir que as coisas corram bem. Gastar tempo no planeamento garantirá que não se depara com problemas durante a execução de atividades migratórias.

> [!NOTE]
> A seguinte orientação foi fortemente contribuida pela equipa de Assessoria de Clientes da Azure e pelos arquitetos da Cloud Solution que trabalham com os clientes na migração de grandes ambientes. Como tal, este documento continuará a ser atualizado à medida que surgem novos padrões de sucesso, por isso, verifique de vez em quando para ver se existem novas recomendações.

Há quatro fases gerais da jornada de migração:

![Fases de migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planear

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Dependendo do tamanho dos seus requisitos técnicos, geografias e práticas operacionais, talvez deva considerar:

1. Porque é que o Gestor de Recursos azure é desejado para a sua organização?  Quais são as razões comerciais para uma migração?
2. Quais são as razões técnicas para o Gestor de Recursos Azure?  Que (se houver) serviços adicionais do Azure gostaria de alavancar?
3. Qual aplicação (ou conjuntos de máquinas virtuais) está incluída na migração?
4. Que cenários são apoiados com a API migratória?  Reveja as [funcionalidades e configurações não suportadas](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. As suas equipas operacionais irão agora apoiar aplicações/VMs tanto no Classic como no Azure Resource Manager?
6. Como (se em tudo) o Gestor de Recursos Azure altera os seus processos de implantação, gestão, monitorização e reporte de VM?  Os seus scripts de implantação precisam de ser atualizados?
7. Qual é o plano de comunicações para alertar as partes interessadas (utilizadores finais, proprietários de aplicações e proprietários de infraestruturas)?
8. Dependendo da complexidade do ambiente, deve haver um período de manutenção em que a aplicação não esteja disponível para os utilizadores finais e para os proprietários de aplicações?  Se sim, por quanto tempo?
9. Qual é o plano de formação para garantir que as partes interessadas sejam conhecedoras e competentes no Gestor de Recursos do Azure?
10. Qual é o plano de gestão de programas ou de gestão de projetos para a migração?
11. Quais são as cronologias da migração do Gestor de Recursos Azure e de outros mapas de estradas de tecnologia conexos?  Estão muito alinhados?

### <a name="patterns-of-success"></a>Padrões de sucesso

Os clientes bem sucedidos têm planos detalhados onde as questões anteriores são discutidas, documentadas e regidas.  Garantir que os planos de migração são amplamente comunicados aos patrocinadores e partes interessadas.  Dote-se com conhecimento sobre as suas opções de migração; ler através deste documento de migração abaixo é altamente recomendado.

* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Use a PowerShell para migrar os recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o CLI para migrar recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

- Falha no plano.  Os passos tecnológicos desta migração estão comprovados e o resultado é previsível.
- Assumindo que a plataforma apoiada pela API da migração irá contabilizar todos os cenários. Leia as [funcionalidades e configurações não suportadas](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para entender quais os cenários suportados.
- Não está a planear uma possível interrupção de aplicações para utilizadores finais.  Planeie um tampão suficiente para alertar adequadamente os utilizadores finais do tempo de aplicação potencialmente indisponível.


## <a name="lab-test"></a>Teste de Laboratório

**Reproduza o seu ambiente e faça uma migração de teste**
  > [!NOTE]
  > A replicação exata do seu ambiente existente é executada utilizando uma ferramenta com contribuição para a comunidade que não é suportada oficialmente pelo Microsoft Support. Portanto, é um passo **opcional,** mas é a melhor maneira de descobrir problemas sem tocar nos seus ambientes de produção. Se utilizar uma ferramenta com contribuição comunitária não é uma opção, então leia sobre a recomendação De Validação/Preparar/Abortar dry Run abaixo.
  >

  Realizar um teste laboratorial do seu cenário exato (computação, networking e armazenamento) é a melhor maneira de garantir uma migração suave. Isto ajudará a garantir:

- Um laboratório totalmente separado ou um ambiente de não produção existente para testar. Recomendamos um laboratório totalmente separado que possa ser migrado repetidamente e possa ser modificado destrutivamente.  Os scripts para recolher/hidratar metadados das subscrições reais estão listados abaixo.
- É uma boa ideia criar o laboratório numa assinatura separada. A razão é que o laboratório será demolido repetidamente, e ter uma subscrição separada e isolada reduzirá a chance de algo real ser acidentalmente apagado.

  Isto pode ser conseguido utilizando a ferramenta AsmMetadataParser. [Leia mais sobre esta ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

Seguiram-se questões descobertas em muitas das maiores migrações. Esta não é uma lista exaustiva e deve consultar as [funcionalidades e configurações não suportadas](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para mais detalhes. Pode ou não encontrar estes problemas técnicos, mas se os resolver antes de tentar a migração garantirá uma experiência mais suave.

- **Faça uma Corrida Seca validada/Prepare/Aborte** - Este é talvez o passo mais importante para garantir o sucesso migratório clássico para o Gestor de Recursos Azure. A API migração tem três passos principais: Validar, Preparar e Comprometer. Validará lerá o estado do seu ambiente clássico e devolverá o resultado de todas as questões. No entanto, como alguns problemas podem existir na pilha de Gestor de Recursos Azure, a Validação não vai apanhar tudo. O próximo passo no processo de migração, a Prepare ajudará a expor essas questões. A Prepare irá mover os metadados do Classic para o Azure Resource Manager, mas não irá comprometer a mudança, e não removerá nem alterará nada do lado clássico. O ensaio a seco envolve preparar a migração, depois abortar **(não cometer)** a preparação da migração. O objetivo de validar/preparar/abortar o ensaio a seco é ver todos os metadados na pilha do Gestor de Recursos Azure, examiná-los *(programáticamente ou no Portal),* e verificar se tudo migra corretamente, e trabalhar através de questões técnicas.  Também lhe dará uma sensação de duração da migração para que possa planear o tempo de inatividade em conformidade.  Um validador/preparação/abortar não causa qualquer tempo de inatividade do utilizador; portanto, não é disruptivo para a utilização da aplicação.
  - Os itens abaixo terão de ser resolvidos antes da corrida a seco, mas um teste de corrida a seco também eliminará com segurança estes passos de preparação se não forem ignorados. Durante a migração da empresa, descobrimos que a corrida a seco é uma forma segura e inestimável de garantir a prontidão migratória.
  - Quando a preparação estiver em funcionamento, o plano de controlo (operações de gestão azure) será bloqueado para toda a rede virtual, pelo que não podem ser feitas alterações nos metadados VM durante o validação/preparação/abortar.  Mas caso contrário, qualquer função de aplicação (RD, utilização VM, etc.) não será afetada.  Os utilizadores dos VMs não saberão que a corrida a seco está a ser executada.

- **Circuitos de Rota Expresso e VPN**. Atualmente, o Express Route Gateways com ligações de autorização não pode ser migrado sem tempo de inatividade. Para obter a socapa, consulte [circuitos Migrate ExpressRoute e redes virtuais associadas do clássico ao modelo](../../expressroute/expressroute-migration-classic-resource-manager.md)de implementação do Gestor de Recursos.

- **Extensões VM** - As extensões da Máquina Virtual são potencialmente um dos maiores bloqueios de estradas para migrar VMs em execução. A reparação das extensões vm pode demorar mais de 1-2 dias, por isso planeie em conformidade.  Um agente Azure em funcionamento é necessário para informar o estado de extensão vm de execução de VMs. Se o estatuto voltar tão mau para um VM em execução, isto vai parar a migração. O próprio agente não precisa de estar em condições de permitir a migração, mas se existirem extensões no VM, então tanto será necessário um agente de trabalho como uma conectividade de acesso à Internet (com DNS) para que a migração avance.
  - Se a conectividade com um servidor DNS for perdida durante a migração, todas as extensões VM exceto BGInfo v1.\* primeiro devem ser removidos de cada VM antes da preparação da migração, e posteriormente readicionados ao VM após a migração do Gestor de Recursos Azure.  **Isto é só para vMs que estão a funcionar.**  Se os VMs forem interrompidos, as extensões VM não precisam de ser removidas. **Nota:** Muitas extensões como os diagnósticos do Azure e a monitorização do centro de segurança reinstalar-se-ão após a migração, pelo que removê-las não é um problema.
  - Além disso, certifique-se de que os grupos de segurança da rede não estão a restringir o acesso à Internet. Isto pode acontecer com algumas configurações de Grupos de Segurança da Rede. O acesso à Internet de saída (e DNS) é necessário para que as extensões vm sejam migradas para o Gestor de Recursos Azure.
  - Existem duas versões da extensão BGInfo: v1 e v2.  Se o VM foi criado utilizando o portal Azure ou powerShell, o VM provavelmente terá a extensão v1 no mesmo. Esta extensão não precisa de ser removida e será ignorada (não migrada) pela API migratória. No entanto, se o Classic VM foi criado com o novo portal Azure, provavelmente terá a versão V2 baseada em JSON da BGInfo, que pode ser migrada para o Azure Resource Manager desde que o agente esteja a trabalhar e tenha acesso à Internet de saída (e DNS).
  - **Opção de reparação 1**. Se sabe que os seus VMs não terão acesso à Internet de saída, um serviço DNS em funcionamento e agentes Azure a trabalhar nos VMs, então desinstale todas as extensões vM como parte da migração antes de preparar e, em seguida, reinstale as Extensões VM após a migração.
  - **Opção de reparação 2**. Se as extensões vm são demasiado grandes de um obstáculo, outra opção é desligar/desalojar todos os VMs antes da migração. Migrar os VMs deallocated e reiniciá-los no lado do Gestor de Recursos Azure. O benefício aqui é que as extensões vm migrarão. A desvantagem é que todos os IPs virtuais virados para o público serão perdidos (isto pode ser um não-arranque), e obviamente os VMs vão desligar causando um impacto muito maior nas aplicações de trabalho.

    > [!NOTE]
    > Se uma política do Centro de Segurança Azure for configurada contra a migração dos VMs em execução, a política de segurança tem de ser interrompida antes de remover as extensões, caso contrário a extensão de monitorização de segurança será reinstalada automaticamente no VM após a sua remoção.

- **Conjuntos** de disponibilidade - Para que uma rede virtual (vNet) seja migrada para o Gestor de Recursos Azure, a implementação clássica (isto é, serviço de nuvem) contida em VMs deve estar em um conjunto de disponibilidade, ou os VMs não devem estar em qualquer conjunto de disponibilidade. Ter mais do que uma disponibilidade definida no serviço de nuvem não é compatível com o Gestor de Recursos Do Azure e vai parar a migração.  Além disso, não pode haver alguns VMs num conjunto de disponibilidade, e alguns VMs não em um conjunto de disponibilidade. Para resolver isto, terá de remediar ou remodelar o seu serviço de cloud.  Planeie em conformidade, pois isto pode ser demorado.

- **Implementações de funções Web/Trabalhador** - Serviços de nuvem que contenham funções web e trabalhadornão podem migrar para O Gestor de Recursos Azure. As funções web/trabalhador devem primeiro ser removidas da rede virtual antes de a migração poder começar.  Uma solução típica é apenas mover exemplos de papéis web/trabalhador para uma rede virtual clássica separada que também está ligada a um circuito ExpressRoute, ou migrar o código para os mais recentes Serviços de Aplicação PaaS (esta discussão está fora do âmbito deste documento). No caso de reimplantação anterior, criar uma nova rede virtual Clássica, mover/reimplantar as funções web/trabalhador para essa nova rede virtual, em seguida, eliminar as implementações da rede virtual que está sendo deslocada. Não são necessárias alterações de código. A nova capacidade [de peering](../../virtual-network/virtual-network-peering-overview.md) de rede virtual pode ser usada para analisar a rede virtual clássica que contém as funções web/trabalhador e outras redes virtuais na mesma região do Azure, como a migração da rede virtual ( após a migração da rede virtual ser concluída como**redes virtuais peered não pode ser migrada),** proporcionando assim as mesmas capacidades sem perda de desempenho e sem penalizações de latência/largura de banda. Dada a adição de [Virtual Network Peering,](../../virtual-network/virtual-network-peering-overview.md)as implementações de funções web/trabalhador podem agora ser facilmente atenuadas e não bloquear a migração para o Gestor de Recursos Azure.

- Quotas de **Gestor de Recursos Azure** - As regiões azure têm quotas/limites separados tanto para o Classic como para o Gestor de Recursos Azure. Mesmo que num cenário de migração não esteja a ser consumido novo hardware *(estamos a trocar vMs existentes de Classic para Azure Resource Manager)* , as quotas do Gestor de Recursos Azure ainda precisam de estar em vigor com capacidade suficiente antes de a migração poder começar. Listados abaixo estão os principais limites que vimos causar problemas.  Abra um bilhete de apoio à quota para aumentar os limites.

    > [!NOTE]
    > Estes limites têm de ser aumentados na mesma região que o seu ambiente atual para ser migrado.
    >

  - Interfaces de Rede
  - Balanceador de Carga
  - IPs públicos
  - IPs públicos estáticos
  - Núcleos
  - Grupos de Segurança de Rede
  - Tabelas de Rota

    Pode verificar as suas atuais quotas do Gestor de Recursos Azure utilizando os seguintes comandos com a versão mais recente do Azure CLI.

    **Compute** *(Cores, Conjuntos de Disponibilidade)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Rede** *(Redes Virtuais, IPs Públicos Estáticos, IPs Públicos, Grupos de Segurança da Rede, Interfaces de Rede, Balanceadores de Carga, Tabelas de Rotas)*

    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    Armazenamento *(Conta* de **Armazenamento)**

    ```bash
    az storage account show-usage
    ```

- Limites de aceleração da **API do Gestor de Recursos Azure** - Se tiver um ambiente suficientemente grande (por exemplo. > 400 VMs num VNET, pode atingir os limites padrão de aceleração da API para escritas (atualmente **1200 escritos/hora)** no Gestor de Recursos Azure. Antes de iniciar a migração, deverá levantar um bilhete de apoio para aumentar este limite para a sua subscrição.

- **Provisionamento Tempode VM Status** - Se algum VM tiver o estatuto de **disposição cronometrada**, isso tem de ser resolvido antes da migração. A única maneira de o fazer é com o tempo de inatividade, desprovisionando/reprovisionando o VM (eliminá-lo, manter o disco e recriar o VM).

- **RoleStateUnknown VM Status** - Se a migração parar devido a uma mensagem de erro **desconhecida do estado de função,** inspecione o VM utilizando o portal e certifique-se de que está em funcionamento. Este erro normalmente desaparece por si só (sem necessidade de reparação) após alguns minutos e é frequentemente um tipo transitório frequentemente visto durante um **arranque**da Máquina Virtual, **paragem,** **reiniciar** operações. **Prática recomendada:** volte a tentar a migração após alguns minutos.

- **O Cluster de Tecidos não existe** - Em alguns casos, certos VMs não podem ser migrados por várias razões estranhas. Um desses casos conhecidos é se o VM foi recentemente criado (na última semana ou assim) e aconteceu para aterrar um cluster Azure que ainda não está equipado para cargas de trabalho do Gestor de Recursos Azure.  Terá um erro que diz que o cluster de **tecidos não existe** e o VM não pode ser migrado. Esperar alguns dias geralmente resolverá este problema em particular, uma vez que o cluster em breve terá o Gestor de Recursos Azure ativado. No entanto, uma suposição imediata é `stop-deallocate` o VM, em seguida, continuar com a migração, e começar o VM de volta em Azure Resource Manager após a migração.

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

- Não tome atalhos e omita as migrações de corrida a seco de validação/preparação/aborto.
- A maioria, se não todos, dos seus potenciais problemas surgirá durante os passos de validação/preparação/abortar.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora estás pronto porque já passaste pelos problemas conhecidos com o teu ambiente.

Para as verdadeiras migrações, talvez queira considerar:

1. Planeie e agende a rede virtual (unidade de migração mais pequena) com prioridade crescente.  Faça as redes virtuais simples primeiro, e progrida com as redes virtuais mais complicadas.
2. A maioria dos clientes terá ambientes de não produção e produção.  Programar a produção por último.
3. **(OPCIONAL)** Agende uma paragem de manutenção com bastante tampão no caso de surgirem problemas inesperados.
4. Comunique-se e alinhe-se com as suas equipas de apoio no caso de surgirem problemas.

### <a name="patterns-of-success"></a>Padrões de sucesso

As orientações técnicas da secção de teste de laboratório acima devem ser consideradas e atenuadas antes de uma migração real.  Com testes adequados, a migração é na verdade um não evento.  Para ambientes de produção, pode ser útil ter suporte adicional, como um parceiro confiável da Microsoft ou serviços Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

Os não testes completos podem causar problemas e atrasos na migração.  

## <a name="beyond-migration"></a>Além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora que está no Azure Resource Manager, maximize a plataforma.  Leia a [visão geral do Gestor de Recursos azure](../../azure-resource-manager/management/overview.md) para saber sobre benefícios adicionais.

Coisas a considerar:

- Agregar a migração com outras atividades.  A maioria dos clientes opta por uma janela de manutenção de aplicações.  Em caso afirmativo, é melhor utilizar este tempo de inatividade para permitir outras capacidades do Gestor de Recursos Azure, como encriptação e migração para Discos Geridos.
- Reveja as razões técnicas e empresariais para o Gestor de Recursos Azure; permitir os serviços adicionais disponíveis apenas no Gestor de Recursos Azure que se aplicam ao seu ambiente.
- Modernizar o seu ambiente com serviços PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Seja propositado sobre os serviços que pretende agora ativar no Gestor de Recursos Azure.  Muitos clientes acham o que está abaixo convincente para os seus ambientes Azure:

- [Controlo de Acesso baseado em funções.](../../role-based-access-control/overview.md)
- [Modelos do Gestor de Recursos Azure para uma implementação mais fácil e controlada.](../../azure-resource-manager/templates/overview.md)
- [Etiquetas.](../../azure-resource-manager/management/tag-resources.md)
- [Controlo de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
- [Políticas Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

Lembre-se porque iniciou esta viagem de migração classic to Azure Resource Manager.  Quais foram as razões originais do negócio? Conseguiu a razão do negócio?


## <a name="next-steps"></a>Passos Seguintes

* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Use a PowerShell para migrar os recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
