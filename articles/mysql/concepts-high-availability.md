---
title: Alta disponibilidade - Base de Dados Azure para MySQL
description: Este artigo fornece informações sobre alta disponibilidade na Base de Dados Azure para o MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 74d6981c0465a1960e920313c1f960f0d781692b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092968"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Alta disponibilidade em Base de Dados Azure para MySQL
A Base de Dados Azure para o serviço MySQL proporciona um elevado nível de disponibilidade garantido com o contrato de nível de serviço (SLA) apoiado financeiramente de [99,99% de tempo de 99,99%.](https://azure.microsoft.com/support/legal/sla/mysql) A Azure Database for MySQL fornece alta disponibilidade durante eventos planeados, como operação de computação em escala initada pelo utilizador, e também quando ocorrem eventos não planeados, tais como hardware, software ou falhas de rede subjacentes. A Azure Database for MySQL pode recuperar rapidamente da maioria das circunstâncias críticas, garantindo praticamente nenhum tempo de inação ao utilizar este serviço.

A Azure Database for MySQL é adequada para executar bases de dados críticas da missão que requerem tempo alto. Construído sobre a arquitetura Azure, o serviço tem capacidades inerentes de alta disponibilidade, redundância e resiliência para mitigar o tempo de inatividade da base de dados a partir de paragens planeadas e não planeadas, sem que você configue quaisquer componentes adicionais. 

## <a name="components-in-azure-database-for-mysql"></a>Componentes na Base de Dados Azure para o MySQL

| **Componente** | **Descrição**|
| ------------ | ----------- |
| <b>Servidor de base de dados MySQL | A Azure Database for MySQL fornece segurança, isolamento, salvaguardas de recursos e capacidade de reinício rápido para servidores de bases de dados. Estas capacidades facilitam operações como a operação de recuperação de servidores de escala e de base de dados após uma paragem em 60-120 segundos, dependendo da atividade transacional na base de dados. <br/> As modificações de dados no servidor da base de dados ocorrem normalmente no contexto de uma transação de base de dados. Todas as alterações na base de dados são registadas sincronizadamente sob a forma de registos de escrita antecipada (ib_log) no Azure Storage – que está anexado ao servidor de base de dados. Durante o processo [de verificação](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html) da base de dados, as páginas de dados da memória do servidor de base de dados também são lavadas para o armazenamento. |
| <b>Armazenamento remoto | Todos os ficheiros de dados físicos e ficheiros de registo do MySQL são armazenados no Azure Storage, que é projetado para armazenar três cópias de dados dentro de uma região para garantir a redundância, disponibilidade e fiabilidade dos dados. A camada de armazenamento também é independente do servidor de base de dados. Pode ser desligado de um servidor de base de dados falhado e religado a um novo servidor de base de dados dentro de 60 segundos. Além disso, o Azure Storage monitoriza continuamente quaisquer falhas de armazenamento. Se for detetada uma corrupção de bloco, é automaticamente corrigida através da instantânea nova cópia de armazenamento. |
| <b>Gateway | O Gateway funciona como um representante de base de dados, encaminha todas as ligações do cliente para o servidor de base de dados. |

## <a name="planned-downtime-mitigation"></a>Mitigação prevista para o tempo de inatividade
A Azure Database for MySQL é projetado para fornecer alta disponibilidade durante as operações planeadas de inatividade. 

:::image type="content" source="./media/concepts-high-availability/elastic-scaling-mysql-server.png" alt-text="vista de Elastic Scaling em Azure MySQL":::

Aqui estão alguns cenários de manutenção planeados:

| **Cenário** | **Descrição**|
| ------------ | ----------- |
| <b>Escala de cálculo para cima/para baixo | Quando o utilizador executa a operação de escala de cálculo para cima/para baixo, um novo servidor de base de dados é a provisionado utilizando a configuração de computação em escala. No antigo servidor de bases de dados, os pontos de verificação ativos são autorizados a completar, as ligações do cliente são drenadas, quaisquer transações não comprometidas são canceladas e, em seguida, é desligado. O armazenamento é então desligado do antigo servidor de base de dados e anexado ao novo servidor de base de dados. Quando a aplicação do cliente retrição a ligação, ou tenta fazer uma nova ligação, o Gateway direciona o pedido de ligação para o novo servidor de base de dados.|
| <b>Armazenamento de escalonamento | O escalonamento do armazenamento é uma operação online e não interrompe o servidor de base de dados.|
| <b>Nova implementação de software (Azure) | As novas funcionalidades de lançamento ou correções de bugs acontecem automaticamente como parte da manutenção planeada do serviço. Para mais informações, consulte a [documentação,](concepts-monitoring.md#planned-maintenance-notification)e verifique também o seu [portal.](https://aka.ms/servicehealthpm)|
| <b>Upgrades de versão menores | A Azure Database for MySQL remenda automaticamente os servidores de base de dados para a versão menor determinada pelo Azure. Acontece como parte da manutenção planeada do serviço. Durante a manutenção planeada, pode haver reinício ou falhas no servidor de base de dados, o que pode levar a uma breve indisponibilidade dos servidores de base de dados para os utilizadores finais. A base de dados Azure para servidores MySQL está a funcionar em contentores para que os recomeçamento do servidor de base de dados sejam normalmente rápidos, esperando-se que completem normalmente em 60-120 segundos. Todo o evento de manutenção planeado, incluindo cada servidor, é cuidadosamente monitorizado pela equipa de engenharia. O tempo de falha do servidor depende do tempo de recuperação da base de dados, o que pode fazer com que a base de dados fique online por mais tempo se tiver uma atividade transacional pesada no servidor no momento da falha. Para evitar um tempo de reinício mais longo, é aconselhável evitar transações de longa duração (cargas a granel) durante os eventos de manutenção planeados. Para mais informações, consulte a [documentação,](concepts-monitoring.md#planned-maintenance-notification)e verifique também o seu [portal.](https://aka.ms/servicehealthpm)|


##  <a name="unplanned-downtime-mitigation"></a>Mitigação não planeada do tempo de inatividade

O tempo de inatividade não planeado pode ocorrer em resultado de falhas imprevistas, incluindo falhas subjacentes ao hardware, problemas de rede e bugs de software. Se o servidor de base de dados se avariar inesperadamente, um novo servidor de base de dados é automaticamente a provisionado em 60-120 segundos. O armazenamento remoto é automaticamente anexado ao novo servidor de base de dados. O motor MySQL executa a operação de recuperação utilizando ficheiros WAL e base de dados e abre o servidor de base de dados para permitir que os clientes se conectem. As transações não autorizadas perdem-se e têm de ser novamente julgadas pelo pedido. Embora não seja possível evitar um tempo de inatividade não planeado, a Base de Dados Azure para o MySQL atenua o tempo de inatividade, realizando automaticamente operações de recuperação tanto no servidor de base de dados como nas camadas de armazenamento sem necessidade de intervenção humana. 


:::image type="content" source="./media/concepts-high-availability/availability-for-mysql-server.png" alt-text="vista de Alta Disponibilidade em Azure MySQL":::

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planeado: cenários de avaria e recuperação de serviços
Aqui estão alguns cenários de falha e como a Base de Dados Azure para o MySQL recupera automaticamente:

| **Cenário** | **Recuperação automática** |
| ---------- | ---------- |
| <B>Falha do servidor de base de dados | Se o servidor de base de dados estiver em baixo devido a alguma falha de hardware subjacente, as ligações ativas são retiradas e quaisquer transações de voo são abortadas. Um novo servidor de base de dados é automaticamente implantado e o armazenamento remoto de dados é anexado ao novo servidor de base de dados. Após a recuperação da base de dados estar concluída, os clientes podem ligar-se ao novo servidor de base de dados através do Gateway. <br /> <br /> As aplicações que utilizam as bases de dados do MySQL precisam de ser construídas de forma a detetarem e recaírem ligações e transações falhadas.  Quando a aplicação recauchutado, o Gateway redireciona transparentemente a ligação para o servidor de base de dados recém-criado. |
| <B>Falha de armazenamento | As aplicações não vêem qualquer impacto para quaisquer questões relacionadas com o armazenamento, tais como uma falha no disco ou uma corrupção de bloco físico. Como os dados são armazenados em 3 exemplares, a cópia dos dados é servida pelo armazenamento sobrevivente. As corrupçãos de blocos são automaticamente corrigidas. Se uma cópia dos dados for perdida, uma nova cópia dos dados é criada automaticamente. |

Eis alguns cenários de falha que exigem que a ação do utilizador recupere:

| **Cenário** | **Plano de recuperação** |
| ---------- | ---------- |
| <b> Falha na região | O fracasso de uma região é um acontecimento raro. No entanto, se precisar de proteção contra uma falha da região, pode configurar uma ou mais réplicas lidas noutras regiões para a recuperação de desastres (DR). (Consulte [este artigo](howto-read-replicas-portal.md) sobre a criação e gestão de réplicas de leitura para mais detalhes). Em caso de falha a nível da região, pode promover manualmente a réplica de leitura configurada na outra região para ser o seu servidor de base de dados de produção. |
| <b> Erros lógicos/de utilizador | A recuperação de erros do utilizador, tais como tabelas acidentalmente largadas ou dados incorretamente atualizados, envolve a realização de uma [recuperação pontual](concepts-backup.md) (PITR), restaurando e recuperando os dados até ao momento antes do erro ter ocorrido.<br> <br>  Se pretender restaurar apenas um subconjunto de bases de dados ou tabelas específicas em vez de todas as bases de dados no servidor de bases de dados, pode restaurar o servidor de base de dados num novo caso, exportar a tabela(s) através do [mysqldump](concepts-migrate-dump-restore.md), e depois utilizar [o restauro](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench) para restaurar essas tabelas na sua base de dados. |



## <a name="summary"></a>Resumo

A Azure Database for MySQL fornece uma capacidade de reinício rápido dos servidores de base de dados, armazenamento redundante e encaminhamento eficiente a partir do Gateway. Para uma proteção adicional de dados, pode configurar cópias de segurança para serem geo-replicadas, e também implementar uma ou mais réplicas de leitura noutras regiões. Com capacidades inerentes de elevada disponibilidade, a Azure Database for MySQL protege as suas bases de dados das interrupções mais comuns, e oferece uma indústria líder, apoiada por financiamento, [99,99% do SLA em alta.](https://azure.microsoft.com/support/legal/sla/mysql) Todas estas capacidades de disponibilidade e fiabilidade permitem ao Azure ser a plataforma ideal para executar as suas aplicações críticas de missão.

## <a name="next-steps"></a>Passos seguintes
- Conheça as [regiões de Azure](../availability-zones/az-overview.md)
- Saiba como [lidar com erros de conectividade transitórios](concepts-connectivity.md)
- Saiba como [replicar os seus dados com réplicas lidas](howto-read-replicas-portal.md)
