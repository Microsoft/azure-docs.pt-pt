---
title: Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager | Documentos da Microsoft
description: Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: de2279d7f24400142f9d47ecf25378e7e4c47f9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111978"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager
Enquanto o Azure Resource Manager oferece uma série de recursos incríveis, é fundamental planejar seu percurso de migração para o fazer se as coisas a serem executados corretamente. Gastando tempo no planejamento irá garantir que não encontrar problemas durante a execução de atividades de migração. 

> [!NOTE] 
> As seguintes orientações foi intensamente contribuiu para a equipe de consultoria de cliente do Azure e arquitetos de soluções de Cloud, trabalhando com clientes em ambientes de grandes dimensões de migração. Como tal este documento irá continuar a ser atualizado quando surgem novos padrões de sucesso, por isso, verifique novamente a partir da hora de tempo para ver se existem quaisquer recomendações de novo.

Existem quatro fases gerais do percurso de migração:

![Fases de migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planear

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e de compromissos

Consoante o tamanho de requisitos técnicos, geografias e práticas operacionais, talvez queira considerar:

1. Por que o Azure Resource Manager é desejado para a sua organização?  Quais são as razões de negócio para uma migração?
2. Quais são as razões técnicas para o Azure Resource Manager?  O que (se houver) serviços adicionais do Azure gostaria de aproveitar?
3. O aplicativo (ou conjuntos de máquinas virtuais) estão incluídos na migração?
4. Quais cenários são suportados com a API de migração?  Reveja os [funcionalidades não suportadas e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Suas equipes operacionais agora suportará aplicações/VMs no Azure Resource Manager e clássico?
6. Como (se houver) do Azure Resource Manager se altera a implementação de VM, gestão, monitorização e relatórios processos?  Os scripts de implementação precisam de ser atualizados?
7. O que é que as comunicações planear alertar os outros intervenientes (os utilizadores finais, os proprietários do aplicativo e proprietários de infraestruturas)?
8. Dependendo da complexidade do ambiente, deve haver um período de manutenção em que a aplicação não está disponível para os utilizadores finais e para os proprietários do aplicativo?  Se sim, durante quanto tempo?
9. O que é o plano de treinamento para garantir que os intervenientes são bem informadas e proficientes no Azure Resource Manager?
10. O que é o gerenciamento do programa ou o plano de gerenciamento de projeto para a migração?
11. Quais são as linhas cronológicas para a migração do Azure Resource Manager e outros relacionados com os mapas tecnologia?  São eles corretamente alinhados?

### <a name="patterns-of-success"></a>Padrões de sucesso

Os clientes bem-sucedidos têm planos de onde as perguntas anteriores são discutidas, documentadas e regem-se detalhados.  Certifique-se de que os planos de migração são amplamente comunicados para patrocinadores e participantes.  Equipa-te com dados de conhecimento sobre as opções de migração; é altamente recomendado ler este documento de migração definido abaixo.

* [Descrição geral da migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utilizar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Falha ao plano.  Os passos de tecnologia desta migração comprovados e o resultado é previsível.
- Pressuposto de que a plataforma suportada API será de conta para todos os cenários de migração. Leitura a [funcionalidades não suportadas e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para compreender quais cenários são suportados.
- Planejando não potencial falha de aplicação para os utilizadores finais.  Planear a memória intermédia suficiente para adequadamente avisar os utilizadores finais de tempo de aplicação potencialmente indisponível.


## <a name="lab-test"></a>Teste de laboratório 

**Replicar o seu ambiente e efetuar uma migração de teste**
  > [!NOTE]
  > Replicação exata do seu ambiente existente é executada usando uma ferramenta fornecidos pela Comunidade para que não é oficialmente suportada pelo Support da Microsoft. Portanto, é uma **opcional** passo mas é a melhor forma de descobrir problemas sem tocar em seus ambientes de produção. Se utilizar uma ferramenta fornecidos pela Comunidade não é uma opção, em seguida, leia sobre a recomendação de execução Prepare/validar/anulação abaixo.
  >
  
  Realizar um teste de laboratório do seu cenário exato (computação, rede e armazenamento) é a melhor forma de garantir uma migração tranquila. Isso o ajudará a garantir que:

- Um laboratório totalmente separado ou um ambiente de não produção existente para testar. Recomendamos um laboratório totalmente separado que pode ser migrado repetidamente e pode ser modificado de forma destrutiva.  Scripts para recolher/hydrate metadados das subscrições do real estão listados abaixo.
- É uma boa idéia para criar o laboratório numa subscrição separada. O motivo é que o laboratório será interrompido repetidamente e ter um separado, subscrição isolada irá reduzir a possibilidade de que algo real irá obter acidentalmente eliminado.

  Isso pode ser feito com a ferramenta de AsmMetadataParser. [Leia mais sobre esta ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

A seguir foram problemas descobertos em muitas das migrações maiores. Não se trata de uma lista exaustiva e deve consultar o [funcionalidades não suportadas e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para obter mais detalhes. Pode ou não pode encontrar estes problemas técnicos, mas se fizer a resolver estes antes de tentar a migração irá garantir uma experiência mais suave.

- **Fazer uma execução completa de validar/preparar/anulação** -essa é talvez a etapa mais importante para se certificar de clássico para o sucesso da migração do Azure Resource Manager. A API de migração tem três etapas principais: Validar, preparar e consolidar. Validar irão ler o estado do seu ambiente clássico e retornar um resultado de todos os problemas. No entanto, uma vez que alguns problemas podem ocorrer na pilha do Azure Resource Manager, para validar não capturará tudo. A próxima etapa no processo de migração, Prepare o ajudará a expor esses problemas. Preparar irá mover os metadados de clássico para o Azure Resource Manager, mas será não consolidar a migração e serão não remover ou alterar nada no lado do clássico. A execução envolve a preparar a migração, em seguida, a abortar (**consolidar não**) preparar a migração. O objetivo de validar/preparar/anulação de execução é ver todos os metadados na pilha do Azure Resource Manager, examiná-lo (*programaticamente ou no Portal*), certifique-se de que tudo o que migra corretamente e de trabalho por meio de técnicas problemas.  Ele também lhe dará uma noção da duração da migração, para que possam planear em conformidade para um período de indisponibilidade.  Uma para validar/preparar/anulação não irá causar qualquer período de inatividade do usuário; portanto, é não disruptivas para utilização da aplicação.
  - Os itens abaixo tem de ser resolvidos antes da execução, mas um teste de execução com a segurança também irá afastar os estes passos de preparação, se eles estão em falta. Durante a migração de enterprise, Descobrimos que a execução seja uma forma segura e inestimável para assegurar a preparação da migração.
  - Quando preparar está em execução, o controle plano (operações de gestão do Azure) será bloqueado para a rede virtual inteira, para que nenhuma alteração pode ser feitas para metadados VM durante a validar/preparar/anulação.  Mas, caso contrário, qualquer função de aplicação (área de trabalho remota, VM utilização, etc.) não serão afetados.  Os utilizadores das VMs não saberá que está a ser executada a execução.

- **Express Route circuitos e VPN**. Atualmente, não é possível migrar Gateways de rota Express com ligações de autorização sem tempo de inatividade. Para obter a solução, consulte [Migrate ExpressRoute circuits e associadas a redes virtuais do clássico para o modelo de implementação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Extensões de VM** -extensões de máquinas virtuais são potencialmente um dos principais entraves à migração de VMs em execução. Correção de extensões de VM pode pelo menos de 1 a 2 dias, então, planeie em conformidade.  Um agente do Azure em funcionamento é necessária para relatar o estado da extensão de VM de VMs em execução. Se o estado de volta como ruim para uma VM em execução, isso irá parar a migração. O próprio agente não precisa de estar na ordem de trabalho para ativar a migração, mas se existirem extensões na VM, em seguida, tanto um agente de trabalho e conectividade de internet saída (com o DNS) serão necessários para a migração para seguir em frente.
  - Se a conectividade a um servidor DNS é perdida durante a migração, todas as extensões de VM, exceto o BGInfo v1. \* tem de ser removido de todas as VM antes de preparação da migração e, em seguida, novamente adicionado novamente à VM após a migração do Azure Resource Manager.  **Este é apenas para as VMs que estão em execução.**  Se as VMs são paradas e desalocadas, extensões de VM não é necessário ser removido. **Nota:** Muitas extensões, como o diagnóstico do Azure e o security center monitorização irá reinstalar próprios após a migração, então, removê-los não é um problema.
  - Além disso, certifique-se de que os grupos de segurança de rede não estão restringir o acesso de internet de saída. Isto pode acontecer com algumas configurações de grupos de segurança de rede. Acesso de internet de saída (e DNS) é necessária para as extensões de VM a ser migrada para o Azure Resource Manager. 
  - Existem duas versões da extensão BGInfo: v1 e v2.  Se a VM foi criada com o portal do Azure ou o PowerShell, a VM será provavelmente ter a extensão de v1. Esta extensão não precisa de ser removido e será ignorada (não migrados), a API de migração. No entanto, se a VM clássica foi criada com o novo portal do Azure, ele provavelmente terá o JSON com base em versão v2 do BGInfo, que pode ser migrado para o Azure Resource Manager fornecidos o agente está a funcionar e tem acesso de internet de saída (e DNS). 
  - **Opção de remediação 1**. Se conhece que as suas VMs não terão saída acesso à internet, um serviço DNS de trabalho e, em seguida, trabalhar agentes do Azure nas VMs, desinstale todas as extensões VM como parte da migração antes de preparar, em seguida, reinstale as extensões de VM após a migração. 
  - **Opção de remediação 2**. Se as extensões de VM são muito grandes de uma barreira, outra opção consiste em Desalocação de encerramento/todas as VMs antes da migração. Migrar as VMs desalocadas, em seguida, reiniciá-las no lado do Azure Resource Manager. A vantagem disso é que as extensões de VM vão migrar. A desvantagem é que todos os público com acesso à IPs virtuais serão perdidos (isso pode ser um não-starter), e, obviamente, as VMs serão desligado a causar um impacto muito maior nos aplicativos em funcionamento.

    > [!NOTE] 
    > Se está configurada uma política de centro de segurança do Azure contra as VMs em execução a ser migradas, a política de segurança tem de ser parado antes de remover as extensões, caso contrário, a extensão de monitorização de segurança será reinstalada automaticamente na VM depois de ser removido.

- **Conjuntos de disponibilidade** – para uma rede virtual (vNet) a serem migrados para o Azure Resource Manager, a implementação clássica (ou seja, o serviço de nuvem), independentes de VMs têm de estar todos num conjunto de disponibilidade ou as VMs deve não estar todos qualquer conjunto de disponibilidade. Ter mais de um conjunto de disponibilidade no serviço em nuvem não é compatível com o Azure Resource Manager e irá parar a migração.  Além disso, não podem existir algumas VMs num conjunto de disponibilidade e algumas VMs não num conjunto de disponibilidade. Para resolver este problema, precisará remediar ou reshuffle seu serviço cloud.  Planear em conformidade, como isso pode ser demorado. 

- **As implementações de função da Web/trabalho** -serviços Cloud, que contém funções web e de trabalho não é possível migrar para o Azure Resource Manager. As funções da web/trabalho tem de ser removidas da rede virtual antes de iniciar a migração.  Uma solução típica é para mover instâncias de função da web/trabalho a uma rede virtual de clássico separada que também está ligada a um circuito do ExpressRoute ou para migrar o código para os serviços mais recente da aplicação de PaaS (essa discussão está além do escopo deste documento). No primeiro Reimplementar o caso, criar uma nova rede virtual clássica, movimentação/voltar a implementar as funções da web/de trabalho para essa nova rede virtual, em seguida, eliminar as implementações da rede virtual que está a ser movida. Sem alterações de código necessárias. A nova [Peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md) recurso pode ser usado para configurar o peering em conjunto a rede virtual clássica, que contém as funções da web/de trabalho e outras redes virtuais na mesma região do Azure, como a rede virtual que está a ser migrados (**depois de concluída a migração de rede virtual como redes virtuais em modo de peering não não possível migrar**), fornecendo, por conseguinte, os mesmos recursos sem perda de desempenho e sem penalidades de latência/largura de banda. Tendo em conta a adição de [Peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md), implementações de função da web/trabalho facilmente agora podem ser atenuadas e não bloqueiam a migração para o Azure Resource Manager.

- **Quotas do Gestor de recursos do Azure** -regiões do Azure tem limites/quotas separadas para clássicas e do Azure Resource Manager. Mesmo que num cenário de migração novo hardware não está a ser consumido *(está a troca de VMs existentes da implementação clássica para Azure Resource Manager)*, quotas do Azure Resource Manager ainda tem de estar no local com capacidade suficiente antes de Pode começar a migração. Abaixo encontram-se os principais limites que já vimos causam problemas.  Abra um pedido de suporte para aumentar os limites de quota. 

    > [!NOTE]
    > Estes limites devem ser gerados na mesma região que o seu ambiente atual a ser migrados.
    >

  - Interfaces de Rede
  - Balanceador de Carga
  - IPs Públicos
  - IPs públicos estáticos
  - Núcleos
  - Grupos de Segurança de Rede
  - Tabelas de Rota

    Pode verificar as suas quotas atual do Azure Resource Manager com os comandos seguintes com a versão mais recente da CLI do Azure.

    **Computação** *(núcleos, conjuntos de disponibilidade)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Rede** *(tabelas de rotas de Interfaces, balanceadores de carga de rede de redes virtuais, IPs públicos estáticos, IPs públicos, grupos de segurança de rede)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Armazenamento** *(conta de armazenamento)*
    
    ```bash
    az storage account show-usage
    ```

- **API de Gestor de recursos do Azure, limites de conjunto** – se tiver um ambiente muito grande (ex. > 400 VMs numa VNET), que pode usar a API de predefinição limitação de limites para escritas (atualmente **1200 escritas/hora**) no Azure Resource Manager. Antes de iniciar a migração, deve gerar um pedido de suporte para aumentar este limite para a sua subscrição.

- **Excedeu o tempo limite estado de VM de aprovisionamento** – se qualquer VM tem o estado de **aprovisionamento ultrapassou o tempo limite**, esse processo precisa ser resolvida de pré-migração. A única forma de fazer isso é com o tempo de inatividade da VM (delete-o, mantenha o disco e recriar a VM) de desaprovisionamento/reprovisionamento. 

- **Estado da VM RoleStateUnknown** - se a migração pára devido a uma **estado de função desconhecido** inspecionar a VM com o portal de mensagem de erro e certifique-se de que está em execução. Este erro normalmente desaparecerá respetivo proprietário (sem remediação necessária) após alguns minutos e é, muitas vezes, um tipo transitório normalmente visto durante uma Máquina Virtual **começar**, **parar**, **reiniciar** operações. **Recomendado prática:** tente novamente a migração novamente após alguns minutos. 

- **Recursos de infraestrutura Cluster não existe** – em alguns casos, determinadas de VMs não podem ser migradas por vários motivos ímpares. Um desses casos conhecidos é se a VM tiver sido recentemente criado (na última semana ou menos) e aconteceu firmar um cluster do Azure que ainda não está preparado para cargas de trabalho do Azure Resource Manager.  Obterá um erro que diz **cluster de recursos de infraestrutura não existe** e não é possível migrar a VM. A aguardar alguns dias, normalmente, resolverá esse problema específico como o cluster em breve ficará ativado o Azure Resource Manager. No entanto, uma solução imediata é `stop-deallocate` a VM, em seguida, continuar em frente com a migração, e iniciar a VM de cópia de segurança no Azure Resource Manager após a migração.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Não utilizo atalhos e omitir as migrações de validar/preparar/anulação de execução.
- Mais, se não todos, dos seus problemas potenciais irão descobrir durante os passos para validar/preparar/abortar.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e de compromissos

Agora, está pronto porque trabalhado com os problemas conhecidos com o seu ambiente.

Para as migrações reais, talvez queira considerar:

1. Planeie e agendar a rede virtual (menor unidade de migração) com o aumento de prioridade.  As redes virtuais simples primeiro e prosseguir com as redes virtuais mais complicadas.
2. A maioria dos clientes terá ambientes de produção e de não produção.  Agende produção pela última vez.
3. **(OPCIONAL)**  Agendar um período de indisponibilidade de manutenção com muita memória intermédia no caso de surgirem problemas inesperados.
4. Comunicar com e alinhar as equipas de suporte no caso de surgir um problema.

### <a name="patterns-of-success"></a>Padrões de sucesso

As orientações técnicas da secção de laboratório de teste acima devem ser considerada e atenuadas antes de uma migração real.  Com testes adequados, a migração é, na verdade, um non-evento.  Para ambientes de produção, poderá ser útil ter suporte adicional, como um parceiro Microsoft fidedigno ou serviços do Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

Não totalmente o teste pode causar problemas e atrasar na migração.  

## <a name="beyond-migration"></a>Além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e de compromissos

Agora que já está no Azure Resource Manager, maximize a plataforma.  Leitura a [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) para obter informações sobre benefícios adicionais.

Aspetos a considerar:

- O agrupamento a migração com outras atividades.  Optar ativamente por participar a maioria dos clientes para uma janela de manutenção do aplicativo.  Nesse caso, pode querer utilizar este período de indisponibilidade para ativar a outros recursos do Azure Resource Manager, como a encriptação e a migração para o Managed Disks.
- Examine os motivos técnicos e empresariais para o Azure Resource Manager; Ative os serviços adicionais disponíveis apenas no Azure Resource Manager que se aplicam ao seu ambiente.
- Modernize o seu ambiente com os serviços PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Ser intencional em que serviços pretende agora ativar no Azure Resource Manager.  Muitos clientes consideram o abaixo apelativas para seus ambientes do Azure:

- [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md).
- [Modelos do Azure Resource Manager para a implantação mais fácil e mais controlada](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Tags](../../azure-resource-manager/resource-group-using-tags.md).
- [Controlo de atividade](../../azure-resource-manager/resource-group-audit.md)
- [Políticas do Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

Lembre-se de que o motivo pelo qual iniciou esta clássico para o percurso de migração do Azure Resource Manager.  Quais eram as razões de negócio original? Atingir o motivo de negócio?


## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utilizar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
