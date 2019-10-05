---
title: Visão geral da continuidade de negócios com o banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve a continuidade dos negócios (restauração pontual, data center interrupção, restauração geográfica) ao usar o banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 3623611bcd22486d90651c6e8b6880c6de1de0c5
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950101"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Visão geral da continuidade de negócios com o banco de dados do Azure para PostgreSQL-servidor único

Esta visão geral descreve os recursos que o banco de dados do Azure para PostgreSQL fornece para continuidade dos negócios e recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos de interrupção que podem causar perda de dados ou fazer com que seu banco de dado e aplicativo se tornem indisponíveis. Saiba o que fazer quando um erro de usuário ou aplicativo afeta a integridade dos dados, uma região do Azure tem uma interrupção ou seu aplicativo requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que você pode usar para fornecer continuidade de negócios

O banco de dados do Azure para PostgreSQL fornece recursos de continuidade de negócios que incluem backups automatizados e a capacidade de os usuários iniciarem a restauração geográfica. Cada um tem características diferentes para ERT (tempo de recuperação estimado) e potencial perda de dados. Depois de entender essas opções, você pode escolher entre elas e usá-las juntas para cenários diferentes. Ao desenvolver seu plano de continuidade de negócios, você precisa entender o tempo máximo aceitável antes que o aplicativo se recupere completamente após o evento de interrupção – esse é o RTO (objetivo de tempo de recuperação). Você também precisa entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar após o evento de interrupção – esse é o RPO (objetivo de ponto de recuperação).

A tabela a seguir compara o ERT e o RPO para os recursos disponíveis:

| **Recursos** | **Básica** | **Uso Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Restauração geográfica de backups replicados geograficamente | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Os servidores excluídos **não podem** ser restaurados. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Use o [bloqueio de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para ajudar a evitar a exclusão acidental do seu servidor.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de usuário ou aplicativo

Você pode usar os backups do serviço para recuperar um servidor de vários eventos de interrupção. Um usuário pode excluir acidentalmente alguns dados, remover inadvertidamente uma tabela importante ou até mesmo remover um banco de dado inteiro. Um aplicativo pode substituir acidentalmente bons dados com dados incorretos devido a um defeito do aplicativo e assim por diante.

Você pode executar uma **restauração pontual** para criar uma cópia do seu servidor para um momento bom conhecido no tempo. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor original pelo servidor restaurado recentemente ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-data-center-outage"></a>Recuperar de uma interrupção de data center do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma interrupção, ela causa uma interrupção de negócios que pode durar apenas alguns minutos, mas pode durar por horas.

Uma opção é aguardar que o servidor volte a ficar online quando a interrupção de data center terminar. Isso funciona para aplicativos que podem deixar o servidor offline por um período de tempo, por exemplo, um ambiente de desenvolvimento. Quando um data center tem uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu servidor por algum tempo.

## <a name="geo-restore"></a>Georrestauro

O recurso de restauração geográfica restaura o servidor usando backups com redundância geográfica. Os backups são hospedados na [região emparelhada](../best-practices-availability-paired-regions.md)do servidor. Você pode restaurar esses backups para qualquer outra região. A restauração geográfica cria um novo servidor com os dados dos backups. Saiba mais sobre a restauração geográfica no [artigo conceitos de backup e restauração](concepts-backup.md).

> [!IMPORTANT]
> A restauração geográfica só será possível se você tiver provisionado o servidor com o armazenamento de backup com redundância geográfica. Se desejar alternar de localmente redundante para backups com redundância geográfica para um servidor existente, você deverá fazer um despejo usando pg_dump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com backups com redundância geográfica.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura entre regiões
Você pode usar réplicas de leitura entre regiões para aprimorar sua continuidade de negócios e planejamento de recuperação de desastre. As réplicas de leitura são atualizadas assincronamente usando a tecnologia de replicação física do PostgreSQL. Saiba mais sobre réplicas de leitura, regiões disponíveis e como fazer failover do [artigo conceitos de leitura de réplicas](concepts-read-replicas.md). 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [backups automatizados no banco de dados do Azure para PostgreSQL](concepts-backup.md). 
- Saiba como restaurar usando [o portal do Azure](howto-restore-server-portal.md) ou [o CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura no banco de dados do Azure para PostgreSQL](concepts-read-replicas.md).