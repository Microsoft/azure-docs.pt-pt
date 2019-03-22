---
title: Integrar o Cofre de chaves com o SQL Server em VMs do Windows no Azure (clássico) | Documentos da Microsoft
description: Saiba como automatizar a configuração de criptografia do SQL Server para utilização com o Azure Key Vault. Este tópico explica como utilizar a integração do Azure Key Vault com o SQL Server criar de máquinas virtuais no modelo de implementação clássica.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312449"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar a integração do Cofre de chaves do Azure para o SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Descrição geral
Existem vários recursos de criptografia do SQL Server, tais como [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), e [encriptação de cópia de segurança](https://msdn.microsoft.com/library/dn449489.aspx). Esses formulários de criptografia requerem a gerir e armazenar as chaves criptográficas que utilizar para a encriptação. O serviço do Azure Key Vault (AKV) foi concebido para melhorar a segurança e gestão destas chaves numa localização segura e de elevada disponibilidade. O [conector do SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server para utilizar estas chaves do Azure Key Vault.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Se estiver a executar o SQL Server com máquinas no local, existem [etapas que pode seguir para acessar Azure Key Vault a partir do seu computador do SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas para o SQL Server em VMs do Azure, pode poupar tempo ao utilizar o *a integração do Azure Key Vault* funcionalidade. Com alguns cmdlets do PowerShell do Azure para ativar esta funcionalidade, pode automatizar a configuração necessários para uma VM do SQL aceder ao Cofre de chave.

Quando esta funcionalidade está ativada, ele automaticamente instala o conector do SQL Server, configura o fornecedor EKM para aceder ao Azure Key Vault e cria a credencial para que possa aceder ao Cofre de. Se verificasse os passos na documentação do locais mencionado anteriormente, pode ver que esse recurso automatiza os passos 2 e 3. A única coisa que ainda precisava manualmente a fazer é criar o Cofre de chaves e as chaves. A partir daí, a configuração completa da sua VM de SQL é automatizada. Depois desta funcionalidade concluída esta configuração, pode executar instruções T-SQL para começar a encriptar as bases de dados ou cópias de segurança, tal como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração AKV
Utilize o PowerShell para configurar a integração do Azure Key Vault. As secções seguintes fornecem uma visão geral dos parâmetros necessários e, em seguida, um script do PowerShell de exemplo.

### <a name="install-the-sql-server-iaas-extension"></a>Instalar a extensão de IaaS do SQL Server
Primeiro, [instalar a extensão de IaaS do SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela seguinte lista os parâmetros necessários para executar o script do PowerShell na secção seguinte.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**O URL do Cofre de chaves** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome do Principal de serviço** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo do Principal de serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome da credencial**: A integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tem acesso ao Cofre de chaves. Escolha um nome para esta credencial. |"mycred1" |
| **$vmName** |**Nome da máquina virtual**: O nome de uma VM de SQL criado anteriormente. |"myvmname" |
| **$serviceName** |**Nome do serviço**: O nome de serviço em nuvem que estão associado com a VM do SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Ativar a integração AKV com o PowerShell
O **New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet cria um objeto de configuração para a funcionalidade de integração do Azure Key Vault. O **Set-AzureVMSqlServerExtension** configura esta integração com o **KeyVaultCredentialSettings** parâmetro. Os passos seguintes mostram como utilizar estes comandos.

1. No Azure PowerShell, primeiro de configurar os parâmetros de entrada com os seus valores específicos, tal como descrito nas secções anteriores deste tópico. O script seguinte é um exemplo.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Em seguida, utilize o seguinte script para configurar e ativar a integração AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão de agente IaaS do SQL irá atualizar a VM do SQL com esta nova configuração.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

