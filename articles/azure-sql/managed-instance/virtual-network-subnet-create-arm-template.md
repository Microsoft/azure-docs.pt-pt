---
title: Criar uma rede virtual
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar uma rede virtual configurada para suportar a implementação de uma Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 923f8b447b1103284b2c999a981826ef19a1c7d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050752"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Criar uma rede virtual para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como criar uma rede virtual válida e uma sub-rede onde pode implementar o Azure SQL Managed Instance.

A Instância Gerida azure SQL deve ser implantada dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md)Azure . Esta implementação permite os seguintes cenários:

- Endereço IP privado seguro
- Ligação a uma Instância Gerida SQL diretamente de uma rede no local
- Ligar uma Instância Gerida SQL a um servidor ligado ou a outra loja de dados no local
- Ligação de uma Instância Gerida SQL aos recursos do Azure  

> [!NOTE]
> Deve [determinar o tamanho da sub-rede para a Instância Gerida SQL](vnet-subnet-determine-size.md) antes de implementar a primeira instância. Não podes redimensionar a sub-rede depois de colocares os recursos lá dentro.
>
> Se planeia utilizar uma rede virtual existente, precisa de modificar essa configuração de rede para acomodar a sua Instância Gerida pelo SQL. Para obter mais informações, consulte [Modificar uma rede virtual existente para a Instância Gerida SQL](vnet-existing-add-subnet.md).
>
> Após a criação de uma Instância Gerida SQL, não é suportada a mudança da Instância Ou VNet gerida sQL para outro grupo de recursos ou subscrição.  A mudança da Instância Gerida SQL para outra sub-rede também não é suportada.
>

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar uma rede virtual é usar um modelo de implementação do Gestor de Recursos Azure.

1. Inicie sessão no Portal do Azure.

2. Selecione o botão **'Enviar para Azure':**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botão abre um formulário que pode utilizar para configurar o ambiente de rede onde pode implementar a Instância Gerida SQL.

   > [!Note]
   > Este modelo de Gestor de Recursos Azure irá implantar uma rede virtual com duas subredes. Uma subnet, chamada **ManagedInstances,** está reservada para a SQL Managed Instance e tem uma tabela de rotas pré-configurada. A outra subrede, denominada **Padrão,** é utilizada para outros recursos que devem aceder à Instância Gerida pela SQL (por exemplo, Máquinas Virtuais Azure).

3. Configure o ambiente de rede. No seguinte formulário, pode configurar parâmetros do ambiente da sua rede:

   ![Modelo de Gestor de Recursos para configurar a rede Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Poderá alterar os nomes da rede virtual e das subredes e ajustar as gamas IP associadas aos seus recursos de rede. Depois de selecionar o botão **Comprar,** este formulário criará e configurará o seu ambiente. Se não precisar de duas subredes, pode eliminar a depresio.

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [O que é um Caso Gerido SQL?](sql-managed-instance-paas-overview.md)
- Conheça a [arquitetura de conectividade em SQL Managed Instance](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para a Instância Gerida SQL](vnet-existing-add-subnet.md).
- Para um tutorial que mostre como criar uma rede virtual, crie uma Instância Gerida SQL e restaure uma base de dados a partir de uma cópia de segurança da base de dados, consulte [Create a Azure SQL Managed Instance](instance-create-quickstart.md).
- Para problemas dNS, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
