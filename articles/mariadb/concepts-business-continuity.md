---
title: Entender a continuidade dos negócios no banco de dados do Azure para MariaDB
description: Saiba mais sobre continuidade de negócios (restauração pontual, data center interrupção, restauração geográfica) ao usar o banco de dados do Azure para serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d364c3a5fafaca514baade25cd3f5b4c0f10dc26
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973600"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Entender a continuidade dos negócios no banco de dados do Azure para MariaDB

Este artigo descreve os recursos que o banco de dados do Azure para MariaDB fornece para a continuidade dos negócios e a recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos de interrupção que podem causar perda de dados ou fazer com que seu banco de dado e aplicativo se tornem indisponíveis. Saiba o que fazer quando um erro de usuário ou aplicativo afeta a integridade dos dados, uma região do Azure tem uma interrupção ou seu aplicativo requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que você pode usar para fornecer continuidade de negócios

O banco de dados do Azure para MariaDB fornece recursos de continuidade de negócios que incluem backups automatizados e a capacidade de os usuários iniciarem a restauração geográfica. Cada um tem características diferentes para ERT (tempo de recuperação estimado) e potencial perda de dados. Depois de entender essas opções, você pode escolher entre elas e usá-las juntas para cenários diferentes. Ao desenvolver seu plano de continuidade de negócios, você precisa entender o tempo máximo aceitável antes que o aplicativo se recupere completamente após o evento de interrupção – esse é o RTO (objetivo de tempo de recuperação). Você também precisa entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar após o evento de interrupção – esse é o RPO (objetivo de ponto de recuperação).

A tabela a seguir compara o ERT e o RPO para os recursos disponíveis:

| **Recursos** | **Básica** | **Uso Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Restauração geográfica de backups replicados geograficamente | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Se você excluir o servidor, todos os bancos de dados contidos no servidor também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de usuário ou aplicativo

Você pode usar os backups do serviço para recuperar um servidor de vários eventos de interrupção. Um usuário pode excluir acidentalmente alguns dados, remover inadvertidamente uma tabela importante ou até mesmo remover um banco de dado inteiro. Um aplicativo pode substituir acidentalmente bons dados com dados incorretos devido a um defeito do aplicativo e assim por diante.

Você pode executar uma restauração pontual para criar uma cópia do seu servidor para um momento bom conhecido no tempo. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor original pelo servidor restaurado recentemente ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma interrupção de data center regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma interrupção, ela causa uma interrupção de negócios que pode durar apenas alguns minutos, mas pode durar por horas.

Uma opção é aguardar que o servidor volte a ficar online quando a interrupção de data center terminar. Isso funciona para aplicativos que podem deixar o servidor offline por um período de tempo, por exemplo, um ambiente de desenvolvimento. Quando data center tiver uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu servidor por algum tempo.

A outra opção é usar o recurso de restauração geográfica do banco de dados do Azure para MariaDB que restaura o servidor usando backups com redundância geográfica. Esses backups são acessíveis mesmo quando a região em que o servidor está hospedado está offline. Você pode restaurar esses backups para qualquer outra região e colocar o servidor online novamente.

> [!IMPORTANT]
> A restauração geográfica só será possível se você tiver provisionado o servidor com o armazenamento de backup com redundância geográfica.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os backups automatizados, consulte [backups no banco de dados do Azure para MariaDB](concepts-backup.md).
- Para restaurar para um ponto no tempo usando o portal do Azure, consulte [restaurar banco de dados para um ponto no tempo usando o portal do Azure](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
