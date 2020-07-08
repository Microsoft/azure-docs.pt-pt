---
title: Backup on-line e restauro de dados on-demand em Azure Cosmos DB
description: Este artigo descreve como os dados automáticos, on-line e on-demand restauram os trabalhos em Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8ed9e23b178b8eeefbd3c3a690491124e6901180
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112927"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup on-line e restauro de dados on-demand em Azure Cosmos DB

A Azure Cosmos DB recolhe automaticamente cópias de segurança dos seus dados em intervalos regulares. As cópias de segurança automáticas são tomadas sem afetar o desempenho ou disponibilidade das operações de base de dados. Todos os backups são armazenados separadamente num serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. As cópias de segurança automáticas são úteis em cenários quando acidentalmente elimina ou atualiza a sua conta, base de dados ou contentor Azure Cosmos e, mais tarde, requer a recuperação de dados.

## <a name="automatic-and-online-backups"></a>Backups automáticos e online

Com a Azure Cosmos DB, não só os seus dados, mas também os backups dos seus dados são altamente redundantes e resistentes a desastres regionais. Os seguintes passos mostram como a Azure Cosmos DB executa a cópia de segurança de dados:

* O Azure Cosmos DB faz uma cópia de segurança automática da base de dados a cada quatro horas e em qualquer momento. Apenas as duas últimas cópias de segurança são armazenadas. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

* A Azure Cosmos DB armazena estas cópias de segurança no armazenamento da Azure Blob, enquanto os dados reais residem localmente dentro do Azure Cosmos DB.

*  Para garantir uma baixa latência, o instantâneo da sua cópia de segurança é armazenado no armazenamento Azure Blob na mesma região que a atual região de escrita (ou uma das regiões de escrita, caso tenha uma configuração multi-master) da sua conta de base de dados Azure Cosmos. Para a resiliência contra o desastre regional, cada instantâneo dos dados de backup no armazenamento de Azure Blob é novamente replicado para outra região através de armazenamento geo-redundante (GRS). A região à qual o backup é replicado baseia-se na sua região de origem e no par regional associado à região de origem. Para saber mais, consulte a [lista de pares geo-redundantes das regiões Azure.](../best-practices-availability-paired-regions.md) Não pode aceder diretamente a esta cópia de segurança. A Azure Cosmos DB só usará este backup se for iniciada uma restauração de backup.

* As cópias de segurança são tomadas sem afetar o desempenho ou disponibilidade da sua aplicação. A Azure Cosmos DB realiza a cópia de segurança de dados em segundo plano sem consumir qualquer produção adicional (RUs) ou afetar o desempenho e disponibilidade da sua base de dados.

* Se acidentalmente eliminou ou corrompeu os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa DB do Azure Cosmos possa ajudá-lo a restaurar os dados das cópias de segurança.

A imagem a seguir mostra como um contentor Azure Cosmos com todas as três divisórias físicas primárias no Oeste dos EUA é apoiado numa conta remota de Armazenamento Azure Blob no Oeste dos EUA e depois replicado para os EUA Orientais:

:::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Backups periódicos completos de todas as entidades da Cosmos DB no Armazenamento GRS Azure" border="false":::

## <a name="options-to-manage-your-own-backups"></a>Opções para gerir os seus próprios backups

Com as contas Azure Cosmos DB SQL API, também pode manter as suas próprias cópias de segurança utilizando uma das seguintes abordagens:

* Utilize [a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover os dados periodicamente para um armazenamento à sua escolha.

* Utilize o feed de [alteração](change-feed.md) DB do Azure Cosmos para ler os dados periodicamente para cópias de segurança completas, bem como para alterações incrementais, e guarde-os no seu próprio armazenamento.

## <a name="backup-retention-period"></a>Período de retenção de backup

A Azure Cosmos DB tira fotos dos seus dados a cada quatro horas. A qualquer momento, apenas os dois últimos instantâneos são mantidos. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

## <a name="restoring-data-from-online-backups"></a>Restaurar dados de backups online

A eliminação acidental ou modificação de dados pode ocorrer num dos seguintes cenários:  

* Toda a conta Azure Cosmos é eliminada

* Uma ou mais bases de dados da Azure Cosmos são eliminadas

* Um ou mais contentores da Azure Cosmos são eliminados

* Os itens Azure Cosmos (por exemplo, documentos) dentro de um recipiente são eliminados ou modificados. Este caso específico é tipicamente referido como "corrupção de dados".

* Uma base de dados de oferta partilhada ou contentores dentro de uma base de dados de oferta partilhada são eliminados ou corrompidos

AZure Cosmos DB pode restaurar dados em todos os cenários acima. O processo de restauro cria sempre uma nova conta do Azure Cosmos para manter os dados restaurados. O nome da nova conta, se não for especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1` . O último dígito é incrementado, se se tentar várias restaurações. Não é possível restaurar os dados numa conta Azure Cosmos pré-criada.

Quando uma conta Azure Cosmos é eliminada, podemos restaurar os dados numa conta com o mesmo nome, desde que o nome da conta não esteja em uso. Nesses casos, recomenda-se não recriar a conta após a eliminação, porque não só impede que os dados restaurados utilizem o mesmo nome, como também torna a descoberta da conta certa para restaurar de forma mais difícil. 

Quando uma base de dados Azure Cosmos é eliminada, é possível restaurar toda a base de dados ou um subconjunto dos contentores dentro dessa base de dados. Também é possível selecionar contentores através de bases de dados e restaurá-los e todos os dados restaurados são colocados numa nova conta Azure Cosmos.

Quando um ou mais itens dentro de um contentor forem acidentalmente eliminados ou alterados (o caso da corrupção de dados), terá de especificar a hora para restaurar. O tempo é essencial para este caso. Uma vez que o contentor está ao vivo, a cópia de segurança ainda está em funcionamento, por isso, se esperar para além do período de retenção (o padrão é de oito horas), as cópias de segurança serão substituídas. No caso de eliminações, os seus dados deixaram de ser armazenados porque não serão substituídos pelo ciclo de backup. As cópias de segurança para bases de dados ou contentores eliminados são guardadas durante 30 dias.

Se fornecer produção ao nível da base de dados (isto é, quando um conjunto de contentores partilha o rendimento provisitado), o processo de backup e restauro, neste caso, acontece a todo o nível da base de dados, e não ao nível de cada contentor. Nesses casos, selecionar um subconjunto de recipientes para restaurar não é uma opção.

## <a name="migrating-data-to-the-original-account"></a>Dados migratórios para a conta original

O objetivo principal da restauração de dados é fornecer uma forma de recuperar quaisquer dados que apague ou modifique acidentalmente. Por isso, recomendamos que inspecione primeiro o conteúdo dos dados recuperados para garantir que contém o que está à espera. Em seguida, trabalhar em migrar os dados de volta para a conta principal. Embora seja possível utilizar a conta restaurada como conta ao vivo, não é uma opção recomendada se tiver cargas de trabalho de produção.  

Seguem-se diferentes formas de migrar os dados para a conta original da Azure Cosmos:

* Utilização da ferramenta de [migração de dados do Cosmos DB](import-data.md)
* Usando [a fábrica de dados Azure]( ../data-factory/connector-azure-cosmos-db.md)
* Utilização de [feed de mudança](change-feed.md) em Azure Cosmos DB 
* Escrever código personalizado

Elimine as contas restauradas assim que terminar a migração, pois incorrerão em encargos em curso.

## <a name="next-steps"></a>Próximos passos

Em seguida, você pode aprender sobre como restaurar dados de uma conta Azure Cosmos ou aprender a migrar dados para uma conta Azure Cosmos

* Para fazer um pedido de restauro, contacte o Azure Support, [arquive um bilhete a partir do portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados de uma conta Azure Cosmos](how-to-backup-and-restore.md)
* [Use o feed de alteração do Cosmos DB](change-feed.md) para mover dados para Azure Cosmos DB.
* [Utilize a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para a Azure Cosmos DB.

