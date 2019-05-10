---
title: Eliminar VNet depois da eliminar a base de dados do Azure SQL instância gerida | Documentos da Microsoft
description: Saiba como eliminar VNet depois da eliminar a base de dados do Azure SQL instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411990"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Eliminar a sub-rede depois da eliminar a base de dados do Azure SQL instância gerida

Este artigo fornece diretrizes sobre como eliminar manualmente a sub-rede depois da eliminar a base de dados do SQL Azure última gerido a instância que residem no mesmo.

O [virtual cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) que continha o eliminado a instância gerida será mantida durante 12 horas contra a eliminação de instância. O cluster virtual é mantido ativo por design para permitir a criação rápida de instâncias geridas na mesma sub-rede. Durante este período, não é possível eliminar a sub-rede associada ao cluster virtual.

Versão imediata da sub-rede utilizada por um cluster virtual vazio é possível através de eliminação manual do virtual cluster. Eliminação do virtual cluster pode ser obtida através do portal do Azure ou clusters virtuais API.

> [!NOTE]
> O cluster virtual não deve conter nenhum instâncias geridas para a eliminação seja concluída com êxito.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Eliminar o virtual cluster do portal do Azure

Para eliminar um cluster virtual através do portal do Azure, procure os recursos de virtual cluster utilizando a pesquisa interna.

![Pesquisar virtual cluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois de localizar o cluster virtual que pretende eliminar, selecione esse recurso e selecione a opção de eliminação. Será solicitado a confirmar a eliminação de virtual cluster.

![Elimine virtual cluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Confirmação de que o cluster virtual foi eliminado é fornecida nas notificações do portal do Azure. Eliminação concluída com êxito do virtual cluster versões imediatamente a sub-rede para uso posterior.

## <a name="delete-virtual-cluster-using-api"></a>Eliminar o cluster virtual com a API

Para eliminar um virtual de cluster através da utilização de API os parâmetros do URI especificados na [clusters virtuais Eliminar método](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a instância gerida](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de dados, consulte [criar um Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas DNS, consulte [configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
