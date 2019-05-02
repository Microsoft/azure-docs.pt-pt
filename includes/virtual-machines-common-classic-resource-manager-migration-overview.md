---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: f60b5421f2bc66cf09ede4178ce18e2394030264
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64929437"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager
Este artigo descreve como migrar a infraestrutura como um recursos de serviço (IaaS) do clássico para modelos de implementação do Resource Manager e os detalhes como ligar recursos a partir de dois modelos de implementação que coexistir na sua subscrição, utilizando a rede virtual gateways de site a site. Pode ler mais sobre [recursos do Azure Resource Manager e os benefícios](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Objetivo para migração
O Resource Manager permite a implementação de aplicações complexas através de modelos, configura as máquinas virtuais utilizando extensões de VM e incorpora a gestão de acesso e identificação. O Azure Resource Manager inclui a implementação de dimensionável e paralela para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implementação também fornece gestão de ciclo de vida de computação, rede e armazenamento de forma independente. Por fim, existe um foco sobre como ativar a segurança por padrão com a imposição de máquinas virtuais numa rede virtual.

Quase todas as funcionalidades do modelo de implementação clássica são suportadas para computação, rede e armazenamento no Azure Resource Manager. Para tirar partido das novas capacidades no Azure Resource Manager, é possível migrar implementações existentes do modelo de implementação clássico.

## <a name="supported-resources-for-migration"></a>Recursos suportados para migração
Estes recursos de IaaS clássicos são suportados durante a migração

* Virtual Machines
* Conjuntos de Disponibilidade
* Serviços Cloud com Máquinas Virtuais
* Contas de Armazenamento
* Redes Virtuais
* Gateways de VPN
* Express Route Gateways _(na mesma subscrição da rede Virtual apenas)_
* Grupos de Segurança de Rede
* Tabelas de Rota
* IPs Reservados

## <a name="supported-scopes-of-migration"></a>Âmbitos suportados de migração
Existem quatro formas diferentes de concluir a migração de recursos de computação, rede e armazenamento:

* [Migração de máquinas virtuais (não numa rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (numa rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos ligados](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (não numa rede virtual)
Por predefinição no modelo de implementação do Resource Manager, a segurança é imposta para as suas aplicações. Todas as VMs tem de estar numa rede virtual no modelo do Resource Manager. Os reinícios de plataforma do Azure (`Stop`, `Deallocate`, e `Start`) as VMs como parte da migração. Tem duas opções para as redes virtuais que serão migrados para as máquinas virtuais:

* Pode solicitar a plataforma para criar uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
* Pode migrar a máquina virtual numa rede virtual existente no Resource Manager.

> [!NOTE]
> Neste âmbito de migração, as operações de plano de gestão e as operações de plano de dados poderão não ser permitidas durante um período de tempo durante a migração.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (numa rede virtual)
Na maioria das configurações de VM, apenas os metadados está migrando entre os modelos de implementação clássica e Resource Manager. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações de plano de gestão poderão não ser permitidas para um determinado período de tempo durante a migração. No entanto, o plano de dados continua a funcionar. Ou seja, as aplicações em execução em cima de VMs (clássicas) não incorrem em tempo de inatividade durante a migração.

As seguintes configurações não são atualmente suportadas. Se o suporte é adicionado no futuro, algumas VMs nesta configuração pode incorrer em períodos de inatividade (aceda através de parar, desalocar e reinicie a operações de VM).

* Tem mais do que um conjunto de disponibilidade no serviço cloud único.
* Tem um ou mais conjuntos de disponibilidade e VMs que não estão num conjunto num serviço cloud único de disponibilidade.

> [!NOTE]
> Neste âmbito de migração, o plano de gestão poderá não ser permitido durante um período de tempo durante a migração. Para determinadas configurações conforme descrito anteriormente, período de indisponibilidade do plano de dados ocorre.
>

### <a name="migration-of-storage-accounts"></a>Migração de contas de armazenamento
Para permitir a migração perfeita, pode implementar VMs do Resource Manager numa conta de armazenamento clássico. Com esta capacidade, os recursos de computação e rede podem e devem ser migrados independentemente das contas de armazenamento. Depois de migrar através de suas máquinas virtuais e a rede Virtual, terá de migrar através de suas contas de armazenamento para concluir o processo de migração.

Se a sua conta de armazenamento não tem quaisquer discos associados ou dados de máquinas virtuais e tem apenas de blobs, ficheiros, tabelas e filas, em seguida, a migração para o Azure Resource Manager pode ser feita como uma migração autónoma, sem dependências.

> [!NOTE]
> O modelo de implementação do Resource Manager não tem o conceito de discos e imagens clássicos. Quando a conta de armazenamento é migradas, clássicas imagens e discos não estão visíveis na pilha do Resource Manager, mas a VHDs de segurança permanecem na conta de armazenamento.

As capturas de ecrã seguintes mostram como atualizar uma conta de armazenamento de clássico para uma conta de armazenamento do Azure Resource Manager através do portal do Azure:
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na **configurações** secção, clique em **para migrar para ARM**.
4. Clique em **Validate** para determinar a viabilidade de migração.
5. Se a validação é bem-sucedida, clique em **preparar** para criar uma conta de armazenamento migrados.
6. Tipo **Sim** para confirmar a migração e clique em **consolidar** para concluir a migração.

    ![Validar a conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar a conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar a migração de contas de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos ligados
Contas de armazenamento sem discos associados ou dados de máquinas virtuais podem ser migradas de forma independente.

Grupos de segurança de rede, tabelas de rotas e IPs reservados que não estão ligados a quaisquer máquinas virtuais e redes virtuais podem também ser migrados de forma independente.

<br>

## <a name="unsupported-features-and-configurations"></a>Configurações e funcionalidades não suportadas
Algumas funcionalidades e configurações não são atualmente suportadas; as secções seguintes descrevem as nossas recomendações em torno delas.

### <a name="unsupported-features"></a>Funcionalidades não suportadas
As seguintes funcionalidades não são atualmente suportadas. Pode, opcionalmente, remover estas definições, migrar as VMs e, em seguida, volte a ativar as definições no modelo de implementação do Resource Manager.

| Fornecedor de recursos | Funcionalidade | Recomendação |
| --- | --- | --- |
| Computação | Discos da máquina de virtual não associados. | Os blobs de VHD por trás destes discos serão são migrados quando a conta de armazenamento é migrada |
| Computação | Imagens de máquinas virtuais. | Os blobs de VHD por trás destes discos serão são migrados quando a conta de armazenamento é migrada |
| Rede | ACLs de ponto final. | Remover ACLs de ponto final e repita a migração. |
| Rede | Gateway de Aplicação | Remover o Gateway de aplicação antes de iniciar a migração e, em seguida, recriar o Gateway de aplicação depois de concluída a migração. |
| Rede | Redes virtuais através de VNet Peering. | Migre a rede Virtual para o Resource Manager, em seguida, configurar o peering. Saiba mais sobre [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações não suportadas
As seguintes configurações não são atualmente suportadas.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Resource Manager |Com base em função de controlo de acesso (RBAC) para recursos clássicos |Uma vez que o URI dos recursos é modificado após a migração, recomendamos que planeie as atualizações de políticas RBAC que têm de ocorrer após a migração. |
| Computação |Várias sub-redes associadas uma VM |Atualize a configuração de sub-rede para referenciar apenas uma sub-rede. Isso pode exigir que remova um NIC secundário (que se refere a outra sub-rede) da VM e voltar a ligá-lo após a migração foi concluída. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual, mas não tem uma sub-rede explícita atribuída |Pode, opcionalmente, elimine a VM. |
| Computação |Máquinas virtuais com alertas, políticas de dimensionamento automático |A migração atravessa e estas definições são ignoradas. É altamente recomendável que avaliar seu ambiente antes de efetuar a migração. Em alternativa, pode reconfigurar as definições de alerta após a conclusão da migração. |
| Computação |Extensões de VM de XML (1. * de BGInfo, depurador do Visual Studio, Web Deploy e depuração remota) |Não é suportada. É recomendado que remova estas extensões da máquina virtual para continuar a migração ou serão removidos automaticamente durante o processo de migração. |
| Computação |Diagnóstico de arranque com o armazenamento Premium |Desative a funcionalidade de diagnóstico de arranque para as VMs antes de continuar a migração. Pode reativar o diagnóstico de arranque na pilha do Resource Manager após a migração estar concluída. Além disso, os blobs que estão a ser utilizadas para captura de ecrã e registos de série devem ser eliminados para que já não é cobradas para os blobs. |
| Computação | Serviços cloud que contêm funções de trabalho/web | Isso não é atualmente suportado. |
| Computação | Conjunto de serviços em nuvem que contêm mais do que uma disponibilidade ou conjuntos de disponibilidade de vários. |Isso não é atualmente suportado. Mova as máquinas virtuais para a mesmo conjunto antes de migrar de disponibilidade. |
| Computação | VM com a extensão do Centro de segurança do Azure | Centro de segurança do Azure instala automaticamente extensões nas suas máquinas virtuais para monitorizar a segurança e emitir alertas. Estas extensões, normalmente, são instaladas automaticamente se a política do Centro de segurança do Azure está ativada na subscrição. Para migrar as máquinas virtuais, desative a política do Centro de segurança na subscrição, o que removerá o Centro de segurança extensão das máquinas virtuais de monitorização. |
| Computação | VM com a extensão de cópia de segurança ou de instantâneo | Estas extensões são instaladas na máquina Virtual configurada com o serviço de cópia de segurança do Azure. Enquanto a migração estas VMS não é suportada, siga as orientações [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) para manter as cópias de segurança que foram tomadas antes da migração.  |
| Rede |Redes virtuais que contêm máquinas virtuais e funções de trabalho/web |Isso não é atualmente suportado. Mova as funções da Web/de trabalho para a sua própria rede Virtual antes de migrar. Assim que a rede Virtual clássica é migrada, a rede Virtual do Gestor de recursos do migrados do Azure pode ser em modo de peering com a rede Virtual clássica para alcançar a configuração semelhante como antes.|
| Rede | Circuitos de Expressroute clássicos |Isso não é atualmente suportado. Estes circuitos tem de ser migrados para o Azure Resource Manager antes de iniciar a migração de IaaS. Para obter mais informações, consulte [circuitos do ExpressRoute mover do clássico para o modelo de implementação do Resource Manager](../articles/expressroute/expressroute-move.md).|
| Serviço de Aplicações do Azure |Redes virtuais que contêm os ambientes de serviço de aplicações |Isso não é atualmente suportado. |
| Azure HDInsight |Redes virtuais que contêm os serviços do HDInsight |Isso não é atualmente suportado. |
| Serviços de ciclo de vida do Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais que são geridas pelo Dynamics Lifecycle Services |Isso não é atualmente suportado. |
| Azure AD Domain Services |Redes virtuais que contêm os serviços de domínio do Azure AD |Isso não é atualmente suportado. |
| Azure RemoteApp |Redes virtuais que contêm as implementações do Azure RemoteApp |Isso não é atualmente suportado. |
| API Management do Azure |Redes virtuais que contenham implementações de API Management do Azure |Isso não é atualmente suportado. Para migrar a VNET de IaaS, altere a VNET da implementação da gestão de API, que não é uma nenhuma operação de tempo de inatividade. |
