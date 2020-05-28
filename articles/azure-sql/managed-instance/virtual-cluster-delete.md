---
title: Eliminar uma subnet após eliminar uma instância gerida azure SQL
description: Aprenda a eliminar uma rede virtual Azure depois de eliminar uma Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 73150ee893ca23ed7996b001bd02acaabbf89dce
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116677"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Eliminar uma subnet após eliminar uma instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece orientações sobre como apagar manualmente uma subnet após eliminar a última Instância Gerida Azure SQL que nela reside.

As instâncias geridas são implantadas em [clusters virtuais.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) Cada cluster virtual está associado a uma sub-rede. O cluster virtual persiste por design durante 12 horas após a eliminação da última instância para permitir criar mais rapidamente casos geridos na mesma subnet. Não há nenhuma acusação por manter um aglomerado virtual vazio. Durante este período, a sub-rede associada ao cluster virtual não pode ser eliminada.

Se não quiser esperar 12 horas e preferir apagar o cluster virtual e a sua subnet mais cedo, pode fazê-lo manualmente. Elimine manualmente o cluster virtual utilizando o portal Azure ou os clusters virtuais API.

> [!IMPORTANT]
> - O cluster virtual não deve conter instâncias geridas para que a eliminação seja bem sucedida. 
> - A eliminação de um cluster virtual é uma operação de longa duração que dura cerca de 1,5 horas (ver operações de [gestão de instâncias geridas sQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) para o tempo de eliminação do cluster virtual atualizado) durante a qual o cluster virtual ainda será visível no portal até que este processo esteja concluído.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Eliminar cluster virtual do portal Azure

Para eliminar um cluster virtual utilizando o portal Azure, procure os recursos de cluster virtual.

![Screenshot do portal Azure, com caixa de pesquisa em destaque](./media/virtual-cluster-delete/virtual-clusters-search.png)

Depois de localizar o cluster virtual que pretende eliminar, selecione este recurso e selecione **Eliminar**. Foi-lhe pedido que confirmasse a eliminação do cluster virtual.

![Screenshot do portal Azure Painel de clusters virtuais, com a opção Delete em destaque](./media/virtual-cluster-delete/virtual-clusters-delete.png)

As notificações do portal Azure mostrar-lhe-ão a confirmação de que o pedido de eliminação do cluster virtual foi submetido com sucesso. A própria operação de eliminação durará cerca de 1,5 horas durante as quais o cluster virtual ainda será visível no portal. Uma vez concluído o processo, o cluster virtual deixará de ser visível e a sub-rede associada ao mesmo será libertada para reutilização.

> [!TIP]
> Se não houver casos geridos mostrados no cluster virtual, e não conseguir eliminar o cluster virtual, certifique-se de que não tem uma implementação de instância em curso. Isto inclui destacamentos iniciados e cancelados que ainda estão em curso. Isto porque estas operações continuarão a utilizar o cluster virtual que o bloqueia da eliminação. Rever o separador de implantações do grupo de recursos, a instância foi implementada para indicar quaisquer implementações em curso. Neste caso, aguarde que a implementação termine, elimine a instância gerida e, em seguida, o cluster virtual.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminar cluster virtual utilizando a API

Para eliminar um cluster virtual através da API, utilize os parâmetros URI especificados nos [clusters virtuais eliminar](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)o método de eliminação .

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [o que é um caso gerido?](sql-managed-instance-paas-overview.md)
- Conheça a [arquitetura de conectividade em Caso Gerido.](connectivity-architecture-overview.md)
- Aprenda a [modificar uma rede virtual existente para O Exemplo Gerido](vnet-existing-add-subnet.md).
- Para um tutorial que mostre como criar uma rede virtual, criar uma Instância Gerida e restaurar uma base de dados a partir de uma cópia de dados, consulte [Create a Azure SQL Managed Instance](instance-create-quickstart.md).
- Para problemas dNS, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
