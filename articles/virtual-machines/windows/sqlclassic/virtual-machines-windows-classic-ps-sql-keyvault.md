---
title: Integrar Key Vault com uma VM clássica do Azure SQL Server
description: Saiba como automatizar a configuração de criptografia de SQL Server para uso com Azure Key Vault. Este tópico explica como usar a integração do Azure Key Vault com SQL Server máquinas virtuais criadas no modelo de implantação clássico.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978133"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar a integração de Azure Key Vault para SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Visão geral
Há vários recursos de criptografia SQL Server, como [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx), [cle (criptografia de nível de coluna)](https://msdn.microsoft.com/library/ms173744.aspx)e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem que você gerencie e armazene as chaves de criptografia usadas para criptografia. O serviço de Azure Key Vault (AKV) foi projetado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [conector do SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use essas chaves de Azure Key Vault.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Se você estiver executando SQL Server com computadores locais, há [etapas que podem ser seguidas para acessar Azure Key Vault do computador SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para SQL Server em VMs do Azure, você pode economizar tempo usando o recurso de *integração de Azure Key Vault* . Com alguns cmdlets Azure PowerShell para habilitar esse recurso, você pode automatizar a configuração necessária para que uma VM do SQL acesse o cofre de chaves.

Quando esse recurso é habilitado, ele instala automaticamente o Conector do SQL Server, configura o provedor EKM para acessar Azure Key Vault e cria a credencial para permitir que você acesse seu cofre. Se você examinou as etapas na documentação local mencionada anteriormente, poderá ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisa fazer manualmente é criar o cofre de chaves e as chaves. A partir daí, toda a configuração da sua VM do SQL é automatizada. Depois que esse recurso tiver concluído essa configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração do AKV
Use o PowerShell para configurar a integração do Azure Key Vault. As seções a seguir fornecem uma visão geral dos parâmetros necessários e, em seguida, um exemplo de script do PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instalar a extensão de IaaS SQL Server
Primeiro, [Instale a extensão de IaaS SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Entender os parâmetros de entrada
A tabela a seguir lista os parâmetros necessários para executar o script do PowerShell na próxima seção.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**A URL do cofre de chaves** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome da entidade de serviço** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo da entidade de serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |"mycred1" |
| **$vmName** |**Nome da máquina virtual**: o nome de uma VM do SQL criada anteriormente. |"myvmname" |
| **$serviceName** |**Nome do serviço**: o nome do serviço de nuvem que está associado à VM do SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Habilitar a integração do AKV com o PowerShell
O cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para o recurso de integração de Azure Key Vault. O **set-AzureVMSqlServerExtension** configura essa integração com o parâmetro **KeyVaultCredentialSettings** . As etapas a seguir mostram como usar esses comandos.

1. Em Azure PowerShell, primeiro configure os parâmetros de entrada com seus valores específicos, conforme descrito nas seções anteriores deste tópico. O script a seguir é um exemplo.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Em seguida, use o script a seguir para configurar e habilitar a integração do AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do agente IaaS do SQL atualizará a VM do SQL com essa nova configuração.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

