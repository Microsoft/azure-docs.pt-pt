---
title: Falha na criação ou exclusão de DB ou tabela no Azure Data Explorer
description: Este artigo descreve passos de resolução de problemas para a criação e aeliminar bases de dados e tabelas no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562399"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Resolução de problemas: Falha na criação ou exclusão de uma base de dados ou de uma tabela no Azure Data Explorer

No Azure Data Explorer, trabalha regularmente com bases de dados e tabelas. Este artigo fornece passos de resolução de problemas para questões que podem surgir.

## <a name="creating-a-database"></a>Criar uma base de dados

1. Verifique se tem as permissões adequadas. Para criar uma base de dados, deve ser membro da função *De Contribuinte* ou *Proprietário* para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que possam adicioná-lo ao papel adequado.

1. Certifique-se de que não existem erros de validação de nomes para o nome da base de dados. O nome deve ser alfanumérico, com um comprimento máximo de 260 caracteres.

1. Certifique-se de que os valores de retenção e cache da base de dados estão dentro dos intervalos permitidos. A retenção deve ser entre 1 e 36500 dias (100 anos). O caching deve estar entre 1 e o valor máximo definido para a retenção.

## <a name="deleting-or-renaming-a-database"></a>Apagar ou renomear uma base de dados

Verifique se tem as permissões adequadas. Para eliminar ou renomear uma base de dados, deve ser membro da função *De Contribuinte* ou *Proprietário* para a subscrição Azure. Se necessário, trabalhe com o seu administrador de subscrição para que possam adicioná-lo ao papel adequado.

## <a name="creating-a-table"></a>Criar uma mesa

1. Verifique se tem as permissões adequadas. Para criar uma tabela, deve ser membro da *função* de utilizador da base de dados ou *da base* de dados na base de dados ou na função *De Contribuinte* ou *Proprietário* para a subscrição do Azure. Se necessário, trabalhe com a sua subscrição ou administrador de cluster para que possam adicioná-lo ao papel adequado.

    Para mais informações sobre permissões, consulte [Gerir permissões de bases de dados](manage-database-permissions.md).

1. Certifique-se de que uma mesa com o mesmo nome já não existe. Se existir, então pode: Criar uma tabela com um nome diferente; renomear a tabela existente (requer função de administrador de *tabela);* ou deixar cair a tabela existente (requer papel de administrador de base de *dados).* Utilize os seguintes comandos.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Apagar ou renomear uma mesa

Verifique se tem as permissões adequadas. Para eliminar ou renomear uma tabela, deve ser membro da *função* de administrador a partir de base de dados ou de administração de *tabelas* na base de dados. Se necessário, trabalhe com a sua subscrição ou administrador de cluster para que possam adicioná-lo ao papel adequado.

Para mais informações sobre permissões, consulte [Gerir permissões de bases de dados](manage-database-permissions.md).

## <a name="general-guidance"></a>Documentação de orientação geral

1. Consulte o [dashboard do estado de funcionamento do serviço Azure](https://azure.microsoft.com/status/) Procure o estado do Azure Data Explorer na região onde está a tentar trabalhar com uma base de dados ou tabela.

    Se o estado não for **bom** (marca de verificação verde), tente novamente depois de o estado melhorar.

1. Se ainda precisar de assistência para resolver o seu problema, por favor abra um pedido de apoio no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Passos seguintes

[Verificar estado de funcionamento do cluster](check-cluster-health.md)