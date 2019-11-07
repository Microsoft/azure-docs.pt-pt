---
title: Atualizações de aplicativo sem interrupção – banco de dados SQL do Azure
description: Saiba como usar a replicação geográfica do banco de dados SQL do Azure para dar suporte a atualizações online do seu aplicativo de nuvem.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 253a10e75832cf6ee8294405e34fa93b801c1b49
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689492"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Gerenciar atualizações sem interrupção de aplicativos de nuvem usando a replicação geográfica ativa do banco de dados SQL

Saiba como usar a [replicação geográfica ativa](sql-database-auto-failover-group.md) no banco de dados SQL do Azure para habilitar atualizações sem interrupção do seu aplicativo de nuvem. Como as atualizações são operações de interrupção, elas devem fazer parte do planejamento e do design de continuidade de negócios. Neste artigo, examinamos dois métodos diferentes para orquestrar o processo de atualização e discutir os benefícios e as compensações de cada opção. Para os fins deste artigo, nos referimos a um aplicativo que consiste em um site que está conectado a um único banco de dados como sua camada de dado. Nosso objetivo é atualizar a versão 1 (v1) do aplicativo para a versão 2 (v2) sem nenhum impacto significativo na experiência do usuário.

Ao avaliar as opções de atualização, considere estes fatores:

* Impacto na disponibilidade do aplicativo durante as atualizações, como quanto tempo as funções de aplicativo podem ser limitadas ou degradadas.
* Capacidade de reverter se a atualização falhar.
* Vulnerabilidade do aplicativo se uma falha catastrófica não relacionada ocorrer durante a atualização.
* Custo total de dólar. Esse fator inclui redundância de banco de dados adicional e custos incrementais dos componentes temporários usados pelo processo de atualização.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizar aplicativos que dependem de backups de banco de dados para recuperação de desastre

Se seu aplicativo depender de backups automáticos de banco de dados e usar a restauração geográfica para recuperação de desastre, ele será implantado em uma única região do Azure. Para minimizar a interrupção do usuário, crie um ambiente de preparo nessa região com todos os componentes do aplicativo envolvidos na atualização. O primeiro diagrama ilustra o ambiente operacional antes do processo de atualização. O ponto de extremidade `contoso.azurewebsites.net` representa um ambiente de produção do aplicativo Web. Para poder reverter a atualização, você deve criar um ambiente de preparo com uma cópia totalmente sincronizada do banco de dados. Siga estas etapas para criar um ambiente de preparo para a atualização:

1. Crie um banco de dados secundário na mesma região do Azure. Monitore o secundário para ver se o processo de propagação está concluído (1).
2. Crie um novo ambiente para seu aplicativo Web e chame-o de ' preparo '. Ele será registrado no DNS do Azure com a URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Essas etapas de preparação não afetarão o ambiente de produção, que pode funcionar no modo de acesso completo.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Quando as etapas de preparação forem concluídas, o aplicativo estará pronto para a atualização real. O próximo diagrama ilustra as etapas envolvidas no processo de atualização:

1. Defina o banco de dados primário para o modo somente leitura (3). Esse modo garante que o ambiente de produção do aplicativo Web (v1) permaneça somente leitura durante a atualização, impedindo, assim, a divergência de dados entre as instâncias de banco de dado v1 e v2.
2. Desconecte o banco de dados secundário usando o modo de encerramento planejado (4). Essa ação cria uma cópia independente e totalmente sincronizada do banco de dados primário. Este banco de dados será atualizado.
3. Transforme o banco de dados secundário no modo de leitura/gravação e execute o script de atualização (5).

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se a atualização for concluída com êxito, agora você estará pronto para alternar os usuários para a cópia atualizada do aplicativo, que se torna um ambiente de produção. A alternância envolve algumas outras etapas, conforme ilustrado no próximo diagrama:

1. Ative uma operação de permuta entre ambientes de produção e de preparo do aplicativo Web (6). Essa operação alterna as URLs dos dois ambientes. Agora `contoso.azurewebsites.net` aponta para a versão v2 do site e do banco de dados (ambiente de produção). 
2. Se você não precisar mais da versão v1, que se tornou uma cópia de preparo após a troca, você pode encerrar o ambiente de preparo (7).

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se o processo de atualização não for bem-sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparo a ser comprometido. Para reverter o aplicativo para o estado de pré-atualização, reverta o aplicativo no ambiente de produção para acesso completo. O próximo diagrama mostra as etapas de reversão:

1. Defina a cópia do banco de dados para o modo de leitura/gravação (8). Essa ação restaura a funcionalidade completa de v1 da cópia de produção.
2. Execute a análise de causa raiz e encerre o ambiente de preparo (9).

Neste ponto, o aplicativo é totalmente funcional e você pode repetir as etapas de atualização.

> [!NOTE]
> A reversão não exige alterações de DNS porque você ainda não executou uma operação de permuta.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

A principal vantagem dessa opção é que você pode atualizar um aplicativo em uma única região seguindo um conjunto de etapas simples. O custo de dólar da atualização é relativamente baixo. 

A principal compensação é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-atualização envolverá a reimplantação do aplicativo em uma região diferente e a restauração do banco de dados a partir do backup usando a restauração geográfica. Esse processo resulta em um tempo de inatividade significativo.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizar aplicativos que dependem da replicação geográfica do banco de dados para recuperação de desastre

Se seu aplicativo usar a replicação geográfica ativa ou grupos de failover automático para continuidade de negócios, ele será implantado em pelo menos duas regiões diferentes. Há um banco de dados ativo e primário em uma região primária e um banco de dados secundário somente leitura em uma região de backup. Junto com os fatores mencionados no início deste artigo, o processo de atualização também deve garantir que:

* O aplicativo permanece protegido contra falhas catastróficas em todos os momentos durante o processo de atualização.
* Os componentes com redundância geográfica do aplicativo são atualizados em paralelo com os componentes ativos.

Para atingir essas metas, além de usar os ambientes de aplicativos Web, você aproveitará o Gerenciador de tráfego do Azure usando um perfil de failover com um ponto de extremidade ativo e um ponto de extremidade de backup. O próximo diagrama ilustra o ambiente operacional antes do processo de atualização. Os sites `contoso-1.azurewebsites.net` e `contoso-dr.azurewebsites.net` representam um ambiente de produção do aplicativo com redundância geográfica total. O ambiente de produção inclui os seguintes componentes:

* O ambiente de produção do aplicativo Web `contoso-1.azurewebsites.net` na região primária (1)
* O banco de dados primário na região primária (2)
* Uma instância em espera do aplicativo Web na região de backup (3)
* O banco de dados secundário replicado geograficamente na região de backup (4)
* Um perfil de desempenho do Gerenciador de tráfego com um ponto de extremidade online chamado `contoso-1.azurewebsites.net` e um ponto de extremidade offline chamado `contoso-dr.azurewebsites.net`

Para tornar possível reverter a atualização, você deve criar um ambiente de preparo com uma cópia totalmente sincronizada do aplicativo. Como você precisa garantir que o aplicativo possa se recuperar rapidamente caso ocorra uma falha catastrófica durante o processo de atualização, o ambiente de preparo também deve ser com redundância geográfica. As etapas a seguir são necessárias para criar um ambiente de preparo para a atualização:

1. Implante um ambiente de preparo do aplicativo Web na região primária (6).
2. Crie um banco de dados secundário na região primária do Azure (7). Configure o ambiente de preparo do aplicativo Web para se conectar a ele. 
3. Crie outro banco de dados secundário com redundância geográfica na região de backup replicando o banco de dados secundário na região primária. (Esse método é chamado *de replicação geográfica encadeada*.) (8).
4. Implante um ambiente de preparo da instância do aplicativo Web na região de backup (9) e configure-o para conectar o banco de dados secundário com redundância geográfica criado em (8).

> [!NOTE]
> Essas etapas de preparação não afetarão o aplicativo no ambiente de produção. Ele permanecerá totalmente funcional no modo de leitura/gravação.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Quando as etapas de preparação forem concluídas, o ambiente de preparo estará pronto para a atualização. O próximo diagrama ilustra essas etapas de atualização:

1. Defina o banco de dados primário no ambiente de produção para o modo somente leitura (10). Esse modo garante que o banco de dados de produção (v1) não será alterado durante a atualização, impedindo, assim, a divergência entre as instâncias de Database v1 e v2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Termine a replicação geográfica desconectando o secundário (11). Essa ação cria uma cópia independente, mas totalmente sincronizada, do banco de dados de produção. Este banco de dados será atualizado. O exemplo a seguir usa o Transact-SQL, mas o [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) também está disponível. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Execute o script de atualização para `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`e o banco de dados primário de preparo (12). As alterações no banco de dados serão replicadas automaticamente para o secundário de preparo.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se a atualização for concluída com êxito, agora você estará pronto para alternar os usuários para a versão v2 do aplicativo. O próximo diagrama ilustra as etapas envolvidas:

1. Ative uma operação de permuta entre os ambientes de produção e de preparo do aplicativo Web na região primária (13) e na região de backup (14). A v2 do aplicativo agora se torna um ambiente de produção, com uma cópia redundante na região de backup.
2. Se você não precisar mais do aplicativo V1 (15 e 16), poderá encerrar o ambiente de preparo.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se o processo de atualização não for bem-sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparo para estar em um estado inconsistente. Para reverter o aplicativo para o estado de pré-atualização, reverta para usando v1 do aplicativo no ambiente de produção. As etapas necessárias são mostradas no diagrama a seguir:

1. Defina a cópia do banco de dados primário no ambiente de produção para o modo de leitura/gravação (17). Essa ação restaura a funcionalidade completa do v1 no ambiente de produção.
2. Execute a análise de causa raiz e repare ou remova o ambiente de preparo (18 e 19).

Neste ponto, o aplicativo é totalmente funcional e você pode repetir as etapas de atualização.

> [!NOTE]
> A reversão não exige alterações de DNS porque você não executou uma operação de permuta.

![Configuração de replicação geográfica do banco de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A principal vantagem dessa opção é que você pode atualizar o aplicativo e sua cópia com redundância geográfica em paralelo, sem comprometer sua continuidade de negócios durante a atualização.

A principal desvantagem é que ele requer redundância dupla de cada componente de aplicativo e, portanto, incorre em um custo de dólar maior. Ele também envolve um fluxo de trabalho mais complicado.

## <a name="summary"></a>Resumo

Os dois métodos de atualização descritos no artigo diferem na complexidade e no custo de dólar, mas ambos se concentram na minimização de quanto tempo o usuário está limitado a operações somente leitura. Essa hora é definida diretamente pela duração do script de atualização. Ele não depende do tamanho do banco de dados, da camada de serviço escolhida, da configuração do site ou de outros fatores que você não pode controlar facilmente. Todas as etapas de preparação são dissociadas das etapas de atualização e não afetam o aplicativo de produção. A eficiência do script de atualização é um fator fundamental que determina a experiência do usuário durante as atualizações. Portanto, a melhor maneira de melhorar essa experiência é concentrar seus esforços em tornar o script de atualização o mais eficiente possível.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
* Para saber mais sobre a replicação geográfica ativa do banco de dados SQL do Azure, confira [criar bancos de dados secundários legíveis usando a replicação geográfica ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de failover automático do banco de dados SQL do Azure, confira [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md).
* Para saber mais sobre ambientes de preparo no serviço Azure App, confira [configurar ambientes de preparo no serviço Azure app](../app-service/deploy-staging-slots.md).
* Para saber mais sobre os perfis do Gerenciador de tráfego do Azure, consulte [gerenciar um perfil do Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).
