---
title: Backup e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba mais sobre cópias de segurança automáticas e restaurar a sua Base de Dados Azure para o servidor PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619632"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Servidor Único

A Base de Dados Azure para PostgreSQL cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Base de Dados Azure para PostgreSQL retira cópias de segurança dos ficheiros de dados e do registo de transações. Dependendo do tamanho máximo de armazenamento suportado, ou tomamos backups completos e diferenciais (4 tb max storage servers) ou backups instantâneos (até 16 TB servidores de armazenamento max). Estas cópias de segurança permitem restaurar um servidor em qualquer ponto-a-tempo dentro do período de retenção de cópia de segurança configurado. O período de retenção de reserva padrão é de sete dias. Pode configurá-lo opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Estes ficheiros de reserva não podem ser exportados. As cópias de segurança só podem ser utilizadas para restaurar as operações na Base de Dados Azure para postgreSQL. Pode utilizar [pg_dump](howto-migrate-using-dump-and-restore.md) para copiar uma base de dados.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

Geralmente, as cópias de segurança completas ocorrem semanalmente, as cópias de segurança diferenciais ocorrem duas vezes por dia para servidores com um armazenamento max suportado de 4 TB. As cópias de segurança de instantâneos ocorrem pelo menos uma vez por dia para os servidores que suportem até 16 TB de armazenamento. Em ambos os casos, as cópias de segurança de registo de transações ocorrem a cada cinco minutos. O primeiro instantâneo de cópia de segurança completa é agendado imediatamente após a criação de um servidor. A cópia de segurança completa inicial pode demorar mais tempo num grande servidor restaurado. O ponto mais cedo em que um novo servidor pode ser restaurado é o momento em que a cópia de segurança completa inicial está completa. Como as imagens são instantâneas, os servidores com suporte até 16 TB de armazenamento podem ser restaurados todo o caminho de volta ao tempo de criação.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Base de Dados Azure para PostgreSQL proporciona a flexibilidade para escolher entre armazenamento de backup redundante localmente redundante ou geo-redundante nos níveis geral de propósito e memória otimizados. Quando as cópias de segurança são armazenadas num armazenamento de backup geo-redundante, não são apenas armazenadas na região em que o seu servidor está hospedado, como também são replicadas para um centro de [dados emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isto proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível Básico apenas oferece armazenamento de reserva redundante localmente.

> [!IMPORTANT]
> Configurar armazenamento localmente redundante ou geo-redundante para cópia de segurança só é permitido durante a criação do servidor. Uma vez que o servidor é aprovisionado, não pode alterar a opção de redundância de armazenamento de reserva.

### <a name="backup-storage-cost"></a>Custo de armazenamento de reserva

A Base de Dados Azure para PostgreSQL fornece até 100% do armazenamento do seu servidor provisionado como armazenamento de backup sem custos adicionais. Tipicamente, isto é adequado para uma retenção de reserva de sete dias. Qualquer armazenamento adicional de reserva utilizado é carregado em gb-month.

Por exemplo, se tiver aprovisionado um servidor com 250 GB, tem 250 GB de armazenamento de reserva sem custos adicionais. É cobrado um armazenamento superior a 250 GB.

## <a name="restore"></a>Restauro

Na Base de Dados Azure para PostgreSQL, a realização de um restauro cria um novo servidor a partir das cópias de segurança do servidor original.

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

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança georedundantes. Os servidores que suportam até 4 TB de armazenamento podem ser restaurados na região geo-emparelhada, ou em qualquer região que suporte até 16 TB de armazenamento. Para servidores que suportem até 16 TB de armazenamento, as geo-backups podem ser restauradas em qualquer região que suporte 16 servidores de TB também. Reveja a Base de Dados Azure para os níveis de [preços postgeSQL](concepts-pricing-tiers.md) para a lista de regiões apoiadas.

A geo-restauração é a opção de recuperação padrão quando o servidor está indisponível devido a um incidente na região onde o servidor está hospedado. Se um incidente em larga escala numa região resultar na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor das cópias de segurança georedundantes para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é replicado para diferentes regiões. Este atraso pode chegar a uma hora, por isso, se ocorrer um desastre, pode haver até uma hora de perda de dados.

Durante a geo-restauração, as configurações do servidor que podem ser alteradas incluem geração de cálculo, vCore, período de retenção de backup e opções de redundância de backup. Não é suportado o nível de alteração dos preços (Básico, Propósito Geral ou Otimização da Memória) ou o tamanho do armazenamento.

### <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Após um restauro de qualquer mecanismo de recuperação, deverá executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor se destina restar o servidor original, redirecione os clientes e as aplicações do cliente para o novo servidor
- Certifique-se de que estão em vigor as regras adequadas para a firewall de nível de servidor e as regras VNet para os utilizadores se conectarem. Estas regras não são copiadas do servidor original.
- Certifique-se de que estão em vigor logins apropriados e permissões de nível de base de dados
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

- Aprenda a restaurar [usando o portal Azure.](howto-restore-server-portal.md)
- Aprenda a restaurar [usando o Azure CLI](howto-restore-server-cli.md).
- Para saber mais sobre a continuidade do negócio, consulte a visão geral da continuidade do [negócio.](concepts-business-continuity.md)
