---
title: Excluir uma sub-rede depois de excluir uma instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Saiba como excluir uma rede virtual do Azure depois de excluir uma instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 7ad09682275b5cc2311b792899a85c1c47eafc0d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431301"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Excluir uma sub-rede depois de excluir uma instância gerenciada do banco de dados SQL do Azure

Este artigo fornece diretrizes sobre como excluir uma sub-rede manualmente depois de excluir a última instância gerenciada do banco de dados SQL do Azure que a reside.

As instâncias gerenciadas são implantadas em [clusters virtuais](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Cada cluster virtual é associado a uma sub-rede. O cluster virtual persiste por design por 12 horas após a última exclusão da instância para permitir que você crie com mais rapidez as instâncias gerenciadas na mesma sub-rede. Não há nenhum encargo para manter um cluster virtual vazio. Durante este período, a sub-rede associada ao cluster virtual não pode ser eliminada.

Se você não quiser aguardar 12 horas e preferir excluir o cluster virtual e sua sub-rede mais cedo, poderá fazer isso manualmente. Exclua o cluster virtual manualmente usando a portal do Azure ou a API de clusters virtuais.

> [!IMPORTANT]
> - O cluster virtual não deve conter nenhuma instância gerenciada para que a exclusão seja bem-sucedida. 
> - A exclusão de um cluster virtual é uma operação de execução longa duradoura por cerca de 1,5 horas (consulte [operações de gerenciamento de instância gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) para tempo de exclusão do cluster virtual atualizado) durante o qual o cluster virtual ainda estará visível no portal até que isso processo concluído.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Excluir o cluster virtual do portal do Azure

Para excluir um cluster virtual usando o portal do Azure, procure os recursos do cluster virtual.

![Captura de tela da portal do Azure, com a caixa de pesquisa realçada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois de localizar o cluster virtual que você deseja excluir, selecione esse recurso e selecione **excluir**. Você será solicitado a confirmar a exclusão do cluster virtual.

![Captura de tela do painel portal do Azure clusters virtuais, com a opção Excluir realçada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Portal do Azure notificações mostrarão uma confirmação de que a solicitação para excluir o cluster virtual foi enviada com êxito. A própria operação de exclusão durará por cerca de 1,5 horas durante as quais o cluster virtual ainda estará visível no Portal. Quando o processo for concluído, o cluster virtual não ficará mais visível e a sub-rede associada a ele será liberada para reutilização.

> [!TIP]
> Se não houver nenhuma instância gerenciada mostrada no cluster virtual e você não puder excluir o cluster virtual, certifique-se de que você não tenha uma implantação de instância em andamento. Isso inclui implantações iniciadas e canceladas que ainda estão em andamento. Isso ocorre porque essas operações ainda usarão o cluster virtual que o bloqueia da exclusão. Revisando a guia implantações do grupo de recursos em que a instância foi implantada indicará todas as implantações em andamento. Nesse caso, aguarde a conclusão da implantação, exclua a instância gerenciada e, em seguida, o cluster virtual.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Excluir o cluster virtual usando a API

Para excluir um cluster virtual por meio da API, use os parâmetros de URI especificados no [método de exclusão de clusters virtuais](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre a [arquitetura de conectividade em instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para instância gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar um Instância Gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte [Configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
