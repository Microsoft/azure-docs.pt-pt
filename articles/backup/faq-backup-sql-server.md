---
title: FAQ - Backup SQL Server bases de dados em VMs Azure
description: Encontre respostas a perguntas comuns sobre o backup das bases de dados do SQL Server em VMs Azure com Backup Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597087"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>FAQ sobre bases de dados do SQL Server que estão em execução numa cópia de segurança Azure VM

Este artigo responde a perguntas comuns sobre o backup das bases de dados do SQL Server que funcionam em máquinas virtuais Azure (VMs) e usam o serviço [de backup Azure.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Posso usar cópias de segurança Azure para IaaS VM, bem como SQL Server na mesma máquina?

Sim, pode ter backup VM e SQL no mesmo VM. In this case, we internally trigger copy-only full backup on the VM to not truncate the logs.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução volta a tentar ou curar automaticamente as cópias de segurança?

Em algumas circunstâncias, o serviço de backup Azure aciona cópias de segurança corretivas. A cicatrização automática pode acontecer em qualquer uma das seis condições mencionadas abaixo:

- Se a cópia de segurança de registo ou diferencial falhar devido ao Erro de Validação LSN, o próximo registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Se não tiver ocorrido qualquer cópia de segurança antes de um registo ou cópia de segurança diferencial, esse registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Se o mais recente ponto-a-tempo completo for superior a 15 dias, o próximo registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Todos os trabalhos de backup que são cancelados devido a uma atualização de extensão são reaccionados após a atualização ser concluída e a extensão é iniciada.
- Se optar por substituir a base de dados durante o Restauro, a próxima cópia de segurança de log/diferencial falha e é ativada uma cópia de segurança completa.
- Nos casos em que é necessária uma cópia de segurança completa para redefinir as cadeias de registo devido à alteração do modelo de recuperação da base de dados, um completo é acionado automaticamente no próximo horário.

A cicatrização automática como uma capacidade está ativada para todos os utilizadores por padrão; No entanto, caso opte por não o fazer, então execute o seguinte:

- Na instância do Servidor SQL, na pasta *C:\Program Files\Azure Workload Backup\bin,* crie ou edite o ficheiro **ExtensionSettingsOverrides.json.**
- No **ExtensionSettingsOverrides.json**, set *{"EnableAutoHealer": false}* .
- Guarde as suas alterações e feche o ficheiro.
- Na instância do Servidor SQL, abra a **Task Manage** e, em seguida, reinicie o serviço **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Posso controlar quantas cópias de segurança simultâneas funcionam no servidor SQL?

Sim. Pode acelerar a taxa a que a política de backup corre para minimizar o impacto numa instância do SQL Server. Para alterar a definição:

1. Na instância do Servidor SQL, na pasta *C:\Program Files\Azure Workload Backup\bin,* crie o ficheiro *ExtensionSettingsOverrides.json.*
2. No ficheiro *ExtensionSettingsOverrides.json,* altere a definição **Predefinido DeBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
O valor predefinido do DefaultBackupTasksThreshold é **de 20**.

3. Guarde as suas alterações e feche o ficheiro.
4. Na instância do Servidor SQL, **open Task Manager**. Reiniciar o serviço **AzureWLBackupCoordenadorSvc.**<br/> <br/>
 Embora este método ajude se a aplicação de backup estiver a consumir uma grande quantidade de recursos, o SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) é uma forma mais genérica de especificar limites na quantidade de CPU, IO físico e memória que os pedidos de aplicação podem usar.

> [!NOTE]
> No UX ainda pode avançar e agendar o maior número de backups em qualquer momento, no entanto eles serão processados numa janela deslizante de digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso fazer um reforço completo de uma réplica secundária?

De acordo com as limitações do SQL, pode executar cópia apenas cópia de cópia completa na Réplica Secundária; no entanto, o backup completo não é permitido.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger os grupos de disponibilidade no local?

Não. O Azure Backup protege as bases de dados do SQL Server em funcionamento no Azure. Se um grupo de disponibilidade (AG) estiver espalhado entre máquinas Azure e no local, a AG só pode ser protegida se a réplica primária estiver em funcionamento em Azure. Além disso, a Azure Backup protege apenas os nós que funcionam na mesma região de Azure que o cofre dos Serviços de Recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger grupos de disponibilidade em todas as regiões?

O cofre dos Serviços de Recuperação de Backup Azure pode detetar e proteger todos os nós que estão na mesma região que o cofre. Se o seu grupo SQL Server Always On disponibilidade abrange várias regiões Azure, configurar o backup da região que tem o nó principal. O Azure Backup pode detetar e proteger todas as bases de dados do grupo de disponibilidade de acordo com a sua preferência de cópia de segurança. Quando a sua preferência de reserva não é cumprida, os backups falham e recebe o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Os trabalhos de apoio bem sucedidos criam alertas?

Não. Trabalhos de apoio bem sucedidos não geram alertas. Os alertas são enviados apenas para trabalhos de reserva que falham. O comportamento detalhado dos alertas do portal está documentado [aqui.](backup-azure-monitoring-built-in-monitor.md) No entanto, caso esteja interessado tem alertas mesmo para trabalhos bem sucedidos, pode utilizar [a Monitorização utilizando](backup-azure-monitoring-use-azuremonitor.md)o Monitor Azure .

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de reserva programados no menu Backup Jobs?

O menu **Backup Job** só mostrará trabalhos de reserva a pedido. Para utilização programada de utilização de trabalho [Monitorização utilizando o Monitor Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>As futuras bases de dados são adicionadas automaticamente para cópia de segurança?

Sim, pode alcançar esta capacidade com [auto-proteção.](backup-sql-server-database-azure-vms.md#enable-auto-protection)  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu apagar uma base de dados de uma instância autoprotegida, o que acontecerá com as cópias de segurança?

Se uma base de dados for retirada de uma instância autoprotegida, as cópias de dados ainda são tentadas. Isto implica que a base de dados eliminada começa a aparecer como pouco saudável em itens de **backup** e ainda está protegida.

A forma correta de parar de proteger esta base de dados é fazer **Parar backup** com **eliminar dados** nesta base de dados.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se eu parar a operação de reserva de uma base de dados autoprotegida qual será o seu comportamento?

Se parar de fazer backup com dados de **retenção,** não haverá futuras cópias de segurança e os pontos de recuperação existentes permanecerão intactos. A base de dados continuará a ser considerada protegida e mostrada nos itens de **backup**.

Se parar de **fazer backup com dados de eliminação,** não haverá futuras cópias de segurança e os pontos de recuperação existentes também serão eliminados. A base de dados será considerada não protegida e será mostrada sob a instância na Cópia de Segurança configurada. No entanto, ao contrário de outras bases de dados protegidas que podem ser selecionadas manualmente ou que podem ser autoprotegidas, esta base de dados parece acinzentada e não pode ser selecionada. A única forma de proteger esta base de dados é desativar a proteção automática na caso. Pode agora selecionar esta base de dados e configurar a proteção na mesma ou voltar a ativar a proteção automática na instância.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu mudar o nome da base de dados depois de ter sido protegida, qual será o comportamento?

Uma base de dados renomeada é tratada como uma nova base de dados. Assim, o serviço tratará esta situação como se a base de dados não tivesse sido encontrada e com falhas nas cópias de segurança.

Pode selecionar a base de dados, que agora é renomeada e configurar a proteção na sua base. No caso de a proteção automática estar ativada na instância, a base de dados renomeada será automaticamente detetada e protegida.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não posso ver uma base de dados adicional para uma instância autoprotegida?

Uma base de dados que [se adiciona a uma instância autoprotegida](backup-sql-server-database-azure-vms.md#enable-auto-protection) pode não aparecer imediatamente em itens protegidos. Isto porque a descoberta normalmente funciona a cada 8 horas. No entanto, pode descobrir e proteger novas bases de dados imediatamente se executar manualmente uma descoberta selecionando **DBs redescobertos,** como mostra a seguinte imagem:

  ![Descubra manualmente uma base de dados recém-adicionada](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a fazer o back up a uma base de [dados do SQL Server](backup-azure-sql-database.md) que está a funcionar num VM Azure.
