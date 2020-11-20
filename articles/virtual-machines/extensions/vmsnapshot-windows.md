---
title: Extensão do Windows snapshot VM para backup do Azure
description: Pegue a cópia de segurança consistente da aplicação da máquina virtual a partir do Azure Backup usando a extensão de instantâneo VM
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: 1d1342cffc6bc8743c34cb4c028c0790de91dc50
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965872"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Extensão do Windows snapshot VM para backup do Azure

O Azure Backup fornece suporte para apoiar cargas de trabalho desde as instalações até à nuvem e para apoiar recursos em nuvem até ao cofre dos Serviços de Recuperação. O Azure Backup utiliza a extensão de instantâneo VM para obter uma cópia de segurança consistente da máquina virtual Azure sem a necessidade de desligar o VM. A extensão VM Snapshot é publicada e suportada pela Microsoft como parte do serviço Azure Backup. O Azure Backup instalará a extensão como parte do primeiro post de backup programado que permite a cópia de segurança. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão VM Snapshot.

A extensão VMSnapshot aparece no portal Azure apenas para VMs não geridos.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo
Para obter uma lista de sistemas operativos suportados, consulte [os Sistemas Operativos suportados pela Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do instantâneo VM. A extensão requer o ID da tarefa - isto identifica o trabalho de backup que desencadeou instantâneo no VM, status blob uri - onde o estado da operação snapshot é escrito, hora de início programada do instantâneo, logs blob uri - onde são escritos registos correspondentes a tarefa snapshot, representação objstr de discos VM e meta-dados.  Uma vez que estas definições devem ser tratadas como dados sensíveis, devem ser armazenadas numa configuração de definição protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Note que estas definições são recomendadas para serem passadas do serviço de backup Azure apenas como parte do trabalho de backup.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
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
| apiVersion | 2015-06-15 | data |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458E+17 | string |
| região | pt-pt | string |
| objetoStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. No entanto, a forma recomendada de adicionar uma extensão de instantâneo VM a uma máquina virtual é permitindo a cópia de segurança na máquina virtual. Isto pode ser alcançado através de um modelo de Gestor de Recursos.  Um modelo de gestor de recursos de amostra que permite a cópia de segurança numa máquina virtual pode ser encontrado na [Galeria Azure Quick Start](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O Azure CLI pode ser utilizado para permitir a cópia de segurança numa máquina virtual. O post ativa a cópia de segurança, o primeiro trabalho de backup programado instalará a extensão de imagem Vm no VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e os seus significados

As informações de resolução de problemas podem ser encontradas no [guia de resolução de problemas de backup da Azure VM](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
