---
title: Importar VMs a partir de outro laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como importar máquinas virtuais do laboratório de outro para o laboratório atual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125904"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importar VMs a partir de outro laboratório no Azure DevTest Labs
O serviço de Azure DevTest Labs melhora significativamente a gestão das máquinas virtuais (VMs) para desenvolvimento e atividades de teste. Permite-lhe mover uma VM a partir de um laboratório para outra como a equipe ou alteram os requisitos de infraestrutura. Seguem-se alguns cenários comuns em que poderá ter de fazer isso:

- Uma pessoa na equipe do move para outro grupo dentro da empresa e deseja levar desenvolvimento VMs para o laboratório do novo agrupamento.
- O grupo atingiu a quota do nível de assinatura e deseja dividir as equipes em várias subscrições.
- A empresa planeia mover para o Express Route (ou alguma outra topologia de rede novo) e a equipe quer mover VMs para utilizar esta nova infra-estrutura.

## <a name="solution-and-constraints"></a>Solução e restrições
O Azure DevTest Labs permite que o proprietário de um laboratório importar as VMs num laboratório de origem para um laboratório de destino. O proprietário de laboratório, opcionalmente, pode fornecer um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências como discos, agendas, as definições de rede, etc. O processo demorar algum tempo e é afetado pelo número/tamanho dos discos ligados à máquina de origem e a distância para o destino (por exemplo, a região E.U.A. leste a região do Sudeste asiático). Quando o processo de importação estiver concluído, a VM de origem continua a ser encerrado e o novo um está em execução no laboratório de destino.

Existem duas restrições de chave a ter em consideração ao planejar a importar VMs para outro laboratório:

- A importação de VMs entre subscrições e na regiões são suportadas, mas as subscrições têm de estar associadas ao mesmo inquilino do Azure Active Directory.
- As VMs não devem estar num Estado reclamáveis no laboratório de origem.

Além disso, para poder importar uma VM a partir de um laboratório para outro, terá de ser o proprietário da VM no laboratório de origem e proprietário do laboratório do laboratório de destino.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Passos para importar uma VM a partir de outro laboratório
Atualmente, pode importar uma VM a partir de um laboratório para outro apenas ao utilizar o Azure PowerShell e a REST API.

### <a name="use-powershell"></a>Utilizar o PowerShell
Transferir o script do PowerShell ImportVirtualMachines.ps1 a partir de ficheiros [repositório de Git de laboratório do DevTest do Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) sua unidade local.

#### <a name="import-a-single-vm"></a>Importar uma única VM
Execute o script de ImportVirtualMachines.ps1 para importar uma única VM a partir de um laboratório de origem para um laboratório de destino. Pode especificar um novo nome para a VM que está a ser copiado ao utilizar o paramer DestinationVirtualMachineName.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importar todas as VMs
Ao executar o script de ImportVirtualMachines.ps1, se não especificar uma VM no laboratório de origem, o script importa todas as VMs do laboratório de origem para o laboratório de destino.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Utilizar a API REST
Invocar a API de REST contra o laboratório de destino/destino e passar o laboratório de origem, uma subscrição e informações da VM como parâmetros, como mostrado no exemplo a seguir:

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre o redimensionamento de uma VM, consulte [redimensionar uma VM](devtest-lab-resize-vm.md).
- Para obter informações sobre a Reimplementar uma VM, consulte [Reimplementar uma VM](devtest-lab-redeploy-vm.md).
