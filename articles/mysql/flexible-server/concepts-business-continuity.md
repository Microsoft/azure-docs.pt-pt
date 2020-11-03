---
title: Visão geral da continuidade do negócio - Base de Dados Azure para MySQL Flexible Server
description: Conheça os conceitos de continuidade de negócios com Azure Database para MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: c29e952e22aaccf31c10de8f6e16d240b4660a23
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240720"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Visão geral da continuidade do negócio com base de dados Azure para MySQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

A Azure Database for MySQL Flexible Server permite capacidades de continuidade de negócios que protegem as suas bases de dados em caso de uma paragem planeada e não planeada. Funcionalidades como cópias de segurança automatizadas e endereços de alta disponibilidade endereços diferentes níveis de proteção contra falhas com diferentes tempos de recuperação e exposições de perda de dados. Ao arquitetar a sua aplicação para proteger contra falhas, deve considerar o objetivo do tempo de recuperação (RTO) e o objetivo do ponto de recuperação (RPO) para cada aplicação. O RTO é a tolerância ao tempo de inatividade e a RPO é a tolerância à perda de dados após uma interrupção do serviço de base de dados.

A tabela abaixo ilustra as funcionalidades que o servidor flexível oferece.

| **Funcionalidade** | **Descrição** | **Restrições** |
| ---------- | ----------- | ------------ |
| **Recuperação de & de apoio** | O servidor flexível realiza automaticamente cópias de segurança diárias dos ficheiros da sua base de dados e faz cópias de segurança contínuas dos registos de transações. As cópias de segurança podem ser mantidas por qualquer período entre 1 e 35 dias. Poderá restaurar o servidor da base de dados a qualquer ponto no tempo dentro do período de retenção de cópias de segurança. O tempo de recuperação dependerá do tamanho dos dados para restaurar + o tempo para realizar a recuperação do registo. Consulte os [Conceitos - Backup e Restaurar](./concepts-backup-restore.md) para mais detalhes. |Os dados de backup permanecem na região |
| **Backup redundante local** | As cópias de segurança flexíveis do servidor são armazenadas automaticamente e de forma segura num armazenamento redundante local dentro de uma região e na mesma zona de disponibilidade. As cópias de segurança locais redundantes replicam os ficheiros de backup do servidor três vezes dentro de uma única localização física na região primária. O armazenamento de backup localmente redundante proporciona pelo menos 99.999999999999999% (11 noves) durabilidade de objetos durante um determinado ano. Consulte os [Conceitos - Backup e Restaurar](./concepts-backup-restore.md) para mais detalhes.| Aplicável em todas as regiões |
| **Zona redundante alta disponibilidade** | O servidor flexível pode ser implementado em modo de alta disponibilidade, que implementa servidores primários e de espera em duas zonas de disponibilidade diferentes dentro de uma região. Isto protege contra falhas ao nível da zona e também ajuda na redução do tempo de inatividade da aplicação durante eventos de inatividade planeados e não planeados. Os dados do servidor primário são sincronizados replicados para a réplica de espera. Durante qualquer evento de tempo de inatividade, o servidor de base de dados é automaticamente falhado na réplica de espera. Consulte os [Conceitos - Alta disponibilidade](./concepts-high-availability.md) para mais detalhes. | Suportado em níveis de computação otimizados de propósito geral e memória. Disponível apenas em regiões onde existem várias zonas.|
| **Ações de ficheiros premium** | Os ficheiros de base de dados são armazenados numa ação de ficheiro premium Azure altamente durável e fiável que fornecem redundância de dados com três cópias de réplica armazenadas dentro de uma zona de disponibilidade com capacidades automáticas de recuperação de dados. Consulte [as ações do Ficheiro Premium](../../storage/files/storage-how-to-create-premium-fileshare.md) para mais detalhes. | Dados armazenados dentro de uma zona de disponibilidade |

> [!IMPORTANT]
> Durante o período de pré-visualização, não são oferecidos tempo de uptime, RTO e RPO SLA. Detalhes fornecidos nesta página apenas para as suas finalidades de informação e planeamento.

## <a name="planned-downtime-mitigation"></a>Mitigação prevista para o tempo de inatividade

Aqui estão alguns cenários de manutenção planeados que incorrem em tempo de inatividade:

| **Cenário** | **Processo**|
| :------------ | :----------- |
| **Escala de cálculo (Utilizador)**| Quando executa a operação de escala de cálculo, um novo servidor flexível é a provisionado usando a configuração de computação em escala. No servidor de base de dados existente, os pontos de verificação ativos são autorizados a ser concluídos, as ligações do cliente são drenadas, quaisquer transações não comprometidas são canceladas e, em seguida, é desligado. O armazenamento é então anexado ao novo servidor e a base de dados é iniciada que realiza a recuperação se necessário antes de aceitar ligações ao cliente. |
| **Nova implementação de software (Azure)** | As novas funcionalidades de lançamento ou correções de bugs acontecem automaticamente como parte da manutenção planeada do serviço, e pode agendar quando essas atividades acontecerem. Para mais informações, consulte a [documentação](https://aka.ms/servicehealthpm)e verifique também o seu [portal](https://aka.ms/servicehealthpm) |
| **Upgrades de versão menores (Azure)** | A Azure Database for MySQL remenda automaticamente os servidores de base de dados para a versão menor determinada pelo Azure. Acontece como parte da manutenção planeada do serviço. Isto incorreria num curto período de inatividade em termos de segundos, e o servidor de base de dados é automaticamente reiniciado com a nova versão menor. Para mais informações, consulte a [documentação](../concepts-monitoring.md#planned-maintenance-notification)e verifique também o seu [portal.](https://aka.ms/servicehealthpm)|

Quando o servidor flexível é configurado com **zona de alta disponibilidade redundante,** o servidor flexível executa as operações no servidor de espera primeiro e depois no servidor primário sem uma falha. Consulte os [Conceitos - Alta disponibilidade](./concepts-high-availability.md) para mais detalhes.

## <a name="unplanned-downtime-mitigation"></a>Mitigação não planeada do tempo de inatividade

Os tempos de inatividade não planeados podem ocorrer em resultado de falhas imprevistas, incluindo falhas subjacentes ao hardware, problemas de rede e bugs de software. Se o servidor de base de dados se avariar inesperadamente, se configurado com alta disponibilidade [HA], então a réplica de espera é ativada. Caso contrário, um novo servidor de base de dados é automaticamente a provisionado. Embora não seja possível evitar um tempo de inatividade não planeado, o servidor flexível atenua o tempo de inatividade, realizando automaticamente operações de recuperação tanto no servidor de base de dados como nas camadas de armazenamento sem necessidade de intervenção humana.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planeado: cenários de avaria e recuperação de serviços

Aqui estão alguns cenários de falha não planeados e o processo de recuperação:

| **Cenário** | **Processo de recuperação [não-HA]** | **Processo de recuperação [HA]** |
| :---------- | ---------- | ------- |
| **Falha do servidor de base de dados** | Se o servidor de base de dados estiver em baixo devido a alguma falha de hardware subjacente, as ligações ativas são retiradas e quaisquer transações de voo são abortadas. O Azure tentará reiniciar o servidor de base de dados. Se isso for bem sucedido, então a recuperação da base de dados é realizada. Se o reinício falhar, o servidor de base de dados será tentado reiniciar outro nó físico.  <br /> <br /> O tempo de recuperação (RTO) depende de vários fatores, incluindo a atividade no momento da falha, como a grande transação e a quantidade de recuperação a realizar durante o processo de arranque do servidor de base de dados. <br /> <br /> As aplicações que utilizam as bases de dados do MySQL precisam de ser construídas de forma a detetarem e recaírem ligações e transações falhadas.  Quando a aplicação é retrástica, as ligações são direcionadas para o servidor de base de dados recém-criado. | Se a falha do servidor de base de dados for detetada, o servidor de base de dados de espera é ativado, reduzindo assim o tempo de inatividade. Consulte a [página de conceitos HA](concepts-high-availability.md) para mais detalhes. Espera-se que o RTO seja de 60-120 s, com RPO=0 |
| **Falha de armazenamento** | As aplicações não vêem qualquer impacto para quaisquer questões relacionadas com o armazenamento, tais como uma falha no disco ou uma corrupção de bloco físico. Como os dados são armazenados em 3 exemplares, a cópia dos dados é servida pelo armazenamento sobrevivente. As corrupçãos de blocos são automaticamente corrigidas. Se uma cópia dos dados for perdida, uma nova cópia dos dados é criada automaticamente. | Para erros não recuperáveis, o servidor flexível é falhado na réplica de espera para reduzir o tempo de inatividade. Consulte a [página de conceitos HA](./concepts-high-availability.md) para mais detalhes. |
| **Erros lógicos/de utilizador** | A recuperação de erros do utilizador, tais como tabelas acidentalmente largadas ou dados incorretamente atualizados, envolve a realização de uma [recuperação pontual](concepts-backup-restore.md) (PITR), restaurando e recuperando os dados até ao momento antes do erro ter ocorrido.<br> <br>  Se pretender restaurar apenas um subconjunto de bases de dados ou tabelas específicas em vez de todas as bases de dados no servidor de bases de dados, pode restaurar o servidor de base de dados num novo caso, exportar a tabela(s) através [de pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html), e depois utilizar [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) para restaurar essas tabelas na sua base de dados. | Estes erros do utilizador não estão protegidos com elevada disponibilidade devido ao facto de todas as operações dos utilizadores serem replicadas também para o standby. |
| **Falha na zona de disponibilidade** | Embora seja um evento raro, se quiser recuperar de uma falha de nível de zona, pode realizar uma recuperação pontual usando a cópia de segurança e escolhendo o ponto de restauro personalizado para chegar aos dados mais recentes. Um novo servidor flexível será implantado noutra zona. O tempo de restauro depende da cópia de segurança anterior e do número de registos de transações para recuperar. | O servidor flexível executa falhas automáticas no local de espera. Consulte a [página de conceitos HA](./concepts-high-availability.md) para mais detalhes. |
| **Falha na região** | As réplicas transversais e as funcionalidades de geo-restauro ainda não são suportadas na pré-visualização. | |

> [!IMPORTANT]
> Os servidores eliminados **não podem** ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Utilize [o bloqueio de recursos Azure](../../azure-resource-manager/management/lock-resources.md) para evitar a eliminação acidental do seu servidor.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
- Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
