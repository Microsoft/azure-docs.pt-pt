---
title: Importar máquinas virtuais a partir de outro laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como importar máquinas virtuais do laboratório de outro para o laboratório atual.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 8b2eee0bfd32b58cd751f8bf70aff1d4f460a353
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340143"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importar máquinas virtuais a partir de outro laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como importar máquinas virtuais do laboratório de outro para o laboratório. 

## <a name="scenarios"></a>Cenários
Seguem-se alguns cenários em que precisa importar VMs a partir de um laboratório para outro laboratório: 

- Uma pessoa da Equipe está a mudar para outro grupo dentro da empresa e precisa pegar a área de trabalho de programador para DevTest Labs o novo agrupamento.
- O grupo de atingir um [quota ao nível de subscrição](../azure-subscription-service-limits.md) e deseja dividir as equipes em algumas subscrições
- A empresa esteja migrando para o Express Route (ou alguma outra topologia de rede novo) e a equipe deseja mover as máquinas virtuais para utilizar esta nova infraestrutura

## <a name="solution-and-constraints"></a>Solução e restrições
Esta funcionalidade permite-lhe importar VMs num laboratório (origem) para outro laboratório (destino). Opcionalmente, pode dar um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências, como discos, agendas, as definições de rede e assim por diante.

O processo demorar algum tempo e é afetado pelos seguintes fatores:

- Número/tamanho dos discos que estão anexados à máquina de origem (uma vez que é uma operação de cópia e não uma operação de movimentação) 
- Distância até o destino (por exemplo, a região E.U.A. leste a sudeste asiático).  

Depois do processo estiver concluído, a Máquina Virtual de origem continua a ser encerrado e o novo um está em execução no laboratório de destino.

Existem duas restrições de chave a ter em consideração ao planejar a importar VMs a partir de um laboratório no laboratório outro:

- Importações de máquina virtual entre subscrições e entre regiões são suportadas, mas as subscrições têm de estar associadas ao mesmo inquilino do Azure Active Directory.
- Máquinas virtuais não pode estar num Estado reclamáveis no laboratório de origem.
- É o proprietário da VM no laboratório de origem e proprietário do laboratório do laboratório de destino.
- Atualmente, esta funcionalidade é suportada apenas através do Powershell e REST API.

## <a name="use-powershell"></a>Utilizar o PowerShell
Transferir ficheiro ImportVirtualMachines.ps1 a partir da [GitHub](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/ImportVirtualMachines/ImportVirtualMachines.ps1). Pode utilizar o script para importar uma VM única ou todas as VMs do laboratório de origem para o laboratório de destino. 

### <a name="use-powershell-to-import-a-single-vm"></a>Utilize o PowerShell para importar uma única VM
Executar este script do powershell requer a identificação da VM de origem e o laboratório de destino e, opcionalmente, fornecendo um novo nome a utilizar para a máquina de destino:

```powershell 
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Utilize o PowerShell para importar todas as VMs do laboratório de origem
Se a Máquina Virtual de origem não for especificado, o script importa automaticamente todas as VMs nos laboratórios DevTest.  Por exemplo:
 
```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Utilizar o REST de HTTP para importar uma VM
A chamada REST é simples. Fornecer informações suficientes para identificar os recursos de origem e de destino. Lembre-se de que a operação ocorre no recurso de laboratório de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
