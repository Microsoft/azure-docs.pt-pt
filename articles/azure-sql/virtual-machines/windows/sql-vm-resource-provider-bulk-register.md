---
title: Registar a granel máquinas virtuais SQL em Azure com o fornecedor de recursos SQL VM Microsoft Docs
description: Registar a granel SQL Server VMs com o fornecedor de recursos SQL VM para melhorar a sua gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b83a44db98907f505c7bf0d8302470cf3031a967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761265"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registar várias máquinas virtuais SQL em Azure com o fornecedor de recursos SQL VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registar as suas máquinas virtuais SQL Server (VMs) a granel em Azure com o fornecedor de recursos SQL VM utilizando o `Register-SqlVMs` cmdlet PowerShell.

Este artigo ensina-o a registar VMs SQL Server a granel. Em alternativa, pode registar [automaticamente todos os VMs do SqL Server](sql-vm-resource-provider-automatic-registration.md) ou [VMs individuais do SQL Server](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Descrição geral

O `Register-SqlVMs` cmdlet pode ser usado para registar todas as máquinas virtuais numa determinada lista de subscrições, grupos de recursos ou uma lista de máquinas virtuais específicas. O cmdlet registará as máquinas virtuais em modo de gestão _leve_ e, em seguida, gerará um [relatório e um ficheiro de registo](#output-description). 

O processo de registo não tem qualquer risco, não tem tempo de inatividade e não reiniciará o SQL Server ou a máquina virtual. 

Para obter mais informações sobre o fornecedor de recursos, consulte o [fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com o fornecedor de recursos, necessitará do seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/) que foi [registada com o fornecedor de recursos](sql-vm-resource-provider-register.md#register-subscription-with-rp) e contém máquinas virtuais SQL Server não registadas. 
- As credenciais do cliente utilizadas para registar as máquinas virtuais existem em qualquer uma das seguintes funções Azure: **Contribuinte máquina virtual,** **contribuinte**ou **Proprietário.** 
- A versão mais recente do [Az PowerShell.](/powershell/azure/new-azureps-module-az) 
- A versão mais recente de [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Introdução

Antes de prosseguir, deve primeiro criar uma cópia local do script, importá-lo como módulo PowerShell e ligar-se ao Azure. 

### <a name="create-the-script"></a>Criar o script

Para criar o script, copie o [script completo](#full-script) a partir do final deste artigo e guarde-o localmente como `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Importe o guião

Após a criação do script, pode importá-lo como módulo no terminal PowerShell. 

Abra um terminal administrativo powerShell e navegue até onde guardou o `RegisterSqlVMs.psm1` ficheiro. Em seguida, executar o seguinte cmdlet PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Ligar ao Azure

Utilize o seguinte cmdlet PowerShell para ligar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Registar todos os VMs numa lista de subscrições 

Utilize o seguinte cmdlet para registar todas as máquinas virtuais do SQL Server numa lista de subscrições:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Exemplo de saída: 

```
Number of subscriptions registration failed for 
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

## <a name="register-all-vms-in-a-single-subscription"></a>Registar todos os VMs numa única subscrição

Utilize o seguinte cmdlet para registar todas as máquinas virtuais sql Server numa única subscrição: 

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Registar todos os VMs em vários grupos de recursos

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

## <a name="register-all-vms-in-a-resource-group"></a>Registar todos os VMs num grupo de recursos

Utilize o seguinte cmdlet para registar todas as máquinas virtuais sql Server num único grupo de recursos: 

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

## <a name="register-specific-vms-in-a-single-resource-group"></a>Registar VMs específicos num único grupo de recursos

Utilize o seguinte cmdlet para registar máquinas virtuais específicas do SQL Server num único grupo de recursos:

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

## <a name="register-a-specific-vm"></a>Registar um VM específico

Utilize o seguinte cmdlet para registar uma máquina virtual específica do SQL Server: 

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

Tanto um relatório como um ficheiro de registo são gerados sempre que o `Register-SqlVMs` cmdlet é utilizado. 

### <a name="report"></a>Relatório

O relatório é gerado como um `.txt` ficheiro chamado onde a hora da hora é a hora em que o `RegisterSqlVMScriptReport<Timestamp>.txt` cmdlet foi iniciado. O relatório enumera os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Número de inscrições falhada porque não tem acesso ou credenciais estão incorretas | Isto fornece o número e a lista de subscrições que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no registo procurando o ID de subscrição. | 
| Número de assinaturas que não puderam ser experimentada por não estarem registadas no RP | Esta secção contém a contagem e a lista de subscrições que não foram registadas no fornecedor de recursos SQL VM. |
| Total de VMs encontrados | A contagem de máquinas virtuais que foram encontradas no âmbito dos parâmetros passados para o cmdlet. | 
| VMs já registados | A contagem de máquinas virtuais que foram ignoradas por já estarem registadas no fornecedor de recursos. |
| Número de VMs registado com sucesso | A contagem de máquinas virtuais que foram registadas com sucesso após a execução do cmdlet. Lista as máquinas virtuais registadas no `SubscriptionID, Resource Group, Virtual Machine` formato. | 
| Número de VMs não registou-se devido a erro | Contagem de máquinas virtuais que não se registaram devido a algum erro. Os detalhes do erro podem ser encontrados no ficheiro de registo. | 
| Número de VMs ignorados como o VM ou o agente de rajada em VM não está em execução | A contagem e a lista de máquinas virtuais que não podiam ser registadas como a máquina virtual ou o agente convidado na máquina virtual não estavam a funcionar. Estes podem ser novamente experimentados uma vez que a máquina virtual ou o agente convidado foi iniciado. Os detalhes podem ser encontrados no ficheiro de registo. |
| Número de VMs ignorados por não estarem a executar o SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas por não estarem a executar o SQL Server ou não serem uma máquina virtual do Windows. As máquinas virtuais estão listadas no `SubscriptionID, Resource Group, Virtual Machine` formato. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Registar 

Os erros são registados no ficheiro de registo denominado `VMsNotRegisteredDueToError<Timestamp>.log` , onde a hora de tempo é a hora em que o script começou. Se o erro estiver no nível de subscrição, o registo contém o ID de subscrição separado em vírgula e a mensagem de erro. Se o erro estiver com o registo da máquina virtual, o registo contém o ID de subscrição, nome do grupo de recursos, nome da máquina virtual, código de erro e mensagem separada por vírgulas. 

## <a name="remarks"></a>Observações

Quando registar VMs sql server com o fornecedor de recursos utilizando o script fornecido, considere o seguinte:

- O registo com o fornecedor de recursos requer um agente convidado em execução no SQL Server VM. As imagens do Windows Server 2008 não têm um agente convidado, pelo que estas máquinas virtuais falharão e devem ser registadas manualmente utilizando o [modo de gestão NoAgent](sql-vm-resource-provider-register.md#management-modes).
- Há uma lógica de reação incorporada para superar erros transparentes. Se a máquina virtual estiver registada com sucesso, então é uma operação rápida. No entanto, se o registo falhar, cada máquina virtual será novamente experimentada.  Como tal, deverá permitir um tempo significativo para concluir o processo de registo - embora o requisito de tempo real dependa do tipo e do número de erros. 

## <a name="full-script"></a>Script completo

Para obter o script completo no GitHub, consulte [O ML VMs de registo a granel com Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o guião completo e guarde-o como `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)
