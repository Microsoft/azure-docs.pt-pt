---
title: Backup e restauração no banco de dados do Azure para MySQL
description: Saiba mais sobre backups automáticos e como restaurar seu banco de dados do Azure para servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: dfbf416c93c78e6ba5e23819084d69e57c47edc8
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273664"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Backup e restauração no banco de dados do Azure para MySQL

O banco de dados do Azure para MySQL cria automaticamente backups de servidor e os armazena em armazenamento com redundância geográfica ou local configurado pelo usuário. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. O backup e a restauração são uma parte essencial de qualquer estratégia de continuidade de negócios, pois eles protegem seus dados contra a corrupção ou exclusão acidental.

## <a name="backups"></a>Cópias de segurança

O banco de dados do Azure para MySQL usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor para qualquer ponto no tempo em seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Opcionalmente, você pode [configurá-lo](howto-restore-server-portal.md#set-backup-configuration) até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

### <a name="backup-frequency"></a>FREQUÊNCIA DE CÓPIA DE SEGURANÇA

Geralmente, os backups completos ocorrem semanalmente, os backups diferenciais ocorrem duas vezes por dia e os backups de log de transações ocorrem a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um servidor. O backup inicial pode levar mais tempo em um servidor restaurado grande. O ponto mais antigo no tempo no qual um novo servidor pode ser restaurado é a hora em que o backup completo inicial é concluído.

### <a name="backup-redundancy-options"></a>Opções de redundância de cópia de segurança

O banco de dados do Azure para MySQL fornece a flexibilidade para escolher entre o armazenamento de backup com redundância local ou com redundância geográfica nas camadas de Uso Geral e com otimização de memória. Quando os backups são armazenados no armazenamento de backup com redundância geográfica, eles não são armazenados apenas na região em que o servidor está hospedado, mas também são replicados em um [Data Center emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isso fornece melhor proteção e capacidade de restaurar o servidor em uma região diferente em caso de desastre. A camada básica oferece apenas armazenamento de backup com redundância local.

> [!IMPORTANT]
> A configuração do armazenamento com redundância local ou geográfica para backup só é permitida durante a criação do servidor. Depois de aprovisionado o servidor você não pode alterar a opção de redundância do armazenamento de backup.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

O banco de dados do Azure para MySQL fornece até 100% de seu armazenamento de servidor provisionado como armazenamento de backup sem custo adicional. Normalmente, isso é adequado para uma retenção de backup de sete dias. Qualquer armazenamento de backup adicional usado é cobrado em GB-mês.

Por exemplo, se você tiver provisionado um servidor com 250 GB, terá 250 GB de armazenamento de backup sem custo adicional. O armazenamento que excede 250 GB é cobrado.

## <a name="restore"></a>Restaurar

No banco de dados do Azure para MySQL, a execução de uma restauração cria um novo servidor a partir dos backups do servidor original.

Há dois tipos de restauração disponíveis:

- A **restauração pontual** está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o servidor original.
- A **restauração geográfica** só estará disponível se você tiver configurado o servidor para o armazenamento com redundância geográfica e ele permitir que você restaure o servidor para uma região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos de banco de dados, o tamanho do log de transações, a largura de banda da rede e o número total de bancos de dados recuperados na mesma região ao mesmo tempo. O tempo de recuperação geralmente é inferior a 12 horas.

> [!IMPORTANT]
> Os servidores excluídos **não podem** ser restaurados. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Para proteger os recursos do servidor, após a implantação, da exclusão acidental ou de alterações inesperadas, os administradores podem aproveitar os [bloqueios de gerenciamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de backup, você pode executar uma restauração para qualquer ponto no tempo dentro do período de retenção de backup. Um novo servidor é criado na mesma região do Azure que o servidor original. Ele é criado com a configuração do servidor original para o tipo de preço, geração de computação, número de vCores, tamanho do armazenamento, período de retenção de backup e opção de redundância de backup.

A restauração pontual é útil em vários cenários. Por exemplo, quando um usuário acidentalmente exclui dados, descarta uma tabela ou um banco de dado importante, ou se um aplicativo substitui acidentalmente dados bons por dados incorretos devido a um defeito do aplicativo.

Talvez seja necessário aguardar até que o próximo backup de log de transações seja realizado antes que você possa restaurar para um ponto no tempo nos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Você pode restaurar um servidor para outra região do Azure em que o serviço estará disponível se você tiver configurado o servidor para backups com redundância geográfica. A restauração geográfica é a opção de recuperação padrão quando o servidor não está disponível devido a um incidente na região em que o servidor está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá restaurar um servidor de backups com redundância geográfica para um servidor em qualquer outra região. Há um atraso entre o momento em que um backup é feito e quando ele é replicado em uma região diferente. Esse atraso pode ser de até uma hora, portanto, se ocorrer um desastre, pode haver uma perda de dados de até uma hora.

Durante a restauração geográfica, as configurações de servidor que podem ser alteradas incluem geração de computação, vCore, período de retenção de backup e opções de redundância de backup. A alteração do tipo de preço (básico, Uso Geral ou otimizado para memória) ou o tamanho do armazenamento durante a restauração geográfica não tem suporte.

### <a name="perform-post-restore-tasks"></a>Executar tarefas após a restauração

Após uma restauração de qualquer mecanismo de recuperação, você deve executar as seguintes tarefas para fazer com que os usuários e aplicativos façam backup e execução:

- Se o novo servidor for destinado a substituir o servidor original, redirecionar clientes e aplicativos cliente para o novo servidor
- Verifique se as regras apropriadas de firewall no nível de servidor estão em vigor para que os usuários se conectem
- Verifique se os logons apropriados e as permissões no nível do banco de dados estão em vigor
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre continuidade de negócios, consulte [visão geral da continuidade de negócios](concepts-business-continuity.md).
- Para restaurar para um ponto no tempo usando o portal do Azure, consulte [restaurar banco de dados para um ponto no tempo usando o portal do Azure](howto-restore-server-portal.md).
- Para restaurar para um ponto no tempo usando CLI do Azure, consulte [restaurar banco de dados para um ponto no tempo usando a CLI](howto-restore-server-cli.md).
