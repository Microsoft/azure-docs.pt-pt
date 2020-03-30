---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192566"
---
Este artigo descreve como migrar a infraestrutura como um serviço (IaaS) recursos dos modelos de implementação Classic to Resource Manager e detalhes como conectar recursos dos dois modelos de implementação que coexistem na sua subscrição usando rede virtual gateways site-to-site. Pode ler mais sobre [funcionalidades e benefícios do Gestor de Recursos do Azure.](../articles/azure-resource-manager/management/overview.md) 

## <a name="goal-for-migration"></a>Objetivo para a migração
O Gestor de Recursos permite a implementação de aplicações complexas através de modelos, configuramáquinas virtuais utilizando extensões VM e incorpora gestão de acesso e marcação. O Azure Resource Manager inclui uma implantação paralela e escalável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implementação também fornece a gestão do ciclo de vida da computação, rede e armazenamento de forma independente. Finalmente, há um foco em permitir a segurança por padrão com a aplicação de máquinas virtuais numa rede virtual.

Quase todas as funcionalidades do modelo clássico de implementação são suportadas para computação, rede e armazenamento sob o Azure Resource Manager. Para beneficiar das novas capacidades no Azure Resource Manager, pode migrar as implementações existentes a partir do modelo de implementação Classic.

## <a name="supported-resources-for-migration"></a>Recursos apoiados para a migração
Estes recursos clássicos do IaaS são apoiados durante a migração

* Máquinas Virtuais
* Conjuntos de Disponibilidade
* Contas de Armazenamento
* Redes Virtuais
* Gateways de VPN
* Gateways de rota expressa _(na mesma subscrição_ que apenas rede virtual)
* Grupos de Segurança de Rede
* Tabelas de Rota
* IPs Reservados

## <a name="supported-scopes-of-migration"></a>Âmbitos de migração apoiados
Existem quatro formas diferentes de completar a migração de recursos de computação, rede e armazenamento:

* [Migração de máquinas virtuais (NÃO numa rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (numa rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos não ligados](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (NÃO numa rede virtual)
No modelo de implementação do Gestor de Recursos, a segurança é executada para as suas aplicações por padrão. Todos os VMs precisam estar numa rede virtual no modelo De Gestão de Recursos. A plataforma Azure`Stop`reinicia `Start`os `Deallocate`VMs como parte da migração. Tem duas opções para as redes virtuais para as as que as Máquinas Virtuais serão migradas:

* Pode solicitar à plataforma que crie uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
* Pode migrar a máquina virtual para uma rede virtual existente no Gestor de Recursos.

> [!NOTE]
> Neste âmbito de migração, tanto as operações de gestão-avião como as operações de avião de dados não podem ser permitidas durante um período de tempo durante a migração.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (numa rede virtual)
Para a maioria das configurações vm, apenas os metadados estão migrando entre os modelos de implementação Classic e Resource Manager. Os VMs subjacentes estão a funcionar no mesmo hardware, na mesma rede, e com o mesmo armazenamento. As operações de gestão-avião não podem ser permitidas durante um determinado período de tempo durante a migração. No entanto, o plano de dados continua a funcionar. Ou seja, as suas aplicações em execução em cima de VMs (clássico) não incorrem em tempo de inatividade durante a migração.

As seguintes configurações não são suportadas atualmente. Se o suporte for adicionado no futuro, alguns VMs nesta configuração podem incorrer em tempo de paragem (passar por stop, desolocar e reiniciar as operações vM).

* Você tem mais de uma disponibilidade definida em um único serviço de nuvem.
* Você tem um ou mais conjuntos de disponibilidade e VMs que não estão em um conjunto de disponibilidade definido em um único serviço na nuvem.

> [!NOTE]
> Neste âmbito de migração, o plano de gestão não pode ser permitido durante um período de tempo durante a migração. Para certas configurações, como descrito anteriormente, ocorre o tempo de inatividade do plano de dados.
>

### <a name="migration-of-storage-accounts"></a>Migração de contas de armazenamento
Para permitir uma migração perfeita, pode implementar VMs do Gestor de Recursos numa conta de armazenamento clássica. Com esta capacidade, os recursos de computação e rede podem e devem ser migrados independentemente das contas de armazenamento. Uma vez migrado sobre as suas Máquinas Virtuais e Rede Virtual, precisa migrar sobre as suas contas de armazenamento para completar o processo de migração.

Se a sua conta de armazenamento não tiver quaisquer dados de discos ou máquinas virtuais associados e tiver apenas bolhas, ficheiros, tabelas e filas, então a migração para o Gestor de Recursos Azure pode ser feita como uma migração autónoma sem dependências.

> [!NOTE]
> O modelo de implementação do Gestor de Recursos não tem o conceito de imagens clássicas e discos. Quando a conta de armazenamento é migrada, as imagens clássicas e os discos não são visíveis na pilha do Gestor de Recursos, mas os VHDs de apoio permanecem na conta de armazenamento.

As seguintes imagens mostram como atualizar uma conta de armazenamento Clássica para uma conta de armazenamento do Gestor de Recursos Azure utilizando o portal Azure:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até à sua conta de armazenamento.
3. Na secção **Definições,** clique em **Migrar para ARM**.
4. Clique em **Validate** para determinar a viabilidade da migração.
5. Se a validação passar, clique em **Prepare para** criar uma conta de armazenamento migrada.
6. Digite **sim** para confirmar a migração e clique **em terminar** a migração.

    ![Validar conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar migração da conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos não ligados
Contas de armazenamento sem discos associados ou dados de Máquinas Virtuais podem ser migrados independentemente.

Grupos de Segurança de Rede, Tabelas de Rotas & IPs reservados que não estejam ligados a quaisquer máquinas virtuais e redes virtuais também podem ser migrados independentemente.

<br>

## <a name="unsupported-features-and-configurations"></a>Funcionalidades e configurações não suportadas
Algumas funcionalidades e configurações não são suportadas atualmente; as seguintes secções descrevem as nossas recomendações à sua volta.

### <a name="unsupported-features"></a>Características não suportadas
As seguintes funcionalidades não são atualmente suportadas. Pode remover opcionalmente estas definições, migrar os VMs e, em seguida, reativar as definições no modelo de implementação do Gestor de Recursos.

| Fornecedor de recursos | Funcionalidade | Recomendação |
| --- | --- | --- |
| Computação | Discos de máquinas virtuais não associados. | As bolhas VHD atrás destes discos serão migradas quando a Conta de Armazenamento for migrada |
| Computação | Imagens de máquinas virtuais. | As bolhas VHD atrás destes discos serão migradas quando a Conta de Armazenamento for migrada |
| Rede | ACLs de ponto final. | Remova os ACLs endpoint e retente a migração. |
| Rede | Gateway de Aplicação | Remova o Gateway de Aplicação antes de iniciar a migração e, em seguida, recriar o Gateway de Aplicação uma vez concluída a migração. |
| Rede | Redes virtuais que utilizam o VNet Peering. | Migrar rede virtual para gestor de recursos, em seguida, peer. Saiba mais sobre [o VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações não suportadas
As seguintes configurações não são suportadas atualmente.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Resource Manager |Controlo de Acesso baseado em funções (RBAC) para recursos clássicos |Uma vez que o URI dos recursos é modificado após a migração, recomenda-se que planeie as atualizações políticas do RBAC que precisam de acontecer após a migração. |
| Computação |Múltiplas subredes associadas a um VM |Atualize a configuração da sub-rede para referência apenas a uma sub-rede. Isto pode exigir que remova um NIC secundário (que se refere a outra sub-rede) do VM e o recoloque após a migração ter terminado. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual mas não têm uma sub-rede explícita atribuída |Pode eliminar opcionalmente o VM. |
| Computação |Máquinas virtuais que têm alertas, políticas de escala automática |A migração passa e estas configurações são abandonadas. É altamente recomendável que avalie o seu ambiente antes de fazer a migração. Em alternativa, pode reconfigurar as definições de alerta após a migração estar completa. |
| Computação |Extensões VM XML (BGInfo 1.*, Visual Studio Debugger, Web Deploy e Remote Debugging) |Esta ação não é suportada. Recomenda-se que remova estas extensões da máquina virtual para continuar a migração ou que sejam retiradas automaticamente durante o processo de migração. |
| Computação |Diagnósticos de arranque com armazenamento Premium |Desative a função Desativação de Diagnósticos de Arranque para os VMs antes de continuar com a migração. Pode reativar os diagnósticos de arranque na pilha do Gestor de Recursos após a migração estar concluída. Além disso, as bolhas que estão a ser utilizadas para screenshot e registos em série devem ser eliminadas para que não seja mais cobrada por essas bolhas. |
| Computação | Serviços em nuvem que contêm funções web/trabalhador | Isto não é atualmente apoiado. |
| Computação | Serviços na nuvem que contêm mais de um conjunto de disponibilidade ou conjuntos de disponibilidade múltiplos. |Isto não é atualmente apoiado. Por favor, mova as Máquinas Virtuais para o mesmo conjunto de disponibilidade antes de migrar. |
| Computação | VM com extensão do Centro de Segurança Azure | O Azure Security Center instala automaticamente extensões nas suas Máquinas Virtuais para monitorizar a sua segurança e levantar alertas. Estas extensões são normalmente instaladas automaticamente se a política do Centro de Segurança Azure estiver ativada na subscrição. Para migrar as Máquinas Virtuais, desative a política do centro de segurança na subscrição, que removerá a extensão de monitorização do Centro de Segurança das Máquinas Virtuais. |
| Computação | VM com extensão de backup ou instantâneo | Estas extensões são instaladas numa Máquina Virtual configurada com o serviço de backup Azure. Embora a migração destes VMs não seja apoiada, siga as orientações [aqui](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) para manter os backups que foram tomados antes da migração.  |
| Computação | VM com extensão de recuperação do site Azure | Estas extensões estão instaladas numa Máquina Virtual configurada com o serviço de recuperação do site Azure. Enquanto a migração de armazenamento usado com a Recuperação do Local funcionará, a replicação atual será impactada. É necessário desativar e ativar a replicação vm após a migração do armazenamento. |
| Rede |Redes virtuais que contêm máquinas virtuais e funções web/trabalhador |Isto não é atualmente apoiado. Por favor, desloque as funções Web/Trabalhador para a sua própria Rede Virtual antes de migrar. Uma vez migrada a rede virtual clássica, a rede virtual do Gestor de Recursos Azure migrada pode ser espreitada com a rede virtual clássica para alcançar uma configuração semelhante à anterior.|
| Rede | Circuitos clássicos da Rota expresso |Isto não é atualmente apoiado. Estes circuitos precisam de ser migrados para o Gestor de Recursos Azure antes de iniciar a migração do IaaS. Para saber mais, consulte [os circuitos Moving ExpressRoute do clássico para o modelo](../articles/expressroute/expressroute-move.md)de implementação do Gestor de Recursos .|
| App Service do Azure |Redes virtuais que contêm ambientes de Serviço de Aplicações |Isto não é atualmente apoiado. |
| Azure HDInsight |Redes virtuais que contêm serviços HDInsight |Isto não é atualmente apoiado. |
| Serviços de Lifecycle Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais que são geridas pela Dynamics Lifecycle Services |Isto não é atualmente apoiado. |
| Azure AD Domain Services |Redes virtuais que contêm serviços de domínio Azure AD |Isto não é atualmente apoiado. |
| API Management do Azure |Redes virtuais que contêm implementações da Azure API Management |Isto não é atualmente apoiado. Para migrar o IaaS VNET, altere o VNET da implantação da API Management, que é uma operação sem tempo de inatividade. |
