---
title: Automatizar migração de máquinas de migração em Azure Migrate
description: Descreve como usar scripts para migrar um grande número de máquinas em Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74196366"
---
# <a name="scale-migration-of-vms"></a>Migração em escala de VMs 

Este artigo ajuda-o a entender como usar scripts para migrar um grande número de máquinas virtuais (VMs). Para escalar a migração, utiliza-se a [Recuperação do Site Azure.](../site-recovery/site-recovery-overview.md) 

Os scripts de Recuperação do Site estão disponíveis para o seu download no [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo no GitHub. Os scripts podem ser usados para migrar VMware, AWS, VMs GCP e servidores físicos para gerir discos em Azure. Também pode usar estes scripts para migrar VMs Hiper-V se migrar os VMs como servidores físicos. Os scripts que alavancam o Site Azure Recovery PowerShell estão documentados [aqui.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)

## <a name="current-limitations"></a>Limitações atuais
- Suporte especificando o endereço IP estático apenas para o NIC primário do VM alvo
- Os scripts não aceitam inputs relacionados com o Azure Hybrid Benefit, é necessário atualizar manualmente as propriedades do VM replicado no portal

## <a name="how-does-it-work"></a>Como funciona?

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de fazer os seguintes passos:
- Certifique-se de que o cofre de recuperação do site é criado na sua subscrição Azure
- Certifique-se de que o Servidor de Configuração e o Servidor de Processoestão instalados no ambiente de origem e o cofre é capaz de descobrir o ambiente
- Certifique-se de que uma Política de Replicação é criada e associada ao Servidor de Configuração
- Certifique-se de que adicionou a conta de administração VM ao servidor de config (que será usado para replicar os VMs no local)
- Certifique-se de que os artefactos-alvo em Azure são criados
    - Grupo de Recursos Alvo
    - Conta de Armazenamento Alvo (e seu Grupo de Recursos) - Crie uma conta de armazenamento premium se planeia migrar para discos geridos por prémios
    - Conta de Armazenamento cache (e seu Grupo de Recursos) - Crie uma conta de armazenamento padrão na mesma região que o cofre
    - Rede Virtual Alvo para failover (e seu Grupo de Recursos)
    - Subnet alvo
    - Rede Virtual Alvo para Falha de Teste (e seu Grupo de Recursos)
    - Conjunto de disponibilidade (se necessário)
    - Grupo de Segurança da Rede Alvo e seu Grupo de Recursos
- Certifique-se de que decidiu sobre as propriedades do alvo VM
    - Nome-alvo VM
    - Tamanho vm alvo em Azure (pode ser decidido usando avaliação de migração Azure)
    - Endereço IP privado do NIC primário no VM
- Descarregue os scripts de [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo no GitHub

### <a name="csv-input-file"></a>Arquivo de entrada CSV
Uma vez preenchidos todos os pré-requisitos, é necessário criar um ficheiro CSV, que tem dados para cada máquina de origem que pretende migrar. O CSV de entrada deve ter uma linha de cabeçalho com os detalhes da entrada e uma linha com detalhes para cada máquina que precisa de ser migrada. Todos os scripts são projetados para trabalhar no mesmo ficheiro CSV. Um modelo CSV de amostra está disponível na pasta de scripts para a sua referência.

### <a name="script-execution"></a>Execução do guião
Uma vez que o CSV esteja pronto, pode executar os seguintes passos para realizar a migração dos VMs no local:

**Passo #** | **Nome script** | **Descrição**
--- | --- | ---
1 | asr_startmigration.ps1 | Ativar a replicação para todos os VMs listados no csv, o script cria uma saída CSV com os detalhes de trabalho para cada VM
2 | asr_replicationstatus.ps1 | Verifique o estado da replicação, o script cria um csv com o estatuto de cada VM
3 | asr_updateproperties.ps1 | Uma vez que os VMs são replicados/protegidos, use este script para atualizar as propriedades-alvo do VM (propriedades computacionais e de rede)
4 | asr_propertiescheck.ps1 | Verifique se as propriedades estão devidamente atualizadas
5 | asr_testmigration.ps1 |  Inicie a falha do teste dos VMs listados no csv, o script cria uma saída de CSV com os detalhes de trabalho para cada VM
6 | asr_cleanuptestmigration.ps1 | Uma vez validado manualmente os VMs que foram testados falhados, pode usar este script para limpar os VMs de falha do teste
7 | asr_migration.ps1 | Execute uma falha não planeada para os VMs listados no csv, o script cria uma saída CSV com os detalhes de trabalho para cada VM. O script não desliga os VMs no local antes de desencadear a falha, para a consistência da aplicação, recomenda-se que desligue manualmente os VMs antes de executar o script.
8 | asr_completemigration.ps1 | Realizar a operação de compromisso nos VMs e eliminar as entidades de recuperação do site Azure
9 | asr_postmigration.ps1 | Se planeia atribuir grupos de segurança de rede aos NICs pós-falha, pode utilizar este script para o fazer. Atribui um NSG a qualquer NIC no Vi.

## <a name="how-to-migrate-to-managed-disks"></a>Como migrar para discos geridos?
O script, por padrão, migra os VMs para os discos geridos em Azure. Se a conta de armazenamento-alvo fornecida for uma conta de armazenamento premium, os discos geridos por prémios são criados após a migração. A conta de armazenamento de cache ainda pode ser uma conta padrão. Se a conta de armazenamento alvo for uma conta de armazenamento padrão, os discos padrão são criados após a migração. 

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sobre servidores migratórios para Azure usando a Recuperação do Site Azure
