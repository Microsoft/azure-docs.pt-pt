---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: b874cefc2521089da02b90b9241be93e80836d6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507438"
---
Este artigo descreve como migrar a infraestrutura como um serviço (IaaS) recursos dos modelos de implementação Classic to Resource Manager e detalhes como conectar recursos dos dois modelos de implementação que coexistem na sua subscrição usando gateways de rede virtual site-to-site. Pode ler mais sobre [funcionalidades e benefícios do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md). 

## <a name="goal-for-migration"></a>Objetivo para a migração
O Gestor de Recursos permite a implementação de aplicações complexas através de modelos, configura máquinas virtuais utilizando extensões VM e incorpora a gestão e marcação de acesso. O Azure Resource Manager inclui uma implementação paralela e escalável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implementação também fornece gestão do ciclo de vida do cálculo, rede e armazenamento de forma independente. Finalmente, há um foco em permitir a segurança por padrão com a aplicação de máquinas virtuais numa rede virtual.

Quase todas as funcionalidades do modelo de implementação clássica são suportadas para computação, rede e armazenamento sob o Azure Resource Manager. Para beneficiar das novas capacidades no Azure Resource Manager, pode migrar as implementações existentes a partir do modelo de implementação Classic.

## <a name="supported-resources--configurations-for-migration"></a>Recursos apoiados & configurações para a migração

### <a name="supported-resources-for-migration"></a>Recursos apoiados para a migração
* Máquinas Virtuais
* Conjuntos de Disponibilidade
* Contas de Armazenamento
* Redes Virtuais
* Gateways de VPN
* Gateways de rota expresso _(apenas na mesma subscrição que apenas a Rede Virtual)_
* Grupos de Segurança de Rede
* Tabelas de Rota
* IPs Reservados

## <a name="supported-configurations-for-migration"></a>Configurações suportadas para migração
Estes recursos clássicos da IAS são apoiados durante a migração

| Serviço | Configuração |
| --- | --- |
| Azure AD Domain Services | [Redes virtuais que contêm serviços de domínio AD Azure](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) |

## <a name="supported-scopes-of-migration"></a>Âmbitos de migração apoiados
Existem quatro maneiras diferentes de completar a migração de recursos de computação, rede e armazenamento:

* [Migração de máquinas virtuais (NÃO numa rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (numa rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos não ligados](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (NÃO numa rede virtual)
No modelo de implementação do Gestor de Recursos, a segurança é aplicada para as suas aplicações por padrão. Todos os VMs precisam de estar numa rede virtual no modelo de Gestor de Recursos. A plataforma Azure reinicia ( `Stop` , e ) os `Deallocate` `Start` VMs como parte da migração. Tem duas opções para as redes virtuais para as quais as Máquinas Virtuais serão migradas para:

* Pode solicitar à plataforma a criação de uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
* Pode migrar a máquina virtual para uma rede virtual existente no Gestor de Recursos.

> [!NOTE]
> Neste âmbito de migração, tanto as operações de gestão-avião como as operações de data-plane não podem ser permitidas durante um período de tempo durante a migração.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (numa rede virtual)
Para a maioria das configurações de VM, apenas os metadados estão a migrar entre os modelos de implementação Classic e Resource Manager. Os VM subjacentes estão a funcionar no mesmo hardware, na mesma rede, e com o mesmo armazenamento. As operações de gestão-avião não podem ser permitidas durante um determinado período de tempo durante a migração. No entanto, o plano de dados continua a funcionar. Ou seja, as suas aplicações em execução em cima de VMs (clássico) não incorrem em tempo de inatividade durante a migração.

As seguintes configurações não são suportadas atualmente. Se o suporte for adicionado no futuro, alguns VMs nesta configuração podem incorrer em tempo de inatividade (passar por stop, deallocate e reiniciar operações VM).

* Tem mais de uma disponibilidade definida num único serviço de nuvem.
* Tem um ou mais conjuntos de disponibilidade e VMs que não estão numa disponibilidade definida num único serviço de nuvem.

> [!NOTE]
> Neste âmbito de migração, o plano de gestão não pode ser autorizado durante um período de tempo durante a migração. Para certas configurações descritas anteriormente, ocorre tempo de inatividade do plano de dados.
>

### <a name="migration-of-storage-accounts"></a>Migração de contas de armazenamento
Para permitir uma migração perfeita, pode implementar VMs do Gestor de Recursos numa conta de armazenamento clássica. Com esta capacidade, os recursos de computação e rede podem e devem ser migrados independentemente das contas de armazenamento. Assim que migrar sobre as máquinas virtuais e a Rede Virtual, precisa de migrar para as suas contas de armazenamento para completar o processo de migração.

Se a sua conta de armazenamento não tiver discos associados ou dados de Máquinas Virtuais e tiver apenas bolhas, ficheiros, tabelas e filas, então a migração para O Gestor de Recursos Azure pode ser feita como uma migração autónoma sem dependências.

> [!NOTE]
> O modelo de implementação do Gestor de Recursos não tem o conceito de imagens e discos clássicos. Quando a conta de armazenamento é migrada, imagens e discos clássicos não são visíveis na pilha de Gestor de Recursos, mas os VHDs de suporte permanecem na conta de armazenamento.

As imagens que se seguem mostram como atualizar uma conta de armazenamento clássico para uma conta de armazenamento do Azure Resource Manager utilizando o portal Azure:
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na secção **Definições,** clique **em Migrar para ARM**.
4. Clique em **Validar** para determinar a viabilidade da migração.
5. Se a validação passar, clique em **Preparar** para criar uma conta de armazenamento migrada.
6. Escreva **sim** para confirmar a migração e clique **em Comprometer-se** a terminar a migração.

    ![Validar Conta de Armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar Conta de Armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar a migração da conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos não ligados
As contas de armazenamento sem discos associados ou dados de Máquinas Virtuais podem ser migradas de forma independente.

Grupos de Segurança de Rede, Tabelas de Rota & IPs reservados que não estejam ligados a máquinas virtuais e redes virtuais também podem ser migrados de forma independente.

<br>

## <a name="unsupported-features-and-configurations"></a>Funcionalidades e configurações não suportadas
Algumas funcionalidades e configurações não são suportadas atualmente; as seguintes secções descrevem as nossas recomendações à sua volta.

### <a name="unsupported-features"></a>Funcionalidades não suportadas
As seguintes funcionalidades não são suportadas atualmente. Pode remover opcionalmente estas definições, migrar os VMs e, em seguida, reativar as definições no modelo de implementação do Gestor de Recursos.

| Fornecedor de recursos | Funcionalidade | Recomendação |
| --- | --- | --- |
| Computação | Discos de máquina virtuais não associados. | As bolhas VHD por trás destes discos serão migradas quando a Conta de Armazenamento for migrada |
| Computação | Imagens de máquina virtual. | As bolhas VHD por trás destes discos serão migradas quando a Conta de Armazenamento for migrada |
| Rede | ACLs de ponto final. | Remova os ACLs de ponto final e relfera a migração. |
| Rede | Gateway de Aplicação | Remova o Gateway de Aplicação antes de iniciar a migração e, em seguida, recrie o Gateway de aplicação uma vez que a migração esteja completa. |
| Rede | Redes virtuais usando VNet Peering. | Migrar a Rede Virtual para Gestor de Recursos, em seguida, peer. Saiba mais sobre [o VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações não suportadas
As seguintes configurações não são suportadas atualmente.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Resource Manager |Role-Based Controlo de Acesso (RBAC) para recursos clássicos |Como o URI dos recursos é modificado após a migração, recomenda-se que planeie as atualizações políticas do RBAC que precisam de acontecer após a migração. |
| Computação |Múltiplas sub-redes associadas a um VM |Atualize a configuração da sub-rede para apenas uma sub-rede. Isto pode exigir que remova um NIC secundário (que se refere a outra sub-rede) do VM e o recoloque após a migração ter terminado. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual mas não têm uma sub-rede explícita atribuída |Pode eliminar opcionalmente o VM. |
| Computação |Máquinas virtuais que têm alertas, políticas de autoescala |A migração passa e estas configurações são abandonadas. É altamente recomendável que avalie o seu ambiente antes de fazer a migração. Em alternativa, pode reconfigurar as definições de alerta após a conclusão da migração. |
| Computação |Extensões XML VM (BGInfo 1.*, Visual Studio Debugger, Web Deploy e Remote Debugging) |Esta ação não é suportada. Recomenda-se que remova estas extensões da máquina virtual para continuar a migração ou serão retiradas automaticamente durante o processo de migração. |
| Computação |Diagnóstico de arranque com armazenamento Premium |Desativar a função de diagnóstico de arranque para os VMs antes de continuar com a migração. Pode voltar a ativar os diagnósticos de arranque na pilha de Gestor de Recursos após a conclusão da migração. Além disso, as bolhas que estão a ser utilizadas para a imagem e registos em série devem ser eliminadas para que não seja mais cobrado por essas bolhas. |
| Computação | Serviços em nuvem que contêm funções web/trabalhador | Atualmente, isto não é apoiado. |
| Computação | Serviços em nuvem que contêm mais de um conjunto de disponibilidade ou conjuntos de disponibilidade múltiplas. |Atualmente, isto não é apoiado. Por favor, mova as Máquinas Virtuais para o mesmo conjunto de disponibilidade antes de migrar. |
| Computação | VM com extensão do Centro de Segurança Azure | O Azure Security Center instala automaticamente extensões nas suas Máquinas Virtuais para monitorizar a sua segurança e elevar os alertas. Estas extensões geralmente são instaladas automaticamente se a política do Centro de Segurança Azure estiver ativada na subscrição. Para migrar as Máquinas Virtuais, desative a política do centro de segurança na subscrição, que removerá a extensão de monitorização do Centro de Segurança das Máquinas Virtuais. |
| Computação | VM com extensão de backup ou instantâneo | Estas extensões são instaladas numa Máquina Virtual configurada com o serviço Azure Backup. Embora a migração destes VM não seja apoiada, siga as orientações [aqui](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) para manter os backups que foram tomados antes da migração.  |
| Computação | VM com extensão de recuperação do local de Azure | Estas extensões são instaladas numa Máquina Virtual configurada com o serviço de Recuperação do Local Azure. Enquanto a migração do armazenamento utilizado com a Recuperação do Local funcionará, a replicação atual será impactada. É necessário desativar e ativar a replicação de VM após a migração do armazenamento. |
| Rede |Redes virtuais que contêm máquinas virtuais e funções web/trabalhador |Atualmente, isto não é apoiado. Por favor, mova as funções Web/Trabalhador para a sua própria Rede Virtual antes de migrar. Uma vez que a rede virtual clássica é migrada, a rede virtual do Gestor de Recursos Azure emigrou pode ser espreitada com a clássica Rede Virtual para obter uma configuração semelhante à anterior.|
| Rede | Circuitos clássicos da Rota Expressa |Atualmente, isto não é apoiado. Estes circuitos precisam de ser migrados para O Gestor de Recursos Azure antes de iniciar a migração da IAAS. Para saber mais, consulte os [circuitos Moving ExpressRoute do clássico ao modelo de implementação do Gestor de Recursos.](../articles/expressroute/expressroute-move.md)|
| Serviço de Aplicações do Azure |Redes virtuais que contêm ambientes de Serviço de Aplicações |Atualmente, isto não é apoiado. |
| Azure HDInsight |Redes virtuais que contêm serviços HDInsight |Atualmente, isto não é apoiado. |
| Serviços de ciclo de vida da Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais que são geridas pela Dynamics Lifecycle Services |Atualmente, isto não é apoiado. |
| Gestão de API do Azure |Redes virtuais que contêm implementações de Gestão API da Azure |Atualmente, isto não é apoiado. Para migrar o IaaS VNET, mude o VNET da implementação da API Management, que não é uma operação de inatividade. |
