---
title: Integrar o Cofre chave com o Servidor SQL em VMs windows em Azure (Gestor de Recursos) [ Microsoft Docs
description: Aprenda a automatizar a configuração da encriptação Do Servidor SQL para utilização com o Cofre de Chaves Azure. Este tópico explica como usar a Integração do Cofre de Chaves Azure com máquinas virtuais SQL Server criadas com O Gestor de Recursos.
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
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102136"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurar a Integração do Azure Key Vault para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássica](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação do SQL Server, tais como [encriptação de dados transparentes (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [encriptação de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação](https://msdn.microsoft.com/library/dn449489.aspx)de backup . Estas formas de encriptação exigem que gere e guarde as chaves criptográficas que utiliza para encriptação. O serviço Azure Key Vault (AKV) foi concebido para melhorar a segurança e gestão destas chaves num local seguro e altamente disponível. O [Conector do Servidor SQL](https://www.microsoft.com/download/details.aspx?id=45344) permite ao Servidor SQL utilizar estas teclas a partir do Cofre de Chaves Azure.

Se estiver a executar o SQL Server com máquinas no local, existem [passos que pode seguir para aceder ao Cofre de Chaves Azure a partir da sua máquina de servidor SQL](https://msdn.microsoft.com/library/dn198405.aspx)no local . Mas para o SQL Server em VMs Azure, pode economizar tempo utilizando a funcionalidade de integração do cofre de *chaves Azure.*

Quando esta funcionalidade está ativada, instala automaticamente o Conector SQL Server, confunde o fornecedor EKM para aceder ao Cofre de Chaves Azure e cria a credencial para permitir o acesso ao seu cofre. Se olhar para os passos na documentação anteriormente mencionada no local, pode ver que esta funcionalidade automatiza os passos 2 e 3. A única coisa que ainda precisas de fazer manualmente é criar o cofre e as chaves. A partir daí, toda a configuração do seu VM SQL é automatizada. Uma vez concluída esta funcionalidade, pode executar declarações T-SQL para começar a encriptar as suas bases de dados ou backups como normalmente faria.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > A versão 1.0.4.0 do Fornecedor EKM está instalada no VM do Servidor SQL através da [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). A atualização da extensão SQL IaaS não atualizará a versão do fornecedor. Por favor, considere a atualização manual da versão do fornecedor EKM, se necessário (por exemplo, ao migrar para uma Instância Gerida SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Habilitar e configurar a integração do AKV
Pode permitir a integração do AKV durante o fornecimento ou configurá-lo para vMs existentes.

### <a name="new-vms"></a>Novos VMs
Se estiver a fornecer uma nova máquina virtual SQL Server com O Gestor de Recursos, o portal Azure fornece uma forma de permitir a integração do Cofre chave Azure. A funcionalidade Azure Key Vault está disponível apenas para as Edições Empresariais, Desenvolvedoras e Avaliação do Servidor SQL.

![Integração do Cofre de Chaves do SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para uma passagem detalhada do provisionamento, consulte [a Provision a SQL Server virtual machine no portal Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais existentes do SQL Server, abra o recurso das [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **A Segurança** em **Definições**. Selecione **Enable** para ativar a integração do Cofre de Chaves Azure. 

![Integração SQL AKV para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Quando terminar, selecione o botão **Aplicar** na parte inferior da página **de Segurança** para guardar as alterações.

> [!NOTE]
> O nome credencial que criamos aqui será mapeado para um login SQL mais tarde. Isto permite que o login SQL aceda ao cofre da chave. 


> [!NOTE]
> Também pode configurar a integração do AKV usando um modelo. Para mais informações, consulte o [modelo de quickstart Azure para integração do Cofre de Chaves Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
