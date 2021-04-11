---
title: Registar vários SQL VMs em Azure com a extensão sql IaaS Agent
description: Registar A granel SQL Server VMs com a extensão SQL IaaS Agent para melhorar a sua gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ebf835cad79f8c011be2fec91f6f4644ecd0941f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284129"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Registar vários SQL VMs em Azure com a extensão sql IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registar as suas máquinas virtuais SQL Server (VMs) a granel em Azure com a [extensão SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) utilizando o `Register-SqlVMs` cmdlet Azure PowerShell. 


Este artigo ensina-o a registar VMs SQL Server manualmente a granel. Em alternativa, pode registar [automaticamente todos os VMs do SqL Server](sql-agent-extension-automatic-registration-all-vms.md) ou [vMs do servidor SQL individual manualmente](sql-agent-extension-manually-register-single-vm.md). 

## <a name="overview"></a>Descrição Geral

O `Register-SqlVMs` cmdlet pode ser usado para registar todas as máquinas virtuais numa determinada lista de subscrições, grupos de recursos ou uma lista de máquinas virtuais específicas. O cmdlet registará as máquinas virtuais no [modo de gestão lightweight_](sql-server-iaas-agent-extension-automate-management.md#management-modes)e, em seguida, gerará um relatório e um ficheiro de [registo](#output-description). 

O processo de registo não tem qualquer risco, não tem tempo de inatividade e não reiniciará o serviço SQL Server ou a máquina virtual. 

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com a extensão, necessitará do seguinte: 

- Uma [subscrição Azure](https://azure.microsoft.com/free/) que foi [registada com o fornecedor **Microsoft.SqlVirtualMachine**](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-resource-provider) e contém máquinas virtuais SQL Server não registadas. 
- As credenciais do cliente utilizadas para registar as máquinas virtuais existem em qualquer uma das seguintes funções Azure: **Contribuinte máquina virtual,** **contribuinte** ou **Proprietário.** 
- A versão mais recente do [Az PowerShell (5.0 mínimo)](/powershell/azure/new-azureps-module-az). 


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


## <a name="all-vms-in-a-list-of-subscriptions"></a>Todos os VMs numa lista de subscrições 

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

## <a name="all-vms-in-a-single-subscription"></a>Todos os VMs numa única subscrição

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

## <a name="all-vms-in-a-resource-group"></a>Todos os VMs de um grupo de recursos

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

## <a name="specific-vms-in-a-single-resource-group"></a>VMs específicos num único grupo de recursos

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

## <a name="a-specific-vm"></a>Um VM específico

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
| Número de assinaturas que não puderam ser experimentada por não estarem registadas no fornecedor de recursos | Esta secção contém a contagem e a lista de assinaturas que não foram registadas na extensão do Agente IAAS SQL. |
| Total de VMs encontrados | A contagem de máquinas virtuais que foram encontradas no âmbito dos parâmetros passados para o cmdlet. | 
| VMs já registados | A contagem de máquinas virtuais que foram ignoradas, uma vez que já estavam registadas com a extensão. |
| Número de VMs registado com sucesso | A contagem de máquinas virtuais que foram registadas com sucesso após a execução do cmdlet. Lista as máquinas virtuais registadas no `SubscriptionID, Resource Group, Virtual Machine` formato. | 
| Número de VMs não registou-se devido a erro | Contagem de máquinas virtuais que não se registaram devido a algum erro. Os detalhes do erro podem ser encontrados no ficheiro de registo. | 
| Número de VMs ignorados como o VM ou o agente de rajada em VM não está em execução | A contagem e a lista de máquinas virtuais que não podiam ser registadas como a máquina virtual ou o agente convidado na máquina virtual não estavam a funcionar. Estes podem ser novamente experimentados uma vez que a máquina virtual ou o agente convidado foi iniciado. Os detalhes podem ser encontrados no ficheiro de registo. |
| Número de VMs ignorados por não estarem a executar o SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas por não estarem a executar o SQL Server ou não serem uma máquina virtual do Windows. As máquinas virtuais estão listadas no `SubscriptionID, Resource Group, Virtual Machine` formato. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Registo 

Os erros são registados no ficheiro de registo denominado `VMsNotRegisteredDueToError<Timestamp>.log` , onde a hora de tempo é a hora em que o script começou. Se o erro estiver no nível de subscrição, o registo contém o ID de subscrição separado em vírgula e a mensagem de erro. Se o erro estiver com o registo da máquina virtual, o registo contém o ID de subscrição, nome do grupo de recursos, nome da máquina virtual, código de erro e mensagem separada por vírgulas. 

## <a name="remarks"></a>Observações

Quando registar VMs sql server com a extensão utilizando o script fornecido, considere o seguinte:

- O registo com a extensão requer um agente convidado em execução no SQL Server VM. As imagens do Windows Server 2008 não têm um agente convidado, pelo que estas máquinas virtuais falharão e devem ser registadas manualmente utilizando o [modo de gestão NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- Há uma lógica de reação incorporada para superar erros transparentes. Se a máquina virtual estiver registada com sucesso, então é uma operação rápida. No entanto, se o registo falhar, cada máquina virtual será novamente experimentada.  Como tal, deverá permitir um tempo significativo para concluir o processo de registo - embora o requisito de tempo real dependa do tipo e do número de erros. 

## <a name="full-script"></a>Script completo

Para obter o script completo no GitHub, consulte [O SQL Server VMs com Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o guião completo e guarde-o como `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)
