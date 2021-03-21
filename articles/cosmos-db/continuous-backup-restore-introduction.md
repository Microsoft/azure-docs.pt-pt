---
title: Backup contínuo com recurso de restauração de ponto no tempo em Azure Cosmos DB
description: A funcionalidade de restauro pontual da Azure Cosmos DB ajuda a recuperar dados de uma operação de escrita acidental, eliminação ou para restaurar dados em qualquer região. Saiba mais sobre os preços e as limitações atuais.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393144"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Backup contínuo com recurso de restauro pontual (Preview) em Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A funcionalidade de restauro pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A funcionalidade de restauro pontual da Azure Cosmos DB (Preview) ajuda em vários cenários, tais como:

* Para recuperar de uma escrita acidental ou apagar o funcionamento dentro de um recipiente.
* Para restaurar uma conta, base de dados ou um recipiente apagado.
* Para restaurar em qualquer região (onde existiam backups) no ponto de restauração no tempo.

A Azure Cosmos DB realiza a cópia de segurança de dados em segundo plano sem consumir qualquer produção extra prevista (RUs) ou afetar o desempenho e disponibilidade da sua base de dados. São tomadas cópias de segurança contínuas em todas as regiões onde a conta existe. A imagem a seguir mostra como um contentor com região de escrita nos EUA ocidentais, lê-se regiões no Leste e Leste dos EUA 2 é apoiado até uma conta remota de Armazenamento Azure Blob nas respetivas regiões. Por padrão, cada região armazena a cópia de segurança em contas de armazenamento localmente redundantes. Se a região tiver [zonas de disponibilidade](high-availability.md#availability-zone-support) ativadas, a cópia de segurança é armazenada em Zone-Redundant contas de armazenamento.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Cópia de segurança de dados da Azure Cosmos para o Armazenamento Azure Blob." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

O tempo disponível para restauro (também conhecido como período de retenção) é o valor mais baixo dos dois seguintes: *30 dias atrás no passado* ou *até ao tempo de criação de recursos*. O ponto no tempo para a restauração pode ser qualquer marca de tempo dentro do período de retenção.

Na pré-visualização pública, pode restaurar a conta DB do Azure Cosmos para os conteúdos da API ou do MongoDB pontuar a tempo para outra conta utilizando o [portal Azure,](continuous-backup-restore-portal.md) [a Interface da Linha de Comando Azure](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell,](continuous-backup-restore-powershell.md)ou o [Gestor de Recursos Azure.](continuous-backup-restore-template.md)

## <a name="what-is-restored"></a>O que é restaurado?

Em estado estável, todas as mutações realizadas na conta de origem (que inclui bases de dados, contentores e itens) são apoiadas assíncronamente dentro de 100 segundos. Se os meios de segurança (isto é o armazenamento do Azure) estiverem em baixo ou indisponíveis, as mutações são persistiu localmente até que os meios de comunicação estejam disponíveis de volta e, em seguida, são expulsos para evitar qualquer perda de fidelidade de operações que possam ser restauradas.

Pode optar por restaurar qualquer combinação de contentores de débito aprovisionados, bases de dados de débito partilhadas ou a conta inteira. A ação de restauro restaura todos os dados, bem como as propriedades de índice, para uma nova conta. O processo de restauro garante que todos os dados restaurados numa conta, numa base de dados ou num contentor têm a garantia de serem consistentes até ao tempo de restauro especificado. A duração do restauro dependerá da quantidade de dados que precisam de ser restaurados.

> [!NOTE]
> Com o modo de backup contínuo, as cópias de segurança são tomadas em todas as regiões onde a sua conta DB Azure Cosmos está disponível. As cópias de segurança tomadas para cada conta da região são localmente redundantes por defeito e a Zona redundante se a sua conta tiver a funcionalidade [de zona de disponibilidade](high-availability.md#availability-zone-support) ativada para essa região. A ação de restauro restaura sempre os dados numa nova conta.

## <a name="what-is-not-restored"></a>O que não é restaurado?

As seguintes configurações não são restauradas após a recuperação pontual:

* Firewall, VNET, definições de ponto final privado.
* Definições de consistência. Por predefinição, a conta é restaurada com consistência da sessão.  
* Regiões.
* Procedimentos armazenados, gatilhos, UDFs.

Pode adicionar estas configurações à conta restaurada após a restauração.

## <a name="restore-scenarios"></a>Cenários de restauro

Seguem-se alguns dos cenários-chave que são abordados pela funcionalidade de restauração pontual. Os cenários [a] através de [c] demonstram como desencadear um restauro se o tempo de restauração for conhecido previamente.
No entanto, pode haver cenários em que não se saiba a hora exata da eliminação acidental ou da corrupção. Os cenários [d] e [e] demonstram como _descobrir_ o tempo de restauração do tempo usando as novas APIs de alimentação de eventos na base de dados ou contentores restauradores.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Eventos de ciclo de vida com timetamps para uma conta restauradora." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Restaurar a conta eliminada** - Todas as contas eliminadas que pode restaurar são visíveis a partir do painel **Restaurar.** Por exemplo, se *a Conta A* for eliminada por vezes omp T3. Neste caso, a marca de tempo pouco antes de T3, localização, nome da conta-alvo, grupo de recursos e nome da conta-alvo é suficiente para restaurar a partir do [portal Azure,](continuous-backup-restore-portal.md#restore-deleted-account) [PowerShell,](continuous-backup-restore-powershell.md#trigger-restore)ou [CLI](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Eventos de ciclo de vida com timetamps para uma base de dados e recipientes restauradores." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Restaurar os dados de uma conta numa determinada região** - Por exemplo, se a *conta A* existir em duas regiões *leste dos EUA* e eua *ocidental* em timetamp T3. Se precisar de uma cópia da conta A nos *EUA,* pode fazer um ponto no tempo restaurado a partir do [portal Azure,](continuous-backup-restore-portal.md) [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ou [CLI](continuous-backup-restore-command-line.md#trigger-restore) com os EUA ocidentais como o local alvo.

c. **Recuperar de uma operação de escrita ou eliminação acidental dentro de um recipiente com um tempotaste de restauração conhecido** - Por exemplo, se **souber** que o conteúdo do *Contentor 1* dentro *da Base de Dados 1* foi modificado acidentalmente ao tempo t3. Pode fazer um ponto no tempo restaurar a partir do [portal Azure](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ou [CLI](continuous-backup-restore-command-line.md#trigger-restore) em outra conta em tempos de temperatura T3 para recuperar o estado desejado do recipiente.

d. **Restaurar uma conta num ponto anterior antes da eliminação acidental da base de dados** - No [portal Azure,](continuous-backup-restore-portal.md#restore-live-account)pode utilizar o painel de alimentação do evento para determinar quando uma base de dados foi eliminada e encontrar o tempo de restauro. Da mesma forma, com [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) e [PowerShell,](continuous-backup-restore-powershell.md#trigger-restore)pode descobrir o evento de eliminação da base de dados enumerando o feed de eventos de base de dados e, em seguida, acionar o comando de restauro com os parâmetros necessários.

e. **Restaurar uma conta num ponto anterior antes da eliminação acidental ou modificação das propriedades do recipiente.** - No [portal Azure,](continuous-backup-restore-portal.md#restore-live-account)pode utilizar o painel de alimentação do evento para determinar quando um recipiente foi criado, modificado ou eliminado para encontrar o tempo de restauro. Da mesma forma, com [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) e [PowerShell,](continuous-backup-restore-powershell.md#trigger-restore)pode descobrir todos os eventos do contentor enumerando os eventos do recipiente e, em seguida, acionar o comando de restauração com os parâmetros necessários.

## <a name="permissions"></a>Permissões

O Azure Cosmos DB permite-lhe isolar e restringir as permissões de restauro para uma conta de backup contínua a um papel específico ou a um principal. O proprietário da conta pode desencadear uma restauração e atribuir uma função a outros principais para realizar a operação de restauro. Para saber mais, consulte o artigo [Permissões.](continuous-backup-restore-permissions.md)

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Preços

As contas DB da Azure Cosmos que tenham uma cópia de segurança contínua ativada incorrerão numa taxa mensal adicional para *armazenar a cópia de segurança* e restaurar os seus *dados*. O custo de restauro é adicionado sempre que a operação de restauro é iniciada. Se configurar uma conta com cópia de segurança contínua mas não restaurar os dados, apenas o custo de armazenamento de backup está incluído na sua conta.

O exemplo a seguir baseia-se no preço de uma conta Azure Cosmos implantada numa região não governamental nos EUA. Os preços e cálculos podem variar dependendo da região que você está usando, consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes sobre preços.

* Todas as contas ativadas com uma política de backup contínua incorrem numa taxa mensal adicional para armazenamento de backup que é calculada da seguinte forma:

  $0,20/GB * Tamanho dos dados em GB em conta * Número de regiões

* Cada restauro da invocação da API incorre numa única carga. A cobrança é uma função da quantidade de dados restaurados e é calculada da seguinte forma:

  $0,15/GB * Tamanho dos dados em GB.

Por exemplo, se tiver 1-TB de dados em duas regiões, então:

* O custo de armazenamento de backup é calculado como (1000 * 0,20 * 2) = $400 por mês

* O custo de restauro é calculado como (1000 * 0,15) = $150 por restauro

## <a name="current-limitations-public-preview"></a>Limitações atuais (visualização pública)

Atualmente, o ponto de restauração da funcionalidade está em visualização pública e tem as seguintes limitações:

* Apenas as APIs do Azure Cosmos DB para SQL e MongoDB são suportadas para a cópia de segurança contínua. As APIs para Cassandra, Tabela e Gremlin ainda não são suportadas.

* Uma conta existente com uma política de backup periódica padrão não pode ser convertida para utilizar o modo de backup contínuo.

* As regiões de nuvem soberanas de Azure e Azure ainda não foram apoiadas.

* As contas com chaves geridas pelo cliente não são suportadas para utilizar cópias de segurança contínuas.

* As contas escritas de várias regiões não são apoiadas.

* As contas com o Synapse Link ativado não são suportadas.

* A conta restaurada é criada na mesma região onde a conta de origem está localizada. Não pode restaurar uma conta para uma região onde não existe a conta de origem.

* A janela de restauro é de apenas 30 dias e não pode ser alterada.

* As cópias de segurança não são automaticamente resistentes a desastres geográficos. Tem de adicionar explicitamente outra região para ter resiliência para a conta e a cópia de segurança.

* Enquanto uma restauração estiver em andamento, não modifique ou elimine as políticas de Gestão de Identidade e Acesso (IAM) que concedem as permissões para a conta ou alteram qualquer configuração VNET, firewall.

* As API do Azure Cosmos DB para as contas SQL ou MongoDB que criam um índice exclusivo após o contentor ser criado não são suportadas para a cópia de segurança contínua. Apenas os contentores que criam um índice exclusivo como parte da criação do contentor inicial são suportados. Para as contas MongoDB, cria um índice único utilizando [comandos de extensão](mongodb-custom-commands.md).

* A funcionalidade de restauro para um ponto anterior no tempo restaura sempre para uma nova conta do Azure Cosmos. Atualmente, o restauro para uma conta existente não é suportado. Se estiver interessado em fornecer feedback sobre a restauração no local, contacte a equipa DB da Azure Cosmos através do seu representante de conta ou [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Todas as novas APIs expostas para listagem `RestorableDatabaseAccount` `RestorableSqlDatabases` , `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` estão sujeitas a alterações enquanto a funcionalidade se encontra em pré-visualização.

* Após restauro, é possível que para certas coleções o índice consistente possa estar a reconstruir. Pode verificar o estado da operação de reconstrução através da propriedade [IndexTransformationProgress.](how-to-manage-indexing-policy.md)

* O processo de restauro restaura todas as propriedades de um contentor, incluindo a configuração TTL. Como resultado, será possível que os dados restaurados sejam eliminados imediatamente se os tiver configurado dessa forma. Para evitar esta situação, o carimbo de data/hora do restauro deve ser adicionado antes das propriedades TTL serem adicionadas ao contentor.

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
