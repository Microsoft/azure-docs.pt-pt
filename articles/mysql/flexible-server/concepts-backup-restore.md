---
title: Backup e restauro na Base de Dados Azure para o MySQL Flexible Server
description: Conheça os conceitos de backup e restauro com Base de Dados Azure para MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240771"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Backup e restauro na Base de Dados Azure para o MySQL Flexible Server (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Azure Database for MySQL Flexible Server, cria automaticamente cópias de segurança do servidor e armazena-as de forma segura em armazenamentos redundantes locais dentro da região. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

O Flexible Server retira cópias de segurança instantâneas dos ficheiros de dados e armazena-os num armazenamento redundante local. O servidor também realiza registos de transações de backup e também os armazena em armazenamento redundante local. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente a cópia de segurança da base de dados de 1 a 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits para os dados armazenados em repouso.

Estes ficheiros de reserva não podem ser exportados. As cópias de segurança só podem ser utilizadas para operações de restauro no servidor Flexível. Também pode utilizar [o mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) de um cliente MySQL para copiar uma base de dados.

## <a name="backup-frequency"></a>Frequência de cópia de segurança

As cópias de segurança nos servidores flexíveis são baseadas em imagens. A primeira cópia de segurança de instantâneos completa é agendada imediatamente após a criação do servidor. A primeira cópia de segurança total do instantâneo é mantida como a cópia de segurança base do servidor. As cópias de segurança de instantâneos subsequentes são apenas cópias de segurança diferenciais.

As cópias de segurança de instantâneos diferenciais ocorrem, pelo menos, uma vez por dia. As cópias de segurança de instantâneos diferenciais não ocorrem num agendamento fixo. As cópias de segurança de instantâneos diferenciais ocorrem a cada 24 horas, a menos que os registos binários no MySQL excedam 50 GB desde a última cópia de segurança diferencial. Num dia, são permitidos, no máximo, seis instantâneos diferenciais. As cópias de segurança de registo de transações ocorrem a cada cinco minutos.

## <a name="backup-retention"></a>Retenção da cópia de segurança

As cópias de dados são armazenadas num armazenamento redundante local (LRS)-- que é armazenado em três cópias dentro de uma região. As cópias de segurança são mantidas com base na definição do período de retenção de cópias de segurança no servidor. Pode selecionar um período de retenção de 1 a 35 dias com um período de retenção predefinido de sete dias. Pode definir o período de retenção durante a criação do servidor ou mais tarde atualizando a configuração de backup utilizando o portal Azure.

O período de retenção de backups regula até onde pode ser realizada uma operação de restauro pontual, uma vez que é baseada em cópias de segurança disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspetiva de restauro. Todas as cópias de segurança necessárias para a restauração pontual dentro do período de retenção de backup são mantidas no armazenamento de backup. Por exemplo - se o período de retenção de backup estiver definido para sete dias, a janela de recuperação é considerada dura sete dias. Neste cenário, todas as cópias de segurança necessárias para restaurar o servidor nos últimos sete dias são mantidas. Com uma janela de retenção de backup de sete dias, as imagens de base de dados e cópias de segurança do registo de transações são armazenadas nos últimos oito dias (1 dia antes da janela).

## <a name="backup-storage-cost"></a>Custo de armazenamento de backup

O servidor flexível fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Qualquer armazenamento adicional de backup utilizado é cobrado em GB por mês. Por exemplo, se tiver provisionado um servidor com 250 GB de armazenamento, tem 250 GB de armazenamento disponível para cópias de segurança do servidor sem custo adicional. Se o uso diário de backup for de 25GB, então pode ter até 10 dias de armazenamento gratuito de backup. O armazenamento consumido para cópias de segurança superiores a 250 GB é cobrado de acordo com o [modelo de preços.](https://azure.microsoft.com/pricing/details/mysql/)

Pode utilizar a métrica [de armazenamento de backup utilizada](../concepts-monitoring.md) no Azure Monitor disponível no portal Azure para monitorizar o armazenamento de cópia de segurança consumido por um servidor. A métrica utilizada no **Armazenamento de Cópia** de Segurança representa a soma de armazenamento consumida por todas as cópias de segurança da base de dados e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. Uma atividade transacional intensa no servidor pode aumentar a utilização do armazenamento de cópias de segurança, independentemente do tamanho total da base de dados.

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup apropriado. Pode selecionar um período de retenção entre 1 a 35 dias.

> [!IMPORTANT]
> As cópias de segurança de um servidor de base de dados configurados numa configuração de alta disponibilidade redundante de zonas acontecem a partir do servidor de base de dados primário, uma vez que a sobrecarga é mínima com cópias de segurança instantâneas.

> [!IMPORTANT]
> Atualmente, as cópias de segurança geo-redundantes não são suportadas com servidor flexível.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Na Base de Dados Azure para o MySQL Flexible Server, executar uma restauração pontual cria um novo servidor a partir das cópias de segurança do servidor flexível na mesma região que o seu servidor de origem. É criado com a configuração do servidor original para o nível de computação, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup. Além disso, tags e configurações como rede virtual e firewall são herdadas do servidor de origem. As definições de computação e armazenamento do servidor restaurado podem ser alteradas após a conclusão da restauração.

> [!NOTE]
> Existem dois parâmetros do servidor que são reiniciados para valores predefinidos (e não são copiados do servidor primário) após a operação de restauro
> *   time_zone - Este valor para definir para DEFAULT Value SYSTEM
> *   event_scheduler - O event_scheduler está definido para OFF no servidor restaurado

A restauração pontual é útil em vários cenários. Alguns dos casos de uso que são comuns incluem -
-   Quando um utilizador elimina acidentalmente os dados na base de dados
-   O utilizador deixa cair uma tabela ou base de dados importantes
-   A aplicação do utilizador substitui acidentalmente bons dados com dados maus devido a um defeito de aplicação.

Pode escolher entre um último ponto de restauro e um ponto de restauração personalizado através do [portal Azure.](how-to-restore-server-portal.md)

-   **Último ponto de restauro** : O último ponto de restauro ajuda-o a restaurar o servidor até à última cópia de segurança realizada no servidor de origem. O tempotampido para restauro também será exibido no portal. Esta opção é útil para restaurar rapidamente o servidor para o estado mais atualizado.
-   **Ponto de restauração personalizado** : Isto permitir-lhe-á escolher qualquer ponto no tempo dentro do período de retenção definido para este servidor flexível. Esta opção é útil para restaurar o servidor no momento exato para recuperar de um erro do utilizador.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho da cópia de segurança do registo de transações, o tamanho do cálculo do SKU e a hora da restauração também. A recuperação do registo de transações é a parte mais demorada do processo de restauro. Se o tempo de restauração for escolhido mais perto do horário de backup total ou diferencial de instantâneo, os restauros são mais rápidos uma vez que a aplicação de registo de transação é mínima. Para estimar o tempo de recuperação exato para o seu servidor, recomendamos vivamente testá-lo no seu ambiente, uma vez que tem demasiadas variáveis específicas do ambiente.

> [!IMPORTANT]
> Se estiver a restaurar um servidor flexível configurado com alta disponibilidade redundante de zona, o servidor restaurado será configurado na mesma região e zona que o seu servidor primário, e implantado como um único servidor flexível num modo não HA. Consulte a [zona redundante alta disponibilidade](concepts-high-availability.md) para servidor flexível.

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Após uma restauração a partir do **último ponto de restauração** ou mecanismo de recuperação **de pontos de restauração personalizado,** deve executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

-   Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor.
-   Certifique-se de que existem regras adequadas de firewall ao nível do servidor e de rede virtual para que os utilizadores se conectem.
-   Certifique-se de que estão em vigor os logins adequados e as permissões de nível de base de dados.
-   Alertas de configuração, conforme apropriado.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
-   Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
