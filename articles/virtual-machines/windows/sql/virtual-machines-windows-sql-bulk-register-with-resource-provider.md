---
title: Registrar em massa máquinas virtuais do SQL no Azure com o provedor de recursos da VM do SQL | Microsoft Docs
description: Registre em massa SQL Server VMs com o provedor de recursos de VM do SQL para melhorar a capacidade de gerenciamento.
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
ms.openlocfilehash: b9d4b99447e7c814d737def54dce03c4acc0e5ca
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172975"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrar em massa máquinas virtuais do SQL no Azure com o provedor de recursos da VM do SQL

Este artigo descreve como registrar em massa seu SQL Server VM (máquina virtual) no Azure com o provedor de recursos de VM do SQL usando o cmdlet do PowerShell do `Register-SqlVMs`.

O cmdlet `Register-SqlVMs` pode ser usado para registrar todas as máquinas virtuais em uma determinada lista de assinaturas, grupos de recursos ou uma lista de máquinas virtuais específicas. O cmdlet registrará as máquinas virtuais no modo de gerenciamento _leve_ e, em seguida, irá gerar um [relatório e um arquivo de log](#output-description). 

O processo de registro não tem risco, não tem nenhum tempo de inatividade e não será reiniciado SQL Server ou a máquina virtual. 

Para obter mais informações sobre o provedor de recursos, consulte [provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/) que foi [registrada com o provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) e contém máquinas virtuais SQL Server não registradas. 
- As credenciais de cliente usadas para registrar as máquinas virtuais existem em qualquer uma das seguintes funções de RBAC: **colaborador de máquina virtual**, **colaborador**ou **proprietário**. 
- A versão mais recente do [AZ PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="getting-started"></a>Introdução

Antes de continuar, você deve primeiro criar uma cópia local do script, importá-la como um módulo do PowerShell e conectar-se ao Azure. 

### <a name="create-script"></a>Criar script

Para criar o script, copie o [script completo](#full-script) do final deste artigo e salve-o localmente como `RegisterSqlVMs.psm1`. 

### <a name="import-script"></a>Importar script

Depois que o script for criado, você poderá importá-lo como um módulo no terminal do PowerShell. 

Abra um terminal do PowerShell administrativo e navegue até onde você salvou o arquivo de `RegisterSqlVMs.psm1`. Em seguida, execute o seguinte cmdlet do PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Ligar ao Azure

Use o seguinte cmdlet do PowerShell para se conectar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Todas as VMs na lista de assinaturas 

Use o seguinte cmdlet para registrar todas as máquinas virtuais SQL Server em uma lista de assinaturas:

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

## <a name="all-vms-in-a-single-subscription"></a>Todas as VMs em uma única assinatura

Use o seguinte cmdlet para registrar todas as máquinas virtuais SQL Server em uma única assinatura: 

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

## <a name="all-vms-in-multiple-resource-groups"></a>Todas as VMs em vários grupos de recursos

Use o cmdlet a seguir para registrar todas as máquinas virtuais SQL Server em vários grupos de recursos em uma única assinatura:

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

## <a name="all-vms-in-a-resource-group"></a>Todas as VMs em um grupo de recursos

Use o seguinte cmdlet para registrar todas as máquinas virtuais SQL Server em um único grupo de recursos: 

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

## <a name="specific-vms-in-single-resource-group"></a>VMs específicas em um único grupo de recursos

Use o seguinte cmdlet para registrar máquinas virtuais SQL Server específicas em um único grupo de recursos:

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

## <a name="specific-vm"></a>VM específica

Use o seguinte cmdlet para registrar uma máquina virtual SQL Server específica: 

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

Tanto um relatório quanto um arquivo de log são gerados toda vez que o cmdlet `Register-SqlVMs` é usado. 

### <a name="report"></a>Relatório

O relatório é gerado como um arquivo de `.txt` chamado `RegisterSqlVMScriptReport<Timestamp>.txt` em que o carimbo de data/hora é o horário em que o cmdlet foi iniciado. O relatório lista os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Falha no registro do número de assinaturas porque você não tem acesso ou as credenciais estão incorretas | Isso fornece o número e a lista de assinaturas que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no log, pesquisando a ID da assinatura. | 
| Número de assinaturas que não puderam ser tentadas porque não estão registradas no RP | Esta seção contém a contagem e a lista de assinaturas que não foram registradas no provedor de recursos da VM do SQL. |
| Total de VMs encontradas | A contagem de máquinas virtuais que foram encontradas no escopo dos parâmetros passados para o cmdlet. | 
| VMs já registradas | A contagem de máquinas virtuais que foram ignoradas porque já foram registradas com o provedor de recursos. |
| Número de VMs registradas com êxito | A contagem de máquinas virtuais que foram registradas com êxito após a execução do cmdlet. Lista as máquinas virtuais registradas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Número de VMs com falha ao registrar devido a erro | Contagem de máquinas virtuais que falharam ao registrar devido a algum erro. Os detalhes do erro podem ser encontrados no arquivo de log. | 
| Número de VMs ignoradas porque a VM ou o agente rajada na VM não está em execução | A contagem e a lista de máquinas virtuais que não puderam ser registradas como a máquina virtual ou o agente convidado na máquina virtual não estavam em execução. Eles podem ser repetidos depois que a máquina virtual ou o agente convidado tiver sido iniciado. Os detalhes podem ser encontrados no arquivo de log. |
| Número de VMs ignoradas porque não estão em execução SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas porque não estão em execução SQL Server ou não são uma máquina virtual do Windows. As máquinas virtuais são listadas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Registo 

Os erros são registrados no arquivo de log chamado `VMsNotRegisteredDueToError<Timestamp>.log` em que Timestamp é a hora em que o script foi iniciado. Se o erro estiver no nível da assinatura, o log conterá a assinatura separada por vírgula e a mensagem de erro. Se o erro for com o registro da máquina virtual, o log conterá a ID da assinatura, o nome do grupo de recursos, o nome da máquina virtual, o código de erro e a mensagem separados por vírgulas. 

## <a name="remarks"></a>Observações

Ao registrar SQL Server VMs com o provedor de recursos usando o script fornecido, considere o seguinte:

- O registro com o provedor de recursos requer um agente convidado em execução na VM SQL Server. As imagens do Windows Server 2008 não têm um agente convidado, portanto, essas máquinas virtuais falharão e deverão ser registradas manualmente usando o [modo de gerenciamento noagent](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).
- Há uma lógica de repetição interna para superar erros transparentes. Se a máquina virtual for registrada com êxito, ela será uma operação rápida. No entanto, se o registro falhar, cada máquina virtual será repetida.  Dessa forma, você deve permitir um tempo significativo para concluir o processo de registro-embora o requisito de tempo real seja dependente do tipo e do número de erros. 

## <a name="full-script"></a>Script completo

Para o script completo no GitHub, consulte [registrar VMs do SQL em massa com AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 


[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
