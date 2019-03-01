---
title: Restaurar a cópia de segurança automática, online e dados a pedido no Azure Cosmos DB
description: Este artigo descreve como restaurar a cópia de segurança automática, online e dados a pedido funciona no Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991823"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Restaurar a cópia de segurança online e dados a pedido no Azure Cosmos DB

O Azure Cosmos DB tira automaticamente cópias de segurança dos seus dados em intervalos regulares. As cópias de segurança automáticas são executadas sem afetar o desempenho ou a disponibilidade das operações de base de dados. Todas as cópias de segurança são armazenadas separadamente num serviço de armazenamento, e essas cópias de segurança são replicadas globalmente para resiliência contra desastres regionais. As cópias de segurança automáticas são úteis em cenários quando acidentalmente eliminar ou atualizar a sua conta do Cosmos do Azure, a base de dados ou o contentor e exigir mais tarde a recuperação de dados.

## <a name="automatic-and-online-backups"></a>Cópias de segurança automáticas e online

Com o Azure Cosmos DB, não apenas seus dados, mas também as cópias de segurança dos seus dados são altamente redundantes e resiliente a desastres regionais. Os passos seguintes mostram como o Azure Cosmos DB executa a cópia de segurança de dados:

* O Azure Cosmos DB tira automaticamente uma cópia de segurança da base de dados a cada 4 horas e em qualquer momento, apenas as mais recentes 2 cópias de segurança são armazenadas. No entanto, se o contentor ou a base de dados é eliminado, o Azure Cosmos DB retém os instantâneos existentes de um dado contêiner ou a base de dados durante 30 dias.

* O Azure Cosmos DB armazena essas cópias de segurança no armazenamento de Blobs do Azure, ao passo que os dados reais residem localmente no Azure Cosmos DB.

*  Para garantir a baixa latência, o instantâneo da cópia de segurança é armazenado no armazenamento de Blobs do Azure na mesma região que a região de escrita atual (ou uma das regiões de escrita, no caso de ter uma configuração de vários mestre) do seu Cosmos do Azure conta de base de dados. Para resiliência contra desastre regional, cada instantâneo dos dados de cópia de segurança no armazenamento de Blobs do Azure novamente é replicado para outra região, através do armazenamento georredundante (GRS). A região para que a cópia de segurança é replicada baseia-se a sua região de origem e o par regional associado com a região de origem. Para obter mais informações, consulte a [lista de pares georredundante de regiões do Azure](../best-practices-availability-paired-regions.md) artigo. Não pode aceder diretamente a esta cópia de segurança. O Azure Cosmos DB irá utilizar esta cópia de segurança apenas se um restauro de cópia de segurança é iniciado.

* As cópias de segurança são efetuadas sem afetar o desempenho ou disponibilidade da sua aplicação. O Azure Cosmos DB executa a cópia de segurança de dados em segundo plano sem consumir qualquer débito aprovisionado adicional (RUs) ou que afetam o desempenho e a disponibilidade da base de dados.

* Se tiver eliminado acidentalmente ou danificado seus dados, deve contactar [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa do Azure Cosmos DB pode ajudar a restaurar os dados de cópias de segurança.

A imagem seguinte mostra como um contentor do Cosmos do Azure com todas as três principais físicos partições na região E.U.A. oeste é uma cópia de segurança numa conta de armazenamento de Blobs do Azure remota na região E.U.A. oeste e, em seguida, replicado para E.U. a leste:

![Cópias de segurança completas periódicas de todas as entidades do Cosmos DB GRS do armazenamento do Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opções para gerir as suas próprias cópias de segurança

Com as contas de API de SQL do Azure Cosmos DB, também pode manter suas próprias cópias de segurança ao utilizar uma das seguintes abordagens:

* Uso [do Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados periodicamente para um armazenamento à sua escolha.

* Utilizar o Azure Cosmos DB [feed de alterações](change-feed.md) para ler dados periodicamente para cópias de segurança completas, bem como para alterações incrementais e armazená-lo no seu armazenamento.

## <a name="backup-retention-period"></a>Período de retenção de cópia de segurança

O Azure Cosmos DB tira instantâneos dos seus dados a cada quatro horas. Em qualquer momento, apenas os últimos dois instantâneos são retidos. No entanto, se o contentor ou a base de dados é eliminado, o Azure Cosmos DB retém os instantâneos existentes de um dado contêiner ou a base de dados durante 30 dias.

## <a name="restoring-data-from-online-backups"></a>Restauração de dados de cópias de segurança online

A eliminação acidental ou modificação de dados pode acontecer em um dos seguintes cenários:  

* Toda a conta do Cosmos do Azure é eliminada

* Um ou mais bases de dados do Cosmos do Azure são eliminados

* Um ou mais contentores do Azure Cosmos são eliminados

* Itens de Cosmos do Azure (por exemplo, documentos) dentro de um contêiner são eliminados ou modificados. Nesse caso específico, normalmente, é referido como "Corrupção de dados".

* Uma base de dados de oferta partilhado ou contentores dentro de uma base de dados partilhado oferta são eliminados ou danificados

O Azure Cosmos DB pode restaurar dados em todos os cenários acima. O processo de restauração sempre cria uma nova conta do Cosmos do Azure para armazenar os dados restaurados. O nome da nova conta, se não for especificado, tem de ter o formato `<Azure_Cosmos_account_original_name>-restored1`. O último dígito é incrementado, se vários restauros são tentados. Não é possível restaurar dados para uma conta do Cosmos do Azure pré-criadas.

Quando uma conta do Cosmos do Azure é eliminada, é possível restaurar os dados para uma conta com o mesmo nome, desde que o nome da conta não está em utilização. Nesses casos, é recomendado não voltar a criar a conta após a eliminação, uma vez que ela não só impede que os dados restaurados para utilizar o mesmo nome, mas também torna a detetar a conta correta para restaurar a partir de mais difícil. 

Quando uma base de dados do Cosmos do Azure é eliminada, é possível restaurar a base de dados inteira ou um subconjunto dos contentores nesse banco de dados. Também é possível selecionar contentores em bases de dados e restaurá-los e todos os dados restaurados são colocados numa nova conta do Cosmos do Azure.

Quando um ou mais itens dentro de um contêiner sejam excluídos acidentalmente ou alterado (o caso de Corrupção de dados), terá de especificar o tempo de restauro para. O tempo é de essência para este caso. Uma vez que o contentor está em direto, a cópia de segurança ainda está em execução, portanto, se esperar para além do período de retenção (a predefinição é de oito horas) as cópias de segurança serão substituídas. No caso de eliminações, os dados já não são armazenados uma vez que eles não sejam substituídos pelo ciclo de cópia de segurança. Cópias de segurança para bases de dados eliminadas ou contentores são guardadas durante 30 dias.

Se aprovisionar o débito ao nível da base de dados (ou seja, em que um conjunto de contentores compartilha o débito aprovisionado), o processo de backup e restauração em vez disso acontecer ao nível da base de dados completa e não ao nível do contentores individuais. Nesses casos, selecionar um subconjunto de contentores para restaurar não é uma opção.

## <a name="migrating-data-to-the-original-account"></a>Migrar dados para a conta original

O principal objetivo do restauro de dados é fornecer uma forma de recuperar dados que excluir ou modificar acidentalmente. Por isso, recomendamos que primeiro inspecionar o conteúdo dos dados recuperados para garantir que contém o que esperava. Em seguida, trabalhe sobre como migrar os dados de volta para a conta principal. Embora seja possível usar a conta restaurada como a conta em direto, não é uma opção recomendada se tiver cargas de trabalho de produção.  

Seguem-se diferentes formas para migrar dados de volta para a conta do Cosmos do Azure original:

* Usando [ferramenta de migração de dados do Cosmos DB](import-data.md)
* Usando [fábrica de dados do Azure]( ../data-factory/connector-azure-cosmos-db.md)
* Usando [feed de alterações](change-feed.md) no Azure Cosmos DB 
* Escrever código personalizado

Eliminar o restaurada contas assim que tiver concluído migração, porque eles incorre em custos contínuos.

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode saber mais sobre como restaurar dados a partir de uma conta do Cosmos do Azure ou saiba como migrar dados para uma conta do Cosmos do Azure

* Para fazer uma restauração do pedido, contacte o suporte do Azure, [um ticket do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados a partir de uma conta do Cosmos do Azure](how-to-backup-and-restore.md)
* [Utilize o Cosmos DB alterar feed](change-feed.md) para mover dados para o Azure Cosmos DB.
* [Utilize o Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.

