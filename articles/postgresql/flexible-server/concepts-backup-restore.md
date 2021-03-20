---
title: Backup e restauro na Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Conheça os conceitos de backup e restauro com Base de Dados Azure para PostgreSQL - Servidor Flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92532650"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Os backups formam uma parte essencial de qualquer estratégia de continuidade de negócios. Ajudam a proteger os dados da corrupção acidental ou da eliminação. Azure Database for PostgreSQL - Flexible Server bloqueia automaticamente o servidor e retém as cópias de segurança durante um período de até 35 dias. Ao restaurar, pode especificar a data e a hora a que pretende restaurar dentro do período de retenção. O tempo global para restaurar e recuperar depende do tamanho dos ficheiros da base de dados e da quantidade de recuperação a realizar. 

### <a name="backup-process-in-flexible-server"></a>Processo de backup no servidor flexível
A primeira cópia de segurança de instantâneos é agendada imediatamente após a criação do servidor flexível. Posteriormente, é realizada uma cópia de segurança instantânea diária dos ficheiros de dados. Os backups são armazenados numa zona de armazenamento redundante dentro de uma região. Os registos de transações (escrever registos à frente - WAL) também são arquivados continuamente, para que possa restaurar a última transação comprometida. Estas cópias de segurança de registos e dados permitem-lhe restaurar um servidor para qualquer ponto no tempo dentro do período de retenção configurado das cópias de segurança. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

Se a base de dados estiver configurada com elevada disponibilidade, são realizadas imagens diárias a partir da primária e as cópias de segurança contínuas são realizadas a partir do standby.

> [!IMPORTANT]
>As cópias de segurança não são executadas em servidores parados. No entanto, as cópias de segurança são retomadas quando a base de dados é iniciada automaticamente após 7 dias ou iniciada pelo utilizador.

As cópias de segurança só podem ser utilizadas para operações de restauro dentro do servidor Flexível. Se pretender exportar ou importar dados para o servidor flexível, utilize a metodologia [de despejo e restauro.](../howto-migrate-using-dump-and-restore.md)


### <a name="backup-retention"></a>Retenção da cópia de segurança

As cópias de segurança são mantidas com base na definição do período de retenção de backup para o servidor. Pode selecionar um período de retenção entre 7 e 35 dias. O período de retenção por defeito é de sete dias. Pode definir o período de retenção durante a criação do servidor ou pode atualizá-lo mais tarde. As cópias de segurança são mantidas mesmo para servidores parados.

O período de retenção de backups regula o quão longe no tempo uma restauração pontual pode ser recuperada, uma vez que \' é baseada em cópias de segurança disponíveis. O período de retenção de backup também pode ser tratado como uma janela de recuperação de uma perspetiva de restauro. Todas as cópias de segurança necessárias para a restauração pontual dentro do período de retenção de backup são mantidas no armazenamento de backup. Por exemplo - se o período de retenção de backup estiver definido para sete dias, a janela de recuperação é considerada como duração de sete dias. Neste cenário, todos os dados e registos necessários para restaurar e recuperar o servidor nos últimos sete dias são mantidos. 


### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

O servidor flexível fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Qualquer armazenamento adicional de backup utilizado é cobrado em GB por mês. Por exemplo, se tiver provisionado um servidor com 250 GiB de armazenamento, então tem 250 GiB de capacidade de armazenamento de backup sem custos adicionais. Se o uso diário de backup for de 25 GiB, então você pode ter até 10 dias de armazenamento de backup gratuito. O consumo de armazenamento de backup superior a 250 GiB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/postgresql/).

Pode utilizar a métrica [Armazenamento de cópias de segurança utilizado](../concepts-monitoring.md) no portal do Azure para monitorizar o armazenamento de cópias de segurança consumido por um servidor. A métrica Armazenamento de Cópias de Segurança utilizado representa a soma do armazenamento consumido por todas as cópias de segurança de bases de dados e cópias de segurança de registos retidas com base no período de retenção das cópias de segurança definido para o servidor.  Uma atividade transacional intensa no servidor pode aumentar a utilização do armazenamento de cópias de segurança, independentemente do tamanho total da base de dados.

O principal meio de controlar o custo de armazenamento de backup é definindo o período de retenção de backup adequado e escolhendo as opções de redundância de backup certas para cumprir os objetivos de recuperação pretendidos.

> [!IMPORTANT]
> Atualmente, as cópias de segurança geo-redundantes não são suportadas com servidor flexível.

## <a name="point-in-time-restore-overview"></a>Visão geral de restauro pontual

No servidor Flexível, executar uma restauração pontual cria um novo servidor a partir das \' cópias de segurança flexíveis do servidor na mesma região que o seu servidor de origem. É criado com a configuração do servidor de origem para o nível de preços, geração de cálculo, número de vCores, tamanho de armazenamento, período de retenção de backup e opção de redundância de backup. Além disso, as etiquetas e as definições, como as definições da VNET e da firewall, são herdadas do servidor de origem. 

 > [!IMPORTANT]
> Se estiver a restaurar um servidor flexível configurado com uma zona redundante de alta disponibilidade, o servidor restaurado será configurado sem alta disponibilidade e na mesma região que o seu servidor primário. 

 ### <a name="restore-process"></a>Processo de restauro

Os ficheiros de base de dados físicos são restaurados pela primeira vez a partir das cópias de segurança instantâneas para a localização dos dados do servidor. A cópia de segurança adequada que foi tomada mais cedo do que o ponto de tempo desejado é automaticamente escolhida e restaurada. Um processo de recuperação é então iniciado usando ficheiros WAL para trazer a base de dados para um estado consistente. 

 Por exemplo, assumamos que os backups são realizados às 23h todas as noites. Se o ponto de restauro for para 15 de agosto de 2020 às 10:00, o backup diário de 14 de agosto de 2020 é restaurado. A base de dados será recuperada até às 10h do dia 25 de agosto de 2020, utilizando os registos de transações entre 24 de agosto, 23h00 e 25 de agosto de 2000. 

 Consulte [estes passos](./how-to-restore-server-portal.md) para restaurar o servidor da base de dados.

> [!IMPORTANT]
> Restaurar as operações em servidor flexível cria um novo servidor de base de dados e não substitui o servidor de base de dados existente.

A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina acidentalmente dados, deixa cair uma tabela ou base de dados importante, ou se uma aplicação acidentalmente substituir bons dados com dados maus devido a um defeito de aplicação. Poderá restaurar a última transação devido à cópia de segurança contínua dos registos de transações.

Pode escolher entre um ponto de restauro mais antigo e um ponto de restauro personalizado.

-   **Ponto de restauro mais precoce**: Dependendo do seu período de retenção, será o momento mais cedo para restaurar. O tempo de cópia de segurança mais antigo será selecionado automaticamente e é apresentado no portal. Isto é útil se quiser investigar ou fazer alguns testes a partir desse ponto no tempo.

-   **Ponto de restauração personalizado**: Esta opção permite-lhe escolher qualquer ponto no tempo dentro do período de retenção definido para este servidor flexível. Por predefinição, a última vez na UTC é selecionada automaticamente e útil se quiser restaurar a última transação comprometida para os seus fins de teste. Pode escolher opcionalmente outros dias e horas. 

O tempo estimado para recuperar depende de vários fatores, incluindo o tamanho da base de dados, o volume de registos de transações para processar, a largura de banda da rede e o número total de bases de dados que se recuperam na mesma região ao mesmo tempo. O tempo geral de recuperação geralmente demora de poucos minutos até poucas horas.


> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Para proteger os recursos do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro

Depois de restaurar a base de dados, pode executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

-   Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor.

-   Certifique-se de que existem regras adequadas ao nível do servidor e das regras VNet para que os utilizadores se conectem. Estas regras não são copiadas do servidor original.
  
-   A computação do servidor restaurado pode ser dimensionada para cima/para baixo conforme necessário.

-   Certifique-se de que estão em vigor os logins adequados e as permissões de nível de base de dados.

-   Alertas de configuração, conforme apropriado.
  
-  Se tivesse restaurado a base de dados configurada com elevada disponibilidade e, se pretendesse configurar o servidor restaurado com elevada disponibilidade, pode seguir [os passos](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
-   Saiba [como restaurar](./how-to-restore-server-portal.md)