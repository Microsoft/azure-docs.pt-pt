---
title: Extensão VM Snapshot Linux para Backup Azure
description: Tome a aplicação de backup consistente da máquina virtual a partir de Backup Azure utilizando extensão de instantâneo VM
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: d0ad54c19749d670f9ab753e1e6d8eb130475ffc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415120"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Extensão VM Snapshot Linux para Backup Azure



A Azure Backup fornece suporte para o backup de cargas de trabalho desde as instalações até à nuvem e apoio aos recursos da nuvem até ao cofre dos Serviços de Recuperação. O Azure Backup utiliza uma extensão de instantâneo VM para obter uma cópia de segurança consistente da máquina virtual Azure sem a necessidade de desligar o VM. A extensão VM Snapshot Linux é publicada e suportada pela Microsoft como parte do serviço de backup Do Azure. O Azure Backup instalará a extensão como parte do primeiro poste de backup acionado programado que permite a cópia de segurança. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão VM Snapshot.

A extensão VMSnapshot aparece no portal Azure apenas para VMs não geridos.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo
Para obter uma lista de sistemas operativos suportados, consulte os [Sistemas Operativos suportados pela Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão instantânea VM. A extensão requer o ID de tarefa - isto identifica o trabalho de backup que desencadeou instantâneo no VM, status blob uri - onde o estado da operação instantânea é escrito, hora de início programada do instantâneo, logs blob uri - onde troncos correspondentes à tarefa instantânea são escritas, objstr-representação de discos VM e meta dados.  Uma vez que estas definições devem ser tratadas como dados sensíveis, devem ser armazenadas numa configuração de definição protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Por favor, note que estas definições são recomendadas para serem passadas do serviço de backup Azure apenas como parte do trabalho de backup.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| comandoStartTimeUTCTicks | 6.36458E+17 | string |
| região | pt-pt | string |
| objetoStr | Codificação de sas uri array- "blobSASUri":\/\/["https: sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig= 5S0A6YDWvVwqPAkzWVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=20 117-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3e e ee ee\/v Mubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYv QKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/\/\/sopattna5365.blob.core.windows.net vhds vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu14 04ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. No entanto, a forma recomendada de adicionar uma extensão de instantâneo VM a uma máquina virtual é permitindo a cópia de segurança na máquina virtual. Isto pode ser conseguido através de um modelo de Gestor de Recursos.  Um modelo de Gestor de Recursos de amostra que permite a cópia de segurança numa máquina virtual pode ser encontrado na [Galeria De Arranque Rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser utilizado para ativar a cópia de segurança numa máquina virtual. O post ativar a cópia de segurança, o primeiro trabalho de backup programado instalará a extensão de instantâneo Vm no VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

Informações de resolução de problemas podem ser encontradas no guia de resolução de problemas de [backup Azure VM](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
