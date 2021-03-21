---
title: Automatizar migrações de VMware sem agente em Azure Migrate
description: Descreve como usar scripts para migrar um grande número de VMware VMs em Azure Migrate
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754271"
---
# <a name="scale-migration-of-vmware-vms"></a>Migração em escala de VMware VMs 

Este artigo ajuda-o a entender como usar scripts para migrar um grande número de máquinas virtuais VMware (VMs) usando o método sem agente. Para escalar as migrações, utiliza-se [o módulo Azure Migrate PowerShell](./tutorial-migrate-vmware-powershell.md). 

Os scripts de automação de migração Azure Migrate VMware estão disponíveis para download no [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) repo no GitHub. Os scripts podem ser usados para migrar VMware VMs para Azure usando o método de migração sem agente. Os comandos Azure Migrate PowerShell utilizados nestes scripts estão documentados [aqui.](./tutorial-migrate-vmware-powershell.md)

## <a name="current-limitations"></a>Limitações atuais
- Estes scripts suportam a migração de VMware VMs com todos os discos. Pode atualizar os scripts se quiser replicar seletivamente os discos ligados a um VMware VM. 
- Os scripts apoiam o uso de recomendações de avaliação. Se as recomendações de avaliação não forem utilizadas, todos os discos ligados ao VMware VM são migrados para o mesmo tipo de disco gerido (Standard ou Premium). Pode atualizar os scripts se quiser utilizar vários tipos de discos geridos com o mesmo VM

## <a name="prerequisites"></a>Pré-requisitos

- [Complete o tutorial de descoberta](tutorial-discover-vmware.md) para preparar Azure e VMware para migração.
- Recomendamos que complete o segundo tutorial para [avaliar vMware VMs](./tutorial-assess-vmware-azure-vm.md) antes de migrar para Azure.
- Tem o módulo Azure `Az` PowerShell. Se precisar de instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Instalar módulo Azure Migrate PowerShell

O módulo Azure Migrate PowerShell está disponível na pré-visualização. Terá de instalar o módulo PowerShell utilizando o seguinte comando. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>Ficheiro de entrada CSV
Uma vez preenchidos todos os pré-requisitos, precisa de criar um ficheiro CSV que tenha dados para cada VM de origem que pretende migrar. Todos os scripts são projetados para trabalhar no mesmo ficheiro CSV. Um modelo de CSV de amostra está disponível na pasta scripts para a sua referência. O ficheiro csv é configurável para que possa utilizar recomendações de avaliação e até mesmo especificar se determinadas operações não devem ser desencadeadas para um determinado VM. 

> [!NOTE]
> O mesmo ficheiro csv pode ser usado para migrar VMs em vários projetos Azure Migrate.

### <a name="csv-file-schema"></a>Esquema de ficheiros CSV

**Cabeçalho da Coluna** | **Descrição**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Fornecer ID de subscrição do projeto Azure Migrate.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Forneça o nome do grupo de recursos Azure Migrate.
AZMIGRATEPROJECT_NAME | Forneça o nome do projeto Azure Migrate na medida em que pretende migrar servidores. 
SOURCE_MACHINE_NAME | Forneça o nome amigável (nome de exibição) para o VM descoberto no projeto Azure Migrate.
AZMIGRATEASSESSMENT_NAME | Fornecer o nome da avaliação que precisa de ser alavancada para a migração.
AZMIGRATEGROUP_NAME | Fornecer o nome do grupo que foi usado para a avaliação de Azure Migrate.
TARGET_RESOURCE_GROUP_NAME | Fornecer o nome do grupo de recursos Azure para o que o VM precisa de ser migrado para.
TARGET_VNET_NAME| Forneça o nome da Rede Virtual Azure que o VM migrado deve utilizar.
TARGET_SUBNET_NAME | Fornecer o nome da sub-rede na rede virtual alvo que o VM migrado deve usar. Se ficar em branco, a sub-rede "predefinido" será utilizada.
TARGET_MACHINE_NAME | Forneça o nome que o VM migrado deve usar em Azure. Se ficar em branco, o nome da máquina de origem será utilizado.  
TARGET_MACHINE_SIZE | Forneça ao SKU que o VM deve utilizar em Azure. Para migrar um VM para D2_v2 VM em Azure, especifique o valor neste campo como "Standard_D2_v2". Se utilizar uma avaliação, então este valor será derivado com base na recomendação de avaliação.
LICENSE_TYPE | Especifique se pretende utilizar o Benefício Híbrido Azure para VMs do Servidor windows. Utilize o valor "WindowsServer" para aproveitar o Benefício Híbrido Azure. Caso contrário, deixe em branco ou utilize "NoLicenseType".
OS_DISK_ID | Forneça o ID do disco de so para que o VM seja migrado. O ID do disco a ser usado é a propriedade de identificador único (UUID) para o disco recuperado usando o Get-AzMigrateServer cmdlet. O script utilizará o primeiro disco do VM como o disco SO no caso de não ser fornecido qualquer valor.
TARGET_DISKTYPE | Forneça o tipo de disco a utilizar para todos os discos do VM em Azure. Utilize 'Premium_LRS' para discos geridos por prémios, 'StandardSSD_LRS' para discos SSD padrão e "Standard_LRS" para utilizar discos HDD padrão. Se optar por utilizar uma avaliação, o script priorizará a utilização de tipos de disco recomendados para cada disco do VM. Se não utilizar a avaliação ou especificar qualquer valor, o script utilizará discos HDD padrão por predefinição.    
AVAILABILITYZONE_NUMBER | Especifique o número da zona de disponibilidade a utilizar para o VM migrado. Pode deixar este espaço em branco no caso de não querer utilizar zonas de disponibilidade. 
AVAILABILITYSET_NAME | Especificar o nome do conjunto de disponibilidade a utilizar para o VM migrado. Pode deixar este espaço em branco no caso de não querer utilizar o conjunto de disponibilidade.
TURNOFF_SOURCESERVER | Especifique 'Y' se quiser desligar a fonte VM no momento da migração. Use 'N' de outra forma. Se ficar em branco, então o script assume o valor como 'N'.
TESTMIGRATE_VNET_NAME | Especificar o nome da rede virtual a utilizar para a migração de testes.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Se pretender atualizar o grupo de recursos a utilizar pelo VM migrado em Azure, especifique o nome do grupo de recursos Azure, caso contrário deixe em branco. 
UPDATED_TARGET_VNET_NAME | Se pretender atualizar a Rede Virtual para ser utilizada pelo VM migrado em Azure, especifique o nome da Rede Virtual Azure, caso contrário deixe em branco.
UPDATED_TARGET_MACHINE_NAME | Se pretender atualizar o nome a utilizar pelo VM migrado em Azure, especifique o novo nome a utilizar, caso contrário deixe em branco.
UPDATED_TARGET_MACHINE_SIZE | Se pretender atualizar o SKU para ser utilizado pelo VM migrado em Azure, especifique o novo SKU a ser utilizado, caso contrário deixe em branco.
UPDATED_AVAILABILITYZONE_NUMBER | Se pretender atualizar a zona de disponibilidade a utilizar pelo VM migrado em Azure, especifique a nova zona de disponibilidade a utilizar, caso contrário deixe em branco.
UPDATED_AVAILABILITYSET_NAME | Se pretender atualizar o conjunto de disponibilidade a utilizar pelo VM migrado em Azure, especifique o novo conjunto de disponibilidade a utilizar, caso contrário deixe em branco.
UPDATE_NIC1_ID | Especifique o ID do NIC para ser atualizado. Se deixado em branco, então o script assume o valor de ser o primeiro NIC do VM descoberto. Se não quiser atualizar o NIC do VM, deixe todos os campos que contenham o nome NIC em branco. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Especifique o valor a utilizar para este NIC. Utilize "Primário", "Secundário" ou "Não Chamado" para especificar se este NIC deve ser o principal, secundário ou não deve ser criado no VM migrado. Apenas um NIC pode ser especificado como o NIC primário para o VM. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC1_SUBNET_NAME | Especifique o nome da sub-rede a utilizar para o NIC no VM migrado. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC1_IP | Especifique o endereço IPv4 para ser utilizado pelo NIC no VM migrado se quiser utilizar IP estático. Utilize "auto" se quiser atribuir automaticamente o IP. Deixe em branco se não quiser atualizar.
UPDATE_NIC2_ID | Especifique o ID do NIC para ser atualizado. Se deixado em branco, então o script assume o valor de ser o segundo NIC do VM descoberto. Se não quiser atualizar o NIC do VM, deixe todos os campos que contenham o nome NIC em branco.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Especifique o valor a utilizar para este NIC. Utilize "Primário", "Secundário" ou "Não Chamado" para especificar se este NIC deve ser o principal, secundário ou não deve ser criado no VM migrado. Apenas um NIC pode ser especificado como o NIC primário para o VM. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC2_SUBNET_NAME | Especifique o nome da sub-rede a utilizar para o NIC no VM migrado. Deixe em branco se não quiser atualizar.
UPDATED_TARGET_NIC2_IP | Especifique o endereço IPv4 para ser utilizado pelo NIC no VM migrado se quiser utilizar IP estático. Utilize "auto" se quiser atribuir automaticamente o IP. Deixe em branco se não quiser atualizar.
OK_TO_UPDATE | Utilize 'Y' para indicar se as propriedades VM precisam de ser atualizadas quando executar o AzMigrate_UpdateMachineProperties script. Utilize 'N' ou deixe em branco de outra forma.
OK_TO_MIGRATE | Utilize 'Y' para indicar se o VM deve ser migrado quando executar o AzMigrate_StartMigration script. Utilize 'N' ou deixe em branco se não quiser migrar o VM. 
OK_TO_USE_ASSESSMENT | Utilize 'Y' para indicar se o VM deve começar a replicação usando recomendações de avaliação quando executar o AzMigrate_StartReplication script. Isto irá sobrepor-se aos valores TARGET_MACHINE_SIZE e TARGET_DISKTYPE no ficheiro CSV. Use 'N' ou deixe em branco se não quiser usar recomendações de avaliação.
OK_TO_TESTMIGRATE | Utilize 'Y' para indicar se o VM deve ser testado migrado quando executar o AzMigrate_StartTestMigration script. Utilize 'N' ou deixe em branco se não quiser testar migrar o VM. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Utilize 'Y' para indicar se o estado de replicação do VM deve ser atualizado quando executar o AzMigrate_ReplicationStatus script. Utilize 'N' ou deixe em branco se não quiser atualizar o estado de replicação.
OK_TO_CLEANUP | Utilize 'Y' para indicar se a replicação do VM deve ser limpa quando executar o AzMigrate_StopReplication script. Utilize 'N' ou deixe em branco de outra forma.
OK_TO_TESTMIGRATE_CLEANUP | Utilize 'Y' para indicar se a migração do teste para o VM deve ser limpa quando executar o AzMigrate_CleanUpTestMigration script. Utilize 'N' ou deixe em branco de outra forma.


## <a name="script-execution"></a>Execução de scripts

Uma vez que o CSV esteja pronto, pode executar os seguintes passos para migrar os seus VMS VMware no local.

**Passo #** | **Nome do guião** | **Descrição**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Ativar a replicação de todos os VMs listados no csv, o script cria uma saída de CSV e um ficheiro de registo para resolução de problemas.
2 | AzMigrate_ReplicationStatus.ps1 | Verifique o estado da replicação, o script cria uma saída csv com o estado de cada VM e um ficheiro de registo para resolução de problemas.
3 | AzMigrate_UpdateMachineProperties.ps1 | Uma vez concluída a replicação inicial, utilize este script para atualizar as propriedades-alvo das propriedades VM (Compute e Network). O script cria uma saída de CSV com os detalhes do trabalho para cada VM.
4 | AzMigrate_StartTestMigration.ps1 |  Inicie o teste de failover para todos os VMs listados no csv configurados para a migração de testes. O script cria uma saída de CSV com os detalhes do trabalho para cada VM.
5 | AzMigrate_CleanUpTestMigration.ps1 | Uma vez validado manualmente os VMs que foram testados falhados, utilize este script para limpar os VMs de failover de teste para todos os VMs listados no csv configurados para a limpeza da migração de testes. O script cria uma saída de CSV com os detalhes do trabalho para cada VM.
6 | AzMigrate_StartMigration.ps1 | Inicie a migração para todos os VMs listados no csv que estão configurados para migração. O script cria uma saída de CSV com os detalhes do trabalho para cada VM.
7 | AzMigrate_StopReplication.ps1 | Para a replicação para o VM depois de ter sido migrada com sucesso ou se pretende cancelar a replicação por outras razões. O script cria uma saída de CSV com os detalhes do trabalho para cada VM.


Os seguintes scripts são invocados por outros scripts para todas as operações da Azure Migrate, como permitir a replicação, iniciar a migração de testes, atualizar propriedades VM e assim por diante. Certifique-se de que todos os scripts estão presentes na mesma pasta/caminho. 

**Passo #** | **Nome do guião** | **Descrição**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Script comum contendo funções para recuperar propriedades de avaliação (através da API), vi os VMs descobertos e replicando VMs. 
2 | AzMigrate_CSV_Processor.ps1 | Script comum contendo funções usadas para operações de ficheiros csv, incluindo carregamento, leitura e impressão para registos. 
3 | AzMigrate_Logger.ps1 | Script comum invocado para gerar o ficheiro de registo para operações de automação Azure Migrate. O ficheiro de registo será do formato log.Scriptname.Datetime.txt.

Além do acima, a pasta também contém AzMigrate_Template.ps1 que contém a estrutura de esqueleto para construir scripts personalizados para diferentes operações de Azure Migrate. 

### <a name="script-execution-syntax"></a>Sintaxe de execução do script

Uma vez descarregados os scripts, os scripts podem ser executados da seguinte forma.

Se pretender executar o script para iniciar a replicação de VMs utilizando o ficheiro Input.csv, utilize a seguinte sintaxe. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Para saber mais sobre a utilização do Azure PowerShell para migrar VMware VMs com Azure Migrate, siga o [tutorial](./tutorial-migrate-vmware-powershell.md).