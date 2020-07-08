---
title: Planeamento para a migração do clássico para o Gestor de Recursos Azure
description: Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 10ae2e1a85d5250e4da836c6f57e3619befd9330
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81865935"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Embora o Azure Resource Manager ofereça muitas funcionalidades incríveis, é fundamental planear a sua jornada de migração para garantir que as coisas corram bem. Passar tempo no planeamento garantirá que não encontrará problemas durante a execução de atividades de migração.

Existem quatro fases gerais da viagem de migração:<br>

![Fases de migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planear

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Dependendo do tamanho dos seus requisitos técnicos, geografias e práticas operacionais, talvez desemocie:

1. Porque é que o Azure Resource Manager é desejado para a sua organização?  Quais são as razões comerciais para uma migração?
2. Quais são as razões técnicas para o Gestor de Recursos Azure?  Que (se houver) serviços adicionais da Azure que gostaria de alavancar?
3. Que aplicação (ou conjuntos de máquinas virtuais) está incluída na migração?
4. Que cenários são suportados com a API migratória?  Reveja as [funcionalidades e configurações não apoiadas.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)
5. As suas equipas operacionais irão agora apoiar aplicações/VMs tanto no Classic como no Azure Resource Manager?
6. Como (se em tudo) o Gestor de Recursos Azure altera os seus processos de implementação, gestão, monitorização e reporte de VM?  Os scripts de implantação precisam de ser atualizados?
7. Qual é o plano de comunicações para alertar as partes interessadas (utilizadores finais, proprietários de aplicações e proprietários de infraestruturas)?
8. Dependendo da complexidade do ambiente, deve haver um período de manutenção em que a aplicação não esteja disponível para os utilizadores finais e para os proprietários das aplicações?  Se sim, por quanto tempo?
9. Qual é o plano de formação para garantir que as partes interessadas são conhecedoras e proficientes no Azure Resource Manager?
10. O que é o plano de gestão de programas ou de gestão de projetos para a migração?
11. Quais são os prazos para a migração do Gestor de Recursos Azure e outros roteiros de tecnologia relacionados?  Estão idealmente alinhados?

### <a name="patterns-of-success"></a>Padrões de sucesso

Os clientes bem sucedidos têm planos detalhados onde as questões anteriores são discutidas, documentadas e regidas.  Assegurar que os planos de migração sejam amplamente comunicados aos patrocinadores e partes interessadas.  Dotar-se de conhecimentos sobre as suas opções de migração; a leitura através deste documento de migração abaixo é altamente recomendado.

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

- Falha no plano.  Os passos tecnológicos desta migração estão comprovados e o resultado é previsível.
- Assumindo que a plataforma suportada a API migratória será responsável por todos os cenários. Leia as [funcionalidades e configurações não apoiadas](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) para entender que cenários são suportados.
- Não planeie uma possível interrupção da aplicação para os utilizadores finais.  Planeie um tampão suficiente para alertar adequadamente os utilizadores finais de tempo de aplicação potencialmente indisponíveis.


## <a name="lab-test"></a>Teste de laboratório

**Replique o seu ambiente e faça uma migração de teste**
  > [!NOTE]
  > A replicação exata do seu ambiente existente é executada utilizando uma ferramenta com contribuições comunitárias que não é oficialmente suportada pelo Microsoft Support. Portanto, é um passo **opcional,** mas é a melhor maneira de descobrir problemas sem tocar nos seus ambientes de produção. Se a utilização de uma ferramenta com contribuição comunitária não for uma opção, leia abaixo a recomendação de Validação/Preparação/Abortar Dry Run.
  >

  Realizar um teste laboratorial do seu cenário exato (computação, networking e armazenamento) é a melhor maneira de garantir uma migração suave. Isto ajudará a garantir:

- Um laboratório totalmente separado ou um ambiente de não produção existente para testar. Recomendamos um laboratório totalmente separado que possa ser migrado repetidamente e possa ser modificado destrutivamente.  Os scripts para recolher/hidratar metadados das subscrições reais estão listados abaixo.
- É uma boa ideia criar o laboratório numa assinatura separada. A razão é que o laboratório será demolido repetidamente, e ter uma assinatura separada e isolada reduzirá a chance de que algo real será acidentalmente eliminado.

  Isto pode ser conseguido utilizando a ferramenta AsmMetadataParser. [Leia mais sobre esta ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

Seguem-se questões descobertas em muitas das maiores migrações. Esta não é uma lista exaustiva e deve consultar as [funcionalidades e configurações não apoiadas](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) para obter mais detalhes.  Pode ou não encontrar estas questões técnicas, mas se as resolver antes de tentar a migração, garantirá uma experiência mais suave.

- **Faça uma validação/preparar/abortar dry run** - Este é talvez o passo mais importante para garantir o sucesso da migração classic to Azure Resource Manager. A API de migração tem três passos principais: Validar, Preparar e Comprometer. Validará o estado do seu ambiente clássico e devolverá o resultado de todos os problemas. No entanto, como alguns problemas podem existir na pilha de Gestor de Recursos Azure, a Validate não vai apanhar tudo. O próximo passo no processo de migração, Prepare-se ajudará a expor essas questões. Prepare-se irá mover os metadados da Classic para a Azure Resource Manager, mas não irá comprometer o movimento, e não removerá nem alterará nada do lado clássico. O período de secagem envolve preparar a migração e, em seguida, abortar **(não comprometer)** a preparação da migração. O objetivo de validar/preparar/abortar a secagem a seco é ver todos os metadados na pilha Azure Resource Manager, examiná-los (*programático ou no Portal*), e verificar se tudo migra corretamente, e trabalhar através de problemas técnicos.  Também lhe dará uma sensação de duração da migração para que possa planear o tempo de inatividade em conformidade.  Uma validação/preparação/abortar não causa qualquer tempo de inatividade do utilizador; portanto, não é perturbador para o uso da aplicação.
  - Os itens abaixo terão de ser resolvidos antes do ensaio a seco, mas um teste de secagem também eliminará com segurança estes passos de preparação se estes forem ignorados. Durante a migração da empresa, descobrimos que a secagem é uma forma segura e inestimável de garantir a prontidão da migração.
  - Quando a preparação estiver em funcionamento, o plano de controlo (operações de gestão Azure) será bloqueado para toda a rede virtual, pelo que não podem ser feitas alterações nos metadados VM durante a validação/preparação/abortar.  Mas caso contrário, qualquer função de aplicação (RD, utilização de VM, etc.) não será afetada.  Os utilizadores dos VM não saberão que o funciona a seco está a ser executado.

- **Circuitos de Rota Expresso e VPN**. Atualmente, os Gateways de Rota Expresso com ligações de autorização não podem ser migrados sem tempo de inatividade. Para a solução alternativa, consulte os [circuitos Migrate ExpressRoute e as redes virtuais associadas do clássico ao modelo de implementação do Gestor de Recursos.](../../expressroute/expressroute-migration-classic-resource-manager.md)

- **Extensões VM** - Extensões de máquinas virtuais são potencialmente um dos maiores bloqueios de estradas para migrar VMs em execução. A reparação das extensões VM pode demorar mais de 1 a 2 dias, por isso planeie em conformidade.  Um agente Azure em funcionamento é necessário para reportar o estado de extensão VM de funcionamento de VMs. Se o estado voltar tão mau para um VM em execução, isto irá parar a migração. O próprio agente não precisa de estar em condições de permitir a migração, mas se existirem extensões no VM, então tanto um agente de trabalho como a conectividade de saída da Internet (com DNS) serão necessários para que a migração avance.
  - Se a conectividade com um servidor DNS for perdida durante a migração, todas as extensões VM exceto a versão 1 da BGInfo. \* devem primeiro ser removidos de todos os VM antes da preparação da migração e, posteriormente, readmeditados de volta ao VM após a migração do Azure Resource Manager.  **Isto é só para os VMs que estão a funcionar.**  Se os VMs forem interrompidos, as extensões VM não precisam de ser removidas.

  > [!NOTE]
  > Muitas extensões como os diagnósticos do Azure e a monitorização do centro de segurança vão reinstalar-se após a migração, por isso removê-las não é um problema.

  - Além disso, certifique-se de que os Grupos de Segurança da Rede não estão a restringir o acesso à Internet de saída. Isto pode acontecer com algumas configurações de Grupos de Segurança de Rede. O acesso à Internet de saída (e DNS) é necessário para que as extensões VM sejam migradas para O Gestor de Recursos Azure.
  - Existem duas versões da extensão BGInfo e são chamadas versões 1 e 2.  

      - Se o VM estiver a utilizar a extensão da versão BGInfo 1, pode deixar esta extensão tal como está. A API de migração ignora esta extensão. A extensão BGInfo pode ser adicionada após a migração.
      - Se o VM estiver a utilizar a extensão bgInfo baseada em JSON na versão 2, o VM foi criado utilizando o portal Azure. A API de migração inclui esta extensão na migração para Azure Resource Manager, desde que o agente esteja a trabalhar e tenha acesso à Internet (e DNS).

  - **Opção de reparação 1**. Se souber que os seus VMs não terão acesso à Internet de saída, um serviço DNS em funcionamento e agentes Azure que trabalham nos VMs, então desinstale todas as extensões VM como parte da migração antes de preparar, em seguida, reinstalar as extensões VM após a migração.
  - **Opção de reparação 2**. Se as extensões VM forem demasiado grandes, outra opção é desligar/negociar todos os VMs antes da migração. Migrar os VMs deallocados e, em seguida, reiniciá-los no lado do Gestor de Recursos Azure. O benefício aqui é que as extensões de VM irão migrar. A desvantagem é que todos os IPs virtuais virados para o público serão perdidos (isto pode ser um não-arranque), e obviamente os VMs vão desligar causando um impacto muito maior nas aplicações de trabalho.

    > [!NOTE]
    > Se uma política do Centro de Segurança Azure estiver configurada contra a migração dos VM em funcionamento, a política de segurança tem de ser interrompida antes de remover as extensões, caso contrário a extensão de monitorização de segurança será reinstalada automaticamente no VM após a sua remoção.

- **Conjuntos de disponibilidade** - Para uma rede virtual (vNet) a ser migrada para O Gestor de Recursos Azure, a implementação clássica (ou seja, serviço de nuvem) contida em VMs deve estar todas num conjunto de disponibilidade, ou os VMs não devem estar todos em qualquer conjunto de disponibilidade. Ter mais do que uma disponibilidade definida no serviço de nuvem não é compatível com o Azure Resource Manager e irá parar a migração.  Além disso, não pode haver alguns VMs em um conjunto de disponibilidade, e alguns VMs não em um conjunto de disponibilidade. Para resolver isto, terá de remediar ou remodelar o seu serviço na nuvem.  Planeie, em conformidade, pois isto pode ser demorado.

- **Implementações de funções web/trabalhador** - Os serviços em nuvem que contêm funções web e de trabalhadores não podem migrar para o Azure Resource Manager. Para migrar o conteúdo das suas funções web e trabalhadora, terá de migrar o código para os mais recentes Serviços de Aplicações PaaS (esta discussão está fora do âmbito deste documento). Se quiser deixar as funções web/trabalhador como está, mas migrar VMs clássicos para o modelo de implementação do Gestor de Recursos, as funções web/trabalhador devem primeiro ser removidas da rede virtual antes de a migração poder começar.  Uma solução típica é apenas mover instâncias de função web/trabalhador para uma rede virtual clássica separada que também está ligada a um circuito ExpressRoute. No primeiro caso de reafectação, crie uma nova rede virtual Clássica, mova/reimplante as funções web/trabalhador para a nova rede virtual e, em seguida, elimine as implementações da rede virtual que está a ser movida. Não são necessárias alterações de código. A nova capacidade [de Peering da Rede Virtual](../../virtual-network/virtual-network-peering-overview.md) pode ser usada para acompanhar a rede virtual clássica que contém as funções web/trabalhador e outras redes virtuais na mesma região de Azure, como a rede virtual que está a ser migrada – após a**migração da rede virtual ser concluída, uma vez que as redes virtuais não podem ser migradas,** proporcionando assim as mesmas capacidades sem perda de desempenho e sem penalidades de latência/largura de banda. Dada a adição de [Virtual Network Peering,](../../virtual-network/virtual-network-peering-overview.md)as implementações de funções web/trabalhador podem agora ser facilmente atenuadas e não bloquear a migração para Azure Resource Manager.

- **Quotas gestoras de recursos Azure** - As regiões do Azure têm quotas/limites separados tanto para o Classic como para o Azure Resource Manager. Apesar de num cenário de migração não estar a ser consumido um novo hardware *(estamos a trocar VMs existentes da Classic para a Azure Resource Manager)*, as quotas do Azure Resource Manager ainda precisam de estar em vigor com capacidade suficiente antes de a migração começar. Listados abaixo estão os principais limites que vimos causar problemas.  Abra um bilhete de apoio à quota para aumentar os limites.

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

    Pode verificar as suas atuais quotas de Gestor de Recursos Azure utilizando os seguintes comandos com a versão mais recente do Azure PowerShell.



    **Compute** *(Núcleos, Conjuntos de Disponibilidade)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Rede** *(Redes Virtuais, IPs públicos estáticos, IPs públicos, Grupos de Segurança de Rede, Interfaces de Rede, Balançadores de Carga, Tabelas de Rotas)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Armazenamento** *(Conta de Armazenamento)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Azure Resource Manager API acelera limites** - Se tiver um ambiente suficientemente grande (por exemplo. > 400 VMs num VNET), pode atingir os limites de aceleração da API padrão para as escritas (atualmente) `1200 writes/hour` no Azure Resource Manager. Antes de iniciar a migração, deverá levantar um bilhete de apoio para aumentar este limite para a sua subscrição.


- **Provisioning Timed out VM Status** - Se algum VM tiver o estatuto de `provisioning timed out` , este deve ser resolvido antes da migração. A única maneira de o fazer é com o tempo de inatividade desprovisionando/reprovisionando o VM (elimine-o, mantenha o disco e recrie o VM).

- **RoleStateUnknown VM Status** - Se a migração parar devido a uma `role state unknown` mensagem de erro, inspecione o VM utilizando o portal e certifique-se de que está em funcionamento. Este erro normalmente desaparece por si só (sem necessidade de reparação) após alguns minutos e é frequentemente um tipo transitório frequentemente visto durante uma Máquina Virtual `start` , `stop` `restart` operações. **Prática recomendada:** volte a tentar a migração após alguns minutos.

- **O Cluster de Tecidos não existe** - Em alguns casos, certos VMs não podem ser migrados por várias razões estranhas. Um desses casos conhecidos é se o VM foi recentemente criado (na última semana ou assim) e aconteceu para aterrar um cluster Azure que ainda não está equipado para cargas de trabalho do Azure Resource Manager.  Terá um erro que diz `fabric cluster does not exist` e o VM não pode ser migrado. Esperar alguns dias irá normalmente resolver este problema em particular, uma vez que o cluster em breve terá o Azure Resource Manager ativado. No entanto, uma solução imediata é para `stop-deallocate` o VM, em seguida, continuar com a migração, e começar o VM de volta em Azure Resource Manager após migração.

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

- Não tome atalhos e omita as migrações validadas/preparar/abortar a secagem.
- A maioria, se não todos, dos seus potenciais problemas surgirão durante os passos de validação/preparação/abortar.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora estás pronto porque trabalhaste com os problemas conhecidos com o teu ambiente.

Para as verdadeiras migrações, talvez desemosse:

1. Planeie e agende a rede virtual (a mais pequena unidade de migração) com cada vez mais prioridade.  Faça as redes virtuais simples primeiro, e progrida com as redes virtuais mais complicadas.
2. A maioria dos clientes terá ambientes de não produção e produção.  A programação da produção por último.
3. **(OPCIONAL)** Agende um tempo de inatividade de manutenção com bastante tampão no caso de surgirem problemas inesperados.
4. Comunique-se e alinhe-se com as suas equipas de apoio no caso de surgirem problemas.

### <a name="patterns-of-success"></a>Padrões de sucesso

A orientação técnica da secção _de teste de laboratório_ deve ser considerada e atenuada antes de uma migração real.  Com testes adequados, a migração é na verdade um não-evento.  Para ambientes de produção, pode ser útil ter suporte adicional, como um parceiro de confiança da Microsoft ou serviços Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

Não os testes completos podem causar problemas e atrasos na migração.  

## <a name="beyond-migration"></a>Além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora que está no Azure Resource Manager, maximize a plataforma.  Leia a [visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md) para saber mais sobre benefícios adicionais.

Coisas a considerar:

- Agregar a migração com outras atividades.  A maioria dos clientes opta por uma janela de manutenção de aplicações.  Em caso afirmativo, é melhor utilizar este tempo de inatividade para ativar outras capacidades do Gestor de Recursos Azure, como encriptação e migração para Discos Geridos.
- Reveja as razões técnicas e empresariais para o Gestor de Recursos Azure; ativar os serviços adicionais disponíveis apenas no Azure Resource Manager que se aplicam ao seu ambiente.
- Modernizar o seu ambiente com serviços PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Tenha de ser propositado em que serviços pretende agora ativar no Azure Resource Manager.  Muitos clientes acham o abaixo convincente para os seus ambientes Azure:

- [Controlo de Acesso Baseado em Fun.](../../role-based-access-control/overview.md)
- [Modelos de Gestor de Recursos Azure para uma implementação mais fácil e controlada](../../azure-resource-manager/templates/overview.md).
- [Etiquetas.](../../azure-resource-manager/management/tag-resources.md)
- [Controlo de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
- [Políticas de Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Armadilhas para evitar

Lembre-se porque começou esta viagem de migração Classic to Azure Resource Manager.  Quais foram as razões comerciais originais? Conseguiu a razão do negócio?


## <a name="next-steps"></a>Próximos passos

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway clássico para migração de Gestor de Recursos](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrar circuitos ExpressRoute e redes virtuais associadas do clássico ao modelo de implementação do Gestor de Recursos](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
