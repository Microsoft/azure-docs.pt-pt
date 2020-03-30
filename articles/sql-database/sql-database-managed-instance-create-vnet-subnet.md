---
title: Criar uma rede virtual para instâncias geridas
description: Este artigo descreve como criar uma rede virtual onde pode implementar a Instância Gerida pela Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 9f115d51657993562642391a235de79420aa434a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823366"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Criar uma rede virtual para a Instância Gerida pela Base de Dados Azure SQL

Este artigo explica como criar uma rede virtual válida e uma sub-rede onde pode implementar a Instância Gerida pela Base de Dados Azure SQL.

A instância gerida pela base de dados Azure SQL deve ser implantada dentro de uma [rede virtual](../virtual-network/virtual-networks-overview.md)Azure . Esta implementação permite os seguintes cenários:

- Endereço IP privado seguro
- Ligação a uma Instância Gerida diretamente de uma rede no local
- Ligar uma Instância Gerida a servidor ligado ou outra loja de dados no local
- Ligação de uma instância gerida aos recursos do Azure  

> [!Note]
> Deve [determinar o tamanho da sub-rede para a Instância Gerida](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implementar a primeira instância. Não podes redimensionar a sub-rede depois de colocares os recursos lá dentro.
>
> Se planeia utilizar uma rede virtual existente, precisa de modificar essa configuração de rede para acomodar a sua Instância Gerida. Para mais informações, consulte [Modificar uma rede virtual existente para O Exemplo Gerido](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Após a criação de uma instância gerida, a mudança da instância gerida ou vNet para outro grupo de recursos ou subscrição não é suportada.


## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar uma rede virtual é usar um modelo de implementação do Gestor de Recursos Azure.

1. Inicie sessão no Portal do Azure.

2. Selecione o botão **'Enviar para Azure':**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botão abre um formulário que pode utilizar para configurar o ambiente de rede onde pode implementar a Instância Gerida.

   > [!Note]
   > Este modelo de Gestor de Recursos Azure irá implantar uma rede virtual com duas subredes. Uma subnet, chamada **ManagedInstances,** está reservada para Instância Gerida e tem uma tabela de rotas pré-configurada. A outra subnet, denominada **Padrão,** é utilizada para outros recursos que devem aceder à Instância Gerida (por exemplo, Máquinas Virtuais Azure).

3. Configure o ambiente de rede. No seguinte formulário, pode configurar parâmetros do ambiente da sua rede:

   ![Modelo de Gestor de Recursos para configurar a rede Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Poderá alterar os nomes da rede virtual e das subredes e ajustar as gamas IP associadas aos seus recursos de rede. Depois de selecionar o botão **Comprar,** este formulário criará e configurará o seu ambiente. Se não precisar de duas subredes, pode eliminar a depresio.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é um caso gerido?](sql-database-managed-instance.md)
- Conheça a [arquitetura de conectividade em Caso Gerido.](sql-database-managed-instance-connectivity-architecture.md)
- Aprenda a [modificar uma rede virtual existente para O Exemplo Gerido](sql-database-managed-instance-configure-vnet-subnet.md).
- Para um tutorial que mostre como criar uma rede virtual, criar uma Instância Gerida e restaurar uma base de dados a partir de uma cópia de dados, consulte [Create a Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas dNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
