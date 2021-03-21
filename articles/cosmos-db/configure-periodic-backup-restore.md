---
title: Configure conta DB Azure Cosmos com backup periódico
description: Este artigo descreve como configurar contas DB da Azure Cosmos com cópias de segurança periódicas com intervalo de backup. e retenção. Também como contactar o suporte para restaurar os seus dados.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565840"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Configure conta DB Azure Cosmos com backup periódico
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB tira automaticamente cópias de segurança dos dados em intervalos regulares. As cópias de segurança automáticas são feitas sem afetar o desempenho ou a disponibilidade das operações da base de dados. Todos os backups são armazenados separadamente num serviço de armazenamento, e esses backups são globalmente replicados para resiliência contra desastres regionais. Com a Azure Cosmos DB, não só os seus dados, mas também os backups dos seus dados são altamente redundantes e resistentes a desastres regionais. Os seguintes passos mostram como a Azure Cosmos DB executa a cópia de segurança de dados:

* A Azure Cosmos DB recebe automaticamente uma cópia de segurança completa da sua base de dados a cada 4 horas e, em qualquer momento, apenas as duas cópias de segurança mais recentes são armazenadas por padrão. Se os intervalos predefinidos não forem suficientes para as suas cargas de trabalho, pode alterar o intervalo de backup e o período de retenção a partir do portal Azure. Pode alterar a configuração de backup durante ou após a criação da conta Azure Cosmos. Se o contentor ou base de dados for eliminado, a Azure Cosmos DB mantém as imagens existentes de um dado ou base de dados durante 30 dias.

* A Azure Cosmos DB armazena estas cópias de segurança no armazenamento da Azure Blob, enquanto os dados reais residem localmente dentro do Azure Cosmos DB.

* Para garantir a baixa latência, o instantâneo da sua cópia de segurança é armazenado no armazenamento Azure Blob na mesma região que a atual região de escrita (ou **uma das** regiões de escrita, caso tenha uma configuração de escrita multi-região). Para obter resiliência face a desastres regionais, cada instantâneo dos dados de cópia de segurança no Armazenamento de blobs do Azure é replicado de novo para outra região através do armazenamento georredundante (GRS). A região para a qual a cópia de segurança é replicada depende da região de origem e do par regional associado a essa região. Para saber mais, consulte a [lista de pares geo-redundantes das regiões Azure.](../best-practices-availability-paired-regions.md) Não pode aceder diretamente a esta cópia de segurança. A equipa do Azure Cosmos DB restaurará a cópia de segurança quando pedir através de um pedido de suporte.

  A imagem a seguir mostra como um contentor Azure Cosmos com todas as três divisórias físicas primárias no Oeste dos EUA é apoiado numa conta remota de Armazenamento Azure Blob no Oeste dos EUA e depois replicado para os EUA Orientais:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Backups periódicos completos de todas as entidades da Cosmos DB no ARMAZENAMENTO GRS Azure." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* As cópias de segurança são tomadas sem afetar o desempenho ou disponibilidade da sua aplicação. A Azure Cosmos DB realiza a cópia de segurança de dados em segundo plano sem consumir qualquer produção extra prevista (RUs) ou afetar o desempenho e disponibilidade da sua base de dados.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modifique o intervalo de backup e o período de retenção

A Azure Cosmos DB requer automaticamente uma cópia de segurança completa dos seus dados por cada 4 horas e, em qualquer momento, as duas últimas cópias de segurança são armazenadas. Esta configuração é a opção padrão e é oferecida sem qualquer custo extra. Pode alterar o intervalo de cópia de segurança e o período de retenção predefinidos durante ou após a criação da conta do Azure Cosmos. A configuração da cópia de segurança é definida ao nível da conta do Azure Cosmos e é preciso configurá-la em cada conta. Depois de configurar as opções de backup para uma conta, é aplicada a todos os contentores dentro dessa conta. Atualmente, só pode alterar as opções de cópia de segurança no portal do Azure.

Se acidentalmente eliminou ou corrompeu os seus dados, **antes de criar um pedido de suporte para restaurar os dados, certifique-se de aumentar a retenção de backup da sua conta para pelo menos sete dias. É melhor aumentar a sua retenção dentro de 8 horas deste evento.** Desta forma, a equipa do Azure Cosmos DB tem tempo suficiente para restaurar a conta.

Utilize os seguintes passos para alterar as opções de backup predefinidas para uma conta Azure Cosmos existente:

1. Inscreva-se no [portal Azure.](https://portal.azure.com/)
1. Navegue na sua conta Azure Cosmos e abra o **painel de & De reserva Restaurar.** Atualize o intervalo de backup e o período de retenção de backup conforme necessário.

   * **Intervalo de backup** - É o intervalo em que a Azure Cosmos DB tenta obter uma cópia de segurança dos seus dados. A cópia de segurança leva uma quantidade não-zero de tempo e, em alguns casos, pode potencialmente falhar devido a dependências a jusante. A Azure Cosmos DB tenta o seu melhor para fazer uma cópia de segurança no intervalo configurado, no entanto, não garante que o backup complete dentro desse intervalo de tempo. Pode configurar este valor em horas ou minutos. O intervalo de reserva não pode ser inferior a 1 hora e superior a 24 horas. Quando se altera este intervalo, o novo intervalo entra em vigor a partir do momento em que a última cópia de segurança foi tomada.

   * **Retenção de Backup** - Representa o período em que cada cópia de segurança é mantida. Pode configurgá-lo em horas ou dias. O período mínimo de retenção não pode ser inferior a duas vezes o intervalo de reserva (em horas) e não pode ser superior a 720 horas.

   * **Cópias dos dados retidos** - Por padrão, duas cópias de backup dos seus dados são oferecidas gratuitamente. Há uma taxa extra se precisar de mais de duas cópias. Consulte a secção de Armazenamento Consumido na [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o preço exato das cópias extras.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Configurar intervalo de backup e retenção para uma conta Azure Cosmos existente." border="true":::

Se configurar opções de backup durante a criação da conta, pode configurar a **política de Backup**, que é **periódica** ou **contínua**. A política periódica permite-lhe configurar o intervalo de backup e a retenção de backup. A política contínua está atualmente disponível apenas por inscrição. A equipa DB da Azure Cosmos avaliará a sua carga de trabalho e aprovará o seu pedido.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Configure a política de backup periódica ou contínua para novas contas Azure Cosmos." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Solicitar a restauração de dados a partir de uma cópia de segurança

Se eliminar acidentalmente a sua base de dados ou um recipiente, pode [arquivar um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou ligar para o suporte do [Azure](https://azure.microsoft.com/support/options/) para restaurar os dados a partir de cópias de segurança online automáticas. O suporte Azure está disponível para planos selecionados apenas como **Standard,** **Developer,** e planos superiores aos. O apoio do Azure não está disponível com o plano **Básico.** Para conhecer diferentes planos de apoio, consulte a página de planos de apoio do [Azure.](https://azure.microsoft.com/support/plans/)

Para restaurar uma imagem específica da cópia de segurança, a Azure Cosmos DB requer que os dados estão disponíveis durante o ciclo de backup para esse instantâneo.
Deve ter os seguintes detalhes antes de solicitar uma restauração:

* Tenha o seu ID de assinatura pronto.

* Com base na forma como os seus dados foram acidentalmente eliminados ou modificados, deverá preparar-se para ter informações adicionais. É aconselhável que tenha a informação disponível para minimizar o retrocesso que pode ser prejudicial em alguns casos sensíveis.

* Se toda a conta DB da Azure Cosmos for eliminada, tem de fornecer o nome da conta eliminada. Se criar outra conta com o mesmo nome que a conta eliminada, partilhe-a com a equipa de suporte porque ajuda a determinar a conta certa para escolher. Recomenda-se arquivar diferentes bilhetes de apoio para cada conta apagada porque minimiza a confusão para o estado de restauração.

* Se uma ou mais bases de dados forem eliminadas, deverá fornecer a conta Azure Cosmos e os nomes da base de dados Azure Cosmos e especificar se existe uma nova base de dados com o mesmo nome.

* Se um ou mais contentores forem eliminados, deve fornecer o nome da conta Azure Cosmos, nomes de bases de dados e os nomes dos contentores. E especificar se existe um recipiente com o mesmo nome.

* Se acidentalmente eliminou ou corrompeu os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa DB do Azure Cosmos possa ajudá-lo a restaurar os dados das cópias de segurança. **Antes de criar um pedido de apoio para restaurar os dados, certifique-se de [aumentar a retenção de backup](#configure-backup-interval-retention) da sua conta para pelo menos sete dias. É melhor aumentar a sua retenção dentro de 8 horas deste evento.** Desta forma, a equipa de apoio do Azure Cosmos terá tempo suficiente para restaurar a sua conta.

Além do nome da conta Azure Cosmos, nomes de bases de dados, nomes de contentores, deve especificar o ponto a tempo para o qual os dados podem ser restaurados. É importante sermos o mais precisos possível para nos ajudar a determinar as melhores cópias de segurança disponíveis nessa altura. **Também é importante especificar o tempo na UTC.**

A imagem que se segue ilustra como criar um pedido de suporte para um recipiente (recolha/gráfico/tabela) para restaurar os dados utilizando o portal Azure. Forneça outros detalhes, como tipo de dados, finalidade da restauração, tempo em que os dados foram eliminados para nos ajudar a priorizar o pedido.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Crie um pedido de suporte de cópia de segurança utilizando o portal Azure." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Considerações para restaurar os dados a partir de uma cópia de segurança

Pode eliminar ou modificar acidentalmente os seus dados num dos seguintes cenários:  

* Apague toda a conta do Azure Cosmos.

* Apague uma ou mais bases de dados da Azure Cosmos.

* Apague um ou mais recipientes Azure Cosmos.

* Elimine ou modifique os itens Azure Cosmos (por exemplo, documentos) dentro de um recipiente. Este caso específico é tipicamente referido como corrupção de dados.

* Uma base de dados de oferta partilhada ou recipientes dentro de uma base de dados de oferta partilhada são eliminados ou corrompidos.

AZure Cosmos DB pode restaurar dados em todos os cenários acima. Ao restaurar, uma nova conta Azure Cosmos é criada para reter os dados restaurados. O nome da nova conta, se não for especificado, terá o formato `<Azure_Cosmos_account_original_name>-restored1` . O último dígito é incrementado quando se tentam várias restaurações. Não é possível restaurar os dados numa conta Azure Cosmos pré-criada.

Quando eliminar acidentalmente uma conta Azure Cosmos, podemos restaurar os dados numa nova conta com o mesmo nome, se o nome da conta não estiver a ser utilizado. Por isso, recomendamos que não reusso a conta depois de a apagar. Porque não só impede que os dados restaurados utilizem o mesmo nome, como também dificulta a descoberta da conta certa para restaurar.

Quando eliminar acidentalmente uma base de dados Azure Cosmos, podemos restaurar toda a base de dados ou um subconjunto dos contentores dentro dessa base de dados. Também é possível selecionar recipientes específicos através de bases de dados e restaurá-los para uma nova conta Azure Cosmos.

Quando eliminar acidentalmente ou modificar um ou mais itens dentro de um recipiente (o caso da corrupção de dados), tem de especificar a hora para restaurar. O tempo é importante se houver corrupção de dados. Como o contentor está ao vivo, a cópia de segurança ainda está em funcionamento, por isso, se esperar para além do período de retenção (o padrão é de oito horas), as cópias de segurança serão substituídas. Para evitar que o backup seja substituído, aumente a retenção de backup da sua conta para pelo menos sete dias. É melhor aumentar a sua retenção dentro de 8 horas da corrupção de dados.

Se acidentalmente eliminou ou corrompeu os seus dados, deverá contactar o [suporte do Azure](https://azure.microsoft.com/support/options/) dentro de 8 horas para que a equipa DB do Azure Cosmos possa ajudá-lo a restaurar os dados das cópias de segurança. Desta forma, a equipa de apoio do Azure Cosmos terá tempo suficiente para restaurar a sua conta.

> [!NOTE]
> Depois de restaurar os dados, nem todas as capacidades ou configurações de origem são transportadas para a conta restaurada. As seguintes definições não são transitadas para a nova conta:
> * Listas de controlo de acesso vNET
> * Procedimentos armazenados, gatilhos e funções definidas pelo utilizador
> * Configurações multi-regiões  

Se fornecer produção ao nível da base de dados, o processo de backup e restauro neste caso ocorra a todo o nível da base de dados, e não ao nível de cada contentor. Nesses casos, não é possível selecionar um subconjunto de recipientes para restaurar.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Permissões necessárias para alterar retenção ou restauro a partir do portal
Os principais que fazem parte do papel [CosmosdbBackupOperator,](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)proprietário ou colaborador são autorizados a solicitar um restauro ou alterar o período de retenção.

## <a name="understanding-costs-of-extra-backups"></a>Compreender os custos das cópias de segurança adicionais
Duas cópias de segurança são fornecidas gratuitamente e são cobradas cópias de segurança extra de acordo com os preços baseados na região para armazenamento de backup descritos nos [preços de armazenamento de backup](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Por exemplo, se a retenção de backup estiver configurada a 240 horas, ou seja, 10 dias e intervalo de backup a 24 horas. Isto implica 10 cópias dos dados de reserva. Assumindo que 1 TB de dados no Oeste dos EUA 2, o custo será de 0,12 * 1000 * 8 para armazenamento de backup em dado mês. 


## <a name="options-to-manage-your-own-backups"></a>Opções para gerir os seus próprios backups

Com as contas Azure Cosmos DB SQL API, também pode manter as suas próprias cópias de segurança utilizando uma das seguintes abordagens:

* Utilize [a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) para mover os dados periodicamente para um armazenamento à sua escolha.

* Utilize o feed de [alteração](change-feed.md) DB do Azure Cosmos para ler os dados periodicamente para cópias de segurança completas ou para alterações incrementais e guarde-os no seu próprio armazenamento.

## <a name="post-restore-actions"></a>Ações pós-restauro

O objetivo principal da restauração dos dados é recuperar os dados que acidentalmente apagou ou modificou. Por isso, recomendamos que inspecione primeiro o conteúdo dos dados recuperados para garantir que contém o que está à espera. Se tudo parecer bom, pode migrar os dados de volta para a conta principal. Embora seja possível utilizar a conta restaurada como a sua nova conta ativa, não é uma opção recomendada se tiver cargas de trabalho de produção. 

Depois de restaurar os dados, recebe uma notificação sobre o nome da nova conta (normalmente está no formato) e o momento em que `<original-name>-restored1` a conta foi restaurada. A conta restaurada terá a mesma produção provisida, políticas de indexação e está na mesma região que a conta original. Um utilizador que seja o administrador de subscrição ou um coadmin pode ver a conta restaurada.

### <a name="migrate-data-to-the-original-account"></a>Migrar dados para a conta original

Seguem-se diferentes formas de migrar os dados para a conta original:

* Utilize a [ferramenta de migração de dados Azure Cosmos DB](import-data.md).
* Utilize a [Fábrica de Dados Azure](../data-factory/connector-azure-cosmos-db.md).
* Utilize o [feed de alteração](change-feed.md) no Azure Cosmos DB.
* Pode escrever o seu próprio código personalizado.

É aconselhável eliminar o recipiente ou base de dados imediatamente após a migração dos dados. Se não eliminar as bases de dados ou contentores restaurados, incorrerão em custos para unidades de pedido, armazenamento e saída.

## <a name="next-steps"></a>Passos seguintes

* Para escamar um pedido de restauro, contacte o Azure Support, [solicite um bilhete a partir do portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
