---
title: Eliminar uma sub-rede depois de eliminar uma instância gerida de SQL Gestd Instance
description: Aprenda a eliminar uma rede virtual Azure depois de eliminar uma instância gerida de Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 0b8ceee1260e4209420778d7f327d771b858a899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617728"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Eliminar uma sub-rede depois de eliminar uma instância gerida de SQL Gestd Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece diretrizes sobre como apagar manualmente uma sub-rede depois de eliminar a última instância gerida de Azure SQL Managed Instance residente no mesmo.

As instâncias geridas são implantadas em [aglomerados virtuais.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) Cada cluster virtual está associado a uma sub-rede. O cluster virtual persiste por design durante 12 horas após a eliminação da última instância para que possa criar mais rapidamente casos geridos na mesma sub-rede. Não há nenhuma acusação para manter um aglomerado virtual vazio. Durante este período, a sub-rede associada ao cluster virtual não pode ser eliminada.

Se não quiser esperar 12 horas e preferir apagar o cluster virtual e a sua sub-rede mais cedo, pode fazê-lo manualmente. Elimine manualmente o cluster virtual utilizando o portal Azure ou a API de Clusters Virtuais.

> [!IMPORTANT]
> - O cluster virtual não deve conter casos geridos para que a supressão seja bem sucedida. 
> - A eliminação de um cluster virtual é uma operação de longa duração com duração de cerca de 1,5 horas (ver operações de gestão de [instâncias geridas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) para o tempo de eliminação de clusters virtuais atualizado). O cluster virtual ainda estará visível no portal até que este processo esteja concluído.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Eliminar um cluster virtual do portal Azure

Para eliminar um cluster virtual utilizando o portal Azure, procure os recursos de cluster virtual.

![Screenshot do portal Azure, com caixa de pesquisa em destaque](./media/virtual-cluster-delete/virtual-clusters-search.png)

Depois de localizar o cluster virtual que pretende eliminar, selecione este recurso e selecione **Delete**. Foi-lhe pedido que confirmasse a eliminação virtual do aglomerado.

![Screenshot do portal Azure Painel de agrupamentos virtuais, com a opção Eliminar em destaque](./media/virtual-cluster-delete/virtual-clusters-delete.png)

As notificações do portal Azure mostrar-lhe-ão a confirmação de que o pedido de eliminação do cluster virtual foi submetido com sucesso. A própria operação de eliminação terá a duração de cerca de 1,5 horas, durante as quais o cluster virtual ainda estará visível no portal. Uma vez concluído o processo, o cluster virtual deixará de ser visível e a sub-rede associada a ele será libertada para reutilização.

> [!TIP]
> Se não houver casos geridos mostrados no cluster virtual, e não conseguir eliminar o cluster virtual, certifique-se de que não tem uma implementação em curso em curso. Isto inclui implementações iniciadas e canceladas que ainda estão em curso. Isto porque estas operações continuarão a utilizar o cluster virtual, bloqueando-o da eliminação. Ao rever o separador **Implementações** do grupo de recursos, a instância foi implementada para indicar quaisquer implementações em curso. Neste caso, aguarde que a implementação esteja concluída, elimine a instância gerida e, em seguida, elimine o cluster virtual.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Eliminar um cluster virtual utilizando a API

Para eliminar um cluster virtual através da API, utilize os parâmetros URI especificados no [método de eliminação](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)de clusters virtuais .

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Conheça a [arquitetura de conectividade em SQL Managed Instance](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para o SQL Managed Instance](vnet-existing-add-subnet.md).
- Para um tutorial que mostre como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de [dados,](instance-create-quickstart.md)consulte Criar um caso gerido .
- Para questões dns, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
