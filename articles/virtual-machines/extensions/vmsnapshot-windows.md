---
title: Extensão do Windows de instantâneo de VM para o Azure Backup | Documentos da Microsoft
description: Efetuar cópia de segurança consistente do aplicativo da máquina virtual do Azure Backup com a extensão de instantâneo VM
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 0392a187bf40e1fe35053b493733c7e89aa6969e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783130"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Extensão do Windows de instantâneo de VM para o Azure Backup

Cópia de segurança do Azure fornece suporte cópias de segurança de cargas de trabalho no local para a nuvem e de cópia de segurança de recursos na cloud para cofre dos serviços de recuperação. Cópia de segurança do Azure utiliza a extensão de instantâneo VM para efetuar uma cópia de segurança consistente de aplicação de máquina virtual do Azure, sem a necessidade para encerrar a VM. Extensão de instantâneo da VM é publicado e suportado pela Microsoft como parte do serviço de cópia de segurança do Azure. Cópia de segurança do Azure irá instalar a extensão como parte da primeira agendada cópia de segurança acionada postagem ativar cópia de segurança. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de instantâneo da VM.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo
Para obter uma lista dos sistemas operativos suportados, consulte [sistemas operativos suportados pelo Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Acesso à Internet

Extensão de instantâneo da VM requer que a máquina virtual de destino está ligada à internet quando Vamos dar uma cópia de segurança da máquina virtual.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de instantâneo VM. A extensão necessita do ID de tarefa - identifica a tarefa cópia de segurança que acionou de instantâneos na VM, agendada de uri de blob de Estado - onde o estado da operação de instantâneo é escrito, a hora de início do instantâneo, uri - onde os registos correspondentes a instantâneo a tarefa de blob de registos são escritos, objstr-representação de discos VM e metadados.  Uma vez que estas definições devem ser tratadas como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Tenha em atenção que estas definições são recomendadas a serem passados do serviço de cópia de segurança do Azure apenas como parte da tarefa de cópia de segurança.

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

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458E + 17 | string |
| região | pt-PT | string |
| objectStr | Codificação de matriz de uri de sas-"blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc201652903941.vhd? sv = 2014-02-14 & sr = b & sig = % De TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA 3D & st = 2017-11-09T14% 3A23% 3A28Z & zar = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd? sv = 2014-02-14 & sr = b & sig = 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % 3D & st = 2017-11-09T14% 3A23% 3A28Z & zar = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna8461.blob.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? sv = 2014-02-14 & sr = b & sig = % De X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4 3D & st = 2017-11-09T14% 3A23% 3A28Z & zar = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160701-163922.vhd? sv = 2014-02-14 & sr = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % 3D & st = 2017-11-09T14% 3A23% 3A28Z & zar = 2017-11-09T17% 3A38% 3A28Z & sp = rw "," https:\/ \/ sopattna5365.blob.Core.Windows.NET\/vhds\/vmwin1404ltsc-20170705-124311.vhd? sv = 2014-02-14 & sr = b & sig = ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0% 3D & st = 2017-11-09T14% 3A23% 3A28Z & Se = 2017-11-09T17% 3A38% 3A28Z & sp = rw "] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Implementação de modelo

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. No entanto, a forma recomendada de adicionar uma extensão de instantâneo VM a uma máquina virtual é ao ativar a cópia de segurança na máquina virtual. Isso pode ser alcançado através de um modelo do Resource Manager.  Um modelo do Resource Manager de exemplo que permite a cópia de segurança numa máquina virtual pode ser encontrado no [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para ativar cópia de segurança numa máquina virtual. Após ativar cópia de segurança, a primeira tarefa de cópia de segurança agendada instalará a extensão de instantâneo de Vm na VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

Informações de resolução de problemas pode ser encontrada no [guia de resolução de problemas de cópia de segurança da VM do Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
