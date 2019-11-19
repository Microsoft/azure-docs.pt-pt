---
title: Proteção de recuperação bare-metal e estado do sistema
description: Use Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de BMR (recuperação bare-metal).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2940ef5b8c0c2a7d751c46209253d4f4dbe6d13f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172253"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Fazer backup do estado do sistema e restaurar para bare-metal com Servidor de Backup do Azure

Servidor de Backup do Azure faz backup do estado do sistema e fornece proteção de BMR (recuperação bare-metal).

* **Backup de estado do sistema**: faz backup dos arquivos do sistema operacional, para que você possa recuperar quando um computador for iniciado, mas os arquivos do sistema e do registro serão perdidos. Um backup de estado do sistema inclui:
  * Membro de domínio: Arquivos de inicialização, banco de dados de registro de classe COM+, registro
  * Controlador de domínio: Windows Server Active Directory (NTDS), arquivos de inicialização, banco de dados de registro de classe COM+, registro, volume do sistema (SYSVOL)
  * Computador que executa os serviços de cluster: metadados do servidor de cluster
  * Computador que executa os serviços de certificados: dados de certificado
* **Backup bare-metal**: faz backup de arquivos do sistema operacional e de todos os dados em volumes críticos (exceto os dados do usuário). Por definição, um backup BMR inclui um backup de estado do sistema. Ele fornece proteção quando um computador não é iniciado e você precisa recuperar tudo.

A tabela a seguir resume o que você pode fazer backup e recuperar. Para obter informações detalhadas sobre as versões de aplicativo que podem ser protegidas com o estado do sistema e a BMR, consulte [o que servidor de backup do Azure backup?](backup-mabs-protection-matrix.md).

|Cópia de segurança|Problema|Recuperar de Servidor de Backup do Azure backup|Recuperar do backup de estado do sistema|METAL|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de arquivo**<br /><br />Backup de dados regular<br /><br />Backup de estado do sistema/BMR|Dados de arquivo perdidos|S|N|N|
|**Dados de arquivo**<br /><br />Servidor de Backup do Azure backup de dados de arquivo<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados de arquivo**<br /><br />Servidor de Backup do Azure backup de dados de arquivo<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de arquivo**<br /><br />Servidor de Backup do Azure backup de dados de arquivo<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados perdidos)|S|Não|Sim (BMR, seguida de recuperação regular de dados de arquivo de backup)|
|**Dados do SharePoint**:<br /><br />Servidor de Backup do Azure backup de dados do farm<br /><br />Backup de estado do sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**:<br /><br />Servidor de Backup do Azure backup de dados do farm<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|S|S|
|**Dados do SharePoint**:<br /><br />Servidor de Backup do Azure backup de dados do farm<br /><br />Backup de estado do sistema/BMR|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Servidor de Backup do Azure backup de host ou convidado do Hyper-V<br /><br />BMR/backup de estado do sistema do host|VM perdida|S|N|N|
|Hyper-V<br /><br />Servidor de Backup do Azure backup de host ou convidado do Hyper-V<br /><br />BMR/backup de estado do sistema do host|Sistema operacional perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Servidor de Backup do Azure backup de host ou convidado do Hyper-V<br /><br />BMR/backup de estado do sistema do host|Host Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />Servidor de Backup do Azure backup de host ou convidado do Hyper-V<br /><br />BMR/backup de estado do sistema do host|Host Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação de Servidor de Backup do Azure regular|
|SQL Server/Exchange<br /><br />Backup do aplicativo Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Dados de aplicativo perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Backup do aplicativo Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Sistema operacional perdido ou danificado|N|Y|S|
|SQL Server/Exchange<br /><br />Backup do aplicativo Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (banco de dados/logs de transações intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup do aplicativo Servidor de Backup do Azure<br /><br />Backup de estado do sistema/BMR|Servidor perdido (banco de dados/logs de transações perdidos)|N|N|S<br /><br />Recuperação BMR, seguida de recuperação regular de Servidor de Backup do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona o backup do estado do sistema

Quando um backup de estado do sistema é executado, o servidor de backup se comunica com Backup do Windows Server para solicitar um backup do estado do sistema do servidor. Por padrão, o servidor de backup e Backup do Windows Server usam a unidade que tem o espaço livre mais disponível. As informações sobre essa unidade são salvas no arquivo PSDataSourceConfig. xml. Esta é a unidade que o Backup do Windows Server usa para backups.

Você pode personalizar a unidade que o servidor de backup usa para o backup de estado do sistema. No servidor protegido, vá para C:\Arquivos de Programas\microsoft Data Protection Manager\MABS\Datasources. Abra o arquivo PSDataSourceConfig. xml para edição. Altere o valor de\> \<FilesToProtect para a letra da unidade. Guarde e feche o ficheiro. Se houver um grupo de proteção definido para proteger o estado do sistema do computador, execute uma verificação de consistência. Se um alerta for gerado, selecione **Modificar Grupo de proteção** no alerta e conclua o assistente. Em seguida, execute outra verificação de consistência.

Observe que, se o servidor de proteção estiver em um cluster, será possível que uma unidade de cluster seja selecionada como a unidade com mais espaço livre. Se essa propriedade da unidade tiver sido alternada para outro nó e um backup de estado do sistema for executado, a unidade não estará disponível e o backup falhará. Nesse cenário, modifique PSDataSourceConfig. xml para apontar para uma unidade local.

Em seguida, Backup do Windows Server cria uma pasta chamada WindowsImageBackup na raiz da pasta Restore. Conforme Backup do Windows Server cria o backup, todos os dados são colocados nessa pasta. Quando o backup é concluído, o arquivo é transferido para o computador do servidor de backup. Tenha em atenção as seguintes informações:

* Essa pasta e seu conteúdo não são limpos quando o backup ou a transferência é concluído. A melhor maneira de considerar isso é que o espaço está sendo reservado para a próxima vez que um backup for concluído.
* A pasta é criada toda vez que um backup é feito. O carimbo de data e hora reflete a hora do último backup de estado do sistema.

## <a name="bmr-backup"></a>Backup de BMR

Para BMR (incluindo um backup de estado do sistema), o trabalho de backup é salvo diretamente em um compartilhamento no computador do servidor de backup. Ele não é salvo em uma pasta no servidor protegido.

O servidor de backup chama Backup do Windows Server e compartilha o volume de réplica para esse backup de BMR. Nesse caso, ele não informa Backup do Windows Server usar a unidade com mais espaço livre. Em vez disso, ele usa o compartilhamento que foi criado para o trabalho.

Quando o backup é concluído, o arquivo é transferido para o computador do servidor de backup. Os logs são armazenados em C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* O BMR não tem suporte para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operacional cliente.

* Não é possível proteger o BMR e o estado do sistema para o mesmo computador em grupos de proteção diferentes.

* Um computador do servidor de backup não pode se proteger para BMR.

* A proteção de curto prazo em fita (disco para fita ou D2T) não tem suporte para BMR. Há suporte para o armazenamento de longo prazo em fita (disco para disco para fita ou D2D2T).

* Para proteção BMR, Backup do Windows Server deve ser instalado no computador protegido.

* Para proteção de BMR, ao contrário da proteção de estado do sistema, o servidor de backup não tem requisitos de espaço no computador protegido. Backup do Windows Server transfere backups diretamente para o computador do servidor de backup. O trabalho de transferência de backup não aparece no modo de exibição **trabalhos** do servidor de backup.

* O servidor de backup reserva 30 GB de espaço no volume da réplica para BMR. Você pode alterar isso na página **alocação de disco** no Assistente para modificar grupo de proteção ou usando os cmdlets do PowerShell Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation. No volume de pontos de recuperação, a proteção BMR requer cerca de 6 GB para uma retenção de cinco dias.
  * Observe que você não pode reduzir o tamanho do volume de réplica para menos de 15 GB.
  * O servidor de backup não calcula o tamanho da fonte de dados BMR. Ele assume 30 GB para todos os servidores. Altere o valor com base no tamanho dos backups de BMR que você espera em seu ambiente. O tamanho de um backup BMR pode ser calculado aproximadamente como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de inicialização + volume do sistema + volume que hospeda dados de estado do sistema, como Active Directory.

* Se você alterar a proteção de estado do sistema para a proteção BMR, a proteção BMR exigirá menos espaço no *volume de pontos de recuperação*. No entanto, o espaço extra no volume não é recuperado. Você pode reduzir manualmente o tamanho do volume na página **modificar alocação do disco** do assistente para modificar grupo de proteção ou usando os cmdlets do PowerShell Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation.

    Se você alterar a proteção de estado do sistema para a proteção BMR, a proteção BMR exigirá mais espaço no *volume da réplica*. O volume é estendido automaticamente. Se você quiser alterar as alocações de espaço padrão, use o cmdlet Modify-DiskAllocation do PowerShell.

* Se você alterar da proteção BMR para a proteção de estado do sistema, precisará de mais espaço no volume de pontos de recuperação. O servidor de backup pode tentar aumentar o volume automaticamente. Se não houver espaço suficiente no pool de armazenamento, ocorrerá um erro.

    Se você alterar da proteção BMR para a proteção de estado do sistema, precisará de espaço no computador protegido. Isso ocorre porque a proteção de estado do sistema primeiro grava a réplica no computador local e, em seguida, a transfere para o computador do servidor de backup.

## <a name="before-you-begin"></a>Antes de começar

1. **Implantar servidor de backup do Azure**. Verifique se o servidor de backup está implantado corretamente. Para obter mais informações, consulte:
    * [Requisitos do sistema para Servidor de Backup do Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do servidor de backup](backup-mabs-protection-matrix.md)

2. **Configure o armazenamento**. Você pode armazenar dados de backup em disco, em fita e na nuvem com o Azure. Para obter mais informações, consulte [preparar o armazenamento de dados](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3. **Configure o agente de proteção**. Instale o agente de proteção no computador que você deseja fazer backup. Para obter mais informações, consulte [implantar o agente de proteção do DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Fazer backup do estado do sistema e bare-metal

Configure um grupo de proteção conforme descrito em [implantar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Observe que você não pode proteger o BMR e o estado do sistema para o mesmo computador em grupos diferentes. Além disso, quando você seleciona BMR, o estado do sistema é habilitado automaticamente.

1. Para abrir o assistente para criar novo grupo de proteção na Console do Administrador servidor de backup, selecione **proteção** > **ações** > **Criar grupo de proteção**.

2. Na página **Selecionar tipo de grupo de proteção** , selecione **servidores**e, em seguida, selecione **Avançar**.

3. Na página **selecionar membros do grupo** , expanda o computador e selecione **BMR** ou o **estado do sistema**.

    Lembre-se de que você não pode proteger a BMR e o estado do sistema para o mesmo computador em grupos diferentes. Além disso, quando você seleciona BMR, o estado do sistema é habilitado automaticamente. Para obter mais informações, consulte [implantar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4. Na página **selecionar método de proteção de dados** , selecione como você deseja lidar com o backup de curto e longo prazo. O backup de curto prazo é sempre para o disco primeiro, com a opção de fazer backup do disco para a nuvem do Azure usando o backup do Azure (curto ou longo prazo). Uma alternativa para o backup de longo prazo na nuvem é configurar o backup de longo prazo para um dispositivo de fita autônomo ou uma biblioteca de fitas que esteja conectada ao servidor de backup.

5. Na página **Selecionar objetivos de curto prazo** , selecione como você deseja fazer backup em armazenamento de curto prazo em disco:
    1. Para **período de retenção**, selecione por quanto tempo deseja manter os dados em disco.
    2. Para **frequência de sincronização**, selecione com que frequência você deseja executar um backup incremental em disco. Se você não quiser definir um intervalo de backup, poderá marcar a opção **logo antes de um ponto de recuperação** . O servidor de backup executará um backup completo expresso antes de cada ponto de recuperação ser agendado.

6. Se você quiser armazenar dados em fita para armazenamento de longo prazo, na página **especificar objetivos de longo prazo** , selecione por quanto tempo deseja manter os dados da fita (1-99 anos).
    1. Para **frequência de backup**, selecione com que frequência o backup em fita deve ser executado. A frequência é baseada no período de retenção que você selecionou:
        * Quando o período de retenção é de 1-99 anos, você pode selecionar os backups para ocorrer diariamente, semanalmente, quinzenalmente, mensal, trimestral, semestral ou anualmente.
        * Quando o período de retenção é de 1-11 meses, você pode selecionar os backups para que ocorram diariamente, semanalmente, quinzenais ou mensais.
        * Quando o período de retenção é de 1-4 semanas, você pode selecionar os backups para que eles ocorram diariamente ou semanalmente.

    2. Na página **selecionar detalhes de fita e biblioteca** , selecione a fita e a biblioteca a serem usadas e se os dados devem ser compactados e criptografados.

7. Na página **rever alocação do disco** , examine o espaço em disco do pool de armazenamento que está alocado para o grupo de proteção.

    1. **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup.
    2. **Espaço em disco a ser provisionado no servidor de backup do Azure** é o espaço que o servidor de backup recomenda para o grupo de proteção. O servidor de backup escolhe o volume de backup ideal com base nas configurações. No entanto, você pode editar as opções de volume de backup em **detalhes de alocação de disco**.
    3. Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferencial. Suas edições alteram os valores para **armazenamento total** e **armazenamento livre** no painel de **armazenamento em disco disponível** . Espaço subprovisionado é a quantidade de armazenamento que o servidor de backup sugere que você adicione ao volume para garantir backups suaves.

8. Na página **escolher método de criação de réplica** , selecione como você deseja manipular a replicação de dados completa inicial. Se você optar por replicar pela rede, recomendamos que você escolha um horário de pico. Para grandes quantidades de dados ou condições de rede menores que o ideal, considere a possibilidade de replicar os dados offline usando mídia removível.

9. Na página **escolher opções de verificação de consistência** , selecione como você deseja automatizar as verificações de consistência. Você pode optar por executar uma verificação somente quando os dados da réplica se tornarem inconsistentes ou em um agendamento. Se você não quiser configurar a verificação de consistência automática, poderá executar uma verificação manual a qualquer momento. Para executar uma verificação manual, na área **proteção** do console do administrador do servidor de backup, clique com o botão direito do mouse no grupo de proteção e selecione **executar verificação de consistência**.

10. Se você tiver optado por fazer backup na nuvem usando o backup do Azure, na página **especificar dados de proteção online** , certifique-se de selecionar as cargas de trabalho que deseja fazer backup no Azure.

11. Na página **especificar agendamento de backup online** , selecione com que frequência os backups incrementais no Azure ocorrerão. Você pode agendar backups para serem executados todos os dias, semana, mês e ano e selecionar a data e a hora em que devem ser executados. Os backups podem ocorrer até duas vezes por dia. Cada vez que um backup é executado, um ponto de recuperação de dados é criado no Azure por meio da cópia dos dados de backup armazenados no disco do servidor de backup.

12. Na página **especificar política de retenção online** , selecione como os pontos de recuperação criados a partir dos backups diários, semanais, mensais e anuais são mantidos no Azure.

13. Na página **escolher replicação online** , selecione como ocorre a replicação inicial completa dos dados. Você pode replicar pela rede ou fazer um backup offline (propagação offline). O backup offline usa o recurso de importação do Azure. Para obter mais informações, consulte [fluxo de trabalho de backup offline no backup do Azure](backup-azure-backup-import-export.md).

14. Na página **Resumo** , examine as configurações. Depois de selecionar **Criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados for concluída, na página **status** , o status do grupo de proteção será **OK**. Em seguida, o backup ocorre de acordo com as configurações do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou BMR

Você pode recuperar a BMR ou o estado do sistema para um local de rede. Se você tiver feito backup da BMR, use o ambiente de recuperação do Windows (WinRE) para iniciar o sistema e conectá-lo à rede. Em seguida, use Backup do Windows Server para recuperar do local de rede. Se você tiver feito backup do estado do sistema, basta usar Backup do Windows Server para recuperar a partir do local de rede.

### <a name="restore-bmr"></a>Restaurar BMR

Execute a recuperação no computador do servidor de backup:

1. No painel **recuperação** , localize o computador que deseja recuperar e selecione **recuperação bare-metal**.

2. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e a hora para o ponto de recuperação que você deseja usar.

3. Na página **Selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede.**

4. Na página **especificar destino** , selecione onde você deseja copiar os dados. Lembre-se de que o destino selecionado precisa ter espaço suficiente. Recomendamos que você crie uma nova pasta.

5. Na página **especificar opções de recuperação** , selecione as configurações de segurança a serem aplicadas. Em seguida, selecione se deseja usar instantâneos de hardware baseados em SAN (rede de área de armazenamento) para uma recuperação mais rápida. (Essa é uma opção somente se você tiver uma SAN com essa funcionalidade disponível e a capacidade de criar e dividir um clone para torná-lo gravável. Além disso, o computador protegido e o computador do servidor de backup devem estar conectados à mesma rede.)

6. Configurar opções de notificação. Na página **confirmação** , selecione **recuperar**.

Configurar o local de compartilhamento:

1. No local de restauração, vá para a pasta que tem o backup.

2. Compartilhe a pasta que é um nível acima de WindowsImageBackup para que a raiz da pasta compartilhada seja a pasta WindowsImageBackup. Se você não fizer isso, a restauração não encontrará o backup. Para se conectar usando o ambiente de recuperação do Windows (WinRE), você precisa de um compartilhamento que pode ser acessado no WinRE com o endereço IP e as credenciais corretos.

Restaure o sistema:

1. Inicie o computador no qual você deseja restaurar a imagem usando o DVD do Windows para o sistema que você está restaurando.

2. Na primeira página, verifique as configurações de idioma e localidade. Na página **instalar** , selecione **reparar o computador**.

3. Na página **Opções de recuperação do sistema** , selecione **restaurar o computador usando uma imagem do sistema que você criou anteriormente**.

4. Na página **selecionar um backup de imagem do sistema** , selecione **selecionar uma imagem do sistema** > **avançado** > **Pesquisar por uma imagem do sistema na rede**. Se aparecer um aviso, selecione **Sim**. Vá para o caminho de compartilhamento, insira as credenciais e, em seguida, selecione o ponto de recuperação. Isso verifica os backups específicos que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que você deseja usar.

5. Na página **escolher como restaurar o backup** , selecione **Formatar e reparticionar discos**. Na página seguinte, verifique as configurações.

6. Para iniciar a restauração, selecione **concluir**. Uma reinicialização é necessária.

### <a name="restore-system-state"></a>Restaurar Estado do sistema

Executar recuperação no servidor de backup:

1. No painel **recuperação** , localize o computador que você deseja recuperar e selecione **recuperação bare-metal**.

2. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e a hora para o ponto de recuperação que você deseja usar.

3. Na página **Selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede**.

4. Na página **especificar destino** , selecione onde você deseja copiar os dados. Lembre-se de que o destino selecionado precisa de espaço suficiente. Recomendamos que você crie uma nova pasta.

5. Na página **especificar opções de recuperação** , selecione as configurações de segurança a serem aplicadas. Em seguida, selecione se deseja usar instantâneos de hardware baseados em SAN para uma recuperação mais rápida. (Essa é uma opção somente se você tiver uma SAN com essa funcionalidade e a capacidade de criar e dividir um clone para torná-lo gravável. Além disso, o computador protegido e o servidor do servidor de backup devem estar conectados à mesma rede.)

6. Configurar opções de notificação. Na página **confirmação** , selecione **recuperar**.

Executar Backup do Windows Server:

1. Selecione **ações** > **recuperar** > **este servidor** > **Avançar**.

2. Selecione **outro servidor**, selecione a página **especificar tipo de local** e, em seguida, selecione **pasta compartilhada remota**. Insira o caminho para a pasta que contém o ponto de recuperação.

3. Na página **Selecionar tipo de recuperação** , selecione **estado do sistema**.

4. Na página **selecionar local para recuperação de estado do sistema** , selecione **local original**.

5. Na página **confirmação** , selecione **recuperar**. Após a restauração, reinicie o servidor.

6. Você também pode executar a restauração do estado do sistema em um prompt de comando. Para fazer isso, inicie Backup do Windows Server no computador que você deseja recuperar. Para obter o identificador de versão, em um prompt de comando, digite: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Use o identificador de versão para iniciar a restauração do estado do sistema. No prompt de comando, digite: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Confirme que você deseja iniciar a recuperação. Você pode ver o processo na janela do prompt de comando. Um log de restauração é criado. Após a restauração, reinicie o servidor.
