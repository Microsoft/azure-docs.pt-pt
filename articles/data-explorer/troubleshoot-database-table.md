---
title: Falha ao criar ou eliminar uma base de dados ou tabela no Explorador de dados do Azure
description: Este artigo descreve os passos de resolução de problemas para criar e eliminar bases de dados e tabelas no Explorador de dados do Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0bd9f5ed00193aa9f872287b6afaf9e2b28c24dc
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188344"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Resolução de problemas: Falha ao criar ou eliminar uma base de dados ou tabela no Explorador de dados do Azure

No Explorador de dados do Azure, regularmente trabalhar com bancos de dados e tabelas. Este artigo fornece passos de resolução de problemas para problemas que podem surgir.

## <a name="creating-a-database"></a>Criar uma base de dados

1. Certifique-se de que tem as permissões adequadas. Para criar uma base de dados, tem de ser um membro do *contribuinte* ou *proprietário* função para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que eles podem adicioná-lo à função adequada.

1. Certifique-se de que não há nenhum erro de validação de nome para o nome de base de dados. O nome tem de ser alfanumérico, com um comprimento máximo de 260 carateres.

1. Certifique-se de que o período de retenção da base de dados e a colocação em cache valores estão dentro dos intervalos permissíveis. Retenção tem de ser entre 1 e 36500 dias (100 anos). Colocação em cache tem de ser entre 1 e o valor máximo definido para a retenção.

## <a name="deleting-or-renaming-a-database"></a>A eliminar ou mudar o nome de uma base de dados

Certifique-se de que tem as permissões adequadas. Para eliminar ou mudar o nome de uma base de dados, tem de ser um membro do *contribuinte* ou *proprietário* função para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que eles podem adicioná-lo à função adequada.

## <a name="creating-a-table"></a>Criar uma tabela

1. Certifique-se de que tem as permissões adequadas. Para criar uma tabela, tem de ser um membro do *administrador da base de dados* ou *utilizador de base de dados* função na base de dados ou o *contribuinte* ou *proprietário* função na subscrição do Azure. Se for necessário, trabalhar com a sua subscrição ou administrador de cluster para que eles podem adicioná-lo à função adequada.

    Para obter mais informações sobre as permissões, consulte [gerir permissões de base de dados](manage-database-permissions.md).

1. Certifique-se de que ainda não existe uma tabela com o mesmo nome. Se existir, em seguida, pode: Criar uma tabela com um nome diferente; mudar o nome da tabela existente (requer *administrador da tabela* função); ou remover a tabela existente (requer *administrador da base de dados* função). Utilize os seguintes comandos.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>A eliminar ou mudar o nome de uma tabela

Certifique-se de que tem as permissões adequadas. Para eliminar ou mudar o nome de uma tabela, tem de ser um membro do *administrador da base de dados* ou *tabela administrador* função na base de dados. Se for necessário, trabalhar com a sua subscrição ou administrador de cluster para que eles podem adicioná-lo à função adequada.

Para obter mais informações sobre as permissões, consulte [gerir permissões de base de dados](manage-database-permissions.md).

## <a name="general-guidance"></a>Documentação de orientação geral

1. Verifique os [dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Procure o estado do Explorador de dados do Azure na região onde está a tentar trabalhar com uma base de dados ou tabela.

    Se o estado não estiver **boa** (marca de verificação verde), tente novamente depois do Estado melhora.

1. Se ainda precisar de assistência para resolver seu problema, abra um pedido de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Passos Seguintes

[Verificar estado de funcionamento do cluster](check-cluster-health.md)