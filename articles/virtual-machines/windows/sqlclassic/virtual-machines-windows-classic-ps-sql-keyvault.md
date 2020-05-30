---
title: Integre o Cofre da Chave com um VM clássico do Azure SQL Server VM
description: Saiba como automatizar a configuração da encriptação do SQL Server para utilização com o Cofre da Chave Azure. Este tópico explica como usar a integração do cofre de chave Azure com máquinas virtuais SQL Server criam no modelo de implementação clássico.
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
ms.openlocfilehash: 7439aa360395490f31a638ac690ed7e5cad1054b
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195843"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar integração de cofre de chave Azure para servidor SQL em máquinas virtuais Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/azure-key-vault-integration-configure.md)
> * [Clássica](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação do SQL Server, tais como [encriptação transparente de dados (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação de backup](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de encriptação requerem que você gere e armazene as chaves criptográficas que utiliza para encriptação. O serviço Azure Key Vault (AKV) foi concebido para melhorar a segurança e a gestão destas chaves num local seguro e altamente disponível. O [Conector do Servidor SQL](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server utilize estas chaves a partir do Cofre da Chave Azure.

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Se estiver a executar o SQL Server com máquinas no local, [existem passos que pode seguir para aceder ao Cofre da Chave Azure a partir do computador SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas para o SQL Server em VMs Azure, pode economizar tempo utilizando a função de Integração do *Cofre de Chave Azure.* Com alguns cmdlets Azure PowerShell para ativar esta função, pode automatizar a configuração necessária para que um SQL VM aceda ao seu cofre de chaves.

Quando esta funcionalidade está ativada, instala automaticamente o Conector do Servidor SQL, configura o fornecedor EKM para aceder ao Cofre da Chave Azure e cria a credencial para permitir o acesso ao seu cofre. Se você olhar para os passos na documentação anteriormente mencionada no local, você pode ver que esta funcionalidade automatiza os passos 2 e 3. A única coisa que ainda precisa fazer manualmente é criar o cofre e as chaves. A partir daí, toda a configuração do seu SQL VM é automatizada. Uma vez concluída esta funcionalidade, pode executar declarações T-SQL para começar a encriptar as suas bases de dados ou cópias de segurança como normalmente faria.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar integração AKV
Utilize o PowerShell para configurar a integração do cofre da chave Azure. As secções seguintes fornecem uma visão geral dos parâmetros necessários e, em seguida, uma amostra do script PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão IAAS do Servidor SQL
Em primeiro lugar, [instale a extensão IAAS do Servidor SQL](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela seguinte lista os parâmetros necessários para executar o script PowerShell na secção seguinte.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**O URL do cofre chave** |"https: \/ /contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome principal do serviço** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo principal do serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |"mycred1" |
| **$vmName** |**Nome da máquina virtual**: O nome de um SQL VM previamente criado. |"myvmname" |
| **$serviceName** |**Nome de serviço**: O nome do Serviço de Cloud que está associado ao SQL VM. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Permitir a integração da AKV com o PowerShell
O cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para a funcionalidade de Integração do Cofre de Chave Azure. O **Set-AzureVMSqlServerExtension** configura esta integração com o parâmetro **KeyVaultCredentialSettings.** Os passos seguintes mostram como utilizar estes comandos.

1. Em Azure PowerShell, configurar primeiro os parâmetros de entrada com os seus valores específicos, conforme descrito nas secções anteriores deste tópico. O seguinte guião é um exemplo.
   
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

A Extensão do Agente SQL IaaS atualizará o SQL VM com esta nova configuração.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

