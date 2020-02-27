---
title: Backup e restauro - Base de Dados Azure para MariaDB
description: Saiba mais sobre cópias de segurança automáticas e restaurar a sua Base de Dados Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e10c23aaaef6315e072348d879d5f077e16382a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623664"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Backup e restauro na Base de Dados Azure para MariaDB

A Base de Dados Azure para MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Base de Dados Azure para MariaDB tem cópias de segurança completas, diferenciais e de registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto-a-tempo dentro do período de retenção de cópia de segurança configurado. O período de retenção de reserva padrão é de sete dias. Pode configurá-lo opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Estes ficheiros de reserva não podem ser exportados. As cópias de segurança só podem ser utilizadas para restaurar as operações na Base de Dados Azure para o MariaDB. Pode usar [mysqldump](howto-migrate-dump-restore.md) para copiar uma base de dados.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

Normalmente, as cópias de segurança completas ocorrem semanalmente, as cópias de segurança diferenciais ocorrem duas vezes por dia e as cópias de segurança de registo de transações ocorrem a cada cinco minutos. A primeira cópia de segurança completa é agendada imediatamente após a criação de um servidor. A cópia de segurança inicial pode demorar mais tempo num grande servidor restaurado. O ponto mais cedo em que um novo servidor pode ser restaurado é o momento em que a cópia de segurança completa inicial está completa.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Base de Dados Azure para MariaDB proporciona a flexibilidade para escolher entre armazenamento de reserva localmente redundante ou geo-redundante nos níveis geral de propósito e memória otimizados. Quando as cópias de segurança são armazenadas num armazenamento de backup geo-redundante, não são apenas armazenadas na região em que o seu servidor está hospedado, como também são replicadas para um centro de [dados emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isto proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível Básico apenas oferece armazenamento de reserva redundante localmente.

> [!IMPORTANT]
> A configuração do armazenamento com redundância local ou geográfica para backup só é permitida durante a criação do servidor. Depois de aprovisionado o servidor você não pode alterar a opção de redundância do armazenamento de backup.

### <a name="backup-storage-cost"></a>Custo de armazenamento de reserva

A Base de Dados Azure para MariaDB fornece até 100% do armazenamento do seu servidor aprovisionado como armazenamento de backup sem custos adicionais. Tipicamente, isto é adequado para uma retenção de reserva de sete dias. Qualquer armazenamento adicional de reserva utilizado é carregado em gb-month.

Por exemplo, se tiver aprovisionado um servidor com 250 GB, tem 250 GB de armazenamento de reserva sem custos adicionais. É cobrado um armazenamento superior a 250 GB.

Para mais informações sobre o custo de armazenamento de backup, visite a [página de preços mariaDB](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Restauro

Na Base de Dados Azure para O MariaDB, a realização de um restauro cria um novo servidor a partir das cópias de segurança do servidor original.

Existem dois tipos de restauro disponíveis:

- **O restauro** pontual está disponível com a opção de redundância de reserva e cria um novo servidor na mesma região que o seu servidor original.
- **A Geo-restore** só está disponível se configurar o seu servidor para armazenamento geo-redundante e permite-lhe restaurar o seu servidor para uma região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo o tamanho da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que se recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implementação de postais, de eliminação acidental ou alterações inesperadas, os administradores podem alavancar bloqueios de [gestão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de reserva, pode efetuar um restauro em qualquer ponto dentro do seu período de retenção de backup. Um novo servidor é criado na mesma região do Azure que o servidor original. É criado com a configuração do servidor original para o nível de preços, geração de cálculo, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup.

A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador acidentalmente elimina dados, deixa cair uma tabela ou base de dados importante, ou se uma aplicação acidentalmente substitui bons dados com dados maus devido a um defeito de aplicação.

Poderá ter de esperar que o próximo backup de registo de transações seja feito antes de poder restaurar a um ponto no tempo dentro dos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança georedundantes. A geo-restauração é a opção de recuperação padrão quando o servidor está indisponível devido a um incidente na região onde o servidor está hospedado. Se um incidente em larga escala numa região resultar na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor das cópias de segurança georedundantes para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é replicado para diferentes regiões. Este atraso pode chegar a uma hora, por isso, se ocorrer um desastre, pode haver até uma hora de perda de dados.

Durante a geo-restauração, as configurações do servidor que podem ser alteradas incluem geração de cálculo, vCore, período de retenção de backup e opções de redundância de backup. Não é suportado o nível de alteração dos preços (Básico, Propósito Geral ou Otimização da Memória) ou o tamanho do armazenamento durante a geo-restauração.

### <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Após um restauro de qualquer mecanismo de recuperação, deverá executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor se destina restar o servidor original, redirecione os clientes e as aplicações do cliente para o novo servidor
- Certifique-se de que estão em vigor regras de firewall adequadas para os utilizadores se conectarem
- Certifique-se de que estão em vigor logins apropriados e permissões de nível de base de dados
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a continuidade do negócio, consulte a visão geral da continuidade do [negócio.](concepts-business-continuity.md)
- Para restaurar a um ponto no tempo utilizando o portal Azure, consulte restaurar a base de [dados a um ponto no tempo utilizando o portal Azure](howto-restore-server-portal.md).
 
<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md).-->
