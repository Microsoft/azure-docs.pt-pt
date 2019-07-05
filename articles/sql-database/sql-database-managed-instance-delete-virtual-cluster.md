---
title: Eliminar uma sub-rede, depois de eliminar uma base de dados do SQL do Azure a instância gerida | Documentos da Microsoft
description: Saiba como eliminar uma rede virtual do Azure após a eliminação de uma base de dados do SQL do Azure a instância gerida.
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427978"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Eliminar uma sub-rede, depois de eliminar uma base de dados do SQL do Azure a instância gerida

Este artigo fornece diretrizes sobre como eliminar manualmente uma sub-rede depois da eliminar a base de dados do SQL Azure última gerido a instância que residem no mesmo.

Base de dados SQL utiliza um [virtual cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) para conter a instância gerida eliminada. O cluster virtual persiste durante 12 horas após a eliminação de instância, que lhe permite criar rapidamente instâncias geridas na mesma sub-rede. Não existe nenhum custo associado para manter um cluster virtual vazio. Durante este período, a sub-rede associada ao cluster virtual não pode ser eliminada.

Se não quiser esperar 12 horas, e preferir ao eliminar o cluster virtual e a respetiva sub-rede imediatamente, pode fazer isso manualmente. Elimine o cluster virtual manualmente com o portal do Azure ou os API de clusters virtuais.

> [!NOTE]
> O cluster virtual não deve conter nenhum instâncias geridas para a eliminação seja concluída com êxito.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Eliminar o virtual cluster do portal do Azure

Para eliminar um cluster virtual com o portal do Azure, procure os recursos de virtual cluster.

![Captura de ecrã do portal do Azure, com a caixa de pesquisa realçado](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois de localizar o cluster virtual que pretende eliminar, selecione esse recurso e selecione **eliminar**. Lhe for pedido para confirmar a eliminação de virtual cluster.

![Captura de ecrã do portal do Azure Virtual clusters do dashboard, com a opção de eliminação realçada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

A área de notificações do portal do Azure mostra-lhe confirmação de que o cluster virtual foi eliminado. Eliminação concluída com êxito do virtual cluster versões imediatamente a sub-rede para reutilização.

> [!TIP]
> Se nenhuma instância gerida mostrada o cluster virtual, e não é possível eliminar o cluster virtual, certifique-se de que não tem uma implementação de instância em curso em curso. Isto inclui implementações de introdução e foi canceladas que estão em curso. Rever as implementações de separador do grupo de recursos a instância foi implementada para irá indicar todas as implementações em curso. Neste caso, await para a implementação concluir, elimine a instância gerida e, em seguida, o cluster virtual.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminar o virtual cluster, utilizando a API

Para eliminar um cluster virtual por meio da API, utilize os parâmetros do URI especificados na [clusters virtuais Eliminar método](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a instância gerida](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de dados, consulte [criar um Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas DNS, consulte [configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
