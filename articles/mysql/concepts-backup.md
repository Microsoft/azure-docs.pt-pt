---
title: Cópia de segurança e restauro na base de dados do Azure para MySQL
description: Saiba mais sobre cópias de segurança automáticas e restaurar a base de dados do Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6fe5aea9b8fa87efdfa7cc57716cf548a52e076b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532132"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Cópia de segurança e restauro na base de dados do Azure para MySQL

Base de dados do Azure para MySQL cria cópias de segurança do servidor e armazena-os no armazenamento localmente redundantes ou georredundantes do utilizador configurada automaticamente. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto anterior no tempo. Backup e restauração são uma parte essencial de qualquer estratégia de continuidade do negócio, porque eles protegem os dados de danos acidentais ou eliminação.

## <a name="backups"></a>Cópias de segurança

Base de dados do Azure para MySQL demora completas, diferenciais e backups de log de transação. Estas cópias de segurança permitem-lhe restaurar um servidor para qualquer ponto anterior no tempo dentro do seu período de retenção de cópia de segurança configurado. O período de retenção de cópia de segurança padrão é sete dias. Opcionalmente, pode configurá-lo até 35 dias. Todas as cópias de segurança são encriptadas com a encriptação AES de 256 bits.

### <a name="backup-frequency"></a>Frequência de cópia de segurança

Em geral, cópias de segurança completas ocorrem semanalmente, cópias de segurança diferenciais ocorrem duas vezes por dia, e backups de log de transação ocorrem a cada cinco minutos. A primeira cópia de segurança completa está agendada imediatamente após a criação de um servidor. A cópia de segurança inicial poderá demorar mais tempo num grande servidor restaurado. O ponto mais antigo na hora em que um novo servidor de pode ser restaurado para é o tempo que a cópia de segurança completa inicial está concluída.

### <a name="backup-redundancy-options"></a>Opções de redundância da cópia de segurança

Base de dados do Azure para MySQL fornece a flexibilidade de escolher entre o armazenamento de cópias de segurança localmente redundante ou georredundantes nas camadas de fins gerais e com otimização de memória. Quando as cópias de segurança são armazenadas no armazenamento de cópia de segurança georredundante, eles não são apenas armazenados dentro da região na qual o servidor está alojada, mas também são replicadas para um [Centro de dados emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Isso fornece a melhor proteção e a capacidade de restaurar o servidor numa região diferente em caso de desastre. O escalão básico oferece apenas armazenamento localmente redundante de cópia de segurança.

> [!IMPORTANT]
> A configuração do armazenamento com redundância local ou geográfica para backup só é permitida durante a criação do servidor. Depois de aprovisionado o servidor você não pode alterar a opção de redundância do armazenamento de backup.

### <a name="backup-storage-cost"></a>Custo de armazenamento de cópia de segurança

Base de dados do Azure para MySQL fornece até 100% do armazenamento de servidor aprovisionado como armazenamento de cópias de segurança sem custos adicionais. Normalmente, isto é adequado para uma retenção de cópia de segurança de sete dias. Qualquer armazenamento de cópia de segurança adicional utilizado é cobrado em GB-mês.

Por exemplo, se aprovisionou um servidor com 250 GB, terá 250 GB de armazenamento de cópia de segurança sem custos adicionais. É cobrado o armazenamento em excesso de 250 GB.

## <a name="restore"></a>Restauro

Na base de dados do Azure para MySQL, executar uma restauração cria um novo servidor de cópias de segurança do servidor original.

Existem dois tipos de restauro disponíveis:

- **Restauro de ponto no tempo** está disponível com qualquer uma das opções de redundância de cópia de segurança e cria um novo servidor na mesma região que o seu servidor original.
- **O restauro geográfico** está disponível apenas se tiver configurado o seu servidor para o armazenamento georredundante e permite-lhe restaurar o servidor para uma região diferente.

O tempo estimado da recuperação depende de vários fatores, incluindo os tamanhos de base de dados, o tamanho do registo de transações, a largura de banda de rede e o número total de bases de dados a recuperar na mesma região ao mesmo tempo. O tempo de recuperação é normalmente inferior a 12 horas.

> [!IMPORTANT]
> Eliminar servidores **não é possível** ser restaurada. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminados e não podem ser recuperados. Para proteger os recursos de servidor, após a implementação, de eliminação acidental ou alterações inesperadas, os administradores podem aproveitar [bloqueios de gestão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Independentemente da sua opção de redundância de cópia de segurança, pode efetuar um restauro para qualquer ponto no tempo dentro do seu período de retenção de cópia de segurança. Um novo servidor é criado na mesma região do Azure como o servidor original. Ele é criado com a configuração do servidor original para o escalão de preço, geração de computação e número de vCores, o tamanho de armazenamento, o período de retenção de cópia de segurança e a opção de redundância de cópia de segurança.

Restauro de ponto no tempo é útil em vários cenários. Por exemplo, quando um usuário exclua acidentalmente dados, ignora uma tabela importante ou a base de dados, ou se um aplicativo substitui acidentalmente dados com dados incorretos devido a um defeito da aplicação.

Terá de aguardar o próximo backup de log de transação a executar antes de pode restaurar para um ponto no tempo nos últimos cinco minutos.

### <a name="geo-restore"></a>Georrestauro

Pode restaurar um servidor para outra região do Azure onde o serviço está disponível se tiver configurado o seu servidor para cópias de segurança georredundante. Georrestauro encontra-se a opção de recuperação predefinida quando o servidor está indisponível devido a um incidente na região onde está alojado o servidor. Se um incidente em grande escala nos resultados da região na indisponibilidade da sua aplicação de base de dados, pode restaurar um servidor de cópias de segurança georredundante para um servidor em qualquer outra região. Existe um atraso entre quando é feita uma cópia de segurança e quando são replicado para uma região diferente. Este atraso pode demorar até uma hora, por isso, se ocorrer um desastre, pode haver até à perda de dados de uma hora.

Durante o restauro geográfico, as configurações de servidor que podem ser alteradas incluem a geração de computação, vCore, período de retenção de cópia de segurança e opções de redundância da cópia de segurança. Alterar o escalão de preço (básico, fins gerais ou com otimização de memória) ou o tamanho de armazenamento durante o restauro geográfico não é suportado.

### <a name="perform-post-restore-tasks"></a>Executar tarefas de pós-restauro

Após um restauro a partir de qualquer mecanismo de recuperação, deve executar as seguintes tarefas para obter os seus utilizadores e aplicações volte a funcionar:

- Se o novo servidor destina-se para substituir o servidor original, redirecionar os clientes e aplicações de cliente para o novo servidor
- Certifique-se de que as regras de firewall de ao nível do servidor apropriados estão em vigor para os usuários se conectem
- Certifique-se de inícios de sessão apropriados e permissões ao nível da base de dados no local
- Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a continuidade do negócio, veja a [descrição geral da continuidade de negócio](concepts-business-continuity.md).
- Para restaurar para um ponto no tempo, com o portal do Azure, consulte [restaurar base de dados para um ponto no tempo com o portal do Azure](howto-restore-server-portal.md).
- Para restaurar para um ponto no tempo, com a CLI do Azure, consulte [restaurar base de dados para um ponto no tempo com a CLI](howto-restore-server-cli.md).
