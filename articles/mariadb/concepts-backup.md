---
title: Backup e restauro - Azure Database for MariaDB
description: Saiba mais sobre cópias de segurança automáticas e restauro da sua Base de Dados Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: c4d5a9ca85237bde1277904a478a0b8828fc2b08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80369228"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Backup e restauro na Base de Dados Azure para MariaDB

A Azure Database for MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Azure Database for MariaDB recebe cópias de segurança completas, diferenciais e de registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Estes ficheiros de cópia de segurança não são expostos ao utilizador e não podem ser exportados. Estas cópias de segurança só podem ser utilizadas para operações de restauro na Base de Dados Azure para o MariaDB. Pode usar [o meu "mysqldump"](howto-migrate-dump-restore.md) para copiar uma base de dados.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

Normalmente, as cópias de segurança completas ocorrem semanalmente, as cópias de segurança diferenciais ocorrem duas vezes por dia e as cópias de segurança de registo de transações ocorrem a cada cinco minutos. A primeira cópia de segurança completa é programada imediatamente após a criação de um servidor. A cópia de segurança inicial pode demorar mais tempo num grande servidor restaurado. O ponto mais antigo no tempo para que um novo servidor possa ser restaurado é o momento em que a cópia de segurança inicial está completa.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Base de Dados Azure para MariaDB proporciona a flexibilidade para escolher entre armazenamento de backup localmente redundante ou geo-redundante nos níveis Geral De Finalidade e Memória Otimizada. Quando as cópias de segurança são armazenadas no armazenamento de backup geo-redundante, não só são armazenadas na região em que o seu servidor está hospedado, como também são replicadas num centro de [dados emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isto proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível básico só oferece armazenamento de backup localmente redundante.

> [!IMPORTANT]
> Configurar armazenamento localmente redundante ou geo-redundante para cópia de segurança só é permitido durante a criação do servidor. Uma vez que o servidor é provisionado, não é possível alterar a opção de redundância de armazenamento de cópia de segurança.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

A Azure Database for MariaDB fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Normalmente, isto é adequado para uma retenção de reserva de sete dias. Qualquer armazenamento adicional de backup utilizado é cobrado no mês gB.

Por exemplo, se tiver provisionado um servidor com 250 GB, tem 250 GB de armazenamento de backup sem custos adicionais. É cobrado um armazenamento superior a 250 GB.

Para obter mais informações sobre o custo de armazenamento de backup, visite a [página de preços mariaDB](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Restauro

Na Base de Dados Azure para MariaDB, a execução de uma restauração cria um novo servidor a partir das cópias de segurança do servidor original e restaura todas as bases de dados contidas no servidor.

Existem dois tipos de restauro disponíveis:

- **O restauro pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original, utilizando a combinação de cópias de segurança completas e de registo de transações.
- **O geo-restauro** só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente utilizando a cópia de segurança mais recente tomada.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de backup, pode efetuar um restauro a qualquer ponto no tempo dentro do seu período de retenção de backup. Um novo servidor é criado na mesma região de Azure que o servidor original. É criado com a configuração do servidor original para o nível de preços, geração de cálculo, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup.

A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina acidentalmente dados, deixa cair uma tabela ou base de dados importante, ou se uma aplicação acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação.

Poderá ter de esperar que a próxima cópia de segurança do registo de transações seja tomada antes de poder restabelecer a um ponto no tempo nos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança geo-redundantes. O geo-restauro é a opção de recuperação padrão quando o seu servidor está indisponível devido a um incidente na região onde o servidor está hospedado. Se um incidente em larga escala numa região resultar na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor das cópias de segurança geo-redundantes para um servidor em qualquer outra região. Geo-restauro utiliza a cópia de segurança mais recente do servidor. Há um atraso entre quando um backup é tomado e quando é replicado para diferentes regiões. Este atraso pode ser de até uma hora, por isso, se ocorrer uma catástrofe, pode haver até uma hora de perda de dados.

Durante o geo-restauro, as configurações do servidor que podem ser alteradas incluem geração de computação, vCore, período de retenção de backup e opções de redundância de backup. A alteração do nível de preços (Básico, Final geral ou memória otimizada) ou o tamanho do armazenamento durante a geo-restauração não é suportado.

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
