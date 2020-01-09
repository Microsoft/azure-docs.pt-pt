---
title: Backup online e restauração de dados sob demanda no Azure Cosmos DB
description: Este artigo descreve como a restauração automática, de backup online e de dados sob demanda funciona em Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4ca4fa8699d9bd4b35f26983f2f7004c63da180f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441547"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup online e restauração de dados sob demanda no Azure Cosmos DB

Azure Cosmos DB automaticamente faz backups de seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade das operações de banco de dados. Todos os backups são armazenados separadamente em um serviço de armazenamento, e esses backups são replicados globalmente para resiliência contra desastres regionais. Os backups automáticos são úteis em cenários em que você exclui acidentalmente ou atualiza sua conta, banco de dados ou contêiner do Azure Cosmos e, mais tarde, requer a recuperação do dado.

## <a name="automatic-and-online-backups"></a>Backups automáticos e online

Com Azure Cosmos DB, não apenas seus dados, mas também os backups de seus dados são altamente redundantes e resilientes a desastres regionais. As etapas a seguir mostram como o Azure Cosmos DB executa o backup de dados:

* O Azure Cosmos DB faz uma cópia de segurança automática da base de dados a cada quatro horas e em qualquer momento. Apenas as duas últimas cópias de segurança são armazenadas. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

* O Azure Cosmos DB armazena esses backups no armazenamento de BLOBs do Azure, enquanto os dados reais residem localmente no Azure Cosmos DB.

*  Para garantir baixa latência, o instantâneo do backup é armazenado no armazenamento de BLOBs do Azure na mesma região que a região de gravação atual (ou uma das regiões de gravação, caso você tenha uma configuração de vários mestres) de sua conta de banco de dados Cosmos do Azure. Para resiliência contra desastres regionais, cada instantâneo dos dados de backup no armazenamento de BLOBs do Azure é novamente replicado para outra região por meio de armazenamento com redundância geográfica (GRS). A região na qual o backup é replicado se baseia na região de origem e no par regional associado à região de origem. Para saber mais, confira o artigo [lista de pares com redundância geográfica de regiões do Azure](../best-practices-availability-paired-regions.md) . Você não pode acessar esse backup diretamente. Azure Cosmos DB usará esse backup somente se uma restauração de backup for iniciada.

* Os backups são feitos sem afetar o desempenho ou a disponibilidade do seu aplicativo. Azure Cosmos DB executa o backup de dados em segundo plano sem consumir nenhuma taxa de transferência provisionada adicional (RUs) ou afetar o desempenho e a disponibilidade do banco de dados.

* Se você acidentalmente excluiu ou danificou seus dados, deve entrar em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipe de Azure Cosmos DB possa ajudá-lo a restaurar os dados dos backups.

A imagem a seguir mostra como um contêiner Cosmos do Azure com todas as três partições físicas primárias no oeste dos EUA é feito backup em uma conta de armazenamento de BLOBs do Azure remota no oeste dos EUA e, em seguida, replicada para o leste dos EUA:

![Backups completos periódicos de todas as entidades de Cosmos DB no armazenamento do Azure GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opções para gerenciar seus próprios backups

Com Azure Cosmos DB contas da API do SQL, você também pode manter seus próprios backups usando uma das seguintes abordagens:

* Use [Azure data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados periodicamente para um armazenamento de sua escolha.

* Use Azure Cosmos DB [feed de alterações](change-feed.md) para ler dados periodicamente para backups completos, bem como para alterações incrementais e armazená-los em seu próprio armazenamento.

## <a name="backup-retention-period"></a>Período de retenção de backup

Azure Cosmos DB Obtém instantâneos de seus dados a cada quatro horas. A qualquer momento, somente os dois últimos instantâneos são retidos. No entanto, se o contentor ou a base de dados forem eliminados, o Azure Cosmos DB reterá as cópias sombra existentes de um dado contentor ou base de dados durante 30 dias.

## <a name="restoring-data-from-online-backups"></a>Restaurando dados de backups online

A exclusão acidental ou a modificação de dados pode ocorrer em um dos seguintes cenários:  

* Toda a conta do Azure cosmos é excluída

* Um ou mais bancos de dados do Azure cosmos são excluídos

* Um ou mais contêineres de Cosmos do Azure foram excluídos

* Os itens Cosmos do Azure (por exemplo, documentos) dentro de um contêiner são excluídos ou modificados. Esse caso específico é normalmente chamado de "dados corrompidos".

* Um banco de dados de oferta compartilhado ou contêineres em um banco de dados de oferta compartilhado são excluídos ou corrompidos

Azure Cosmos DB pode restaurar dados em todos os cenários acima. O processo de restauro cria sempre uma nova conta do Azure Cosmos para manter os dados restaurados. O nome da nova conta, se não for especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1`. O último dígito é incrementado, se várias restaurações forem tentadas. Não é possível restaurar dados para uma conta do Azure Cosmos criada previamente.

Quando uma conta do Azure cosmos é excluída, podemos restaurar os dados em uma conta com o mesmo nome, desde que o nome da conta não esteja em uso. Nesses casos, é recomendável não recriar a conta após a exclusão, pois ela não apenas impede que os dados restaurados usem o mesmo nome, mas também faz com que a descoberta da conta correta seja restaurada de forma mais difícil. 

Quando um banco de dados Cosmos do Azure é excluído, é possível restaurar todo o banco de dados ou um subconjunto dos contêineres dentro desse banco de dados. Também é possível selecionar contêineres em bancos de dados e restaurá-los, e todos eles são colocados em uma nova conta do Azure Cosmos.

Quando um ou mais itens dentro de um contêiner são acidentalmente excluídos ou alterados (o caso de corrupção de dados), você precisará especificar o tempo para restaurar. O tempo é de essência para esse caso. Como o contêiner está ativo, o backup ainda está em execução. portanto, se você aguardar além do período de retenção (o padrão é de oito horas), os backups serão substituídos. No caso de exclusões, os dados não são mais armazenados porque não serão substituídos pelo ciclo de backup. Os backups de bancos de dados ou contêineres excluídos são salvos por 30 dias.

Se você provisionar a taxa de transferência no nível do banco de dados (ou seja, onde um conjunto de contêineres compartilha a taxa de transferência provisionada), o processo de backup e restauração, nesse caso, ocorrerá no nível do banco de dados inteiro e não no nível de contêineres individuais. Nesses casos, selecionar um subconjunto de contêineres para restaurar não é uma opção.

## <a name="migrating-data-to-the-original-account"></a>Migrando dados para a conta original

O objetivo principal da restauração de dados é fornecer uma maneira de recuperar todos os dados que você excluir ou modificar acidentalmente. Portanto, recomendamos que você primeiro Inspecione o conteúdo dos dados recuperados para garantir que ele contenha o que você espera. Em seguida, trabalhe na migração dos dados de volta para a conta primária. Embora seja possível usar a conta restaurada como a conta dinâmica, ela não será uma opção recomendada se você tiver cargas de trabalho de produção.  

A seguir, há diferentes maneiras de migrar dados de volta para a conta original do Azure Cosmos:

* Usando a [ferramenta de migração de dados Cosmos DB](import-data.md)
* Usando [Azure data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Usando o [feed de alterações](change-feed.md) no Azure Cosmos DB 
* Escrever código personalizado

Exclua as contas restauradas assim que terminar a migração, pois elas incorrerão em encargos contínuos.

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode aprender sobre como restaurar dados de uma conta do Azure Cosmos ou aprender a migrar dados para uma conta do Azure Cosmos

* Para fazer uma solicitação de restauração, entre em contato com o suporte do Azure, [arquivar um tíquete do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Como restaurar dados de uma conta do Azure Cosmos](how-to-backup-and-restore.md)
* [Use Cosmos DB o feed de alterações](change-feed.md) para mover dados para Azure Cosmos DB.
* [Use Azure data Factory](../data-factory/connector-azure-cosmos-db.md) para mover dados para Azure Cosmos DB.

