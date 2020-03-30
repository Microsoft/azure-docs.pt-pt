---
title: Backup online e restauro de dados a pedido em Azure Cosmos DB
description: Este artigo descreve como a cópia automática, de backup on-line e de dados a pedido de restauro funciona em Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240438"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup online e restauro de dados a pedido em Azure Cosmos DB

O Azure Cosmos DB recebe automaticamente cópias de segurança dos seus dados em intervalos regulares. As cópias de segurança automáticas são tomadas sem afetar o desempenho ou a disponibilidade das operações da base de dados. Todos os backups são armazenados separadamente num serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. As cópias de segurança automáticas são úteis em cenários quando acidentalmente apaga ou atualiza a sua conta Azure Cosmos, base de dados ou contentor e, posteriormente, necessita da recuperação de dados.

## <a name="automatic-and-online-backups"></a>Backups automáticos e online

Com o Azure Cosmos DB, não só os seus dados, mas também os backups dos seus dados são altamente redundantes e resilientes a desastres regionais. Os seguintes passos mostram como o Azure Cosmos DB executa a cópia de segurança de dados:

* O Azure Cosmos DB faz uma cópia de segurança automática da base de dados a cada quatro horas e em qualquer momento. Apenas as duas últimas cópias de segurança são armazenadas. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

* A Azure Cosmos DB armazena estes backups no armazenamento da Azure Blob, enquanto os dados reais residem localmente dentro do Azure Cosmos DB.

*  Para garantir baixa latência, o instantâneo da sua cópia de segurança é armazenado no armazenamento azure Blob na mesma região que a atual região de escrita (ou uma das regiões de escrita, caso tenha uma configuração multi-master) da sua conta de base de dados Azure Cosmos. Para a resiliência contra o desastre regional, cada instantâneo dos dados de backup no armazenamento de Blob Azure é novamente replicado para outra região através de armazenamento geo-redundante (GRS). A região a que o backup é replicado baseia-se na sua região de origem e no par regional associado à região de origem. Para saber mais, consulte a [lista de pares geo-redundantes das regiões de Azure.](../best-practices-availability-paired-regions.md) Não pode aceder diretamente a este backup. O Azure Cosmos DB só utilizará esta cópia de segurança se for iniciada uma restauração de reserva.

* As cópias de segurança são tomadas sem afetar o desempenho ou disponibilidade da sua aplicação. A Azure Cosmos DB realiza cópias de segurança de dados em segundo plano sem consumir qualquer bioscância adicional (RUs) ou afetar o desempenho e disponibilidade da sua base de dados.

* Se tiver acidentalmente eliminado ou corrompido os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa do Azure Cosmos DB possa ajudá-lo a restaurar os dados das cópias de segurança.

A imagem que se segue mostra como um contentor Azure Cosmos com todas as três divisórias físicas primárias nos EUA Ocidentais é apoiado numa remota conta de Armazenamento De Blob Azure nos EUA Ocidentais e depois replicado para os EUA Orientais:

![Backups completos periódicos de todas as entidades da Cosmos DB no Armazenamento GRS Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opções para gerir os seus próprios backups

Com as contas API Da Azure Cosmos DB SQL, também pode manter as suas próprias cópias de segurança utilizando uma das seguintes abordagens:

* Utilize a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover os dados periodicamente para um armazenamento à sua escolha.

* Utilize o feed de [alteração](change-feed.md) DB Azure Cosmos para ler os dados periodicamente para cópias de segurança completas, bem como para alterações incrementais, e guarde-os no seu próprio armazenamento.

## <a name="backup-retention-period"></a>Período de retenção de cópias de segurança

A Azure Cosmos DB tira fotos dos seus dados a cada quatro horas. A qualquer momento, apenas as duas últimas fotos são mantidas. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

## <a name="restoring-data-from-online-backups"></a>Restaurar dados de backups online

A eliminação acidental ou modificação de dados pode ocorrer num dos seguintes cenários:  

* Toda a conta Azure Cosmos é eliminada

* Uma ou mais bases de dados do Azure Cosmos são eliminadas

* Um ou mais contentores Azure Cosmos são eliminados

* Os itens Da Azure Cosmos (por exemplo, documentos) dentro de um recipiente são eliminados ou modificados. Este caso específico é tipicamente referido como "corrupção de dados".

* Uma base de dados de oferta partilhada ou contentores dentro de uma base de dados de oferta partilhada são eliminados ou corrompidos

A Azure Cosmos DB pode restaurar os dados em todos os cenários acima referidos. O processo de restauro cria sempre uma nova conta do Azure Cosmos para manter os dados restaurados. O nome da nova conta, se não especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1`. O último dígito é incrementado, se várias restaurações forem tentadas. Não se pode restaurar os dados numa conta Azure Cosmos pré-criada.

Quando uma conta Azure Cosmos é eliminada, podemos restaurar os dados numa conta com o mesmo nome, desde que o nome da conta não esteja a ser utilizado. Nestes casos, recomenda-se não recriar a conta após a eliminação, pois não só impede que os dados restaurados utilizem o mesmo nome, como também dificulta a descoberta da conta certa para restaurar de forma mais difícil. 

Quando uma base de dados Azure Cosmos é eliminada, é possível restaurar toda a base de dados ou um subconjunto dos contentores nessa base de dados. Também é possível selecionar contentores através de bases de dados e restaurá-los e todos os dados restaurados são colocados numa nova conta Azure Cosmos.

Quando um ou mais itens dentro de um contentor forem acidentalmente apagados ou alterados (o caso de corrupção de dados), terá de especificar o tempo para restaurar. O tempo é essencial para este caso. Uma vez que o recipiente está ao vivo, o backup ainda está em funcionamento, por isso, se esperar para além do período de retenção (o padrão é de oito horas), as cópias de segurança serão substituídas. No caso de eliminações, os seus dados deixaram de ser armazenados porque não serão substituídos pelo ciclo de cópia de segurança. As cópias de segurança para bases de dados ou contentores eliminados são guardadas durante 30 dias.

Se fornecer a entrada ao nível da base de dados (isto é, quando um conjunto de contentores partilha a entrada prevista), o processo de cópia de segurança e restauro neste caso acontece a todo o nível da base de dados, e não ao nível de cada contentor. Nesses casos, selecionar um subconjunto de recipientes para restaurar não é uma opção.

## <a name="migrating-data-to-the-original-account"></a>Dados migratórios para a conta original

O principal objetivo da restauração de dados é fornecer uma forma de recuperar quaisquer dados que você apague ou modifique acidentalmente. Por isso, recomendamos que primeiro inspecione o conteúdo dos dados recuperados para garantir que contém o que espera. Em seguida, trabalhar na migração dos dados de volta para a conta primária. Embora seja possível utilizar a conta restaurada como conta ao vivo, não é uma opção recomendada se tiver cargas de trabalho de produção.  

Seguem-se diferentes formas de migrar dados de volta para a conta original do Azure Cosmos:

* Usando a ferramenta de migração de [dados cosmos DB](import-data.md)
* Utilização da Fábrica de [Dados Azure]( ../data-factory/connector-azure-cosmos-db.md)
* Usando [o feed de mudança](change-feed.md) em Azure Cosmos DB 
* Escrever código personalizado

Elimine as contas restauradas assim que terminar a migração, porque incorrerão em encargos contínuos.

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode aprender sobre como restaurar dados de uma conta Azure Cosmos ou aprender a migrar dados para uma conta Azure Cosmos

* Para fazer um pedido de restauro, contacte o Suporte Azure, [preencha um bilhete do portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados de uma conta Azure Cosmos](how-to-backup-and-restore.md)
* Use o feed de [mudança de Db cosmos](change-feed.md) para mover dados para O BD Do Cosmos.
* Utilize a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para o Azure Cosmos DB.

