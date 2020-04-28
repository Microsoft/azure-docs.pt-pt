---
title: Implementar atualizações de aplicações
description: Saiba como utilizar a geo-replicação da Base de Dados Azure SQL para suportar atualizações online da sua aplicação na nuvem.
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
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822863"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Gerir atualizações rolantes de aplicações em nuvem utilizando geo-replicação ativa sQL Database

Aprenda a utilizar a [geo-replicação ativa](sql-database-auto-failover-group.md) na Base de Dados Azure SQL para permitir atualizações da sua aplicação em nuvem. Como as atualizações são operações disruptivas, devem fazer parte do planeamento e design de continuidade do negócio. Neste artigo, olhamos para dois métodos diferentes de orquestrar o processo de upgrade e discutir os benefícios e trocas de cada opção. Para efeitos deste artigo, referimo-nos a uma aplicação que consiste num website que está ligado a uma única base de dados como seu nível de dados. O nosso objetivo é atualizar a versão 1 (V1) da aplicação para a versão 2 (V2) sem qualquer impacto significativo na experiência do utilizador.

Ao avaliar as opções de upgrade, considere estes fatores:

* Impacto na disponibilidade de aplicações durante as atualizações, tais como o tempo que as funções de aplicação podem ser limitadas ou degradadas.
* Capacidade de retrocesso se a atualização falhar.
* Vulnerabilidade da aplicação se ocorrer uma falha não relacionada e catastrófica durante a atualização.
* Custo total do dólar. Este fator inclui redundância adicional na base de dados e custos incrementais dos componentes temporários utilizados pelo processo de atualização.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizar aplicações que dependem de backups de base de dados para recuperação de desastres

Se a sua aplicação depender de cópias de dados automáticas e utilizar geo-restauro para recuperação de desastres, está implantada numa única região do Azure. Para minimizar a perturbação do utilizador, crie um ambiente de paragem naquela região com todos os componentes da aplicação envolvidos na atualização. O primeiro diagrama ilustra o ambiente operacional antes do processo de atualização. O ponto `contoso.azurewebsites.net` final representa um ambiente de produção da aplicação web. Para poder reverter a atualização, deve criar um ambiente de encenação com uma cópia totalmente sincronizada da base de dados. Siga estes passos para criar um ambiente de preparação para a atualização:

1. Crie uma base de dados secundária na mesma região de Azure. Monitorize o secundário para ver se o processo de sementeir está concluído (1).
2. Crie um novo ambiente para a sua aplicação web e chame-a de 'Staging'. Será registado em Azure DNS `contoso-staging.azurewebsites.net` com o URL (2).

> [!NOTE]
> Estes passos de preparação não afetarão o ambiente de produção, que pode funcionar em modo de acesso total.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Quando os passos de preparação estiverem completos, a aplicação está pronta para a atualização real. O diagrama seguinte ilustra os passos envolvidos no processo de atualização:

1. Detete a base de dados primária para o modo de leitura (3). Este modo garante que o ambiente de produção da aplicação web (V1) permanece apenas lido durante a atualização, evitando assim a divergência de dados entre as instâncias de base de dados V1 e V2.
2. Desligue a base de dados secundária utilizando o modo de terminação planeado (4). Esta ação cria uma cópia totalmente sincronizada e independente da base de dados primária. Esta base de dados será atualizada.
3. Rode a base de dados secundária para o modo de leitura-escrita e execute o script de atualização (5).

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se a atualização terminar com sucesso, está agora pronto para mudar os utilizadores para a cópia atualizada da aplicação, que se torna um ambiente de produção. A comutação envolve mais alguns passos, como ilustrado no próximo diagrama:

1. Ativar uma operação de troca entre ambientes de produção e encenação da aplicação web (6). Esta operação comuta os URLs dos dois ambientes. Agora `contoso.azurewebsites.net` aponta para a versão V2 do web site e da base de dados (ambiente de produção). 
2. Se já não precisar da versão V1, que se tornou uma cópia de encenação após a troca, pode desativar o ambiente de encenação (7).

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se o processo de atualização não for bem sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparação comprometido. Para reverter a aplicação para o estado de pré-actualização, reverta a aplicação no ambiente de produção para acesso total. O próximo diagrama mostra os passos de reversão:

1. Detete a cópia da base de dados para o modo de leitura-escrita (8). Esta ação restaura a funcionalidade V1 completa da cópia de produção.
2. Efetuar a análise da causa-raiz e desativar o ambiente de encenação (9).

Neste ponto, a aplicação está totalmente funcional e pode repetir os passos de upgrade.

> [!NOTE]
> A reversão não requer alterações dNS porque ainda não executou uma operação de troca.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

A principal vantagem desta opção é que você pode atualizar uma aplicação em uma única região seguindo um conjunto de passos simples. O custo do dólar da atualização é relativamente baixo. 

A principal contrapartida é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-actualização implica reimplantar a aplicação numa região diferente e restaurar a base de dados de backup utilizando o geo-restauro. Este processo resulta em tempo de inatividade significativo.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizações de aplicações que dependem da geo-replicação da base de dados para recuperação de desastres

Se a sua aplicação utilizar grupos de geo-replicação ou auto-failover ativos para continuidade de negócios, está implantado em pelo menos duas regiões diferentes. Há uma base de dados primária ativa numa região primária e uma base de dados secundária apenas para leitura numa região de reserva. Juntamente com os fatores mencionados no início deste artigo, o processo de atualização deve também garantir que:

* A aplicação permanece protegida de falhas catastróficas em todos os momentos durante o processo de atualização.
* Os componentes geo-redundantes da aplicação são atualizados em paralelo com os componentes ativos.

Para atingir estes objetivos, além de utilizar os ambientes de Web Apps, irá aproveitar o Gestor de Tráfego do Azure utilizando um perfil de failover com um ponto final ativo e um ponto final de backup. O diagrama seguinte ilustra o ambiente operacional antes do processo de atualização. Os web `contoso-1.azurewebsites.net` `contoso-dr.azurewebsites.net` sites e representam um ambiente de produção da aplicação com total redundância geográfica. O ambiente de produção inclui os seguintes componentes:

* O ambiente de produção `contoso-1.azurewebsites.net` da aplicação web na região primária (1)
* A base de dados primária na região primária (2)
* Uma instância de espera da aplicação web na região de backup (3)
* A base de dados secundária geo-replicada na região de backup (4)
* Um perfil de desempenho do Traffic `contoso-1.azurewebsites.net` Manager com um ponto final on-line chamado e um ponto final offline chamado`contoso-dr.azurewebsites.net`

Para permitir a reversão da atualização, deve criar um ambiente de encenação com uma cópia totalmente sincronizada da aplicação. Uma vez que é necessário garantir que a aplicação possa recuperar rapidamente caso ocorra uma falha catastrófica durante o processo de atualização, o ambiente de preparação deve ser também geo-redundante. São necessários os seguintes passos para criar um ambiente de preparação para a atualização:

1. Implementar um ambiente de encenação da aplicação web na região primária (6).
2. Criar uma base de dados secundária na região primária de Azure (7). Configure o ambiente de encenação da aplicação web para se ligar a ela. 
3. Criar outra base de dados geo-redundante e secundária na região de backup, replicando a base de dados secundária na região primária. (Este método *chama-se geo-replicação acorrentada*.) (8).
4. Implementar um ambiente de encenação da instância da aplicação web na região de backup (9) e configurá-lo para ligar a base de dados secundária georedundante criada em (8).

> [!NOTE]
> Estes passos de preparação não afetarão a aplicação no ambiente de produção. Permanecerá totalmente funcional no modo de leitura-escrita.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Quando os passos de preparação estiverem completos, o ambiente de preparação está pronto para a atualização. O diagrama seguinte ilustra estes passos de atualização:

1. Detete a base de dados primária no ambiente de produção para o modo de leitura (10). Este modo garante que a base de dados de produção (V1) não se altera durante a atualização, evitando assim a divergência de dados entre as instâncias de base de dados V1 e V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Terminar a geo-replicação desligando o secundário (11). Esta ação cria uma cópia independente, mas totalmente sincronizada, da base de dados de produção. Esta base de dados será atualizada. O exemplo que se segue utiliza a Transact-SQL, mas a [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) também está disponível. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Executar o script `contoso-1-staging.azurewebsites.net` `contoso-dr-staging.azurewebsites.net`de atualização contra , e a base de dados primária de encenação (12). As alterações na base de dados serão replicadas automaticamente para a encenação secundária.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se a atualização terminar com sucesso, está agora pronto para mudar os utilizadores para a versão V2 da aplicação. O próximo diagrama ilustra os passos envolvidos:

1. Ativar uma operação de troca entre ambientes de produção e encenação da aplicação web na região primária (13) e na região de backup (14). A V2 da aplicação torna-se agora um ambiente de produção, com uma cópia redundante na região de backup.
2. Se já não necessitar da aplicação V1 (15 e 16), pode desativar o ambiente de preparação.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se o processo de atualização não for bem sucedido (por exemplo, devido a um erro no script de atualização), considere o ambiente de preparação num estado inconsistente. Para reverter a aplicação para o estado de pré-actualização, volte a utilizar a V1 da aplicação no ambiente de produção. Os passos necessários são mostrados no próximo diagrama:

1. Detete a cópia da base de dados primária no ambiente de produção para o modo de leitura-escrita (17). Esta ação restaura a funcionalidade V1 completa no ambiente de produção.
2. Efetuar a análise da causa da raiz e reparar ou remover o ambiente de paragem (18 e 19).

Neste ponto, a aplicação está totalmente funcional e pode repetir os passos de upgrade.

> [!NOTE]
> A reversão não requer alterações dNS porque não executou uma operação de troca.

![Configuração de geo-replicação de geo-replicação da Base de Dados SQL para recuperação de desastres em nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A principal vantagem desta opção é que pode atualizar a aplicação e a sua cópia georedundante paralelamente sem comprometer a continuidade do seu negócio durante a atualização.

A principal contrapartida é que requer o dobro do despedimento de cada componente de aplicação e, por conseguinte, incorre num custo mais elevado do dólar. Envolve também um fluxo de trabalho mais complicado.

## <a name="summary"></a>Resumo

Os dois métodos de atualização descritos no artigo diferem em complexidade e custo do dólar, mas ambos se concentram em minimizar o tempo que o utilizador está limitado a operações apenas de leitura. Esse tempo é definido diretamente pela duração do script de atualização. Não depende do tamanho da base de dados, do nível de serviço que escolheu, da configuração do site ou de outros fatores que não consegue controlar facilmente. Todas as etapas de preparação são dissociadas das etapas de upgrade e não impactam a aplicação de produção. A eficiência do script de upgrade é um fator chave que determina a experiência do utilizador durante as atualizações. Assim, a melhor maneira de melhorar essa experiência é concentrar os seus esforços em tornar o script de upgrade o mais eficiente possível.

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio.](sql-database-business-continuity.md)
* Para saber sobre a geo-replicação ativa da Base de Dados Azure SQL, consulte Criar bases de [dados secundárias legíveis utilizando geo-replicação ativa](sql-database-active-geo-replication.md).
* Para conhecer os grupos de falha automática da Base de Dados Azure SQL, consulte Utilize grupos de falha automática para permitir falhas [transparentes e coordenadas de várias bases de dados](sql-database-auto-failover-group.md).
* Para aprender sobre ambientes de encenação no Serviço de Aplicações Azure, consulte [Configurar ambientes de encenação no Serviço de Aplicações Azure](../app-service/deploy-staging-slots.md).
* Para saber sobre os perfis do Gestor de Tráfego Azure, consulte Gerir um perfil de Gestor de [Tráfego Azure](../traffic-manager/traffic-manager-manage-profiles.md).
