---
title: Backup e restauro - Azure Database for MySQL
description: Saiba mais sobre cópias de segurança automáticas e restauro da sua Base de Dados Azure para o servidor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 4438ceaa7bb4e9c29a05de0481acdad571e3bb64
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542342"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Backup e restauro na Base de Dados Azure para o MySQL

A Azure Database for MySQL cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Azure Database for MySQL retira cópias dos ficheiros de dados e do registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode [configurar opcionalmente](howto-restore-server-portal.md#set-backup-configuration) até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

Estes ficheiros de cópia de segurança não são expostos ao utilizador e não podem ser exportados. Estas cópias de segurança só podem ser utilizadas para operações de restauro na Base de Dados Azure para o MySQL. Pode usar [o meu "mysqldump"](concepts-migrate-dump-restore.md) para copiar uma base de dados.

O tipo de cópia de segurança e a frequência dependem do armazenamento de backend para os servidores.

### <a name="backup-type-and-frequency"></a>Tipo de backup e frequência

#### <a name="basic-storage-servers"></a>Servidores básicos de armazenamento

O armazenamento básico é o armazenamento de backend que suporta [servidores de nível básico](concepts-pricing-tiers.md). As cópias de segurança nos servidores básicos de armazenamento são baseadas em instantâneos. Uma foto de base de dados completa é realizada diariamente. Não existem cópias de segurança diferenciais realizadas para servidores de armazenamento básicos e todas as cópias de segurança instantâneas são apenas cópias de segurança completas da base de dados.

As cópias de segurança de registo de transações ocorrem a cada cinco minutos.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Servidores de armazenamento de finalidade geral com armazenamento até 4-TB

O armazenamento para fins gerais é o armazenamento de backend suportando o servidor de nível [de finalidade geral](concepts-pricing-tiers.md) e [de memória otimizado.](concepts-pricing-tiers.md) Para servidores com armazenamento de finalidade geral até 4 TB, as cópias de segurança completas ocorrem uma vez por semana. As cópias de segurança diferenciais ocorrem duas vezes por dia. As cópias de segurança de registo de transações ocorrem a cada cinco minutos. As cópias de segurança no armazenamento de fins gerais até 4-TB não são baseadas em instantâneos e consomem largura de banda IO no momento da cópia de segurança. Para grandes bases de dados (> 1 TB) no armazenamento de 4-TB, recomendamos que considere

- Provisionando mais IOPs para responder a iOs de backup OR
- Alternativamente, migrar para o armazenamento de fins gerais que suporte até 16-TB de armazenamento se a infraestrutura de armazenamento subjacente estiver disponível nas suas [regiões de Azure preferidas](/azure/mysql/concepts-pricing-tiers#storage). Não existe um custo adicional para o armazenamento para fins gerais que suporte até 16-TB armazenamento. Para assistência com a migração para o armazenamento de 16-TB, abra um bilhete de apoio a partir do portal Azure.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Servidores de armazenamento de finalidade geral com armazenamento até 16-TB

Num subconjunto de [regiões Azure,](/azure/mysql/concepts-pricing-tiers#storage)todos os servidores recém-abastados podem suportar o armazenamento de fins gerais até 16-TB. Por outras palavras, o armazenamento até 16-TB é o armazenamento geral padrão para todas as [regiões](concepts-pricing-tiers.md#storage) onde é suportado. As cópias de segurança nestes servidores de armazenamento de 16 TB são baseadas em instantâneos. A primeira cópia de segurança de instantâneos completa é agendada imediatamente após a criação do servidor. A primeira cópia de segurança total do instantâneo é mantida como a cópia de segurança base do servidor. As cópias de segurança de instantâneos subsequentes são apenas cópias de segurança diferenciais.

Num subconjunto de [regiões Azure,](concepts-pricing-tiers.md#storage)todos os servidores recém-abastados podem suportar o armazenamento de fins gerais até 16-TB. Por outras palavras, o armazenamento até 16-TB é o armazenamento geral padrão para todas as [regiões](concepts-pricing-tiers.md#storage) onde é suportado. As cópias de segurança nestes servidores de armazenamento de 16 TB são baseadas em instantâneos. A primeira cópia de segurança de instantâneos completa é agendada imediatamente após a criação do servidor. A primeira cópia de segurança total do instantâneo é mantida como a cópia de segurança base do servidor. As cópias de segurança de instantâneos subsequentes são apenas cópias de segurança diferenciais.

As cópias de segurança de instantâneos diferenciais ocorrem, pelo menos, uma vez por dia. As cópias de segurança de instantâneos diferenciais não ocorrem num agendamento fixo. As cópias de segurança instantânea diferenciais ocorrem a cada 24 horas, a menos que o registo de transação (binlog no MySQL) exceda 50 GB desde a última cópia de segurança diferencial. Num dia, são permitidos, no máximo, seis instantâneos diferenciais.

As cópias de segurança de registo de transações ocorrem a cada cinco minutos.

### <a name="backup-retention"></a>Retenção da cópia de segurança

As cópias de segurança são mantidas com base na definição do período de retenção de cópias de segurança no servidor. Pode selecionar um período de retenção de 7 a 35 dias. O período de retenção por defeito é de 7 dias. Pode definir o período de retenção durante a criação do servidor ou posteriormente atualizando a configuração de backup utilizando o [portal Azure](./howto-restore-server-portal.md#set-backup-configuration) ou [O Azure CLI](./howto-restore-server-cli.md#set-backup-configuration).

O período de retenção de backups regula o quão longe no tempo um restauro de ponto no tempo pode ser recuperado, uma vez que é baseado em backups disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspetiva de restauro. Todas as cópias de segurança necessárias para a restauração pontual dentro do período de retenção de backup são mantidas no armazenamento de backup. Por exemplo, se o período de retenção de backup estiver definido para 7 dias, a janela de recuperação é considerada dura 7 dias. Neste cenário, todas as cópias de segurança necessárias para restaurar o servidor nos últimos 7 dias são mantidas. Com uma janela de retenção de reserva de sete dias:

- Os servidores com armazenamento até 4-TB irão reter até 2 cópias de dados completas, todas as cópias de segurança diferenciais e cópias de segurança de registo de transações realizadas desde a primeira cópia de segurança completa da base de dados.
- Os servidores com armazenamento até 16-TB conservarão o instantâneo completo da base de dados, todas as imagens diferenciais e cópias de segurança de registo de transações nos últimos 8 dias.

#### <a name="long-term-retention"></a>Retenção de longa duração

A retenção de backups a longo prazo para além de 35 dias ainda não é suportada de forma nativa pelo serviço. Você tem a opção de usar mysqldump para pegar cópias de segurança e armazená-las para retenção a longo prazo. A nossa equipa de apoio bloqueou um [artigo passo a passo](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) para partilhar como pode alcançá-lo.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Azure Database for MySQL proporciona a flexibilidade para escolher entre armazenamento de backup localmente redundante ou geo-redundante nos níveis Geral De Finalidade e Memória Otimizada. Quando as cópias de segurança são armazenadas no armazenamento de backup geo-redundante, não só são armazenadas na região em que o seu servidor está hospedado, como também são replicadas num centro de [dados emparelhado](../best-practices-availability-paired-regions.md). Esta geo-redundância proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível básico só oferece armazenamento de backup localmente redundante.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Passar de localmente redundante para armazenamento de backup geo-redundante

Só é permitido configurar o armazenamento localmente redundante ou georredundante para cópias de segurança durante a criação do servidor. Assim que o servidor tiver sido aprovisionado, não poderá alterar a opção de redundância do armazenamento de cópias de segurança. Para mover o armazenamento de backup do armazenamento localmente redundante para o armazenamento geo-redundante, criar um novo servidor e migrar os dados usando [o despejo e restaurar](concepts-migrate-dump-restore.md) é a única opção suportada.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

A Azure Database for MySQL fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Qualquer armazenamento adicional de backup utilizado é cobrado em GB por mês. Por exemplo, se tiver provisionado um servidor com 250 GB de armazenamento, tem 250 GB de armazenamento adicional disponível para cópias de segurança do servidor sem custo adicional. O armazenamento consumido para cópias de segurança superiores a 250 GB é cobrado de acordo com o [modelo de preços.](https://azure.microsoft.com/pricing/details/mysql/)

Pode utilizar a métrica [de armazenamento de backup utilizada](concepts-monitoring.md) no Azure Monitor disponível através do portal Azure para monitorizar o armazenamento de cópia de segurança consumido por um servidor. A métrica utilizada no Backup Storage representa a soma de armazenamento consumida por todas as cópias de segurança completas da base de dados, cópias de segurança diferenciais e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. A frequência das cópias de segurança é gerida e explicada anteriormente. Uma atividade transacional intensa no servidor pode aumentar a utilização do armazenamento de cópias de segurança, independentemente do tamanho total da base de dados. Para o armazenamento geo-redundante, o uso de armazenamento de backup é o dobro do armazenamento localmente redundante.

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup adequado e escolhendo as opções de redundância de backup certas para cumprir os objetivos de recuperação pretendidos. Pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Os servidores otimizados para fins gerais e memória podem optar por ter armazenamento geo-redundante para cópias de segurança.

## <a name="restore"></a>Restauro

Na Base de Dados Azure para o MySQL, a execução de uma restauração cria um novo servidor a partir das cópias de segurança do servidor original e restaura todas as bases de dados contidas no servidor.

Existem dois tipos de restauro disponíveis:

- **O restauro pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original, utilizando a combinação de cópias de segurança completas e de registo de transações.
- **O geo-restauro** só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente utilizando a cópia de segurança mais recente tomada.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

> [!IMPORTANT]
> Os servidores eliminados só podem ser restaurados no prazo de **cinco dias** após o qual as cópias de segurança são eliminadas. A cópia de segurança da base de dados só pode ser acedida e restaurada a partir da subscrição Azure que hospeda o servidor. Para restaurar um servidor abandonado, consulte [os passos documentados](howto-restore-dropped-server.md). Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de backup, pode efetuar um restauro a qualquer ponto no tempo dentro do seu período de retenção de backup. Um novo servidor é criado na mesma região de Azure que o servidor original. É criado com a configuração do servidor original para o nível de preços, geração de cálculo, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup.

> [!NOTE]
> Existem dois parâmetros do servidor que são reiniciados para valores predefinidos (e não são copiados do servidor primário) após a operação de restauro
>
> - time_zone - Este valor para definir para DEFAULT Value **SYSTEM**
> - event_scheduler - O event_scheduler está definido para **OFF** no servidor restaurado
>
> Terá de definir estes parâmetros do servidor reconfigurando o parâmetro do [servidor](howto-server-parameters.md)

A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina acidentalmente dados, deixa cair uma tabela ou base de dados importante, ou se uma aplicação acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação.

Poderá ter de esperar que a próxima cópia de segurança do registo de transações seja tomada antes de poder restabelecer a um ponto no tempo nos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança geo-redundantes. Os servidores que suportam até 4 TB de armazenamento podem ser restaurados na região geo emparelhada, ou em qualquer região que suporte até 16 TB de armazenamento. Para servidores que suportam até 16 TB de armazenamento, as geo-cópias podem ser restauradas em qualquer região que suporte também servidores de 16-TB. Reveja [a base de dados Azure para os níveis de preços do MySQL](concepts-pricing-tiers.md) para a lista de regiões apoiadas.

O geo-restauro é a opção de recuperação padrão quando o seu servidor está indisponível devido a um incidente na região onde o servidor está hospedado. Se um incidente em larga escala numa região resultar na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor das cópias de segurança geo-redundantes para um servidor em qualquer outra região. Geo-restauro utiliza a cópia de segurança mais recente do servidor. Há um atraso entre quando um backup é tomado e quando é replicado para diferentes regiões. Este atraso pode ser de até uma hora, por isso, se ocorrer uma catástrofe, pode haver até uma hora de perda de dados.

> [!IMPORTANT]
>Se um geo-restauro for realizado para um servidor recém-criado, a sincronização inicial de backup pode demorar mais de 24 horas, dependendo do tamanho dos dados, uma vez que o tempo inicial de cópia de cópia de cópia de cópia de cópia de instantâneo completo é muito maior. As cópias de segurança instantânea subsequentes são cópias incrementais e, portanto, as restaurações são mais rápidas após 24 horas de criação do servidor. Se estiver a avaliar geo-restauros para definir o seu RTO, recomendamos que espere e avalie o geo-restauro **apenas após 24 horas** de criação do servidor para obter melhores estimativas.

Durante o geo-restauro, as configurações do servidor que podem ser alteradas incluem geração de computação, vCore, período de retenção de backup e opções de redundância de backup. A alteração do nível de preços (Básico, Final geral ou memória otimizada) ou o tamanho do armazenamento durante a geo-restauração não é suportado.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

### <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Após uma restauração de qualquer mecanismo de recuperação, deve executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor
- Certifique-se de que existem regras VNet adequadas para que os utilizadores se conectem. Estas regras não são copiadas do servidor original.
- Certifique-se de que estão em vigor logins e permissões de nível de base de dados apropriados
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a continuidade do negócio, consulte a [visão geral](concepts-business-continuity.md)da continuidade do negócio.
- Para restaurar um ponto no tempo usando o portal Azure, consulte restaurar o [servidor a um ponto no tempo utilizando o portal Azure](howto-restore-server-portal.md).
- Para restaurar a um ponto no tempo usando O Azure CLI, consulte [restaurar o servidor a um ponto no tempo usando CLI](howto-restore-server-cli.md).
