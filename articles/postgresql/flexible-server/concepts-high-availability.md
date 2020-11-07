---
title: Visão geral da zona redundante alta disponibilidade com Base de Dados Azure para PostgreSQL - Servidor Flexível (Pré-visualização)
description: Conheça os conceitos de zona redundante alta disponibilidade com Base de Dados Azure para PostgreSQL - Servidor Flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b23c95ef0005c8246feb8dc32e4a07a0ae19b72f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359549"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Conceitos de alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A azure Database for PostgreSQL - Flexible Server oferece uma configuração de alta disponibilidade com capacidade automática de failover utilizando a implementação de servidor redundante de **zona.** Quando implementado numa configuração com redundância de zona, o servidor flexível aprovisiona e gere automaticamente uma réplica de reserva numa zona de disponibilidade diferente. Utilizando a replicação de streaming PostgreSQL, os dados são replicados para o servidor de réplica de standby em modo **sincronizado.** 

A configuração redundante da zona permite a capacidade automática de falha com perda de dados zero durante eventos planeados, como a operação de computação de escala iniciada pelo utilizador, e também durante eventos não planeados, tais como falhas de hardware e software subjacentes, falhas de rede e falhas na zona de disponibilidade. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="zona redundante alta disponibilidade"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Zona redundante arquitetura de alta disponibilidade

Pode escolher a região e a zona de disponibilidade para implementar o servidor de bases de dados primário. Um servidor de réplica de reserva é aprovisionado numa zona de disponibilidade diferente com a mesma configuração que o servidor primário, incluindo a camada de computação, o tamanho da computação, o tamanho do armazenamento e a configuração de rede. Os registos de transações são replicados em modo sincronizado para a réplica de espera utilizando a replicação de streaming PostgreSQL. As cópias de segurança automáticas são executadas periodicamente a partir do servidor de base de dados primário, enquanto os registos de transações são continuamente arquivados para o armazenamento de cópia de segurança a partir da réplica de espera. 

A saúde da configuração de alta disponibilidade é continuamente monitorizada e reportada no portal. Os estados de alta disponibilidade redundantes da zona são listados abaixo:

| **Estado** | **Descrição** |
| ------- | ------ |
| <b> Inicialização | No processo de criação de um novo servidor de espera |
| <b> Replicação de dados | Depois de o standby ser criado, está a recuperar as primárias. |
| <b> Saudável | A replicação está em estado estável e saudável. |
| <b> Falhando mais | O servidor de base de dados está em processo de falha no standby. |
| <b> Remoção de standby | No processo de eliminação do servidor de espera. | 
| <b> Não Habilidoso | A zona redundante de alta disponibilidade não está ativada.  |

## <a name="steady-state-operations"></a>Operações de estado estacioná-lo

As aplicações de clientes PostgreSQL estão ligadas ao servidor primário utilizando o nome do servidor DB. As leituras de aplicações são servidas diretamente a partir do servidor primário, enquanto os compromissos e as escritas são confirmados para a aplicação apenas após a persistência dos dados tanto no servidor primário como na réplica de espera. Devido a este requisito adicional de ida e volta, as aplicações podem esperar uma latência elevada para escritas e compromissos. Pode monitorizar a saúde da elevada disponibilidade no portal.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="zona redundante alta disponibilidade - estado estável"::: 

1. Os clientes conectam-se ao servidor flexível e realizam operações de escrita.
2. As alterações são replicadas no local de espera.
3. O primário recebe reconhecimento.
4. As gravações/compromissos são reconhecidas.

## <a name="failover-process---planned-downtimes"></a>Processo de failover - tempos de inatividade planeados

Os eventos de inatividade planeados incluem atualizações de software periódico programadas do Azure e atualizações de versão menores. Quando configuradas em alta disponibilidade, estas operações são aplicadas pela primeira vez na réplica de espera enquanto as aplicações continuam a aceder ao servidor primário. Uma vez que a réplica de espera é atualizada, as ligações do servidor primário são drenadas e uma falha é ativada que ativa a réplica de espera para ser a principal com o mesmo nome do servidor de base de dados. As aplicações do cliente terão de voltar a ligar-se com o mesmo nome do servidor de base de dados ao novo servidor primário e podem retomar as suas operações. Um novo servidor de espera será estabelecido na mesma zona que a antiga primária. 

Para outras operações iniciadas pelo utilizador, tais como o cálculo em escala ou o armazenamento em escala, as alterações são aplicadas primeiro em standby, seguidas pela primária. Atualmente, as ligações não são falhadas no modo de espera e, portanto, incorrem em tempo de inatividade enquanto a operação é realizada no servidor primário.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Redução do tempo de inatividade planeado com janela de manutenção gerida

 Pode agendar as atividades de manutenção iniciadas pela Azure, escolhendo uma janela de 30 minutos num dia da sua preferência, onde se espera que as atividades nas bases de dados sejam baixas. Tarefas de manutenção azure, tais como remendos ou atualizações de versão menores, aconteceriam durante essa janela.  Para servidores flexíveis configurados com elevada disponibilidade, estas atividades de manutenção são realizadas na réplica de espera primeiro e depois são ativadas. As aplicações voltam a ligar-se ao novo servidor primário e retomam as suas operações enquanto é a provisionado um novo standby.

## <a name="failover-process---unplanned-downtimes"></a>Processo de failover - tempos de inatividade não planeados

As interrupções não planeadas incluem bugs de software ou falhas de componentes de infraestrutura que afetam a disponibilidade da base de dados. No caso de a indisponibilidade do servidor ser detetada pelo sistema de monitorização, a replicação da réplica de espera é cortada e a réplica de espera é ativada para ser o servidor de base de dados primário. Os clientes podem voltar a ligar-se ao servidor da base de dados utilizando a mesma cadeia de ligação e retomar as suas operações. Espera-se que o tempo total de insusição de 60-120. No entanto, dependendo da atividade no servidor de base de dados primário no momento da falência, como grandes transações e tempo de recuperação, o failover pode demorar mais tempo.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="zona redundante alta disponibilidade - failover"::: 

1. O servidor de base de dados primário está em baixo e os clientes perdem a conectividade da base de dados. 
2. O servidor de espera é ativado para se tornar o novo servidor primário. O cliente liga-se ao novo servidor primário utilizando a mesma cadeia de ligação. Ter a aplicação do cliente na mesma zona que o servidor de base de dados primário reduz a latência e melhora o desempenho.
3. O servidor de espera é estabelecido na mesma zona que o antigo servidor primário e a replicação de streaming é iniciada. 
4. Uma vez estabelecida a replicação do estado estacionálido, a aplicação do cliente compromete-se e as escritas são reconhecidas após a persistência dos dados em ambos os sites.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo 

Servidores flexíveis configurados com alta disponibilidade, replicam dados em tempo real para o servidor de espera para manter isso atualizado. Quaisquer erros do utilizador no servidor primário - como uma queda acidental de uma tabela ou atualizações incorretas de dados são fielmente replicados na réplica de espera. Por isso, não podes usar o standby para recuperar de tais erros lógicos. Para recuperar de tais erros, tem de efetuar a restauração pontual a partir de backups.  Utilizando a capacidade de restauro pontual do servidor flexível, pode restaurar o tempo antes do erro ocorrer. Para bases de dados configuradas com elevada disponibilidade, um novo servidor de base de dados será restaurado como um servidor flexível de uma única zona com um nome fornecido pelo utilizador. Em seguida, pode exportar o objeto do servidor de base de dados e importá-lo para o seu servidor de base de dados de produção. Da mesma forma, se pretender clonar o servidor de base de dados para fins de teste e desenvolvimento, ou pretender restaurar para qualquer outro fim, pode realizar restauros pontuais.

## <a name="zone-redundant-high-availability---features"></a>Zona redundante alta disponibilidade - características

-   A réplica de standby será implantada numa configuração VM exata, igual à do servidor primário, incluindo vCores, armazenamento, definições de rede (VNET, Firewall), etc.

-   Capacidade de adicionar alta disponibilidade para um servidor de base de dados existente.

-   Capacidade de remover réplica de espera desativando alta disponibilidade.

-   Capacidade de escolher a sua zona de disponibilidade para o seu servidor de base de dados primário.

-   Capacidade de parar, iniciar e reiniciar servidores de bases de dados primários e de espera.

-   As cópias de segurança automáticas são realizadas a partir do servidor de base de dados primário e armazenadas numa zona de armazenamento redundante.

-   Os clientes ligam-se sempre ao servidor de base de dados primário.

-   Capacidade de reiniciar o servidor para captar quaisquer alterações estáticas do parâmetro do servidor.
  
-   As atividades de manutenção periódica, como pequenas atualizações de versão, acontecem primeiro no standby e o serviço é falhado para reduzir o tempo de inatividade.  

## <a name="zone-redundant-high-availability---limitations"></a>Zona redundante alta disponibilidade - limitações

-   A alta disponibilidade não é suportada com um nível de cálculo rebentado.
-   A elevada disponibilidade só é suportada em regiões onde existem várias zonas.
-   Devido à replicação sincronizada para outra zona de disponibilidade, as aplicações podem experimentar escrita elevada e comprometer latência.

-   A réplica de espera não pode ser utilizada para consultas de leitura.

-   Dependendo da atividade no servidor primário no momento do failover, pode demorar até dois minutos ou mais para que a falha se complete.

-   Reiniciar o servidor de base de dados primário para recolher alterações de parâmetros estáticos também reinicia a réplica de espera.

-   Configurar réplicas de leitura adicionais não são suportadas.

-   A configuração das tarefas de gestão iniciadas pelo cliente não pode ser programada durante a janela de manutenção gerida.

-   Os eventos planeados, como dimensionar a computação e o armazenamento, ocorrem primeiro no servidor de reserva e, em seguida, no servidor primário. O serviço não efetuou a ativação pós-falha. 

-  Se a descodificação lógica ou a replicação lógica forem configuradas com um servidor flexível configurado ha, no caso de uma falha no servidor de espera, as ranhuras de replicação lógica não são copiadas para o servidor de espera.  

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba como gerir a [alta disponibilidade](./how-to-manage-high-availability-portal.md)
-   Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)