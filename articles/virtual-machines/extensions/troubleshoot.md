---
title: Falhas de extensão do Windows VM de resolução de problemas
description: Conheça as falhas de extensão do Azure Windows VM
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564803"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Falhas de extensão Azure Windows VM de resolução de problemas
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Estado de extensão de visualização
Os modelos do Gestor de Recursos Azure podem ser executados a partir do Azure PowerShell. Uma vez executado o modelo, o estado de extensão pode ser visualizado a partir do Azure Resource Explorer ou das ferramentas da linha de comando.

Segue-se um exemplo:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Segue-se o resultado do exemplo:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Falhas na extensão de resolução de problemas

### <a name="rerun-the-extension-on-the-vm"></a>Reencauma a extensão no VM
Se estiver a executar scripts no VM utilizando a Extensão de Script Personalizado, pode por vezes encontrar um erro em que o VM foi criado com sucesso, mas o script falhou. Nestas condições, a forma recomendada de recuperar deste erro é remover a extensão e voltar a repetir o modelo.
Nota: No futuro, esta funcionalidade seria melhorada para remover a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remova a extensão do Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Uma vez removida a extensão, o modelo pode ser re-executado para executar os scripts no VM.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Desencadear um novo GoalState para o VM
Pode notar que uma extensão não foi executada, ou que não está a ser executada devido a um "Gerador de Certificados CRP do Windows Azure" (esse certificado é utilizado para garantir o transporte das definições protegidas da extensão).
Este certificado será regenerado automaticamente reiniciando o Windows Guest Agent a partir de dentro da Máquina Virtual:
- Abra o Gestor de Tarefas
- Vá ao separador Detalhes
- Localizar o processo de WindowsAzureGuestAgent.exe
- Clique à direita e selecione "End Task". O processo será automaticamente reiniciado


Também pode desencadear um novo GoalState para o VM, executando uma "Reapply VM". A VM [Reapply](/rest/api/compute/virtualmachines/reapply) é uma API introduzida em 2020 para reaplicar o estado de um VM. Recomendamos fazê-lo numa altura em que pode tolerar um curto tempo de inatividade em VM. Embora a Reapply em si não cause um reboot VM, e a grande maioria das vezes chamando Reapply não reiniciará o VM, existe um risco muito pequeno de que alguma outra atualização pendente para o modelo VM seja aplicada quando a Reapply desencadeia um novo estado de objetivo, e que outra mudança pode exigir um reinício. 

Portal Azure:

No portal, selecione o VM e no painel esquerdo sob o **Support + resolução de problemas**, selecione **Reploy + recandidatar-se,** em seguida, selecione **Recandidatar-se**.


Azure PowerShell *(substitua o nome RG e o nome VM pelos seus valores)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(substitua o nome RG e o nome VM pelos seus valores)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Se um "VM Reapply" não funcionar, pode adicionar um novo Disco de Dados vazio ao VM do Portal de Gestão de Azure e, em seguida, removê-lo mais tarde uma vez que o certificado tenha sido adicionado de volta.