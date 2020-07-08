---
title: Backup e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba mais sobre cópias de segurança automáticas e restaurar a sua Base de Dados Azure para servidor PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77619632"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Servidor Único

A Azure Database for PostgreSQL cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

A Azure Database for PostgreSQL retira cópias dos ficheiros de dados e do registo de transações. Dependendo do tamanho máximo de armazenamento suportado, ou fazemos cópias de segurança completas e diferenciais (4 servidores de armazenamento máximo de TB) ou cópias de segurança instantâneas (até 16 servidores de armazenamento máximo de TB). Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Estes ficheiros de reserva não podem ser exportados. As cópias de segurança só podem ser utilizadas para operações de restauro na Base de Dados Azure para PostgreSQL. Pode usar [pg_dump](howto-migrate-using-dump-and-restore.md) para copiar uma base de dados.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

Geralmente, as cópias de segurança completas ocorrem semanalmente, as cópias de segurança diferenciais ocorrem duas vezes por dia para servidores com um armazenamento máximo suportado de 4 TB. As cópias de segurança de instantâneos ocorrem pelo menos uma vez por dia para os servidores que suportem até 16 TB de armazenamento. Em ambos os casos, as cópias de segurança de registo de transações ocorrem a cada cinco minutos. A primeira imagem de cópia de segurança completa é programada imediatamente após a criação de um servidor. A cópia de segurança inicial pode demorar mais tempo num grande servidor restaurado. O ponto mais antigo no tempo para que um novo servidor possa ser restaurado é o momento em que a cópia de segurança inicial está completa. Como as imagens instantâneas são instantâneas, os servidores com suporte até 16 TB de armazenamento podem ser restaurados até ao tempo de criação.

### <a name="backup-redundancy-options"></a>Opções de redundância de backup

A Base de Dados Azure para PostgreSQL proporciona a flexibilidade para escolher entre armazenamento de backup localmente redundante ou geo-redundante nos níveis Geral De Finalidade e Memória Otimizada. Quando as cópias de segurança são armazenadas no armazenamento de backup geo-redundante, não só são armazenadas na região em que o seu servidor está hospedado, como também são replicadas num centro de [dados emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isto proporciona uma melhor proteção e capacidade de restaurar o seu servidor numa região diferente em caso de desastre. O nível básico só oferece armazenamento de backup localmente redundante.

> [!IMPORTANT]
> Configurar armazenamento localmente redundante ou geo-redundante para cópia de segurança só é permitido durante a criação do servidor. Uma vez que o servidor é provisionado, não é possível alterar a opção de redundância de armazenamento de cópia de segurança.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

A Azure Database for PostgreSQL fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Normalmente, isto é adequado para uma retenção de reserva de sete dias. Qualquer armazenamento adicional de backup utilizado é cobrado no mês gB.

Por exemplo, se tiver provisionado um servidor com 250 GB, tem 250 GB de armazenamento de backup sem custos adicionais. É cobrado um armazenamento superior a 250 GB.

## <a name="restore"></a>Restauro

Na Base de Dados Azure para PostgreSQL, executar uma restauração cria um novo servidor a partir das cópias de segurança do servidor original.

Existem dois tipos de restauro disponíveis:

- **O restauro pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original.
- **O geo-restauro** só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

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

## <a name="next-steps"></a>Próximos passos

- Saiba como restaurar utilizando [o portal Azure.](howto-restore-server-portal.md)
- Saiba como restaurar utilizando [o Azure CLI](howto-restore-server-cli.md).
- Para saber mais sobre a continuidade do negócio, consulte a [visão geral](concepts-business-continuity.md)da continuidade do negócio.
