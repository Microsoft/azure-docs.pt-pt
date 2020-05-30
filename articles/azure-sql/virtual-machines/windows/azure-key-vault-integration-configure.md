---
title: Integrar o Cofre de Chaves com O Servidor SQL em VMs windows em Azure (Gestor de Recursos) Microsoft Docs
description: Saiba como automatizar a configuração da encriptação do SQL Server para utilização com o Cofre da Chave Azure. Este tópico explica como usar a Azure Key Vault Integration com máquinas virtuais SQL criadas com o Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f604f46853263b5724282f53bbf331e996be4dcb
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219472"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurar a integração do Cofre de Chaves Azure para o SQL Server em VMs Azure (Gestor de Recursos)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Resource Manager](azure-key-vault-integration-configure.md)
> * [Clássica](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

Existem várias funcionalidades de encriptação do SQL Server, tais como [encriptação transparente de dados (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação de backup](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de encriptação requerem que você gere e armazene as chaves criptográficas que utiliza para encriptação. O serviço Azure Key Vault foi concebido para melhorar a segurança e gestão destas chaves num local seguro e altamente disponível. O [Conector do Servidor SQL](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server utilize estas chaves a partir do Cofre da Chave Azure.

Se estiver a executar o SQL Server no local, existem passos que pode seguir para aceder ao [Cofre da Chave Azure a partir da sua instância do SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas para o SQL Server em VMs Azure, pode economizar tempo utilizando a função de Integração do *Cofre de Chave Azure.*

Quando esta funcionalidade está ativada, instala automaticamente o Conector do Servidor SQL, configura o fornecedor EKM para aceder ao Cofre da Chave Azure e cria a credencial para permitir o acesso ao seu cofre. Se você olhar para os passos na documentação anteriormente mencionada no local, você pode ver que esta funcionalidade automatiza os passos 2 e 3. A única coisa que ainda precisa fazer manualmente é criar o cofre e as chaves. A partir daí, toda a configuração do seu SQL Server VM é automatizada. Uma vez concluída esta funcionalidade, pode executar declarações Transact-SQL (T-SQL) para começar a encriptar as suas bases de dados ou backups como normalmente faria.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Gestão de Chaves Extensível (EKM) A versão 1.0.4.0 do Fornecedor está instalada no SQL Server VM através da [infraestrutura SQL como extensão de serviço (IaaS).](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) A atualização da extensão SQL IaaS não atualizará a versão do fornecedor. Por favor, considere a atualização manual da versão do fornecedor EKM se necessário (por exemplo, ao migrar para uma SQL Managed Instance).


## <a name="enabling-and-configuring-key-vault-integration"></a>Permitir e configurar a integração do Cofre de Chaves
Pode ativar a integração do Key Vault durante o provisionamento ou configurar para os VM existentes.

### <a name="new-vms"></a>Novos VMs
Se estiver a fornecer uma nova máquina virtual SQL com o Resource Manager, o portal Azure fornece uma forma de permitir a integração do Azure Key Vault. A funcionalidade Azure Key Vault está disponível apenas para as Edições Enterprise, Developer e Evaluation do SQL Server.

![Integração do Cofre de Chaves do SQL Azure](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Para obter uma análise detalhada do provisionamento, consulte [Disposição de uma máquina virtual SQL no portal Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL existentes, abra o [recurso das máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selecione **Segurança** em **Definições**. **Selecione Ativar** para permitir a integração do Cofre da Chave Azure. 

![Integração do SqL Key Vault para os VMs existentes](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Quando terminar, selecione o botão **Aplicar** na parte inferior da página **'Segurança'** para guardar as alterações.

> [!NOTE]
> O nome de credencial que criamos aqui será mapeado para um login SQL mais tarde. Isto permite ao login SQL aceder ao cofre da chave. 


> [!NOTE]
> Também pode configurar a integração do Key Vault utilizando um modelo. Para obter mais informações, consulte [o modelo de arranque rápido Azure para a integração do Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
