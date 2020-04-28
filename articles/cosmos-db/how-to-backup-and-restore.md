---
title: Como restaurar os dados do Azure Cosmos DB a partir de uma cópia de segurança
description: Este artigo descreve como restaurar os dados do Azure Cosmos DB a partir de uma cópia de segurança, como contactar o suporte do Azure para restaurar os dados, medidas a tomar após a restauração dos dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70240769"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurar os dados de uma cópia de segurança em Azure Cosmos DB 

Se eliminar acidentalmente a sua base de dados ou um recipiente, pode [arquivar um bilhete]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) de apoio ou ligar para o suporte do [Azure]( https://azure.microsoft.com/support/options/) para restaurar os dados de cópias de segurança online automáticas. O suporte azure está disponível para planos selecionados apenas como **Standard**, **Developer,** e planos superiores a eles. O suporte azure não está disponível com plano **Básico.** Para conhecer diferentes planos de apoio, consulte a página de planos de [apoio do Azure.](https://azure.microsoft.com/support/plans/) 

Para restaurar uma imagem específica da cópia de segurança, o Azure Cosmos DB requer que os dados estão disponíveis durante a duração do ciclo de backup para esse instantâneo.

## <a name="request-a-restore"></a>Solicite uma restauração

Deve ter os seguintes detalhes antes de solicitar um restauro:

* Tenha o seu ID de subscrição pronto.

* Com base na forma como os seus dados foram acidentalmente eliminados ou modificados, deve preparar-se para ter informações adicionais. É aconselhável ter a informação disponível para minimizar o back-and-forth que pode ser prejudicial em alguns casos sensíveis ao tempo.

* Se toda a conta Azure Cosmos DB for eliminada, terá de fornecer o nome da conta eliminada. Se criar outra conta com o mesmo nome da conta eliminada, partilhe-a com a equipa de suporte porque ajuda a determinar a conta certa para escolher. Recomenda-se arquivar diferentes bilhetes de apoio para cada conta apagada porque minimiza a confusão do estado do restauro.

* Se uma ou mais bases de dados forem eliminadas, deverá fornecer a conta Azure Cosmos, bem como os nomes da base de dados Azure Cosmos e especificar se existe uma nova base de dados com o mesmo nome.

* Se um ou mais contentores forem eliminados, deverá fornecer o nome da conta Azure Cosmos, nomes de bases de dados e os nomes dos contentores. E especifique se existe um recipiente com o mesmo nome.

* Se tiver acidentalmente eliminado ou corrompido os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa do Azure Cosmos DB possa ajudá-lo a restaurar os dados das cópias de segurança.
  
  * Se tiver apagado acidentalmente a sua base de dados ou contentor, abra um caso de suporte Sev B ou Sev C Azure. 
  * Se tiver acidentalmente apagado ou corrompido alguns documentos dentro do contentor, abra um caso de apoio sev A. 

Quando ocorrer a corrupção de dados e se os documentos dentro de um contentor forem modificados ou eliminados, **elimine o recipiente o mais rapidamente possível**. Ao apagar o recipiente, pode evitar que o Azure Cosmos DB sobreare as cópias de segurança. Se por alguma razão a eliminação não for possível, deverá apresentar um bilhete o mais rapidamente possível. Além do nome da conta Azure Cosmos, nomes de bases de dados, nomes de contentores, deve especificar o ponto no tempo para o qual os dados podem ser restaurados. É importante ser mosso o mais preciso possível para nos ajudar a determinar os melhores backups disponíveis nessa altura. Também é importante especificar o tempo na UTC. 

A imagem que se segue ilustra como criar um pedido de suporte para um recipiente (recolha/gráfico/tabela) para restaurar os dados utilizando o portal Azure. Forneça detalhes adicionais, tais como o tipo de dados, finalidade da restauração, tempo em que os dados foram eliminados para nos ajudar a priorizar o pedido.

![Criar um pedido de apoio de backup utilizando o portal Azure](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Ações pós-restauro

Depois de restaurar os dados, recebe uma notificação sobre o nome da `<original-name>-restored1`nova conta (normalmente está no formato) e o momento em que a conta foi restaurada. A conta restaurada terá o mesmo rendimento provisionado, indexando políticas e encontra-se na mesma região que a conta original. Um utilizador que seja o administrador de subscrição ou um coadminpode ver a conta restaurada.

Depois de restaurados os dados, deve inspecionar e validar os dados na conta restaurada e certificar-se de que contém a versão que espera. Se tudo correr bem, deverá migrar os dados de volta para a sua conta original utilizando o feed de [mudança de Db Da Azure Cosmos](change-feed.md) ou a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

É aconselhável eliminar o recipiente ou a base de dados imediatamente após a migração dos dados. Se não eliminar as bases de dados ou contentores restaurados, incorrerá em custos para unidades de pedido, armazenamento e saída.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode aprender como migrar os dados de volta para a sua conta original usando os seguintes artigos:

* Para fazer um pedido de restauro, contacte o Suporte Azure, [preencha um bilhete do portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* Use o feed de [mudança de Db cosmos](change-feed.md) para mover dados para O BD Do Cosmos.

* Utilize a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.
