---
title: Como restaurar os dados do Azure Cosmos DB a partir de uma cópia de segurança
description: Este artigo descreve como restaurar os dados DB do Azure Cosmos a partir de uma cópia de segurança, como contactar o suporte do Azure para restaurar os dados, passos a tomar após a restauração dos dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797977"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar dados de uma cópia de segurança em Azure Cosmos DB

Se eliminar acidentalmente a sua base de dados ou um recipiente, pode [arquivar um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou ligar para o suporte do [Azure](https://azure.microsoft.com/support/options/) para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como **Standard,** **Developer,** e planos superiores aos. O apoio do Azure não está disponível com o plano **Básico.** Para conhecer diferentes planos de apoio, consulte a página de planos de apoio do [Azure.](https://azure.microsoft.com/support/plans/)

Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo.

## <a name="request-a-restore"></a>Pedir um restauro

Deve ter os seguintes detalhes antes de solicitar uma restauração:

* Tenha o seu ID de assinatura pronto.

* Com base na forma como os seus dados foram acidentalmente eliminados ou modificados, deverá preparar-se para ter informações adicionais. É aconselhável que tenha a informação disponível para minimizar o retrocesso que pode ser prejudicial em alguns casos sensíveis.

* Se toda a conta DB da Azure Cosmos for eliminada, tem de fornecer o nome da conta eliminada. Se criar outra conta com o mesmo nome que a conta eliminada, partilhe-a com a equipa de suporte porque ajuda a determinar a conta certa para escolher. Recomenda-se arquivar diferentes bilhetes de apoio para cada conta apagada porque minimiza a confusão para o estado de restauração.

* Se uma ou mais bases de dados forem eliminadas, deverá fornecer a conta Azure Cosmos, bem como os nomes da base de dados Azure Cosmos e especificar se existe uma nova base de dados com o mesmo nome.

* Se um ou mais contentores forem eliminados, deve fornecer o nome da conta Azure Cosmos, nomes de bases de dados e os nomes dos contentores. E especificar se existe um recipiente com o mesmo nome.

* Se acidentalmente eliminou ou corrompeu os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa DB do Azure Cosmos possa ajudá-lo a restaurar os dados das cópias de segurança. **Antes de criar um pedido de apoio para restaurar os dados, certifique-se de [aumentar a retenção de backup](online-backup-and-restore.md) da sua conta para pelo menos sete dias. É melhor aumentar a sua retenção dentro de 8 horas deste evento.** Desta forma, a equipa de apoio do Azure Cosmos terá tempo suficiente para restaurar a sua conta.

Além do nome da conta Azure Cosmos, nomes de bases de dados, nomes de contentores, deve especificar o ponto a tempo para o qual os dados podem ser restaurados. É importante sermos o mais precisos possível para nos ajudar a determinar as melhores cópias de segurança disponíveis nessa altura. **Também é importante especificar o tempo na UTC.**

A imagem que se segue ilustra como criar um pedido de suporte para um recipiente (recolha/gráfico/tabela) para restaurar os dados utilizando o portal Azure. Fornecer detalhes adicionais como tipo de dados, finalidade da restauração, tempo em que os dados foram eliminados para nos ajudar a priorizar o pedido.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Crie um pedido de suporte de backup utilizando o portal Azure":::

## <a name="post-restore-actions"></a>Ações pós-restauro

Depois de restaurar os dados, recebe uma notificação sobre o nome da nova conta (normalmente está no formato) e o momento em que `<original-name>-restored1` a conta foi restaurada. A conta restaurada terá a mesma produção provisida, políticas de indexação e está na mesma região que a conta original. Um utilizador que seja o administrador de subscrição ou um coadministrador pode ver a conta restaurada.

Após a restauração dos dados, deverá inspecionar e validar os dados na conta restaurada e certificar-se de que contém a versão que está à espera. Se tudo estiver bem, deverá migrar os dados de volta para a sua conta original utilizando [o feed de alteração DB Azure Cosmos](change-feed.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

É aconselhável eliminar o recipiente ou base de dados imediatamente após a migração dos dados. Se não eliminar as bases de dados ou contentores restaurados, incorrerão em custos para unidades de pedido, armazenamento e saída.

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá aprender como migrar os dados de volta para a sua conta original utilizando os seguintes artigos:

* Para fazer um pedido de restauro, contacte o Azure Support, [arquive um bilhete a partir do portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para Azure Cosmos DB.

* [Utilize a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para a Azure Cosmos DB.
