---
title: Automatizar a migração de máquinas de migração em Azure Migrate
description: Descreve como usar scripts para migrar um grande número de máquinas em Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: c354c1c9dfacfcb6bf84f1140b58deca60c1874e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86109846"
---
# <a name="scale-migration-of-vms"></a>Migração em escala de VMs 

Este artigo ajuda-o a entender como usar scripts para migrar um grande número de máquinas virtuais (VMs). Para escalar a migração, utiliza-se [a Recuperação do Sítio Azure.](../site-recovery/site-recovery-overview.md) 

Os scripts de recuperação do site estão disponíveis para o seu download no [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo no GitHub. Os scripts podem ser usados para migrar VMware, AWS, VMs GCP e servidores físicos para gerir discos em Azure. Também pode utilizar estes scripts para migrar Hiper-VMs se migrar os VMs como servidores físicos. Os scripts que alavancam o Poder de Recuperação do Site Azure estão documentados [aqui.](../site-recovery/vmware-azure-disaster-recovery-powershell.md)

## <a name="current-limitations"></a>Limitações atuais
- Suporte especificando o endereço IP estático apenas para o NIC primário do VM alvo
- Os scripts não tomam as entradas relacionadas com o Benefício Híbrido Azure, é necessário atualizar manualmente as propriedades do VM replicado no portal

## <a name="how-does-it-work"></a>Como funciona?

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de fazer os seguintes passos:
- Certifique-se de que o cofre de recuperação do site é criado na sua subscrição Azure
- Certifique-se de que o Servidor de Configuração e o Servidor de Processo estão instalados no ambiente de origem e que o cofre é capaz de descobrir o ambiente
- Certifique-se de que uma Política de Replicação é criada e associada ao Servidor de Configuração
- Certifique-se de que adicionou a conta de administração VM ao servidor config (que será usado para replicar os VMs nas instalações)
- Certifique-se de que os artefactos-alvo em Azure são criados
    - Grupo de Recursos-Alvo
    - Conta de Armazenamento Alvo (e seu Grupo de Recursos) - Criar uma conta de armazenamento premium se pretender migrar para discos geridos por prémios
    - Conta de Armazenamento de Cache (e seu Grupo de Recursos) - Criar uma conta de armazenamento padrão na mesma região que o cofre
    - Rede Virtual alvo para falha (e seu Grupo de Recursos)
    - Sub-rede alvo
    - Rede Virtual alvo para falha de teste (e seu Grupo de Recursos)
    - Conjunto de disponibilidade (se necessário)
    - Grupo de Segurança da Rede Alvo e seu Grupo de Recursos
- Certifique-se de que decidiu sobre as propriedades do VM alvo
    - Nome VM alvo
    - Tamanho do VM alvo em Azure (pode ser decidido usando a avaliação de Azure Migrate)
    - Endereço IP privado do NIC primário no VM
- Descarregue os scripts da [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo no GitHub

### <a name="csv-input-file"></a>Ficheiro de entrada CSV
Uma vez preenchidos todos os pré-requisitos, é necessário criar um ficheiro CSV, que tenha dados para cada máquina de origem que pretende migrar. O CSV de entrada deve ter uma linha de cabeçalho com os detalhes de entrada e uma linha com detalhes para cada máquina que precisa de ser migrada. Todos os scripts são projetados para trabalhar no mesmo ficheiro CSV. Um modelo de CSV de amostra está disponível na pasta scripts para a sua referência.

### <a name="script-execution"></a>Execução de scripts
Uma vez que o CSV esteja pronto, pode executar os seguintes passos para realizar a migração dos VMs no local:

**Passo #** | **Nome do guião** | **Descrição**
--- | --- | ---
1 | asr_startmigration.ps1 | Ativar a replicação de todos os VMs listados no csv, o script cria uma saída de CSV com os detalhes de trabalho para cada VM
2 | asr_replicationstatus.ps1 | Verifique o estado da replicação, o script cria um csv com o estado de cada VM
3 | asr_updateproperties.ps1 | Uma vez replicados/protegidos os VMs, utilize este script para atualizar as propriedades-alvo do VM (propriedades de Computação e Rede)
4 | asr_propertiescheck.ps1 | Verifique se as propriedades estão devidamente atualizadas
5 | asr_testmigration.ps1 |  Iniciar o teste de failover dos VMs listados no csv, o script cria uma saída de CSV com os detalhes do trabalho para cada VM
6 | asr_cleanuptestmigration.ps1 | Uma vez validado manualmente os VMs que foram testados falhados, pode usar este script para limpar os VMs de failover de teste
7 | asr_migration.ps1 | Execute uma falha não planeada para os VMs listados no csv, o script cria uma saída de CSV com os detalhes de trabalho para cada VM. O script não desliga os VMs nas instalações antes de desencadear o failover, para consistência da aplicação, recomenda-se que desligue manualmente os VMs antes de executar o script.
8 | asr_completemigration.ps1 | Efetuar a operação de empenhamento nos VMs e eliminar as entidades de Recuperação do Sítio Azure
9 | asr_postmigration.ps1 | Se planeia atribuir grupos de segurança de rede aos NICs pós-failover, pode usar este script para o fazer. Atribui um NSG a qualquer NIC no VM alvo.

## <a name="how-to-migrate-to-managed-disks"></a>Como migrar para discos geridos?
O script, por padrão, migra os VMs para discos geridos em Azure. Se a conta de armazenamento-alvo fornecida for uma conta de armazenamento premium, os discos geridos com prémios são criados após a migração. A conta de armazenamento de cache ainda pode ser uma conta padrão. Se a conta de armazenamento alvo for uma conta de armazenamento padrão, os discos padrão são criados após a migração. 

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](../site-recovery/migrate-tutorial-on-premises-azure.md) sobre a migração de servidores para Azure usando a recuperação do site Azure
