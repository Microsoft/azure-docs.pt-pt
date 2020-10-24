---
title: Backup e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba mais sobre cópias de segurança automáticas e restaurar a sua Base de Dados Azure para servidor PostgreSQL - Servidor Único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: b267a97b640c9d069f83223206200fc4814c86b9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488015"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Servidor Único

A Azure Database for PostgreSQL cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Azure Database for PostgreSQL retira cópias dos ficheiros de dados e do registo de transações. Dependendo do tamanho máximo de armazenamento suportado, ou fazemos cópias de segurança completas e diferenciais (servidores de armazenamento máximo de 4-TB) ou cópias de segurança instantâneas (até 16-TB máximo de armazenamento). Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

Estes ficheiros de reserva não podem ser exportados. As cópias de segurança só podem ser utilizadas para operações de restauro na Base de Dados Azure para PostgreSQL. Pode usar [pg_dump](howto-migrate-using-dump-and-restore.md) para copiar uma base de dados.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

#### <a name="servers-with-up-to-4-tb-storage"></a>Servidores com armazenamento até 4-TB

Para os servidores que suportam até 4-TB de armazenamento máximo, as cópias de segurança completas ocorrem uma vez por semana. As cópias de segurança diferenciais ocorrem duas vezes por dia. As cópias de segurança de registo de transações ocorrem a cada cinco minutos.


#### <a name="servers-with-up-to-16-tb-storage"></a>Servidores com armazenamento até 16-TB

Num subconjunto de [regiões Azure,](./concepts-pricing-tiers.md#storage)todos os servidores recém-abastados podem suportar até 16-TB de armazenamento. As cópias de segurança nestes grandes servidores de armazenamento são baseadas em instantâneos. A primeira cópia de segurança de instantâneos completa é agendada imediatamente após a criação do servidor. A primeira cópia de segurança total do instantâneo é mantida como a cópia de segurança base do servidor. As cópias de segurança de instantâneos subsequentes são apenas cópias de segurança diferenciais. As cópias de segurança de instantâneos diferenciais não ocorrem num agendamento fixo. Num dia, são realizadas três cópias de segurança instantâneas diferenciais. As cópias de segurança de registo de transações ocorrem a cada cinco minutos. 

### <a name="backup-retention"></a>Retenção da cópia de segurança

As cópias de segurança são mantidas com base na definição do período de retenção de cópias de segurança no servidor. Pode selecionar um período de retenção de 7 a 35 dias. O período de retenção por defeito é de 7 dias. Pode definir o período de retenção durante a criação do servidor ou posteriormente atualizando a configuração de backup utilizando o [portal Azure](./howto-restore-server-portal.md#set-backup-configuration) ou [O Azure CLI](./howto-restore-server-cli.md#set-backup-configuration). 

O período de retenção de backups regula o quão longe no tempo um restauro de ponto no tempo pode ser recuperado, uma vez que é baseado em backups disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspetiva de restauro. Todas as cópias de segurança necessárias para a restauração pontual dentro do período de retenção de backup são mantidas no armazenamento de backup. Por exemplo - se o período de retenção de backup estiver definido para 7 dias, a janela de recuperação é considerada dura 7 dias. Neste cenário, todas as cópias de segurança necessárias para restaurar o servidor nos últimos 7 dias são mantidas. Com uma janela de retenção de reserva de sete dias:
- Os servidores com armazenamento até 4-TB irão reter até 2 cópias de dados completas, todas as cópias de segurança diferenciais e cópias de segurança de registo de transações realizadas desde a primeira cópia de segurança completa da base de dados.
-   Os servidores com armazenamento até 16-TB conservarão o instantâneo completo da base de dados, todas as imagens diferenciais e cópias de segurança de registo de transações nos últimos 8 dias.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Base de Dados Azure para PostgreSQL proporciona a flexibilidade para escolher entre armazenamento de backup localmente redundante ou geo-redundante nos níveis Geral De Finalidade e Memória Otimizada. Quando as cópias de segurança são armazenadas no armazenamento de backup geo-redundante, não só são armazenadas na região em que o seu servidor está hospedado, como também são replicadas num centro de [dados emparelhado](../best-practices-availability-paired-regions.md). Isto proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível básico só oferece armazenamento de backup localmente redundante.

> [!IMPORTANT]
> Configurar armazenamento localmente redundante ou geo-redundante para cópia de segurança só é permitido durante a criação do servidor. Uma vez que o servidor é provisionado, não é possível alterar a opção de redundância de armazenamento de cópia de segurança.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

A Azure Database for PostgreSQL fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Qualquer armazenamento adicional de backup utilizado é cobrado em GB por mês. Por exemplo, se tiver provisionado um servidor com 250 GB de armazenamento, tem 250 GB de armazenamento adicional disponível para cópias de segurança do servidor sem custo adicional. O armazenamento consumido para cópias de segurança superiores a 250 GB é cobrado de acordo com o [modelo de preços.](https://azure.microsoft.com/pricing/details/postgresql/)

Pode utilizar a métrica [de armazenamento de backup utilizada](concepts-monitoring.md) no Azure Monitor disponível no portal Azure para monitorizar o armazenamento de cópia de segurança consumido por um servidor. A métrica utilizada no Backup Storage representa a soma de armazenamento consumida por todas as cópias de segurança completas da base de dados, cópias de segurança diferenciais e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. A frequência das cópias de segurança é gerida e explicada anteriormente. Uma atividade transacional intensa no servidor pode aumentar a utilização do armazenamento de cópias de segurança, independentemente do tamanho total da base de dados. Para o armazenamento geo-redundante, o uso de armazenamento de backup é o dobro do armazenamento localmente redundante. 

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup adequado e escolhendo as opções de redundância de backup certas para cumprir os objetivos de recuperação pretendidos. Pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Os servidores otimizados para fins gerais e memória podem optar por ter armazenamento geo-redundante para cópias de segurança.

## <a name="restore"></a>Restauro

Na Base de Dados Azure para PostgreSQL, executar uma restauração cria um novo servidor a partir das cópias de segurança do servidor original.

Existem dois tipos de restauro disponíveis:

- **O restauro pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original.
- **O geo-restauro** só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de backup, pode efetuar um restauro a qualquer ponto no tempo dentro do seu período de retenção de backup. Um novo servidor é criado na mesma região de Azure que o servidor original. É criado com a configuração do servidor original para o nível de preços, geração de cálculo, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup.

A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina acidentalmente dados, deixa cair uma tabela ou base de dados importante, ou se uma aplicação acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação.

Poderá ter de esperar que a próxima cópia de segurança do registo de transações seja tomada antes de poder restabelecer a um ponto no tempo nos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança geo-redundantes. Os servidores que suportam até 4 TB de armazenamento podem ser restaurados na região geo emparelhada, ou em qualquer região que suporte até 16 TB de armazenamento. Para servidores que suportam até 16 TB de armazenamento, as geo-cópias podem ser restauradas em qualquer região que suporte também 16 servidores TB. Reveja [a base de dados Azure para os níveis de preços postgeSQL](concepts-pricing-tiers.md) para a lista de regiões apoiadas.

O geo-restauro é a opção de recuperação padrão quando o seu servidor está indisponível devido a um incidente na região onde o servidor está hospedado. Se um incidente em larga escala numa região resultar na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor das cópias de segurança geo-redundantes para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é replicado para diferentes regiões. Este atraso pode ser de até uma hora, por isso, se ocorrer uma catástrofe, pode haver até uma hora de perda de dados.

Durante o geo-restauro, as configurações do servidor que podem ser alteradas incluem geração de computação, vCore, período de retenção de backup e opções de redundância de backup. A alteração do nível de preços (Básico, Final geral ou memória otimizada) ou o tamanho do armazenamento não é suportado.

### <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Após uma restauração de qualquer mecanismo de recuperação, deve executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor
- Certifique-se de que existem regras adequadas ao nível do servidor e das regras VNet para que os utilizadores se conectem. Estas regras não são copiadas do servidor original.
- Certifique-se de que estão em vigor logins e permissões de nível de base de dados apropriados
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

- Saiba como restaurar utilizando [o portal Azure.](howto-restore-server-portal.md)
- Saiba como restaurar utilizando [o Azure CLI](howto-restore-server-cli.md).
- Para saber mais sobre a continuidade do negócio, consulte a [visão geral](concepts-business-continuity.md)da continuidade do negócio.