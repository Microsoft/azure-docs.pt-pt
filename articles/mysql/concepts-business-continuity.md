---
title: Continuidade dos negócios-banco de dados do Azure para MySQL
description: Saiba mais sobre continuidade de negócios (restauração pontual, data center interrupção, restauração geográfica) ao usar o banco de dados do Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3f82dfd5e289b09761dbdbdc5af4da76d7c961d4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765363"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Entender a continuidade dos negócios no banco de dados do Azure para MySQL

Este artigo descreve os recursos que o banco de dados do Azure para MySQL fornece para a continuidade dos negócios e a recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos de interrupção que podem causar perda de dados ou fazer com que seu banco de dado e aplicativo se tornem indisponíveis. Saiba o que fazer quando um erro de usuário ou aplicativo afeta a integridade dos dados, uma região do Azure tem uma interrupção ou seu aplicativo requer manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que você pode usar para fornecer continuidade de negócios

O banco de dados do Azure para MySQL fornece recursos de continuidade de negócios que incluem backups automatizados e a capacidade de os usuários iniciarem a restauração geográfica. Cada um tem características diferentes para ERT (tempo de recuperação estimado) e potencial perda de dados. Depois de entender essas opções, você pode escolher entre elas e usá-las juntas para cenários diferentes. Ao desenvolver seu plano de continuidade de negócios, você precisa entender o tempo máximo aceitável antes que o aplicativo se recupere completamente após o evento de interrupção – esse é o RTO (objetivo de tempo de recuperação). Você também precisa entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar após o evento de interrupção – esse é o RPO (objetivo de ponto de recuperação).

A tabela a seguir compara o ERT e o RPO para os recursos disponíveis:

| **Recursos** | **Básica** | **Uso Geral** | **Com otimização de memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Restauração geográfica de backups replicados geograficamente | Não suportado | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Os servidores excluídos **não podem** ser restaurados. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de usuário ou aplicativo

Você pode usar os backups do serviço para recuperar um servidor de vários eventos de interrupção. Um usuário pode excluir acidentalmente alguns dados, remover inadvertidamente uma tabela importante ou até mesmo remover um banco de dado inteiro. Um aplicativo pode substituir acidentalmente bons dados com dados incorretos devido a um defeito do aplicativo e assim por diante.

Você pode executar uma restauração pontual para criar uma cópia do seu servidor para um momento bom conhecido no tempo. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor original pelo servidor restaurado recentemente ou copiar os dados necessários do servidor restaurado para o servidor original.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma interrupção de data center regional do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma interrupção, ela causa uma interrupção de negócios que pode durar apenas alguns minutos, mas pode durar por horas.

Uma opção é aguardar que o servidor volte a ficar online quando a interrupção de data center terminar. Isso funciona para aplicativos que podem deixar o servidor offline por um período de tempo, por exemplo, um ambiente de desenvolvimento. Quando data center tiver uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu servidor por algum tempo.

A outra opção é usar o recurso de restauração geográfica do banco de dados do Azure para MySQL que restaura o servidor usando backups com redundância geográfica. Esses backups são acessíveis mesmo quando a região em que o servidor está hospedado está offline. Você pode restaurar esses backups para qualquer outra região e colocar o servidor online novamente.

> [!IMPORTANT]
> A restauração geográfica só será possível se você tiver provisionado o servidor com o armazenamento de backup com redundância geográfica. Se desejar alternar de localmente redundante para backups com redundância geográfica para um servidor existente, você deverá fazer um despejo usando mysqldump do seu servidor existente e restaurá-lo para um servidor recém-criado configurado com backups com redundância geográfica.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [backups automatizados no banco de dados do Azure para MySQL](concepts-backup.md).
- Saiba como restaurar usando [o portal do Azure](howto-restore-server-portal.md) ou [o CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura no banco de dados do Azure para MySQL](concepts-read-replicas.md).
