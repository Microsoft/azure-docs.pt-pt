---
title: Visão geral da zona redundante alta disponibilidade com Base de Dados Azure para MySQL Flexible Server
description: Conheça os conceitos de zona redundante alta disponibilidade com Base de Dados Azure para MySQL Flexible Server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6629beacb5c3edc6fe1d21509051b915c0894479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109697"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceitos de alta disponibilidade na Base de Dados Azure para o MySQL Flexible Server (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

A base de dados Azure para o MySQL Flexible Server (Preview), permite configurar alta disponibilidade com falha automática utilizando a opção de alta disponibilidade redundante da **zona.** Quando implementado numa configuração com redundância de zona, o servidor flexível aprovisiona e gere automaticamente uma réplica de reserva numa zona de disponibilidade diferente. Utilizando a replicação do nível de armazenamento, os dados são **sincronizados replicados** no servidor de espera na zona secundária para permitir a perda de dados zero após uma falha. O failover é totalmente transparente a partir da aplicação do cliente e não requer quaisquer ações do utilizador. O servidor de espera não está disponível para qualquer operação de leitura ou escrita, mas é um standby passivo para permitir uma rápida falha. Os tempos de insí bem-estar variam tipicamente entre 60 e 120 segundos.

A configuração de alta disponibilidade redundante da zona permite a falha automática durante eventos planeados, tais como operações de computação de escala iniciadas pelo utilizador, e eventos não planeados, tais como falhas de hardware e software subjacentes, falhas de rede e até falhas na zona de disponibilidade.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="vista de zona redundante alta disponibilidade":::

## <a name="zone-redundancy-architecture"></a>Arquitetura de redundância de zona

O servidor primário é implantado na região e uma zona de disponibilidade específica. Quando a alta disponibilidade é escolhida, um servidor de réplica de espera com a mesma configuração do servidor primário é automaticamente implantado, incluindo nível de cálculo, tamanho de cálculo, tamanho de armazenamento e configuração de rede. Os dados do registo são sincronizados replicados na réplica de standby para garantir a perda zero de dados em qualquer situação de falha. As cópias de segurança automáticas, tanto as imagens como as cópias de segurança de registo, são realizadas a partir do servidor de base de dados primário. 

A saúde da HA é continuamente monitorizada e reportada na página geral.

Os vários estados de replicação estão listados abaixo:

| **Estado** | **Descrição** |
| :----- | :------ |
| <b>NotEnabled | Zona redundante HA não está habilitado |
| <b>Criação doStandby | No processo de criação de um novo standby |
| <b>ReplicatingData | Depois de o standby ser criado, está a recuperar com o servidor primário. |
| <b>Falha ao fazer | O servidor de base de dados está em processo de falha do primário para o standby. |
| <b>Bom estado de funcionamento | A zona redundante ha está em estado estável e saudável. |
| <b>Remoção doStandby | Com base na ação do utilizador, a réplica de espera está em processo de eliminação.| 

## <a name="advantages"></a>Vantagens

Aqui ficam algumas vantagens para a utilização da funcionalidade HA de redundância de zona: 

- A réplica de standby implanta-se numa configuração VM exata como a das primárias, tais como vCores, armazenamento, definições de rede (VNET, Firewall), etc.
- Capacidade de remover réplica de espera desativando alta disponibilidade.
- As cópias de segurança automáticas são baseadas em instantâneos, realizadas a partir do servidor de base de dados primário e armazenadas numa zona de armazenamento redundante.
- Em caso de falha, a Base de Dados Azure para o servidor flexível MySQL falha automaticamente na réplica de espera se estiver ativada uma elevada disponibilidade. A configuração de alta disponibilidade monitoriza o servidor primário e o ressate de novo.
- Os clientes ligam-se sempre ao servidor de base de dados primário.
- Se houver uma falha de dados ou falha no nó, o reinício é tentado primeiro no mesmo nó. Se isso falhar, a falha automática é acionada.
- Capacidade de reiniciar o servidor para captar quaisquer alterações estáticas do parâmetro do servidor.

## <a name="steady-state-operations"></a>Operações de estado estacioná-lo

As aplicações estão ligadas ao servidor primário utilizando o nome do servidor de base de dados. A informação de réplica de espera não está exposta para acesso direto. Os compromissos e as gravações só são reconhecidos à aplicação depois de os ficheiros de registo serem persistentes tanto no disco do servidor primário como na réplica de standby de forma sincronizada. Devido a este requisito adicional de ida e volta, as aplicações podem esperar uma latência elevada para escritas e compromissos. Pode monitorizar a saúde da elevada disponibilidade no portal.

## <a name="failover-process"></a>Processo de failover 
Para a continuidade do negócio, é necessário ter um processo de failover para eventos planeados e não planeados. 

### <a name="planned-events"></a>Eventos planeados

Os eventos de inatividade planeados incluem atividades programadas pela Azure, tais como atualizações periódicas de software, atualizações de versão menores ou que são iniciadas por clientes como operações de computação em escala e armazenamento em escala. Todas estas alterações são aplicadas pela primeira vez na réplica de espera. Durante esse tempo, as aplicações continuam a aceder ao servidor primário. Uma vez que a réplica de espera é atualizada, as ligações do servidor primário são drenadas, uma falha é ativada que ativa a réplica de espera para ser a principal com o mesmo nome do servidor de base de dados, atualizando o registo DNS. As ligações do cliente estão desligadas e têm de voltar a ligar-se e podem retomar as suas operações. Um novo servidor de espera é estabelecido na mesma zona que a antiga primária. Espera-se que o tempo total de insusição seja de 60-120 s. 

>[!NOTE]
> No caso da operação de escala de cálculo, escalamos o servidor de réplica secundária seguido pelo servidor primário. Não há nenhuma falha envolvida.

### <a name="failover-process---unplanned-events"></a>Processo de failover - eventos não planeados
Os tempos de inatividade de serviço não planeados incluem bugs de software que ou falhas de infraestrutura como computação, rede, falhas de armazenamento ou falhas de energia afetam a disponibilidade da base de dados. Em caso de indisponibilidade da base de dados, a replicação da réplica de espera é cortada e a réplica de espera é ativada para ser a base de dados primária. O DNS é atualizado e os clientes voltam a ligar-se ao servidor de base de dados e retomam as suas operações. Espera-se que o tempo total de insusição seja de 60-120 s. No entanto, dependendo da atividade no servidor de base de dados primário no momento da falência, como grandes transações e tempo de recuperação, o failover pode demorar mais tempo.

## <a name="schedule-maintenance-window"></a>Janela de manutenção de horários 

Os servidores flexíveis oferecem capacidade de agendamento de manutenção onde pode escolher uma janela de 30 minutos num dia da sua preferência durante o qual a manutenção do Azure funciona como remendos ou pequenas atualizações de versão. Para os seus servidores flexíveis configurados com alta disponibilidade, estas atividades de manutenção são realizadas primeiro na réplica de espera. 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo 
Uma vez que o servidor flexível é configurado em alta disponibilidade synchronously replica dados, o servidor de espera está atualizado com o principal. Quaisquer erros do utilizador na primária - como uma queda acidental de uma tabela ou atualizações incorretas são fielmente replicados para o standby. Por isso, não se pode usar em espera para se recuperar de tais falhas lógicas. Para recuperar destes erros lógicos, tem de efetuar o restauro pontual antes do erro ocorrer. Utilizando a capacidade de restauro pontual do servidor flexível, quando restaura a base de dados configurada com alta disponibilidade, um novo servidor de base de dados é restaurado como um novo servidor flexível com um nome fornecido pelo utilizador. Em seguida, pode exportar o objeto do servidor de base de dados e importá-lo para o seu servidor de base de dados de produção. Da mesma forma, se pretender clonar o servidor da base de dados para fins de teste e desenvolvimento, ou pretender restaurar para qualquer outro fim, pode escolher o último ponto de restauro ou um ponto de restauro personalizado. A operação de restauro cria um servidor flexível de uma única zona.

## <a name="mitigate-downtime"></a>Atenuar o tempo de inatividade 
Quando não estiver a utilizar a funcionalidade DE HA de redundância da Zona, ainda precisa de ser capaz de atenuar o tempo de inatividade para a sua aplicação. Os tempos de paragem do serviço de planeamento, tais como patches programados, atualizações de versão menores ou operações iniciadas pelo utilizador podem ser realizados como parte de janelas de manutenção programadas. Tempos de paragem de serviço planeados, tais como patches programados, atualizações de versão menores ou operações iniciadas pelo utilizador, tais como o cálculo de escala incorre em tempo de inatividade. Para mitigar o impacto da aplicação para tarefas de manutenção iniciadas pela Azure, pode optar por croná-las durante o dia da semana e a hora que menos impacta a aplicação. 

Durante eventos de inatividade não planeados, como falha de base de dados ou falha do servidor, o servidor impactado é reiniciado dentro da mesma zona. Embora raro, se toda a zona for afetada, você pode restaurar a base de dados em outra zona dentro da região. 

## <a name="things-to-know-with-zone-redundancy"></a>Coisas a saber com redundância de Zona 

Aqui estão poucas considerações a ter em mente quando utiliza a alta disponibilidade de redundância de zona: 

- A alta disponibilidade **só** pode ser definida durante o tempo de criação de servidor flexível.
- A alta disponibilidade não é suportada no nível de cálculo rebentado.
- Devido à replicação sincronizada para outra zona de disponibilidade, o servidor de base de dados primário pode experimentar a escrita elevada e comprometer a latência.
- A réplica de espera não pode ser utilizada para consultas de leitura.
- Dependendo da atividade no servidor primário no momento do failover, pode demorar até 60-120 segundos ou mais para que a falha seja concluída.
- Reiniciar o servidor de base de dados primário para recolher alterações de parâmetros estáticos também reinicia a réplica de espera.
- Configurar réplicas de leitura para servidores de alta disponibilidade redundantes de zona não é suportado.
- Configurar tarefas de gestão iniciadas pelo cliente não pode ser automatizada durante a janela de manutenção gerida.
- Eventos planeados, como o cálculo de escala e as atualizações de versão menor, acontecem tanto em fase primária como em modo de espera ao mesmo tempo. 


## <a name="next-steps"></a>Passos seguintes

- Conheça a [continuidade do negócio](./concepts-business-continuity.md)
- Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
- Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
