---
title: Integrar Key Vault com SQL Server em VMs do Windows no Azure (Resource Manager) | Microsoft Docs
description: Saiba como automatizar a configuração de criptografia de SQL Server para uso com Azure Key Vault. Este tópico explica como usar a integração do Azure Key Vault com SQL Server máquinas virtuais criadas com o Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e7de54f7da8cef5942a8d8f41031eaf3e2565580
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846245"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurar a integração de Azure Key Vault para SQL Server em máquinas virtuais do Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Descrição geral
Há vários recursos de criptografia SQL Server, como [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx), [cle (criptografia de nível de coluna)](https://msdn.microsoft.com/library/ms173744.aspx)e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem que você gerencie e armazene as chaves de criptografia usadas para criptografia. O serviço de Azure Key Vault (AKV) foi projetado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [conector do SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use essas chaves de Azure Key Vault.

Se você estiver executando SQL Server com computadores locais, há [etapas que podem ser seguidas para acessar Azure Key Vault do computador SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para SQL Server em VMs do Azure, você pode economizar tempo usando o recurso de *integração de Azure Key Vault* .

Quando esse recurso é habilitado, ele instala automaticamente o Conector do SQL Server, configura o provedor EKM para acessar Azure Key Vault e cria a credencial para permitir que você acesse seu cofre. Se você examinou as etapas na documentação local mencionada anteriormente, poderá ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisa fazer manualmente é criar o cofre de chaves e as chaves. A partir daí, toda a configuração da sua VM do SQL é automatizada. Depois que esse recurso tiver concluído essa configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > O provedor EKM versão 1.0.4.0 está instalado na VM SQL Server por meio da [extensão IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). A atualização da extensão IaaS do SQL não atualizará a versão do provedor. Considere atualizar manualmente a versão do provedor EKM, se necessário (por exemplo, ao migrar para um Instância Gerenciada do SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Habilitando e configurando a integração do AKV
Você pode habilitar a integração do AKV durante o provisionamento ou configurá-lo para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Se você estiver Provisionando uma nova máquina virtual SQL Server com o Gerenciador de recursos, a portal do Azure fornecerá uma maneira de habilitar a integração Azure Key Vault. O recurso Azure Key Vault está disponível apenas para as edições Enterprise, Developer e Evaluation do SQL Server.

![Integração do Cofre de Chaves do SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obter uma explicação detalhada do provisionamento, consulte provisionar [um SQL Server máquina virtual no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL Server existentes, abra o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **segurança** em **configurações**. Selecione **habilitar** para habilitar a integração de Azure Key Vault. 

![Integração do SQL AKV para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Quando terminar, selecione o botão **aplicar** na parte inferior da página **segurança** para salvar as alterações.

> [!NOTE]
> O nome da credencial que criamos aqui será mapeado para um logon do SQL posteriormente. Isso permite que o logon do SQL acesse o cofre de chaves. 


> [!NOTE]
> Você também pode configurar a integração do AKV usando um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para integração de Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
