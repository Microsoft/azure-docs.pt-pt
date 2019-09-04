---
title: Como restaurar dados de Azure Cosmos DB de um backup
description: Este artigo descreve como restaurar dados de Azure Cosmos DB de um backup, como entrar em contato com o suporte do Azure para restaurar dados, etapas a serem executadas depois que os dados forem restaurados.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240769"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar dados de um backup no Azure Cosmos DB 

Se você excluir acidentalmente seu banco de dados ou um contêiner, poderá [arquivar um tíquete de suporte]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [chamar o suporte do Azure]( https://azure.microsoft.com/support/options/) para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como **Standard**, **Developer**e Plans superiores. O suporte do Azure não está disponível com o plano **básico** . Para saber mais sobre diferentes planos de suporte, consulte a página [planos de suporte do Azure](https://azure.microsoft.com/support/plans/) . 

Para restaurar um instantâneo específico do backup, Azure Cosmos DB requer que os dados estejam disponíveis durante o ciclo de backup para esse instantâneo.

## <a name="request-a-restore"></a>Solicitar uma restauração

Você deve ter os seguintes detalhes antes de solicitar uma restauração:

* Prepare sua ID de assinatura.

* Com base em como os dados foram acidentalmente excluídos ou modificados, você deve se preparar para ter informações adicionais. É recomendável que você tenha as informações disponíveis para minimizar o apoio que podem ser prejudiciais em alguns casos sensíveis ao tempo.

* Se toda a conta de Azure Cosmos DB for excluída, você precisará fornecer o nome da conta excluída. Se você criar outra conta com o mesmo nome da conta excluída, compartilhe-a com a equipe de suporte, pois ela ajuda a determinar a conta certa a ser escolhida. É recomendável arquivar tíquetes de suporte diferentes para cada conta excluída porque minimiza a confusão do estado da restauração.

* Se um ou mais bancos de dados forem excluídos, você deverá fornecer a conta do Azure Cosmos, bem como os nomes do banco de dados Cosmos do Azure e especificar se existe um novo banco de dados com o mesmo nome.

* Se um ou mais contêineres forem excluídos, você deverá fornecer o nome da conta do Azure Cosmos, os nomes de banco de dados e os nomes de contêiner. E especifique se existe um contêiner com o mesmo nome.

* Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups.
  
  * Se você excluiu acidentalmente seu banco de dados ou contêiner, abra um caso de suporte do Azure Sev B ou Sev C. 
  * Se você tiver excluído ou corrompido acidentalmente alguns documentos no contêiner, abra um Sev um caso de suporte. 

Quando ocorrer corrupção de dados e se os documentos dentro de um contêiner forem modificados ou excluídos, **exclua o contêiner assim que possível**. Ao excluir o contêiner, você pode evitar Azure Cosmos DB de substituir os backups. Se, por algum motivo, a exclusão não for possível, você deverá arquivar um tíquete assim que possível. Além do nome da conta do Azure Cosmos, nomes de banco de dados, nomes de contêiner, você deve especificar o ponto no tempo para o qual os dados podem ser restaurados. É importante ser o mais preciso possível para nos ajudar a determinar os melhores backups disponíveis naquele momento. Também é importante especificar a hora em UTC. 

A captura de tela a seguir ilustra como criar uma solicitação de suporte para um contêiner (coleção/grafo/tabela) para restaurar dados usando portal do Azure. Forneça detalhes adicionais, como o tipo de dados, a finalidade da restauração, a hora em que os dados foram excluídos para nos ajudar a priorizar a solicitação.

![Criar uma solicitação de suporte de backup usando o portal do Azure](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Ações de pós-restauração

Depois de restaurar os dados, você receberá uma notificação sobre o nome da nova conta (normalmente no formato `<original-name>-restored1`) e a hora em que a conta foi restaurada. A conta restaurada terá a mesma taxa de transferência provisionada, políticas de indexação e está na mesma região que a conta original. Um usuário que é o administrador da assinatura ou um coadministrador pode ver a conta restaurada.

Depois que os dados são restaurados, você deve inspecionar e validar os dados na conta restaurada e certificar-se de que ele contém a versão esperada. Se tudo estiver correto, você deverá migrar os dados de volta para sua conta original usando [Azure Cosmos DB feed de alterações](change-feed.md) ou [Azure data Factory](../data-factory/connector-azure-cosmos-db.md).

É recomendável que você exclua o contêiner ou o banco de dados imediatamente após a migração. Se você não excluir os bancos de dados ou contêineres restaurados, eles incorrerão em custo para unidades de solicitação, armazenamento e saída.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, você pode aprender sobre como migrar os dados de volta para sua conta original usando os seguintes artigos:

* Para fazer uma solicitação de restauração, entre em contato com o suporte do Azure, [arquivar um tíquete do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Use Cosmos DB o feed de alterações](change-feed.md) para mover dados para Azure Cosmos DB.

* [Use Azure data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para Azure Cosmos DB.
