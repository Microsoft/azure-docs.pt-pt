---
title: Implementar atualizações de aplicações
description: Saiba como utilizar a geo-replicação da Base de Dados Azure SQL para suportar atualizações rolantes da sua aplicação em nuvem
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: b7d21852ad684782fa1cb917442fee236d3c882b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502149"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Gerir atualizações rolantes de aplicações em nuvem utilizando a geo-replicação ativa da SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Saiba como utilizar a [geo-replicação ativa](auto-failover-group-overview.md) na Base de Dados Azure SQL para permitir atualizações rolantes da sua aplicação em nuvem. Como as atualizações são operações disruptivas, devem fazer parte do planeamento e design da sua continuidade de negócios. Neste artigo, olhamos para dois métodos diferentes de orquestração do processo de upgrade e discutimos os benefícios e trocas de cada opção. Para efeitos deste artigo, referimo-nos a uma aplicação que consiste num website que está ligado a uma única base de dados como o seu nível de dados. O nosso objetivo é atualizar a versão 1 (V1) da aplicação para a versão 2 (V2) sem qualquer impacto significativo na experiência do utilizador.

Ao avaliar as opções de upgrade, considere estes fatores:

* Impacto na disponibilidade de aplicações durante as atualizações, tais como a duração das funções de aplicação que podem ser limitadas ou degradadas.
* Capacidade de retroceda se a atualização falhar.
* Vulnerabilidade da aplicação se ocorrer uma falha catastrófica não relacionada durante a atualização.
* Custo total do dólar. Este fator inclui redundância adicional da base de dados e custos incrementais dos componentes temporários utilizados pelo processo de atualização.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizações de aplicações que dependem de backups de bases de dados para recuperação de desastres

Se a sua aplicação depender de cópias de dados automáticas e utilizar geo-restauro para recuperação de desastres, é implantada numa única região de Azure. Para minimizar a perturbação do utilizador, crie um ambiente de preparação naquela região com todos os componentes da aplicação envolvidos na atualização. O primeiro diagrama ilustra o ambiente operacional antes do processo de atualização. O ponto final `contoso.azurewebsites.net` representa um ambiente de produção da aplicação web. Para poder reverter a atualização, tem de criar um ambiente de preparação com uma cópia totalmente sincronizada da base de dados. Siga estes passos para criar um ambiente de preparação para a atualização:

1. Crie uma base de dados secundária na mesma região de Azure. Monitorize o secundário para ver se o processo de sementeira está completo (1).
2. Crie um novo ambiente para a sua aplicação web e chame-lhe 'Staging'. Será registado no Azure DNS com o URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Estes passos de preparação não afetarão o ambiente de produção, que pode funcionar em modo de acesso total.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para a recuperação de desastres em nuvem.](./media/manage-application-rolling-upgrade/option1-1.png)

Quando os passos de preparação estiverem completos, a aplicação está pronta para a atualização real. O próximo diagrama ilustra os passos envolvidos no processo de upgrade:

1. Desajuste a base de dados primária para o modo apenas de leitura (3). Este modo garante que o ambiente de produção da aplicação web (V1) permanece apenas lido durante a atualização, evitando assim a divergência de dados entre as instâncias da base de dados V1 e V2.
2. Desligue a base de dados secundária utilizando o modo de terminação planeado (4). Esta ação cria uma cópia totalmente sincronizada e independente da base de dados primária. Esta base de dados será atualizada.
3. Rode a base de dados secundária para o modo de leitura-escrita e execute o script de atualização (5).

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para a recuperação de desastres em nuvem que executa o script de atualização.](./media/manage-application-rolling-upgrade/option1-2.png)

Se a atualização terminar com sucesso, está agora pronta para mudar os utilizadores para a cópia atualizada da aplicação, que se torna um ambiente de produção. A troca envolve mais alguns passos, como ilustrado no diagrama seguinte:

1. Ative uma operação de troca entre ambientes de produção e encenação da aplicação web (6). Esta operação troca os URLs dos dois ambientes. Agora `contoso.azurewebsites.net` aponta para a versão V2 do web site e para a base de dados (ambiente de produção). 
2. Se já não precisar da versão V1, que se tornou uma cópia de encenação após a troca, pode desativar o ambiente de preparação (7).

![Configuração de geo-replicação da base de dados SQL para recuperação de desastres em nuvem.](./media/manage-application-rolling-upgrade/option1-3.png)

Se o processo de atualização não for bem sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparação comprometido. Para reverter a aplicação para o estado de pré-upgrade, reverta a aplicação no ambiente de produção para acesso total. O próximo diagrama mostra os passos de reversão:

1. Descreva a cópia da base de dados para o modo de leitura-escrita (8). Esta ação restaura a funcionalidade V1 completa da cópia de produção.
2. Efetue a análise da causa-raiz e desative o ambiente de preparação (9).

Neste ponto, a aplicação está totalmente funcional e pode repetir os passos de atualização.

> [!NOTE]
> O revés não requer alterações de DNS porque ainda não efetuou uma operação de troca.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para a recuperação de desastres em nuvem com o ambiente de preparação desativado.](./media/manage-application-rolling-upgrade/option1-4.png)

A principal vantagem desta opção é que você pode atualizar uma aplicação em uma única região seguindo um conjunto de passos simples. O custo do dólar da atualização é relativamente baixo. 

A principal compensação é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-actualização envolve a reafectação da aplicação numa região diferente e restaurar a base de dados a partir de cópias de segurança utilizando o geo-restauro. Este processo resulta em tempo de inatividade significativo.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizar aplicações que dependem da geo-replicação da base de dados para recuperação de desastres

Se a sua aplicação utilizar grupos de geo-replicação ativa ou de falha automática para a continuidade do negócio, é implantado em pelo menos duas regiões diferentes. Há uma base de dados primária ativa numa região primária e uma base de dados secundária só de leitura numa região de reserva. Juntamente com os fatores mencionados no início deste artigo, o processo de atualização deve também garantir que:

* A aplicação permanece sempre protegida contra falhas catastróficas durante o processo de atualização.
* Os componentes geo-redundantes da aplicação são atualizados em paralelo com os componentes ativos.

Para atingir estes objetivos, além de utilizar os ambientes de Aplicações Web, você vai aproveitar o Azure Traffic Manager usando um perfil de failover com um ponto final ativo e um ponto final de backup. O diagrama seguinte ilustra o ambiente operacional antes do processo de atualização. Os sites `contoso-1.azurewebsites.net` e representam um ambiente de `contoso-dr.azurewebsites.net` produção da aplicação com total redundância geográfica. O ambiente de produção inclui os seguintes componentes:

* O ambiente de produção da aplicação web `contoso-1.azurewebsites.net` na região primária (1)
* A base de dados primária na região primária (2)
* Uma instância de espera da aplicação web na região de backup (3)
* A base de dados secundária geo-replicada na região de backup (4)
* Um perfil de desempenho do Traffic Manager com um ponto final on-line chamado `contoso-1.azurewebsites.net` e um ponto final offline chamado `contoso-dr.azurewebsites.net`

Para permitir a reversão da atualização, tem de criar um ambiente de preparação com uma cópia totalmente sincronizada da aplicação. Uma vez que é necessário garantir que a aplicação pode recuperar rapidamente no caso de ocorrer uma falha catastrófica durante o processo de atualização, o ambiente de preparação também deve ser geo-redundante. São necessários os seguintes passos para criar um ambiente de preparação para a atualização:

1. Implementar um ambiente de preparação da aplicação web na região primária (6).
2. Criar uma base de dados secundária na região primária de Azure (7). Configure o ambiente de preparação da aplicação web para se conectar a ela. 
3. Criar outra base de dados secundária geo-redundante na região de backup, replicando a base de dados secundária na região primária. (Este método *chama-se geo-replicação acorrentada*.) (8).
4. Implementar um ambiente de preparação da aplicação web na região de backup (9) e configurá-lo para ligar a base de dados secundária geo-redundante criada em (8).

> [!NOTE]
> Estes passos de preparação não terão impacto na aplicação no ambiente de produção. Permanecerá totalmente funcional no modo de leitura-escrita.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para recuperação de desastres em nuvem com uma cópia totalmente sincronizada da aplicação.](./media/manage-application-rolling-upgrade/option2-1.png)

Quando os passos de preparação estiverem completos, o ambiente de preparação está pronto para a atualização. O próximo diagrama ilustra estes passos de upgrade:

1. Desajuste a base de dados primária no ambiente de produção para o modo apenas de leitura (10). Este modo garante que a base de dados de produção (V1) não se alterará durante a atualização, evitando assim a divergência de dados entre as instâncias da base de dados V1 e V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Terminar a geo-replicação desligando o secundário (11). Esta ação cria uma cópia independente mas totalmente sincronizada da base de dados de produção. Esta base de dados será atualizada. O exemplo a seguir utiliza o Transact-SQL, mas [o PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary) também está disponível. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Executar o script de atualização contra `contoso-1-staging.azurewebsites.net` , e a base de `contoso-dr-staging.azurewebsites.net` dados primária de encenação (12). As alterações na base de dados serão replicadas automaticamente para o estágio secundário.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para recuperação de desastres em nuvem com alterações na base de dados replicadas à encenação.](./media/manage-application-rolling-upgrade/option2-2.png)

Se a atualização terminar com sucesso, está agora pronto para mudar os utilizadores para a versão V2 da aplicação. O próximo diagrama ilustra os passos envolvidos:

1. Ativar uma operação de troca entre ambientes de produção e encenação da aplicação web na região primária (13) e na região de backup (14). V2 da aplicação passa a ser um ambiente de produção, com uma cópia redundante na região de reserva.
2. Se já não precisar da aplicação V1 (15 e 16), pode desativar o ambiente de preparação.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para a recuperação de desastres em nuvem com o desmantelamento opcional do ambiente de preparação.](./media/manage-application-rolling-upgrade/option2-3.png)

Se o processo de atualização não for bem sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparação num estado inconsistente. Para reverter a aplicação para o estado de pré-actualização, volte a utilizar a V1 da aplicação no ambiente de produção. Os passos necessários são mostrados no diagrama seguinte:

1. Descreva a cópia da base de dados primária no ambiente de produção para o modo de leitura-escrita (17). Esta ação restaura a funcionalidade V1 completa no ambiente de produção.
2. Efetuar a análise e reparação de causas de raiz ou remover o ambiente de preparação (18 e 19).

Neste ponto, a aplicação está totalmente funcional e pode repetir os passos de atualização.

> [!NOTE]
> O revés não requer alterações de DNS porque não efetuou uma operação de troca.

![O diagrama mostra a configuração de geo-replicação da base de dados SQL para recuperação de desastres em nuvem com o processo de atualização recuado.](./media/manage-application-rolling-upgrade/option2-4.png)

A principal vantagem desta opção é que pode atualizar a aplicação e a sua cópia geo-redundante em paralelo sem comprometer a continuidade do seu negócio durante a atualização.

A principal compensação é que requer o dobro do despedimento de cada componente de aplicação e, por conseguinte, incorre num custo mais elevado em dólares. Envolve também um fluxo de trabalho mais complicado.

## <a name="summary"></a>Resumo

Os dois métodos de atualização descritos no artigo diferem em complexidade e custo do dólar, mas ambos se concentram em minimizar o tempo que o utilizador está limitado a operações apenas de leitura. Esse tempo é definido diretamente pela duração do script de atualização. Não depende do tamanho da base de dados, do nível de serviço que escolheu, da configuração do site ou de outros fatores que não pode controlar facilmente. Todos os passos de preparação são dissociados das etapas de upgrade e não impactam a aplicação de produção. A eficiência do script de upgrade é um fator chave que determina a experiência do utilizador durante as atualizações. Assim, a melhor maneira de melhorar essa experiência é concentrar os seus esforços em tornar o script de upgrade o mais eficiente possível.

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para obter uma geo-replicação ativa da Base de Dados Azure SQL, consulte [criar bases de dados secundárias legíveis utilizando a geo-replicação ativa.](active-geo-replication-overview.md)
* Para obter mais sobre os grupos de auto-falência da Base de Dados Azure SQL, consulte [utilize grupos de falha automática para permitir a falha transparente e coordenada de várias bases de dados](auto-failover-group-overview.md).
* Para saber sobre ambientes de encenação no Azure App Service, consulte [Configurar ambientes de preparação no Azure App Service](../../app-service/deploy-staging-slots.md).
* Para saber mais sobre os perfis do Gestor de Tráfego da Azure, consulte [gerir um perfil do Gestor de Tráfego Azure](../../traffic-manager/traffic-manager-manage-profiles.md).
