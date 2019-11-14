---
title: Extensão do Linux de instantâneo de VM para o backup do Azure
description: Fazer backup consistente com o aplicativo da máquina virtual do backup do Azure usando a extensão de instantâneo de VM
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 186468119fb5b630b56a91b38026f202b98630d6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072915"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Extensão do Linux de instantâneo de VM para o backup do Azure



O backup do Azure fornece suporte para o backup de cargas de trabalho do local para a nuvem e o backup de recursos de nuvem para o cofre dos serviços de recuperação. O backup do Azure usa a extensão de instantâneo de VM para obter um backup consistente com o aplicativo da máquina virtual do Azure sem a necessidade de desligar a VM. A extensão de instantâneo de VM Linux é publicada e tem suporte da Microsoft como parte do serviço de backup do Azure. O backup do Azure instalará a extensão como parte do primeiro backup programado iniciado após a habilitação do backup. Este documento detalha as plataformas com suporte, as configurações e as opções de implantação para a extensão de instantâneo da VM.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo
Para obter uma lista de sistemas operacionais com suporte, consulte [sistemas operacionais com suporte do backup do Azure](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de instantâneo de VM requer que a máquina virtual de destino esteja conectada à Internet quando pegamos um backup da máquina virtual.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão de instantâneo da VM. A extensão requer a ID da tarefa – identifica o trabalho de backup que disparou o instantâneo na VM, status URI de blob-onde o status da operação de instantâneo é gravado, hora de início agendada do instantâneo, log URI de blob-onde os logs correspondentes à tarefa de instantâneo são gravados, objstr de discos de VM e metadados.  Como essas configurações devem ser tratadas como dados confidenciais, elas devem ser armazenadas em uma configuração protegida. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Observe que essas configurações são recomendadas para serem passadas do serviço de backup do Azure somente como parte do trabalho de backup.

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
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458 e + 17 | string |
| localidade | pt-pt | string |
| objectStr | Codificação da matriz de URI de SAS-"blobSASUri": ["https:\/\/\/VHDs\/vmubuntu1404ltsc201652903941. vhd? VA = 2014-02-14 & Sr = b & SIG = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW", "https:\/\/sopattna8461.blob.core.windows.net\/VHDs\/vmubuntu1404ltsc-20160629-122418. vhd? VA = 2014-02-14 & Sr = b & SIG = 5S0A6YDWvVwqPAkzWXVy% 2BS% 2FqMwzFMbamT5upwx05v8Q% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541. vhd? VA = 2014-02-14 & Sr = b & SIG = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/VHDs\/vmubuntu1404ltsc-20160701-163922. vhd? VA = 2014-02-14 & Sr = b & SIG = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r% 2BC% 2BNIAork% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/VHDs\/vmubuntu1404ltsc-20170705-124311. vhd? VA = 2014-02-14 & Sr = b & SIG = ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl% 2FQ9rs0% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. No entanto, a maneira recomendada de adicionar uma extensão de instantâneo de VM a uma máquina virtual é habilitar o backup na máquina virtual. Isso pode ser obtido por meio de um modelo do Resource Manager.  Um modelo do Resource Manager de exemplo que permite o backup em uma máquina virtual pode ser encontrado na [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O CLI do Azure pode ser usado para habilitar o backup em uma máquina virtual. Pós-habilitar backup, o primeiro trabalho de backup agendado instalará a extensão de instantâneo da VM na VM.

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
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

As informações de solução de problemas podem ser encontradas no [Guia de solução de problemas de backup da VM do Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
