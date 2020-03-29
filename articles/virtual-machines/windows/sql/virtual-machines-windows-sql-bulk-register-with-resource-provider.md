---
title: Registo a granel máquinas virtuais SQL em Azure com o fornecedor de recursos SQL VM [ Microsoft Docs
description: Registo a granel VMs do Servidor SQL com o fornecedor de recursos SQL VM para melhorar a gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353891"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registo a granel máquinas virtuais SQL em Azure com o fornecedor de recursos SQL VM

Este artigo descreve como registar a sua máquina virtual SQL Server (VM) em Azure com o fornecedor de recursos SQL VM utilizando o `Register-SqlVMs` cmdlet PowerShell.

O `Register-SqlVMs` cmdlet pode ser usado para registar todas as máquinas virtuais numa determinada lista de subscrições, grupos de recursos ou uma lista de máquinas virtuais específicas. O cmdlet registará as máquinas virtuais em modo de gestão _leve_ e, em seguida, gerará tanto um relatório como [um ficheiro](#output-description)de registo . 

O processo de registo não tem risco, não tem tempo de paragem e não reiniciará o SQL Server ou a máquina virtual. 

Para obter mais informações sobre o fornecedor de recursos, consulte o fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu VM do Servidor SQL com o fornecedor de recursos, necessitará do seguinte: 

- Uma [subscrição Azure](https://azure.microsoft.com/free/) que foi [registada no fornecedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) e contém máquinas virtuais SQL Server não registadas. 
- As credenciais do cliente utilizadas para registar as máquinas virtuais existem em qualquer uma das seguintes funções RBAC: **Colaborador da Máquina Virtual,** **Colaborador**ou **Proprietário.** 
- A versão mais recente do [Az PowerShell.](/powershell/azure/new-azureps-module-az) 
- A versão mais recente de [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Introdução

Antes de prosseguir, primeiro deve criar uma cópia local do script, importá-lo como um módulo PowerShell e ligar-se ao Azure. 

### <a name="create-script"></a>Criar script

Para criar o script, copie o [script completo](#full-script) a `RegisterSqlVMs.psm1`partir do final deste artigo e guarde-o localmente como . 

### <a name="import-script"></a>Roteiro de importação

Assim que o script for criado, pode importá-lo como um módulo no terminal Powershell. 

Abra um terminal administrativo da PowerShell `RegisterSqlVMs.psm1` e navegue até onde guardou o ficheiro. Em seguida, executar o seguinte cmdlet PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Ligar ao Azure

Utilize o seguinte cmdlet PowerShell para ligar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Todos os VMs na lista de subscrições 

Utilize o seguinte cmdlet para registar todas as máquinas virtuais do SQL Server numa lista de subscrições:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Exemplo de saída: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Todos os VMs numa única subscrição

Utilize o seguinte cmdlet para registar todas as máquinas virtuais do SQL Server numa única subscrição: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Exemplo de saída:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Todos os VMs em vários grupos de recursos

Utilize o seguinte cmdlet para registar todas as máquinas virtuais do SQL Server em vários grupos de recursos dentro de uma única subscrição:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Exemplo de saída:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Todos os VMs em um grupo de recursos

Utilize o seguinte cmdlet para registar todas as máquinas virtuais do SQL Server num único grupo de recursos: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Exemplo de saída:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>VMs específicos em grupo de recursos únicos

Utilize o seguinte cmdlet para registar máquinas virtuais Específicas do SQL Server dentro de um único grupo de recursos:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Exemplo de saída:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>VM específico

Utilize o seguinte cmdlet para registar uma máquina virtual SQL Server específica: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Exemplo de saída:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Descrição da saída

Tanto um relatório como um ficheiro `Register-SqlVMs` de registo são gerados sempre que o cmdlet é utilizado. 

### <a name="report"></a>Relatório

O relatório é gerado `.txt` como `RegisterSqlVMScriptReport<Timestamp>.txt` um ficheiro chamado onde o carimbo de tempo é o momento em que o cmdlet foi iniciado. O relatório enumera os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Número de inscrições falhou porque não tem acesso ou credenciais estão incorretas | Isto fornece o número e lista de subscrições que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no registo procurando o ID de subscrição. | 
| Número de assinaturas que não puderam ser experimentadas por não estarem registadas no RP | Esta secção contém a contagem e a lista de subscrições que não foram registadas no fornecedor de recursos SQL VM. |
| Total de VMs encontrados | A contagem de máquinas virtuais que foram encontradas no âmbito dos parâmetros passou para o cmdlet. | 
| VMs já registados | A contagem de máquinas virtuais que foram ignoradas por já estarem registadas junto do fornecedor de recursos. |
| Número de VMs registados com sucesso | A contagem de máquinas virtuais que foram registadas com sucesso após a execução do cmdlet. Lista as máquinas virtuais `SubscriptionID, Resource Group, Virtual Machine`registadas no formato . | 
| Número de VMs não se registou devido a erro | Contagem de máquinas virtuais que não se registaram devido a algum erro. Os detalhes do erro podem ser encontrados no ficheiro de registo. | 
| Número de VMs ignorados como o VM ou o agente de rajadas em VM não está funcionando | Contar e lista de máquinas virtuais que não podiam ser registadas como a máquina virtual ou o agente convidado na máquina virtual não estavam a funcionar. Estes podem ser novamente experimentados uma vez que a máquina virtual ou agente convidado foi iniciado. Os detalhes podem ser encontrados no ficheiro de registo. |
| Número de VMs ignorados por não estarem a executar o Servidor SQL no Windows | Contagem de máquinas virtuais que foram ignoradas por não estarem a executar o SQL Server ou não serem uma máquina virtual do Windows. As máquinas virtuais estão `SubscriptionID, Resource Group, Virtual Machine`listadas no formato . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Registar 

Os erros são registados `VMsNotRegisteredDueToError<Timestamp>.log` no ficheiro de registo chamado onde a marca de tempo é o momento em que o script começou. Se o erro estiver ao nível da subscrição, o registo contém o Id de Subscrição separado da vírlém e a mensagem de erro. Se o erro estiver no registo da máquina virtual, o registo contém o ID de subscrição, o nome do grupo Derecurso, o nome da máquina virtual, o código de erro e a mensagem separados por vírgulas. 

## <a name="remarks"></a>Observações

Quando registar VMs do Servidor SQL com o fornecedor de recursos utilizando o script fornecido, considere o seguinte:

- O registo com o fornecedor de recursos requer um agente convidado a funcionar no VM do Servidor SQL. As imagens do Windows Server 2008 não têm um agente convidado, pelo que estas máquinas virtuais falharão e devem ser registadas manualmente utilizando o modo de [gestão NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- Há uma lógica de retry incorporada para superar erros transparentes. Se a máquina virtual for registada com sucesso, então é uma operação rápida. No entanto, se o registo falhar, cada máquina virtual será novamente experimentada.  Como tal, deve dar tempo significativo para concluir o processo de registo - embora o requisito de tempo real dependa do tipo e número de erros. 

## <a name="full-script"></a>Script completo

Para obter o script completo no GitHub, consulte [O Registo a granel VMs SQL com Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o script completo `RegisterSqLVMs.psm1`e guarde-o como .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
