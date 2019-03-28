---
title: Implementar atualizações de aplicações - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como utilizar georreplicação de base de dados do Azure SQL para oferecer suporte a atualizações online da sua aplicação na cloud.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 63f301b4618df9764460d0a9a133834fb72e33bb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540589"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Gerir atualizações sem interrupção das aplicações na cloud com a base de dados SQL georreplicação ativa

Aprenda a usar [georreplicação ativa](sql-database-auto-failover-group.md) na base de dados do Azure SQL para ativar a implementação de atualizações da sua aplicação na cloud. Uma vez que as atualizações são disruptivas operações, eles devem ser parte de seu planejamento de continuidade do negócio e design. Neste artigo, vamos observar dois métodos diferentes para orquestrar o processo de atualização e discutir os benefícios e compromissos de cada opção. Para os fins deste artigo, nos Referimos a um aplicativo que consiste num Web site que está ligado a uma base de dados como sua camada de dados. Nosso objetivo é atualizar a versão 1 (V1) do aplicativo para a versão 2 (V2) sem qualquer impacto significativo na experiência do usuário.

Ao avaliar as opções de atualização, considere estes fatores:

* Impacto na disponibilidade de aplicações durante as atualizações, como quanto as funções de aplicação podem ser limitadas ou degradadas.
* Capacidade de reverta se a atualização falhar.
* Vulnerabilidade do aplicativo se ocorrer uma falha de não relacionada, muito grave durante a atualização.
* Dólar de total de custos. Esse fator inclui redundância da base de dados adicionais e os custos incrementais dos componentes temporários utilizados pelo processo de atualização.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Atualizar aplicações que dependem de cópias de segurança da base de dados para recuperação após desastre

Se seu aplicativo depende de cópias de segurança automáticas da base de dados e utiliza o restauro geográfico para recuperação após desastre, é implementada numa única região do Azure. Para minimizar a interrupção de utilizador, crie um ambiente de teste nessa região com todos os componentes de aplicação envolvidos na atualização. O primeiro diagrama ilustra o ambiente operacional antes do processo de atualização. O ponto final `contoso.azurewebsites.net` representa um ambiente de produção da aplicação web. Para conseguir reverter a atualização, tem de criar um ambiente de teste com uma cópia totalmente sincronizada da base de dados. Siga estes passos para criar um ambiente de teste para a atualização:

1. Crie uma base de dados secundário na mesma região do Azure. Monitorize o secundário para ver se o processo de propagação é concluída (1).
2. Criar um novo ambiente para a sua aplicação web e chamá-lo de "Teste". Ele será registado no DNS do Azure com o URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Estes passos de preparação não afetarão o ambiente de produção, que pode funcionar no modo de acesso total.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Quando os passos de preparação estiverem concluídos, a aplicação está pronta para a atualização real. O diagrama seguinte ilustra as etapas envolvidas no processo de atualização:

1. Defina a base de dados principal para o modo só de leitura (3). Neste modo, garante que o ambiente de produção da aplicação web (V1) permanece só de leitura durante a atualização, evitando assim o potencial divergência de dados entre as instâncias de base de dados V1 e V2.
2. Desligar a base de dados secundário utilizando o modo de terminação planeada (4). Esta ação cria uma cópia totalmente sincronizada, independente da base de dados primária. Esta base de dados será atualizado.
3. Ativar a base de dados secundária para o modo de leitura / escrita e execute o script de atualização (5).

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se a atualização for concluída com êxito, agora está pronto para mudar os utilizadores para a cópia atualizada do aplicativo, o que torna-se de um ambiente de produção. Mudar envolve mais algumas etapas, conforme ilustrado no diagrama seguinte:

1. Ative uma operação de troca entre a produção e ambientes de teste da aplicação web (6). Esta operação muda os URLs dos dois ambientes. Agora `contoso.azurewebsites.net` aponta para a versão V2 do web site e a base de dados (ambiente de produção). 
2. Se já não precisar da versão de V1, que se tornou uma cópia de teste após a troca, pode desativar o ambiente de teste (7).

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se o processo de atualização for concluída com êxito (por exemplo, devido a um erro no script de atualização), considere o ambiente de teste para ser comprometido. Para reverter o aplicativo para o estado de pré-atualização, reverta a aplicação no ambiente de produção para acesso total. O diagrama seguinte mostra os passos de reversion:

1. Defina a cópia da base de dados para o modo de leitura / escrita (8). Esta ação restaura todas as funcionalidades de V1 da cópia de produção.
2. Executar a análise de causa raiz e desativar o ambiente de teste (9).

Neste momento, o aplicativo é totalmente funcional e pode repetir os passos de atualização.

> [!NOTE]
> A reversão não requer alterações DNS, porque ainda não tiver sido executada uma operação de troca.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

A principal vantagem desta opção é que pode atualizar uma aplicação numa única região, seguindo um conjunto de passos simples. O custo de dólar da atualização é relativamente baixo. 

A principal desvantagem é que, se ocorrer uma falha catastrófica durante a atualização, a recuperação para o estado de pré-atualização envolve a reimplantação do aplicativo numa região diferente e restaurar a base de dados a partir da cópia de segurança utilizando o restauro geográfico. Este processo traduz-se no período de indisponibilidade significativo.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Atualizar aplicações que dependem de georreplicação da base de dados para recuperação após desastre

Se a sua aplicação utilizar a georreplicação ativa ou grupos de ativação pós-falha automática para continuidade do negócio, ele é implementado, pelo menos, duas regiões diferentes. Existe uma base de dados do Active Directory, principal numa região primária e uma base de dados só de leitura, secundária numa região cópia de segurança. Juntamente com os fatores mencionados no início deste artigo, o processo de atualização deve também garantir que:

* O aplicativo permanece protegido contra falhas catastróficas durante todo o tempo durante o processo de atualização.
* Os componentes com redundância geográfica do aplicativo são atualizados em paralelo com os componentes do Active Directory.

Para alcançar estes objetivos, além de utilizar os ambientes de aplicações Web, irá se beneficiar do Gestor de tráfego do Azure com um perfil de ativação pós-falha com um ponto final ativo e um ponto de extremidade de cópia de segurança. O diagrama seguinte ilustra o ambiente operacional antes do processo de atualização. Os web sites `contoso-1.azurewebsites.net` e `contoso-dr.azurewebsites.net` representam um ambiente de produção do aplicativo com redundância geográfica completa. O ambiente de produção inclui os seguintes componentes:

* O ambiente de produção da aplicação web `contoso-1.azurewebsites.net` na região primária (1)
* A base de dados primária na região primária (2)
* Uma instância de reserva dinâmica da aplicação web na região de cópia de segurança (3)
* A georreplicação secundária base de dados na região de cópia de segurança (4)
* Um perfil de desempenho do Gestor de tráfego com um ponto de extremidade online chamado `contoso-1.azurewebsites.net` e chamado de um ponto final offline `contoso-dr.azurewebsites.net`

Para que seja possível reverter a atualização, tem de criar um ambiente de teste com uma cópia totalmente sincronizada do aplicativo. Como precisa garantir que o aplicativo pode recuperar rapidamente no caso de ocorrer uma falha catastrófica durante o processo de atualização, o ambiente de teste deve ser georredundante também. Os seguintes passos são necessários para criar um ambiente de teste para a atualização:

1. Implemente um ambiente de teste da aplicação web na região primária (6).
2. Crie uma base de dados secundária na região primária do Azure (7). Configure o ambiente de teste da aplicação web para ligar ao mesmo. 
3. Crie outra base de dados georredundante, secundário na região de cópia de segurança através da replicação de base de dados secundária numa região primária. (Esse método é chamado *encadeados georreplicação*.) (8).
4. Implementar um ambiente de teste da instância de aplicação web na região de cópia de segurança (9) e configurá-lo para ligar a base de dados secundária georredundante criado às (8).

> [!NOTE]
> Estes passos de preparação não afetam a aplicação no ambiente de produção. Irá permanecer totalmente funcional no modo de leitura / escrita.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Quando os passos de preparação estiverem concluídos, o ambiente de teste está pronto para a atualização. O diagrama seguinte ilustra estes passos de atualização:

1. Defina a base de dados primária no ambiente de produção para o modo só de leitura (10). Neste modo, garante que a base de dados de produção (V1) não será alterado durante a atualização, impedindo a potencial divergência de dados entre as instâncias de base de dados V1 e V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Termine a georreplicação ao desconectar secundário (11). Esta ação cria uma cópia totalmente sincronizada mas independente da base de dados de produção. Esta base de dados será atualizado. O exemplo seguinte utiliza o Transact-SQL, mas [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) também está disponível. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABSE V1
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Execute o script de atualização em relação a `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`e o teste principal da base de dados (12). As alterações de base de dados serão replicadas automaticamente para o teste secundário.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se a atualização for concluída com êxito, agora está pronto para alternar os usuários para a versão V2 do aplicativo. O diagrama seguinte ilustra as etapas envolvidas:

1. Ative uma operação de troca entre a produção e ambientes de teste da aplicação web na região primária (13) e na região de cópia de segurança (14). V2 do aplicativo agora se torna um ambiente de produção, com uma cópia redundante na região de cópia de segurança.
2. Se já não precisar da aplicação V1 (15 e 16), pode desativar o ambiente de teste.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se o processo de atualização for concluída com êxito (por exemplo, devido a um erro no script de atualização), considere o ambiente de teste num estado inconsistente. Para reverter o aplicativo para o estado de pré-atualização, reverta para utilizar a V1 do aplicativo no ambiente de produção. Os passos necessários são apresentados no diagrama seguinte:

1. Defina a cópia da base de dados primária no ambiente de produção para o modo de leitura / escrita (17). Esta ação restaura todas as funcionalidades V1 no ambiente de produção.
2. Executar a análise de causa raiz e reparar ou remover o ambiente de teste (18 e 19).

Neste momento, o aplicativo é totalmente funcional e pode repetir os passos de atualização.

> [!NOTE]
> A reversão não requer que alterações de DNS porque não executar uma operação de troca.

![Configuração de georreplicação de base de dados SQL para recuperação de desastre na nuvem.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A principal vantagem desta opção é que pode atualizar o aplicativo e sua cópia georredundante em paralelo sem comprometer a continuidade do negócio durante a atualização.

A principal desvantagem é que ele requer redundância dupla de cada componente da aplicação e, portanto, incorre em custo mais elevado de dólar. Ela também envolve um fluxo de trabalho mais complicado.

## <a name="summary"></a>Resumo

Os dois métodos de atualização descritos no artigo diferem em complexidade e o custo de dólar, mas ambos se concentrar em minimizando o tempo que o utilizador está limitado a operações só de leitura. Esse tempo é definido diretamente pela duração do script de atualização. Não depende do tamanho de base de dados, a camada de serviços que escolheu, a configuração do Web site ou outros fatores que não pode controlar facilmente. Todos os passos de preparação estão desassociados dos passos de atualização e não tenham impacto sobre a aplicação de produção. A eficiência do script de atualização é um fator-chave que determina a experiência do usuário durante as atualizações. Por isso, a melhor forma de melhorar essa experiência é concentrar seus esforços em conferir o script de atualização mais eficiente possível.

## <a name="next-steps"></a>Passos Seguintes

* Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
* Para saber mais sobre a base de dados do Azure SQL georreplicação ativa, veja [criar legíveis bases de dados secundárias com georreplicação ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre os grupos de ativação pós-falha automática da base de dados do Azure SQL, veja [utilizar grupos de ativação pós-falha automática para ativar a ativação pós-falha transparente e coordenada de várias bases de dados](sql-database-auto-failover-group.md).
* Para saber mais sobre ambientes de teste no serviço de aplicações do Azure, veja [configurar ambientes de teste no serviço de aplicações do Azure](../app-service/deploy-staging-slots.md).
* Para saber mais sobre os perfis do Gestor de tráfego do Azure, veja [gerir um perfil do Gestor de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).
