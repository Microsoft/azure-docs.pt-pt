---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: 0ffbbe5505c9316f362ebbc41d311f97408f1b17
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887884"
---
Este artigo descreve como migrar recursos de IaaS (infraestrutura como serviço) dos modelos de implantação clássico para o Resource Manager e detalha como conectar recursos de dois modelos de implantação que coexistem em sua assinatura usando a rede virtual gateways site a site. Você pode ler mais sobre [Azure Resource Manager recursos e benefícios](../articles/azure-resource-manager/management/overview.md). 

## <a name="goal-for-migration"></a>Meta para a migração
O Gerenciador de recursos permite a implantação de aplicativos complexos por meio de modelos, configura as máquinas virtuais usando extensões de VM e incorpora o gerenciamento e a marcação de acesso. O Azure Resource Manager inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implantação também fornece gerenciamento de ciclo de vida de computação, rede e armazenamento independentemente. Por fim, há um foco na habilitação da segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Quase todos os recursos do modelo de implantação clássico têm suporte para computação, rede e armazenamento em Azure Resource Manager. Para se beneficiar dos novos recursos do Azure Resource Manager, você pode migrar as implantações existentes do modelo de implantação clássico.

## <a name="supported-resources-for-migration"></a>Recursos com suporte para migração
Esses recursos clássicos de IaaS têm suporte durante a migração

* Virtual Machines
* Conjuntos de Disponibilidade
* Serviços Cloud com Máquinas Virtuais
* Contas de Armazenamento
* Redes Virtuais
* Gateways de VPN
* Gateways de rota expressa _(na mesma assinatura que apenas na rede virtual)_
* Grupos de Segurança de Rede
* Tabelas de Rota
* IPs Reservados

## <a name="supported-scopes-of-migration"></a>Escopos de migração com suporte
Há quatro maneiras diferentes de concluir a migração de recursos de computação, rede e armazenamento:

* [Migração de máquinas virtuais (não em uma rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (em uma rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos desanexados](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (não em uma rede virtual)
No modelo de implantação do Gerenciador de recursos, a segurança é imposta para seus aplicativos por padrão. Todas as VMs precisam estar em uma rede virtual no modelo do Resource Manager. A plataforma Azure reinicia (`Stop`, `Deallocate`e `Start`) as VMs como parte da migração. Você tem duas opções para as redes virtuais nas quais as máquinas virtuais serão migradas:

* Você pode solicitar a plataforma para criar uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
* Você pode migrar a máquina virtual para uma rede virtual existente no Gerenciador de recursos.

> [!NOTE]
> Nesse escopo de migração, as operações de plano de gerenciamento e de plano de dados podem não ser permitidas por um período de tempo durante a migração.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (em uma rede virtual)
Para a maioria das configurações de VM, somente os metadados são migrados entre os modelos de implantação clássico e do Resource Manager. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações do plano de gerenciamento podem não ser permitidas por um determinado período de tempo durante a migração. No entanto, o plano de dados continua funcionando. Ou seja, seus aplicativos em execução na parte superior das VMs (clássicas) não incorrem em tempo de inatividade durante a migração.

No momento, não há suporte para as configurações a seguir. Se o suporte for adicionado no futuro, algumas VMs nessa configuração poderão incorrer em tempo de inatividade (passar por parar, desalocar e reiniciar operações de VM).

* Você tem mais de um conjunto de disponibilidade em um único serviço de nuvem.
* Você tem um ou mais conjuntos de disponibilidade e VMs que não estão em um conjunto de disponibilidade em um único serviço de nuvem.

> [!NOTE]
> Nesse escopo de migração, o plano de gerenciamento pode não ser permitido por um período de tempo durante a migração. Para determinadas configurações, conforme descrito anteriormente, o tempo de inatividade do plano de dados ocorre.
>

### <a name="migration-of-storage-accounts"></a>Migração de contas de armazenamento
Para permitir a migração direta, você pode implantar VMs do Resource Manager em uma conta de armazenamento clássica. Com esse recurso, os recursos de computação e rede podem e devem ser migrados independentemente das contas de armazenamento. Depois de migrar suas máquinas virtuais e rede virtual, você precisa migrar suas contas de armazenamento para concluir o processo de migração.

Se sua conta de armazenamento não tiver discos associados ou dados de máquinas virtuais e apenas tiver BLOBs, arquivos, tabelas e filas, a migração para Azure Resource Manager pode ser feita como uma migração autônoma sem dependências.

> [!NOTE]
> O modelo de implantação do Gerenciador de recursos não tem o conceito de discos e imagens clássicas. Quando a conta de armazenamento é migrada, as imagens clássicas e os discos não são visíveis na pilha do Gerenciador de recursos, mas os VHDs de backup permanecem na conta de armazenamento.

As capturas de tela a seguir mostram como atualizar uma conta de armazenamento clássico para uma conta de armazenamento Azure Resource Manager usando portal do Azure:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na seção **configurações** , clique em **migrar para o ARM**.
4. Clique em **validar** para determinar a viabilidade de migração.
5. Se a validação for aprovada, clique em **preparar** para criar uma conta de armazenamento migrada.
6. Digite **Sim** para confirmar a migração e clique em **confirmar** para concluir a migração.

    ![Validar conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar migração da conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos desanexados
Contas de armazenamento sem discos associados ou dados de máquinas virtuais podem ser migradas de forma independente.

Grupos de segurança de rede, tabelas de rotas & IPs reservados que não estão anexados a máquinas virtuais e redes virtuais também podem ser migrados de forma independente.

<br>

## <a name="unsupported-features-and-configurations"></a>Recursos e configurações sem suporte
Atualmente, não há suporte para alguns recursos e configurações; as seções a seguir descrevem nossas recomendações em relação a elas.

### <a name="unsupported-features"></a>Recursos sem suporte
Atualmente, não há suporte para os recursos a seguir. Opcionalmente, você pode remover essas configurações, migrar as VMs e reabilitar as configurações no modelo de implantação do Gerenciador de recursos.

| Fornecedor de recursos | Funcionalidade | Recomendação |
| --- | --- | --- |
| Computação | Discos de máquina virtual não associados. | Os BLOBs VHD por trás desses discos serão migrados quando a conta de armazenamento for migrada |
| Computação | Imagens da máquina virtual. | Os BLOBs VHD por trás desses discos serão migrados quando a conta de armazenamento for migrada |
| Rede | ACLs de ponto de extremidade. | Remova as ACLs de ponto de extremidade e tente a migração novamente. |
| Rede | Gateway da Aplicação | Remova o gateway de aplicativo antes de iniciar a migração e recrie o gateway de aplicativo após a conclusão da migração. |
| Rede | Redes virtuais usando o emparelhamento VNet. | Migre a rede virtual para o Gerenciador de recursos e, em seguida, o par. Saiba mais sobre o [emparelhamento VNet](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações não suportadas
No momento, não há suporte para as configurações a seguir.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Resource Manager |RBAC (controle de acesso baseado em função) para recursos clássicos |Como o URI dos recursos é modificado após a migração, é recomendável que você planeje as atualizações de política do RBAC que precisam ocorrer após a migração. |
| Computação |Várias sub-redes associadas a uma VM |Atualize a configuração de sub-rede para fazer referência a apenas uma sub-rede. Isso pode exigir que você remova uma NIC secundária (que está se referindo a outra sub-rede) da VM e a anexe novamente após a conclusão da migração. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual, mas que não têm uma sub-rede explícita atribuída |Opcionalmente, você pode excluir a VM. |
| Computação |Máquinas virtuais com alertas, políticas de dimensionamento automático |A migração passa e essas configurações são descartadas. É altamente recomendável que você avalie seu ambiente antes de fazer a migração. Como alternativa, você pode reconfigurar as configurações de alerta após a conclusão da migração. |
| Computação |Extensões de VM XML (BGInfo 1. *, depurador do Visual Studio, Implantação da Web e depuração remota) |Isto não é apoiado. É recomendável que você remova essas extensões da máquina virtual para continuar a migração ou elas serão descartadas automaticamente durante o processo de migração. |
| Computação |Diagnóstico de inicialização com armazenamento Premium |Desabilite o recurso de diagnóstico de inicialização para as VMs antes de continuar com a migração. Você pode reabilitar o diagnóstico de inicialização na pilha do Gerenciador de recursos após a conclusão da migração. Além disso, os blobs que estão sendo usados para captura de tela e logs seriais devem ser excluídos para que você não seja mais cobrado por esses BLOBs. |
| Computação | Serviços de nuvem que contêm funções Web/de trabalho | Não há suporte para isso no momento. |
| Computação | Serviços de nuvem que contêm mais de um conjunto de disponibilidade ou vários conjuntos de disponibilidade. |Não há suporte para isso no momento. Mova as máquinas virtuais para o mesmo conjunto de disponibilidade antes de migrar. |
| Computação | VM com a extensão da central de segurança do Azure | A central de segurança do Azure instala extensões automaticamente em suas máquinas virtuais para monitorar a segurança e gerar alertas. Essas extensões geralmente são instaladas automaticamente se a política da central de segurança do Azure estiver habilitada na assinatura. Para migrar as máquinas virtuais, desabilite a política da central de segurança na assinatura, o que removerá a extensão de monitoramento da central de segurança das máquinas virtuais. |
| Computação | VM com extensão de backup ou instantâneo | Essas extensões são instaladas em uma máquina virtual configurada com o serviço de backup do Azure. Embora não haja suporte para a migração dessas VMs, siga as orientações [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) para manter os backups que foram feitos antes da migração.  |
| Computação | VM com extensão de recuperação do site Azure | Estas extensões estão instaladas numa Máquina Virtual configurada com o serviço de recuperação do site Azure. Enquanto a migração de armazenamento usado com a Recuperação do Local funcionará, a replicação atual será impactada. É necessário desativar e ativar a replicação vm após a migração do armazenamento. |
| Rede |Redes virtuais que contêm máquinas virtuais e funções Web/de trabalho |Não há suporte para isso no momento. Mova as funções Web/de trabalho para sua própria rede virtual antes de migrar. Depois que a rede virtual clássica for migrada, a rede virtual Azure Resource Manager migrada poderá ser emparelhada com a rede virtual clássica para obter uma configuração semelhante como antes.|
| Rede | Circuitos de rota expressa clássica |Não há suporte para isso no momento. Esses circuitos precisam ser migrados para Azure Resource Manager antes de iniciar a migração de IaaS. Para saber mais, consulte [movendo circuitos do ExpressRoute do modelo de implantação clássico para o Gerenciador de recursos](../articles/expressroute/expressroute-move.md).|
| App Service do Azure |Redes virtuais que contêm ambientes de serviço de aplicativo |Não há suporte para isso no momento. |
| Azure HDInsight |Redes virtuais que contêm serviços do HDInsight |Não há suporte para isso no momento. |
| Serviços de ciclo de vida do Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais que são gerenciadas pelos serviços de ciclo de vida do Dynamics |Não há suporte para isso no momento. |
| Serviços de Domínio do Azure AD |Redes virtuais que contêm os serviços de domínio do Azure AD |Não há suporte para isso no momento. |
| API Management do Azure |Redes virtuais que contêm implantações de gerenciamento de API do Azure |Não há suporte para isso no momento. Para migrar a VNET de IaaS, altere a VNET da implantação de gerenciamento de API, que é uma operação sem tempo de inatividade. |
