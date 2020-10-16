---
title: Criar uma rede virtual
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar uma rede virtual configurada para suportar a implementação de Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617711"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Criar uma rede virtual para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como criar uma rede virtual válida e uma sub-rede onde pode implementar Azure SQL Managed Instance.

Azure SQL Managed Instance deve ser implantado dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md)Azure . Esta implementação permite os seguintes cenários:

- Endereço IP privado seguro
- Ligação à SQL Gestão de Instâncias diretamente de uma rede no local
- Ligação da SQL Gestd Instance a um servidor ligado ou a outra loja de dados no local
- Ligação da SQL Gestão de Instâncias a Recursos Azure  

> [!NOTE]
> Deve [determinar o tamanho da sub-rede para SQL Managed Instance](vnet-subnet-determine-size.md) antes de implementar a primeira instância. Não podes redimensionar a sub-rede depois de colocares os recursos lá dentro.
>
> Se planeia utilizar uma rede virtual existente, tem de modificar essa configuração de rede para acomodar a SQL Managed Instance. Para obter mais informações, consulte [Modificar uma rede virtual existente para sql Managed Instance](vnet-existing-add-subnet.md).
>
> Após a criação de um caso gerido, a deslocação da instância gerida ou da rede virtual para outro grupo de recursos ou subscrição não é suportada.  A deslocação da instância gerida para outra sub-rede também não é suportada.
>

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A forma mais fácil de criar e configurar uma rede virtual é utilizar um modelo de implementação do Gestor de Recursos Azure.

1. Inicie sessão no Portal do Azure.

2. Selecione o botão **'Implementar para Azure':**

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Este botão abre um formulário que pode utilizar para configurar o ambiente de rede onde pode implantar a SQL Managed Instance.

   > [!Note]
   > Este modelo de Gestor de Recursos Azure irá implantar uma rede virtual com duas sub-redes. Uma sub-rede, **denominada ManagedInstances,** é reservada para SQL Managed Instance e tem uma tabela de rotas pré-configurada. A outra sub-rede, chamada **Padrão,** é utilizada para outros recursos que devem aceder à SQL Managed Instance (por exemplo, Azure Virtual Machines).

3. Configure o ambiente de rede. No seguinte formulário, pode configurar parâmetros do seu ambiente de rede:

   ![Modelo de gestor de recursos para configurar a rede Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Pode alterar os nomes da rede virtual e sub-redes e ajustar as gamas IP associadas aos seus recursos de rede. Depois de selecionar o botão **Comprar,** este formulário criará e configurará o seu ambiente. Se não precisar de duas sub-redes, pode eliminar a padrão.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é a SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Conheça a [arquitetura de conectividade em SQL Managed Instance](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para o SQL Managed Instance](vnet-existing-add-subnet.md).
- Para um tutorial que mostre como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de [dados,](instance-create-quickstart.md)consulte Criar um caso gerido .
- Para questões dns, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
