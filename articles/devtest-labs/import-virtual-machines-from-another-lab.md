---
title: Importar máquinas virtuais de outro laboratório em Azure DevTest Labs
description: Este artigo descreve como importar máquinas virtuais de outro laboratório para o laboratório atual em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85475939"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importar máquinas virtuais de outro laboratório em Azure DevTest Labs
Este artigo fornece informações sobre como importar máquinas virtuais de outro laboratório para o seu laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão alguns cenários em que é preciso importar VMs de um laboratório para outro laboratório:

- Um indivíduo da equipa está a mudar-se para outro grupo dentro da empresa e quer levar o ambiente de trabalho do desenvolvedor para os DevTest Labs da nova equipa.
- O grupo atingiu uma [quota de nível de subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md) e quer dividir as equipas em algumas subscrições
- A empresa está a mudar-se para a Rota Expresso (ou outra nova topologia de rede) e a equipa quer mover as Máquinas Virtuais para usar esta nova infraestrutura

## <a name="solution-and-constraints"></a>Solução e constrangimentos
Esta funcionalidade permite-lhe importar VMs num laboratório (fonte) para outro laboratório (destino). Pode opcionalmente dar um novo nome para o destino VM no processo. O processo de importação inclui todas as dependências como discos, horários, definições de rede, e assim por diante.

O processo demora algum tempo e é impactado pelos seguintes fatores:

- Número/tamanho dos discos que estão ligados à máquina de origem (uma vez que é uma operação de cópia e não uma operação de movimento)
- Distância ao destino (por exemplo, região leste dos EUA para o Sudeste Asiático).

Uma vez concluído o processo, a máquina virtual de origem permanece fechada e a nova está a funcionar no laboratório de destino.

Há dois constrangimentos fundamentais a ter em conta quando planeia importar VMs de um laboratório para outro laboratório:

- As importações de máquinas virtuais entre subscrições e regiões são apoiadas, mas as subscrições devem ser associadas ao mesmo inquilino do Azure Ative Directory.
- As Máquinas Virtuais não devem estar num estado reivindicaível no laboratório de origem.
- És o dono do VM no laboratório de origem e dono do laboratório no laboratório de destino.
- Atualmente, esta funcionalidade é suportada apenas através da API Powershell e REST.

## <a name="use-powershell"></a>Utilizar o PowerShell
Descarregue ImportVirtualMachines.ps1 ficheiro do [GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) Pode usar o script para importar um único VM ou todos os VMs no laboratório de origem para o laboratório de destino.

### <a name="use-powershell-to-import-a-single-vm"></a>Use powerShell para importar um único VM
A execução deste script powershell requer a identificação da origem VM e do laboratório de destino, e opcionalmente fornecer um novo nome para usar para a máquina de destino:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Use o PowerShell para importar todos os VMs no laboratório de origem
Se a Máquina Virtual Fonte não for especificada, o script importa automaticamente todos os VMs nos Laboratórios DevTest.  Por exemplo:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Use HTTP REST para importar um VM
A chamada do REST é simples. Você dá informações suficientes para identificar os recursos de origem e destino. Lembre-se que a operação ocorre no recurso do laboratório de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Definir políticas para um laboratório](devtest-lab-set-lab-policy.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
