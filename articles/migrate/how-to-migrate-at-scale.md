---
title: Automatizar a migração do computador de migração nas migrações para Azure
description: Descreve como usar scripts para migrar um grande número de computadores nas migrações para Azure
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196366"
---
# <a name="scale-migration-of-vms"></a>Dimensionar a migração de VMs 

Este artigo ajuda você a entender como usar scripts para migrar um grande número de VMs (máquinas virtuais). Para dimensionar a migração, use [Azure site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery scripts estão disponíveis para download em Azure PowerShell repositório de [exemplos](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) no github. Os scripts podem ser usados para migrar VMs VMware, AWS, GCP e servidores físicos para Managed disks no Azure. Você também pode usar esses scripts para migrar VMs do Hyper-V se migrar as VMs como servidores físicos. Os scripts que aproveitam Azure Site Recovery PowerShell estão documentados [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitações atuais
- Suporte para especificar o endereço IP estático somente para a NIC primária da VM de destino
- Os scripts não utilizam Benefício Híbrido do Azure entradas relacionadas, você precisa atualizar manualmente as propriedades da VM replicada no portal

## <a name="how-does-it-work"></a>Como funciona?

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa executar as seguintes etapas:
- Verifique se o cofre de Site Recovery foi criado na sua assinatura do Azure
- Verifique se o servidor de configuração e o servidor de processo estão instalados no ambiente de origem e se o cofre é capaz de descobrir o ambiente
- Verifique se uma política de replicação foi criada e associada ao servidor de configuração
- Verifique se você adicionou a conta do administrador da VM ao servidor de configuração (que será usado para replicar as VMs locais)
- Verifique se os artefatos de destino no Azure foram criados
    - Grupo de recursos de destino
    - Conta de armazenamento de destino (e seu grupo de recursos) – crie uma conta de armazenamento Premium se você planeja migrar para discos gerenciados Premium
    - Conta de armazenamento em cache (e seu grupo de recursos)-criar uma conta de armazenamento padrão na mesma região que o cofre
    - Rede virtual de destino para failover (e seu grupo de recursos)
    - Sub-rede de destino
    - Rede virtual de destino para failover de teste (e seu grupo de recursos)
    - Conjunto de disponibilidade (se necessário)
    - Grupo de segurança de rede de destino e seu grupo de recursos
- Verifique se você decidiu as propriedades da VM de destino
    - Nome da VM de destino
    - Tamanho da VM de destino no Azure (pode ser decidido usando a avaliação de migrações para Azure)
    - Endereço IP privado da NIC primária na VM
- Baixar os scripts do repositório de [exemplos Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) no github

### <a name="csv-input-file"></a>Arquivo de entrada CSV
Depois que todos os pré-requisitos forem concluídos, você precisará criar um arquivo CSV, que tem dados para cada computador de origem que você deseja migrar. O CSV de entrada deve ter uma linha de cabeçalho com os detalhes de entrada e uma linha com detalhes para cada computador que precisa ser migrado. Todos os scripts foram projetados para funcionar no mesmo arquivo CSV. Um modelo CSV de exemplo está disponível na pasta scripts para sua referência.

### <a name="script-execution"></a>Execução do script
Quando o CSV estiver pronto, você poderá executar as seguintes etapas para executar a migração das VMs locais:

**Etapa #** | **Nome do script** | **Descrição**
--- | --- | ---
1 | asr_startmigration.ps1 | Habilitar a replicação para todas as VMs listadas no CSV, o script cria uma saída CSV com os detalhes do trabalho para cada VM
2 | asr_replicationstatus.ps1 | Verificar o status da replicação, o script cria um CSV com o status de cada VM
3 | asr_updateproperties.ps1 | Depois que as VMs forem replicadas/protegidas, use esse script para atualizar as propriedades de destino da VM (Propriedades de computação e rede)
4 | asr_propertiescheck.ps1 | Verifique se as propriedades são atualizadas adequadamente
5 | asr_testmigration.ps1 |  Iniciar o failover de teste das VMs listadas no CSV, o script cria uma saída CSV com os detalhes do trabalho para cada VM
6 | asr_cleanuptestmigration.ps1 | Depois de validar manualmente as VMs que foram testadas com failover, você pode usar esse script para limpar as VMs de teste
7 | asr_migration.ps1 | Executar um failover não planejado para as VMs listadas no CSV, o script cria uma saída CSV com os detalhes do trabalho para cada VM. O script não desliga as VMs locais antes de disparar o failover, para a consistência do aplicativo, é recomendável que você desligue manualmente as VMs antes de executar o script.
8 | asr_completemigration.ps1 | Executar a operação de confirmação nas VMs e excluir as entidades de Azure Site Recovery
9 | asr_postmigration.ps1 | Se você planeja atribuir grupos de segurança de rede para os NICs após o failover, você pode usar esse script para fazer isso. Ele atribui um NSG a qualquer NIC na VM de destino.

## <a name="how-to-migrate-to-managed-disks"></a>Como migrar para o Managed disks?
O script, por padrão, migra as VMs para os Managed disks no Azure. Se a conta de armazenamento de destino fornecida for uma conta de armazenamento Premium, os discos gerenciados Premium serão criados após a migração. A conta de armazenamento em cache ainda pode ser uma conta padrão. Se a conta de armazenamento de destino for uma conta de armazenamento padrão, os discos padrão serão criados após a migração. 

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sobre a migração de servidores para o Azure usando o Azure site Recovery
