---
title: FAQ - Backup de bases de dados do SQL Server em VMs Azure
description: Encontre respostas a perguntas comuns sobre como fazer backup das bases de dados do SQL Server em VMs Azure com Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6abfdb09fe16272e870fff517359759968417f79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461228"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>FAQ sobre bases de dados do SQL Server que estão a funcionar numa cópia de segurança do Azure VM

Este artigo responde a perguntas comuns sobre o backup das bases de dados do SQL Server que funcionam em máquinas virtuais Azure (VMs) e utiliza o serviço [de Backup Azure.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Posso utilizar o Azure Backup para IaaS VM, bem como o SQL Server na mesma máquina?

Sim, podes ter backup VM e SQL no mesmo VM. Neste caso, desencadeamos internamente cópias de backup completa apenas no VM para não truncar os registos.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução volta a tentar ou a curar automaticamente as cópias de segurança?

Em algumas circunstâncias, o serviço de backup Azure aciona cópias de segurança. A auto-cicatrização pode acontecer em qualquer uma das seis condições abaixo mencionadas:

- Se o registo ou a cópia de segurança diferencial falharem devido ao Erro de Validação do LSN, o próximo registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Se não tiver ocorrido uma cópia de segurança completa antes de um registo ou cópia de segurança diferencial, esse registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Se o último ponto de tempo da cópia de segurança completa for superior a 15 dias, o próximo registo ou cópia de segurança diferencial é convertido para uma cópia de segurança completa.
- Todos os trabalhos de backup que são cancelados devido a uma atualização de extensão são re-desencadeados após a conclusão da atualização e a extensão é iniciada.
- Se optar por substituir a base de dados durante o Restauro, a cópia de segurança de registo/diferencial seguinte falha e é ativada uma cópia de segurança completa.
- Nos casos em que é necessária uma cópia de segurança completa para repor as cadeias de registos devido à alteração do modelo de recuperação da base de dados, um completo é ativado automaticamente no horário seguinte.

A cura automática como capacidade é ativada por padrão para todos os utilizadores. No entanto, se optar por não o fazer, então execute os seguintes passos:

- Na instância do Servidor SQL, na pasta de backup\bin de cópia de segurança da *carga de trabalho do programa C:\Azure,* criar ou editar oExtensionSettingsOverrides.js** no** ficheiro.
- No **ExtensionSettingsOverrides.jsem**, definir *{"EnableAutoHealer": falso}*.
- Guarde as suas alterações e feche o ficheiro.
- Na instância do SQL Server, abra **a Task Manage** e, em seguida, reinicie o serviço **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Posso controlar quantas cópias de segurança simultâneas são executadas no servidor SQL?

Sim. Pode acelerar a velocidade a que a política de backup funciona para minimizar o impacto numa instância do SQL Server. Para alterar a definição:

1. Na instância do Servidor SQL, na pasta *de backup\bin de cópia de trabalho do programa C:\Azure,* crie a *ExtensionSettingsOverrides.jsno* ficheiro.
2. Na *ExtensionSettingsOverrides.jsno* ficheiro, altere a definição **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
O valor predefinido de DefaultBackupTasksThreshold é **de 20**.

3. Guarde as suas alterações e feche o ficheiro.
4. Na instância do SqL Server, abra o **Gestor de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc.**<br/> <br/>
 Embora este método ajude se a aplicação de backup estiver a consumir uma grande quantidade de recursos, [o SqL](/sql/relational-databases/resource-governor/resource-governor) Server Resource Governor é uma forma mais genérica de especificar limites na quantidade de CPU, IO físico e memória que os pedidos de aplicação podem usar.

> [!NOTE]
> No UX ainda pode avançar e agendar o máximo de backups em qualquer momento. No entanto, serão processados numa janela deslizante de, digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso fazer uma cópia de segurança de uma réplica secundária?

According to SQL limitations, you can run Copy Only Full backup on Secondary Replica. No entanto, não é permitido um reforço completo.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger grupos de disponibilidade no local?

N.º O Azure Backup protege as bases de dados do SQL Server em execução no Azure. Se um grupo de disponibilidade (AG) estiver espalhado entre as máquinas Azure e no local, a AG só pode ser protegida se a réplica primária estiver em funcionamento em Azure. Além disso, o Azure Backup protege apenas os nós que funcionam na mesma região de Azure que o cofre dos Serviços de Recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger os grupos de disponibilidade entre regiões?

O cofre dos Serviços de Recuperação de Backup Azure pode detetar e proteger todos os nós que estão na mesma região que o cofre. Se o seu grupo SQL Server Always On disponibilidade abrange várias regiões do Azure, confmede o backup da região que tem o nó primário. O Azure Backup pode detetar e proteger todas as bases de dados do grupo de disponibilidade de acordo com a sua preferência de backup. Quando a sua preferência de backup não é cumprida, os backups falham e você recebe o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>As tarefas de cópia de segurança bem-sucedida criam alertas?

N.º Trabalhos de apoio bem sucedidos não geram alertas. Os alertas são enviados apenas para trabalhos de reserva que falham. O comportamento detalhado dos alertas do portal está documentado [aqui.](backup-azure-monitoring-built-in-monitor.md) No entanto, se estiver interessado em ter alertas mesmo para trabalhos bem sucedidos, pode utilizar [a Monitorização utilizando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de reserva agendados no menu Backup Jobs?

O menu **Backup Job** mostra todas as operações programadas e a pedido, exceto as cópias de segurança agendadas, uma vez que podem ser muito frequentes. Para trabalhos de registo programados, utilize [monitorar utilizando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>As bases de dados futuras são adicionadas automaticamente para cópia de segurança?

Sim, pode conseguir esta capacidade com [proteção automática.](backup-sql-server-database-azure-vms.md#enable-auto-protection)  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu apagar uma base de dados de um caso autoprotegido, o que acontecerá com as cópias de segurança?

Se uma base de dados for retirada de um caso autoprotegido, as cópias de segurança da base de dados ainda são tentadas. Isto implica que a base de dados eliminada começa a aparecer como pouco saudável sob **Itens de Cópia de Segurança** e ainda está protegida.

A forma correta de parar de proteger esta base de dados é fazer **parar a cópia de segurança** com os dados de eliminação nesta base de **dados.**  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se eu parar a operação de backup de uma base de dados autoprotegido, qual será o seu comportamento?

Se **parar a cópia de segurança com os dados de retenção,** não haverá futuras cópias de segurança e os pontos de recuperação existentes permanecerão intactos. A base de dados continuará a ser considerada protegida e mostrada nos **itens de reserva**.

Se **parar a cópia de segurança com os dados de eliminação,** não serão efetuadas cópias de segurança futuras e os pontos de recuperação existentes também serão eliminados. A base de dados será considerada não protegida e mostrada sob a instância na Cópia de Segurança Configure. No entanto, ao contrário de outras bases de dados protegidas que podem ser selecionadas manualmente ou que podem ser autoprotegidas, esta base de dados aparece cinzenta e não pode ser selecionada. A única forma de re-proteger esta base de dados é desativar a proteção automática no caso. Pode agora selecionar esta base de dados e configurar a proteção sobre ela ou voltar a ativar a proteção automática no caso.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu mudar o nome da base de dados depois de ter sido protegida, qual será o comportamento?

Uma base de dados renomeada é tratada como uma nova base de dados. Assim, o serviço tratará esta situação como se a base de dados não tivesse sido encontrada e com falhas nas cópias de segurança.

Pode selecionar a base de dados, que agora é renomeada e configurar a proteção. Se a proteção automática estiver ativada no caso, a base de dados renomeada será automaticamente detetada e protegida.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não posso ver uma base de dados adicional para um caso autoprotegido?

Uma base de dados que [adicione a uma instância autoprotegido](backup-sql-server-database-azure-vms.md#enable-auto-protection) pode não aparecer imediatamente em itens protegidos. Isto porque a descoberta normalmente corre a cada 8 horas. No entanto, pode descobrir e proteger novas bases de dados imediatamente se executar manualmente uma descoberta selecionando **Rediscover DBs,** como mostra a seguinte imagem:

  ![Descubra manualmente uma base de dados recém-adicionada](./media/backup-azure-sql-database/view-newly-added-database.png)
  
## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Posso proteger bases de dados que tenham o TDE (Encriptação de Dados Transparente) ligado e que a base de dados permaneça encriptada durante todo o processo de backup?

Sim, o Azure Backup suporta a cópia de segurança das bases de dados do SQL Server ou do servidor com TDE ativada. A cópia de segurança suporta [o TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) com teclas geridas pela Azure, ou com teclas geridas pelo cliente (BYOK).  A cópia de segurança não executa qualquer encriptação SQL como parte do processo de backup para que a base de dados permaneça encriptada quando estiver cópia de segurança.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer o back up de uma base de dados](backup-azure-sql-database.md) do SQL Server que está a funcionar num VM Azure.
