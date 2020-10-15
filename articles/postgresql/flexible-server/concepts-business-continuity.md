---
title: Visão geral da continuidade do negócio com base de dados Azure para PostgreSQL - Servidor Flexível
description: Conheça os conceitos de continuidade de negócios com base de dados Azure para PostgreSQL - Servidor Flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1854b8cf65b8747e07fb3d25413432c108b29071
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096713"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Visão geral da continuidade do negócio com base de dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

**A continuidade do negócio** na Base de Dados Azure para PostgreSQL - Servidor Flexível refere-se aos mecanismos, políticas e procedimentos que permitem ao seu negócio continuar a operar face a perturbações, particularmente à sua infraestrutura de computação. Na maioria dos casos, o servidor flexível lidará com os eventos disruptivos que podem ocorrer no ambiente da nuvem e manter as suas aplicações e processos de negócio em funcionamento. No entanto, existem alguns eventos que não podem ser tratados automaticamente, tais como:

- O utilizador elimina ou atualiza acidentalmente uma linha numa tabela.
- O terramoto causa uma falha de energia e desativa temporariamente um datacenter ou uma zona de disponibilidade.
- Patching de base de dados necessário para corrigir um problema de erro ou segurança.

O servidor flexível fornece funcionalidades que protegem os dados e atenuam o tempo de inatividade das bases de dados críticas da sua missão em caso de eventos de inatividade planeados e não planeados. Construído em cima da infraestrutura Azure que já oferece resiliência e disponibilidade robustas, o servidor flexível tem funcionalidades de continuidade de negócios que fornecem proteção adicional de falhas, requisitos de tempo de recuperação de endereços e redução da exposição à perda de dados. Ao arquitetar as suas aplicações, deve considerar a tolerância ao tempo de inatividade - que é o objetivo do tempo de recuperação (RTO) e a exposição à perda de dados - que é o objetivo do ponto de recuperação (RPO). Por exemplo, a sua base de dados crítica de negócios requer requisitos de tempo de a prazo muito mais rigorosos em comparação com uma base de dados de teste.  

> [!IMPORTANT]
> O acordo de nível de serviço de uptime % (SLA) não é oferecido durante a pré-visualização. 

A tabela abaixo ilustra as funcionalidades que o servidor flexível oferece.


| **Funcionalidade** | **Descrição** | **Considerações** |
| ---------- | ----------- | ------------ |
| **Backups automáticos** | O servidor flexível realiza automaticamente cópias de segurança diárias dos ficheiros da sua base de dados e faz cópias de segurança contínuas dos registos de transações. As cópias de segurança podem ser mantidas de 7 dias até 35 dias. Poderá restaurar o servidor da base de dados a qualquer ponto no tempo dentro do período de retenção de cópias de segurança. O RTO depende do tamanho dos dados para restaurar + o tempo para realizar a recuperação do registo. Pode ir de poucos minutos até 12 horas. Para mais detalhes, consulte [Conceitos - Backup e Restaurar.](./concepts-backup-restore.md) |Os dados de reserva permanecem na região. |
| **Zona redundante alta disponibilidade** | O servidor flexível pode ser implantado com configuração de alta disponibilidade redundante (HA) onde os servidores primários e de espera são implantados em duas zonas de disponibilidade diferentes dentro de uma região. Esta configuração HA protege as suas bases de dados de falhas ao nível da zona e também ajuda na redução do tempo de inatividade da aplicação durante eventos de inatividade planeados e não planeados. Os dados do servidor primário são replicados para a réplica de espera no modo sincronizado. Em caso de qualquer interrupção no servidor primário, o servidor é automaticamente falhado na réplica de espera. Espera-se que o RTO, na maioria dos casos, seja entre os 60 e os 120 anos. Espera-se que a RPO seja zero (sem perda de dados). Para mais informações, consulte [Conceitos - Alta disponibilidade.](./concepts-high-availability.md) | Suportado em níveis de computação otimizados de propósito geral e memória. Disponível apenas em regiões onde existem várias zonas. |
| **Discos geridos por prémios** | Os ficheiros de base de dados são armazenados num armazenamento altamente durável e fiável gerido por prémios. Isto fornece redundância de dados com três cópias de réplica armazenadas dentro de uma zona de disponibilidade com capacidades automáticas de recuperação de dados. Para obter mais informações, consulte [a documentação dos discos geridos.](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) | Dados armazenados dentro de uma zona de disponibilidade. |
| **Backup redundante da zona** | As cópias de segurança flexíveis do servidor são armazenadas automaticamente e de forma segura numa zona de armazenamento redundante dentro de uma região. Durante uma falha de nível de zona onde o seu servidor é provisionado, e se o seu servidor não estiver configurado com redundância de zona, ainda pode restaurar a sua base de dados utilizando o último ponto de restauro numa zona diferente. Para mais informações, consulte [Conceitos - Backup e Restaurar.](./concepts-backup-restore.md)| Apenas aplicável em regiões onde existem várias zonas.|


## <a name="planned-downtime-events"></a>Eventos de inatividade planeados
Abaixo estão alguns cenários de manutenção planeados. Estes eventos normalmente incorrem até poucos minutos de tempo de inatividade, e sem perda de dados.

| **Cenário** | **Processo**|
| ------------------- | ----------- | 
| <b>Escala de cálculo (iniciado pelo utilizador)| Durante a operação de escala de cálculo, os postos de controlo ativos são autorizados a completar, as ligações do cliente são drenadas, quaisquer transações não comprometidas são canceladas, o armazenamento é desligado e, em seguida, é desligado. Um novo servidor flexível com o mesmo nome do servidor de base de dados é a provisionado com a configuração de computação em escala. O armazenamento é então anexado ao novo servidor e a base de dados é iniciada que realiza a recuperação se necessário antes de aceitar ligações ao cliente. |
| <b>Ampliação do armazenamento (iniciado pelo utilizador) | Quando uma operação de armazenamento de escalonamento é iniciada, os pontos de verificação ativos são autorizados a ser concluídos, as ligações do cliente são drenadas, quaisquer transações não comprometidas são canceladas e, em seguida, é desligado. O armazenamento é dimensionado para o tamanho desejado e, em seguida, ligado ao novo servidor. Se necessário, é realizada uma recuperação antes de aceitar ligações com o cliente. Note que a redução do tamanho do armazenamento não é suportada. |
| <b>Nova implementação de software (iniciado pelo Azure) | As novas funcionalidades de lançamento ou correções de bugs acontecem automaticamente como parte da manutenção planeada do serviço, e pode agendar quando essas atividades acontecerem. Para mais informações, consulte o seu [portal.](https://aka.ms/servicehealthpm) | 
| <b>Upgrades de versão menores (iniciado pelo Azure) | A Azure Database for PostgreSQL remenda automaticamente os servidores de base de dados para a versão menor determinada pelo Azure. Acontece como parte da manutenção planeada do serviço. O servidor de base de dados é automaticamente reiniciado com a nova versão menor. Para mais informações, consulte [a documentação.](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification) Também pode verificar o seu [portal.](https://aka.ms/servicehealthpm)| 

 Quando o servidor flexível é configurado com **zona redundante de alta disponibilidade,** o servidor flexível executa primeiro as operações de escala e manutenção no servidor de espera. Para mais informações, consulte [Conceitos - Alta disponibilidade.](./concepts-high-availability.md)

##  <a name="unplanned-downtime-mitigation"></a>Mitigação não planeada do tempo de inatividade

Os tempos de inatividade não planeados podem ocorrer como resultado de perturbações imprevistas, tais como falhas subjacentes ao hardware, problemas de rede e bugs de software. Se o servidor de base de dados configurado com alta disponibilidade diminuir inesperadamente, então a réplica de espera é ativada e os clientes podem retomar as suas operações. Se não estiver configurado com alta disponibilidade (HA), então se a tentativa de reinicialização falhar, um novo servidor de base de dados é automaticamente provisionado. Embora não seja possível evitar um tempo de inatividade não planeado, o servidor flexível ajuda a atenuar o tempo de inatividade realizando automaticamente operações de recuperação sem exigir intervenção humana. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo de inatividade não planeado: cenários de avaria e recuperação de serviços
Abaixo estão alguns cenários de falha não planeados e o processo de recuperação. 

| **Cenário** | **Processo de recuperação [não-HA]** | **Processo de recuperação [HA]** |
| ---------- | ---------- | ------- |
| <B>Falha do servidor de base de dados | Se o servidor de base de dados estiver em baixo, o Azure tentará reiniciar o servidor de base de dados. Se isso falhar, o servidor de base de dados será reiniciado noutro nó físico.  <br /> <br /> O tempo de recuperação (RTO) depende de vários fatores, incluindo a atividade no momento da falha, como a grande transação e o volume de recuperação a realizar durante o processo de arranque do servidor de base de dados. <br /> <br /> As aplicações que utilizam as bases de dados PostgreSQL precisam de ser construídas de forma a detetarem e recaírem ligações e transações falhadas. | Se a falha do servidor de base de dados for detetada, o servidor é falhado no servidor de espera, reduzindo assim o tempo de inatividade. Para mais informações, consulte [a página de conceitos HA.](./concepts-high-availability.md) Espera-se que o RTO seja de 60-120, com zero perdas de dados. |
| <B>Falha de armazenamento | As aplicações não vêem qualquer impacto para quaisquer questões relacionadas com o armazenamento, tais como uma falha no disco ou uma corrupção de bloco físico. Como os dados são armazenados em três cópias, a cópia dos dados é servida pelo armazenamento sobrevivente. O bloco de dados corrompido é reparado automaticamente e uma nova cópia dos dados é automaticamente criada. | Para quaisquer erros raros e não recuperáveis, como todo o armazenamento, é inacessível, o servidor flexível é falhado na réplica de espera para reduzir o tempo de inatividade. Para mais informações, consulte [a página de conceitos HA.](./concepts-high-availability.md) |
| <b> Erros lógicos/de utilizador | Para se recuperar de erros do utilizador, tais como tabelas acidentalmente largadas ou dados incorretamente atualizados, tem de efetuar uma [recuperação pontual](https://docs.microsoft.com/azure/postgresql/concepts-backup) (PITR). Durante a operação de restauro, especifique o ponto de restauro personalizado, que é a hora imediatamente antes do erro ocorrer.<br> <br>  Se pretender restaurar apenas um subconjunto de bases de dados ou tabelas específicas em vez de todas as bases de dados no servidor de bases de dados, pode restaurar o servidor de base de dados num novo caso, exportar a tabela(s) através [de pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html), e depois utilizar [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) para restaurar essas tabelas na sua base de dados. | Estes erros do utilizador não estão protegidos com elevada disponibilidade, uma vez que todas as alterações são replicadas na réplica de standby sincronizada. Tem que fazer restauro pontual para recuperar de tais erros. |
| <b> Falha na zona de disponibilidade | Para recuperar de uma falha de nível de zona, pode executar a restauração pontual usando a cópia de segurança e escolhendo um ponto de restauração personalizado com a última hora para restaurar os dados mais recentes. Um novo servidor flexível será implantado noutra zona sem impacto. O tempo de restauro depende da cópia de segurança anterior e do volume de registos de transações para recuperar. | O servidor flexível é automaticamente falhado no servidor de espera dentro de 60-120s com perda de dados zero. Para mais informações, consulte [a página de conceitos HA.](./concepts-high-availability.md) | 
| <b> Falha na região | Réplica de leitura transversal e geo-restauro de funcionalidades de backup ainda não são suportadas na pré-visualização. | |


> [!IMPORTANT]
> Os servidores eliminados **não podem**   ser restaurados. Se eliminar o servidor, todas as bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Utilize [o bloqueio de recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)para evitar a   eliminação acidental do seu servidor.


## <a name="next-steps"></a>Passos seguintes

-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
-   Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
