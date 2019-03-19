---
title: Criar uma rede virtual para a instância gerida da base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo descreve como criar uma rede virtual onde pode implementar a instância gerida da base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: d2a9c29733c42cf9753ce48b2a37a83fb8dda30d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993073"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Criar uma rede virtual para a instância gerida da base de dados SQL do Azure

Este artigo explica como criar uma rede virtual válido e a sub-rede onde pode implementar a instância gerida da base de dados SQL do Azure.

Instância de gerida de base de dados de SQL do Azure tem de ser implementada dentro do Azure [rede virtual](../virtual-network/virtual-networks-overview.md). Esta implementação permite que os seguintes cenários:

- Proteger o endereço IP privado
- Ligar-se para uma instância gerida diretamente a partir de uma rede no local
- Ligar uma instância gerida para o servidor ligado ou de outra-arquivo de dados no local
- Ligar uma instância gerida nos recursos do Azure  

> [!Note]
> Deve [determinar o tamanho da sub-rede da instância gerida](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implementar a primeira instância. Não pode redimensionar a sub-rede depois que coloque os recursos no interior.
>
> Se planeia utilizar uma rede virtual existente, terá de modificar essa configuração de rede para acomodar a sua instância gerida. Para obter mais informações, consulte [modificar uma rede virtual existente para a instância gerida](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil para criar e configurar uma rede virtual é usar um modelo de implementação Azure Resource Manager.

1. Inicie sessão no Portal do Azure.

2. Selecione o **implementar no Azure** botão:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botão abre um formulário que pode utilizar para configurar o ambiente de rede onde pode implementar a instância gerida.

   > [!Note]
   > Este modelo do Azure Resource Manager irá implementar uma rede virtual com duas sub-redes. Uma sub-rede, chamada **ManagedInstances**, está reservado para a instância gerida e tem uma tabela de rotas pré-configurada. A sub-rede, chamada **predefinido**, é utilizada para outros recursos que devem acessar a instância gerida (por exemplo, máquinas virtuais do Azure).

3. Configure o ambiente de rede. No formulário seguinte, pode configurar parâmetros do seu ambiente de rede:

   ![Modelo do Resource Manager para configurar a rede do Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Pode alterar os nomes de rede virtual e sub-redes e ajustar os intervalos IP associados com os seus recursos de rede. Depois de selecionar o **Compra** botão, este formulário irá criar e configurar o seu ambiente. Se não precisar de duas sub-redes, pode eliminar predefinido.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a instância gerida](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de dados, consulte [criar um Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas DNS, consulte [configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
