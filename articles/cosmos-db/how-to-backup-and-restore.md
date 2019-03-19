---
title: Como restaurar dados do Azure Cosmos DB a partir de uma cópia de segurança
description: Este artigo descreve como restaurar dados do Azure Cosmos DB a partir de uma cópia de segurança, como contactar o suporte do Azure para restaurar os dados, os passos a efetuar depois dos dados são restaurados.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1d886e146e9e18eb735e6f88d2cb2c1a4a472924
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996873"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar dados a partir de uma cópia de segurança no Azure Cosmos DB 

Se eliminar acidentalmente a base de dados ou um contentor, pode [enviar um pedido de suporte]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [contactar o suporte do Azure]( https://azure.microsoft.com/support/options/) para restaurar os dados a partir de cópias de segurança online automáticas. Suporte do Azure só está disponível para planos selecionados como **padrão**, **programador**e planos superiores-los. Suporte do Azure não está disponível com **básica** plano. Para saber mais sobre os planos de suporte a diferentes, veja a [planos de suporte do Azure](https://azure.microsoft.com/support/plans/) página. 

Para restaurar um instantâneo específico da cópia de segurança, o Azure Cosmos DB requer que os dados estão disponíveis durante o ciclo de cópia de segurança para que o instantâneo.

## <a name="request-a-restore"></a>Pedir um restauro

Deve ter os seguintes detalhes antes de solicitar um restauro:

* Ter seu preparado para ID de subscrição.

* Com base em como seus dados foi acidentalmente eliminados ou modificados, deve se preparar para que informações adicionais. Recomenda-se que tem as informações disponíveis em frente para minimizar o back-e-estabelecido que pode ser prejudicial em alguns casos confidenciais do tempo.

* Se toda a conta do Azure Cosmos DB for eliminada, terá de fornecer o nome da conta foi eliminada. Se criar outra conta com o mesmo nome que a conta foi eliminada, partilhá-lo com a equipa de suporte porque ajuda a determinar a conta correta para escolher. É recomendado para pedidos de suporte diferente do ficheiro para cada conta eliminada porque minimiza a confusão do Estado da restauração.

* Se um ou mais bases de dados são eliminados, deve fornecer a conta do Cosmos do Azure, bem como os nomes de base de dados do Cosmos do Azure e especificar se existe uma nova base de dados com o mesmo nome.

* Se um ou mais contentores são eliminados, deve fornecer o nome de conta do Cosmos do Azure, os nomes de base de dados e os nomes de contentor. E especificar se existe um contentor com o mesmo nome.

* Se tiver eliminado acidentalmente ou danificado seus dados, deve contactar [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa do Azure Cosmos DB pode ajudar a restaurar os dados de cópias de segurança.
  
  * Se tiver eliminado acidentalmente a base de dados ou o contentor, abra um incidente de suporte de gravidade B ou a gravidade C Azure. 
  * Se o ter acidentalmente excluída ou danificada alguns documentos dentro do contêiner, abra um incidente de suporte de gravidade A. 

Quando ocorre a Corrupção de dados e se os documentos dentro de um contêiner serem modificados ou eliminados, **eliminar o contentor logo que possível**. Ao eliminar o contentor, pode evitar o Azure Cosmos DB substituam as cópias de segurança. Se por algum motivo, a eliminação não for possível, deve enviar um pedido de logo que possível. Além do nome da conta do Cosmos do Azure, os nomes de base de dados, nomes de coleções, deve especificar o ponto no tempo para que os dados possam ser restaurados. É importante usarmos o possível ajudar-na determinar as melhor cópias de segurança disponíveis nesse momento. Também é importante especificar a hora em UTC. 

Captura de ecrã seguinte ilustra como criar um pedido de suporte para um container(collection/graph/table) restaurar dados com o portal do Azure. Forneça detalhes adicionais, como o tipo de dados, o objetivo da restauração, tempo quando os dados foram eliminados para ajudar-na priorizar o pedido.

![Criar um pedido de suporte de cópia de segurança através do portal do Azure](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Ações de pós-restauro

Depois de restaurar os dados, recebe uma notificação sobre o nome da nova conta (normalmente se encontra no formato `<original-name>-restored1`) e a hora quando a conta foi restaurada para. A conta de restaurada terá o mesmo débito aprovisionado, políticas de indexação e é na mesma região que a conta original. Um utilizador que seja o administrador da subscrição ou um coadmin pode ver a conta restaurada.

Depois de restaurar os dados, deve inspecionar e validar os dados na conta restaurada e certifique-se de que contém a versão que está esperando. Se estiver tudo correto, deve migrar os dados de volta para a sua conta original com [feed de alterações do Azure Cosmos DB](change-feed.md) ou [do Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Recomenda-se que elimina o contentor ou a base de dados imediatamente depois de migrar os dados. Se não eliminar as bases de dados restauradas ou contentores, eles incorre em custos para unidades de pedido, armazenamento e saída.

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode saber mais sobre como migrar os dados de volta para a sua conta original com os seguintes artigos:

* Para fazer uma restauração do pedido, contacte o suporte do Azure, [um ticket do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Utilize o Cosmos DB alterar feed](change-feed.md) para mover dados para o Azure Cosmos DB.

* [Utilize o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.
