---
title: Automatizar a migração de grande número de VMs para o Azure | Documentos da Microsoft
description: Descreve como usar scripts para migrar um grande número de VMs com o Azure Site Recovery
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597116"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Migração de dimensionamento de VMs com o Azure Site Recovery

Este artigo ajuda-o a compreender o processo de uso de scripts para migrar o grande número de VMs com o Azure Site Recovery. Estes scripts estão disponíveis para a transferência a [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repositório no GitHub. Os scripts podem ser utilizados para migrar VMware, AWS, GCP VMs e servidores físicos para migração do Azure e o suporte para discos geridos. Também pode utilizar estes scripts para migrar VMs de Hyper-V, se migrar as VMs que os servidores físicos. Os scripts de tirar partido do Azure Site Recovery PowerShell documentados [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitações atuais:
- Suporta a especificação de endereço IP estático apenas para o NIC primário de VM de destino
- Os scripts não realizar relacionados com o Azure Hybrid Benefit entradas, tem de atualizar manualmente as propriedades da VM replicada no portal

## <a name="how-does-it-work"></a>Como funciona?

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, terá de efetuar os passos seguintes:
- Certifique-se de que o Cofre de recuperação de Site é criado na sua subscrição do Azure
- Certifique-se de que o servidor de configuração e o servidor de processos são instalados no ambiente de origem e o Cofre é capaz de detetar o ambiente
- Certifique-se de que uma política de replicação é criada e associada ao servidor de configuração
- Certifique-se de que adicionou a conta de administrador da VM para o servidor de configuração (que será utilizado para replicar no local, VMs)
- Certifique-se de que os artefactos de destino no Azure são criados
    - Grupo de Recursos de Destino
    - Conta de armazenamento de destino (e seu grupo de recursos) - criar uma conta de armazenamento premium se planeia migrar para discos geridos premium
    - Conta de armazenamento de cache (e seu grupo de recursos) - criar uma conta de armazenamento standard na mesma região que o Cofre
    - Segmentar a rede Virtual para a ativação pós-falha (e seu grupo de recursos)
    - Sub-rede de destino
    - Segmentar a rede Virtual para a ativação pós-falha de teste (e seu grupo de recursos)
    - Conjunto de disponibilidade (se necessário)
    - Grupo de segurança de rede de destino e o respetivo grupo de recursos
- Certifique-se de que tenha decidido nas propriedades de VM de destino
    - Nome da VM de destino
    - Tamanho da VM de destino no Azure (pode ser escolhidas com o Azure Migrate avaliação)
    - Endereço de IP privado da NIC principal a VM
- Transferir os scripts a partir de [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repositório no GitHub

### <a name="csv-input-file"></a>Ficheiro de entrada de CSV
Quando tiver todos os pré-requisitos foi concluídos, terá de criar um ficheiro CSV, que tem dados para cada máquina de origem que pretende migrar. A entrada CSV tem de ter uma linha de cabeçalho com os detalhes de entrada e de uma linha com detalhes para cada máquina que tem de ser migrada. Todos os scripts foram concebidos para funcionar no mesmo ficheiro CSV. Um modelo de CSV de exemplo está disponível na pasta de scripts para sua referência.

### <a name="script-execution"></a>Execução do script
Depois do CSV está pronto, pode executar os seguintes passos para efetuar a migração das VMs no local:

**Passo n. º** | **Nome do script** | **Descrição**
--- | --- | ---
1 | asr_startmigration.ps1 | Ativar a replicação para todas as VMs listadas no csv, o script cria uma saída CSV com os detalhes da tarefa para cada VM
2 | asr_replicationstatus.ps1 | Verificar o estado da replicação, o script cria um csv com o estado para cada VM
3 | asr_updateproperties.ps1 | Assim que as VMs são replicados/protegido, utilize este script para atualizar as propriedades de destino da VM (propriedades de computação e rede)
4 | asr_propertiescheck.ps1 | Verificar se as propriedades são corretamente atualizadas
5 | asr_testmigration.ps1 |  Iniciar ativação pós-falha de teste das VMs listadas no csv, o script cria uma saída CSV com os detalhes da tarefa para cada VM
6 | asr_cleanuptestmigration.ps1 | Depois de validar manualmente as VMs que foram com ativação pós-falha de teste, pode utilizar este script para limpar ativação pós-falha de teste VMs
7 | asr_migration.ps1 | Executar uma ativação pós-falha não planeada para as VMs listadas no csv, o script cria uma saída CSV com os detalhes da tarefa para cada VM. O script não será encerrado no local, VMs antes de acionar a ativação pós-falha, para obter consistência de aplicação, é recomendável que encerrado manualmente as VMs antes de executar o script.
8 | asr_completemigration.ps1 | Executar a operação de consolidação nas VMs e eliminar as entidades do Azure Site Recovery
9 | asr_postmigration.ps1 | Se pretender atribuir grupos de segurança de rede para ativação de pós-falha NICs, pode utilizar este script para fazer isso. Atribui um NSG a qualquer uma NIC na VM de destino.

## <a name="how-to-migrate-to-managed-disks"></a>Como migrar para discos geridos?
Por predefinição, o script, migra as VMs para os managed disks no Azure. Se a conta de armazenamento de destino fornecida é uma conta de armazenamento premium, o discos geridos premium são criados após migração. A conta de armazenamento de cache ainda pode ser uma conta standard. Se a conta de armazenamento de destino for uma conta de armazenamento standard, os discos standard são criados após migração. 

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sobre servidores de migrar para o Azure com o Azure Site Recovery
