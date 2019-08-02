---
title: Criar uma rede virtual para Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como criar uma rede virtual na qual você pode implantar Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 832f43d1f25393a29df4b836c353611a19e3a417
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567578"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Criar uma rede virtual para Instância Gerenciada do Banco de Dados SQL do Azure

Este artigo explica como criar uma rede virtual e uma sub-rede válidas onde você pode implantar Instância Gerenciada do Banco de Dados SQL do Azure.

Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [rede virtual](../virtual-network/virtual-networks-overview.md)do Azure. Essa implantação habilita os seguintes cenários:

- Endereço IP privado seguro
- Conectando-se a um Instância Gerenciada diretamente de uma rede local
- Conectando um Instância Gerenciada ao servidor vinculado ou a outro armazenamento de dados local
- Conectando um Instância Gerenciada aos recursos do Azure  

> [!Note]
> Você deve [determinar o tamanho da sub-rede para instância gerenciada](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implantar a primeira instância. Você não pode redimensionar a sub-rede depois de colocar os recursos dentro.
>
> Se você planeja usar uma rede virtual existente, precisará modificar essa configuração de rede para acomodar seu Instância Gerenciada. Para obter mais informações, consulte [modificar uma rede virtual existente para instância gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da VNet para outro grupo de recursos ou assinatura.


## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar uma rede virtual é usar um modelo de implantação Azure Resource Manager.

1. Inicie sessão no Portal do Azure.

2. Selecione o botão **implantar no Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Esse botão abre um formulário que você pode usar para configurar o ambiente de rede no qual você pode implantar Instância Gerenciada.

   > [!Note]
   > Este modelo de Azure Resource Manager implantará uma rede virtual com duas sub-redes. Uma sub-rede, chamada **ManagedInstances**, é reservada para instância gerenciada e tem uma tabela de rotas pré-configurada. A outra sub-rede, chamada **padrão**, é usada para outros recursos que devem acessar instância gerenciada (por exemplo, máquinas virtuais do Azure).

3. Configure o ambiente de rede. No formulário a seguir, você pode configurar parâmetros do seu ambiente de rede:

   ![Modelo do Resource Manager para configurar a rede do Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Você pode alterar os nomes da rede virtual e sub-redes e ajustar os intervalos de IP associados aos seus recursos de rede. Depois de selecionar o botão **comprar** , este formulário criará e configurará seu ambiente. Se você não precisar de duas sub-redes, poderá excluir a padrão.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre a [arquitetura de conectividade em instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para instância gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar um Instância Gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte Configurando [um DNS personalizado](sql-database-managed-instance-custom-dns.md).
