---
title: Integrar o cofre chave com um clássico Azure SQL Server VM
description: Aprenda a automatizar a configuração da encriptação Do Servidor SQL para utilização com o Cofre de Chaves Azure. Este tópico explica como usar a Integração do Cofre chave Azure com máquinas virtuais SQL Server criar no modelo de implementação clássico.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978133"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configure Integração do cofre chave azure para servidor SQL em máquinas virtuais azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássica](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação do SQL Server, tais como [encriptação de dados transparentes (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação](https://msdn.microsoft.com/library/dn449489.aspx)de backup . Estas formas de encriptação exigem que gere e guarde as chaves criptográficas que utiliza para encriptação. O serviço Azure Key Vault (AKV) foi concebido para melhorar a segurança e gestão destas chaves num local seguro e altamente disponível. O [Conector do Servidor SQL](https://www.microsoft.com/download/details.aspx?id=45344) permite ao Servidor SQL utilizar estas teclas a partir do Cofre de Chaves Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Se estiver a executar o SQL Server com máquinas no local, existem [passos que pode seguir para aceder ao Cofre de Chaves Azure a partir da sua máquina de servidor SQL](https://msdn.microsoft.com/library/dn198405.aspx)no local . Mas para o SQL Server em VMs Azure, pode economizar tempo utilizando a funcionalidade de integração do cofre de *chaves Azure.* Com alguns cmdlets Azure PowerShell para ativar esta funcionalidade, pode automatizar a configuração necessária para que um VM SQL aceda ao seu cofre chave.

Quando esta funcionalidade está ativada, instala automaticamente o Conector SQL Server, confunde o fornecedor EKM para aceder ao Cofre de Chaves Azure e cria a credencial para permitir o acesso ao seu cofre. Se olhar para os passos na documentação anteriormente mencionada no local, pode ver que esta funcionalidade automatiza os passos 2 e 3. A única coisa que ainda precisas de fazer manualmente é criar o cofre e as chaves. A partir daí, toda a configuração do seu VM SQL é automatizada. Uma vez concluída esta funcionalidade, pode executar declarações T-SQL para começar a encriptar as suas bases de dados ou backups como normalmente faria.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configure integração AKV
Utilize a PowerShell para configurar a Integração do Cofre de Chaves Azure. As seguintes secções fornecem uma visão geral dos parâmetros necessários e, em seguida, um script powerShell de amostra.

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão SQL Server IaaS
Primeiro, [instale a extensão SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreenda os parâmetros de entrada
A tabela seguinte lista os parâmetros necessários para executar o script PowerShell na secção seguinte.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**O URL do cofre chave** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome principal do serviço** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo principal de serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |"Mycred1" |
| **$vmName** |**Nome**da máquina virtual : O nome de um SQL VM previamente criado. |"myvmname" |
| **$serviceName** |**Nome do serviço**: O nome cloud service que está associado ao VM SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Ativar a integração akv com a PowerShell
O **New-AzureVMSqlServerKeyCofreCredentialConfig** cmdlet cria um objeto de configuração para a funcionalidade de integração do cofre de chaves Azure. O **Set-AzureVMSqlServerExtension** configura esta integração com o parâmetro **KeyVaultCredentialSettings.** Os seguintes passos mostram como usar estes comandos.

1. No Azure PowerShell, configure primeiro os parâmetros de entrada com os seus valores específicos, conforme descrito nas secções anteriores deste tópico. O seguinte guião é um exemplo.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Em seguida, utilize o seguinte script para configurar e ativar a integração akv.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do agente SQL IaaS atualizará o VM SQL com esta nova configuração.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

