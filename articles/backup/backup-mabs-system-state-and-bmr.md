---
title: Sistema e proteção de recuperação de metal nu
description: Utilize o Servidor de Backup Azure para fazer backup o seu estado de sistema e fornecer proteção de recuperação de metal (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 0e89b149fe8b06bdd70c72aa442f50125c5e3786
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025508"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Back up system state and restore to bare metal with Azure Backup Server

O Azure Backup Server apoia o estado do sistema e fornece proteção de recuperação de metal nu (BMR).

* **Backup do estado do sistema**: Backup ficheiros do sistema operativo, para que possa recuperar quando um computador começa, mas os ficheiros do sistema e o registo perdem-se. Uma cópia de segurança do estado do sistema inclui:
  * Membro do domínio: Ficheiros de arranque, base de dados de registo de classes COM+, registo
  * Controlador de domínio: Diretório Ativo do Servidor do Windows (NTDS), ficheiros de boot, base de dados de registo de classe COM+, registo, volume do sistema (SYSVOL)
  * Computador que executa serviços de cluster: Metadados de servidores cluster
  * Computador que executa serviços de certificado: Dados de certificados
* **Backup bare-metal**: Cópia de segurança dos ficheiros do sistema operativo e de todos os dados em volumes críticos (exceto dados do utilizador). Por definição, uma cópia de segurança BMR inclui uma cópia de segurança do estado do sistema. Fornece proteção quando um computador não começa e tem que recuperar tudo.

A tabela que se segue resume o que pode recuar e recuperar. Para obter informações detalhadas sobre versões de aplicações que podem ser protegidas com o estado do sistema e bMR, consulte [o que o Azure Backup Server faz](backup-mabs-protection-matrix.md)cópia de segurança?

|Backup|Problema|Recuperar da cópia de segurança do Servidor de Backup Azure|Recuperar da cópia de segurança do estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de ficheiros**<br /><br />Backup regular de dados<br /><br />Backup do estado BMR/sistema|Dados de ficheiros perdidos|S|N|N|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Backup do estado BMR/sistema|Sistema operativo perdido ou danificado|N|S|S|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Backup do estado BMR/sistema|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Backup do estado BMR/sistema|Servidor perdido (volumes de dados perdidos)|S|Não|Sim (BMR, seguido de recuperação regular de dados de ficheiros de back-up)|
|**Dados do SharePoint**:<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Backup do estado BMR/sistema|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**:<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Backup do estado BMR/sistema|Sistema operativo perdido ou danificado|N|S|S|
|**Dados do SharePoint**:<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Backup do estado BMR/sistema|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />BMR/sistema de reserva do estado do hospedeiro|VM perdido|S|N|N|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />BMR/sistema de reserva do estado do hospedeiro|Sistema operativo perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />BMR/sistema de reserva do estado do hospedeiro|Hospedeiro hiper-V perdido (VMs intactos)|N|N|S|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />BMR/sistema de reserva do estado do hospedeiro|Hospedeiro hiper-V perdido (VMs perdidos)|N|N|S<br /><br />BMR, seguido de recuperação regular do Servidor de Backup Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Backup do estado BMR/sistema|Dados perdidos da aplicação|S|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Backup do estado BMR/sistema|Sistema operativo perdido ou danificado|N|Y|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Backup do estado BMR/sistema|Servidor perdido (registos de base de dados/transações intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Backup do estado BMR/sistema|Servidor perdido (registos de base de dados/transações perdidos)|N|N|S<br /><br />Recuperação bMR, seguida de recuperação regular do Servidor de Backup Azure|

## <a name="how-system-state-backup-works"></a>Como funciona a cópia de segurança do estado do sistema

Quando uma cópia de segurança do estado do sistema é executado, o Backup Server comunica com o Windows Server Backup para solicitar uma cópia de segurança do estado do sistema do servidor. Por predefinição, o Backup Server e o Windows Server Backup utilizam a unidade que possui o espaço livre mais disponível. As informações sobre esta unidade são guardadas no ficheiro PSDataSourceConfig.xml. Esta é a unidade que o Windows Server Backup utiliza para cópias de segurança.

Pode personalizar a unidade que o Backup Server utiliza para a cópia de segurança do estado do sistema. No servidor protegido, vá a C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Abra o ficheiro PSDataSourceConfig.xml para edição. Altere o valor \<FilesToProtect\> para a letra de unidade. Guarde e feche o ficheiro. Se houver um grupo de proteção definido para proteger o estado do sistema do computador, faça uma verificação de consistência. Se for gerado um alerta, selecione Modificar o **grupo de proteção** no alerta e, em seguida, completar o assistente. Em seguida, faça outra verificação de consistência.

Note que se o servidor de proteção estiver num cluster, é possível que uma unidade de cluster seja selecionada como a unidade com o espaço mais livre. Se a propriedade da unidade foi mudada para outro nó e uma cópia de segurança do estado do sistema corre, a unidade não está disponível e a cópia de segurança falha. Neste cenário, modifique PSDataSourceConfig.xml para apontar para uma unidade local.

Em seguida, o Windows Server Backup cria uma pasta chamada WindowsImageBackup na raiz da pasta de restauro. À medida que o Windows Server Backup cria a cópia de segurança, todos os dados são colocados nesta pasta. Quando a cópia de segurança estiver terminada, o ficheiro é transferido para o computador 'Servidor de cópia de segurança'. Tenha em atenção as seguintes informações:

* Esta pasta e o seu conteúdo não são limpos quando a cópia de segurança ou transferência estiverem terminadas. A melhor maneira de pensar nisto é que o espaço está sendo reservado para a próxima vez que um backup estiver terminado.
* A pasta é criada sempre que é feita uma cópia de segurança. A hora e a data refletem a hora do seu último sistema de reserva.

## <a name="bmr-backup"></a>Backup BMR

Para a BMR (incluindo uma cópia de segurança do estado do sistema), o trabalho de backup é guardado diretamente para uma parte no computador 'Backup Server'. Não é guardado para uma pasta no servidor protegido.

O Servidor de Backup chama o Windows Server backup e partilha o volume de réplica seleção para a cópia de segurança BMR. Neste caso, não diz ao Windows Server Backup para utilizar a unidade com o espaço mais livre. Em vez disso, usa a parte que foi criada para o trabalho.

Quando a cópia de segurança estiver terminada, o ficheiro é transferido para o computador 'Servidor de cópia de segurança'. Os registos são armazenados em C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* A BMR não é suportada para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operativo de clientes.

* Não se pode proteger a BMR e o estado do sistema para o mesmo computador em diferentes grupos de proteção.

* Um computador do Servidor de Backup não pode proteger-se para a BMR.

* A proteção a curto prazo para fita (disco-a-fita, ou D2T) não é suportada para BMR. O armazenamento a longo prazo para fita (disco-a-disco-para-fita, ou D2D2T) é suportado.

* Para a proteção BMR, a Cópia de Segurança do Servidor do Windows deve ser instalada no computador protegido.

* Para a proteção BMR, ao contrário da proteção do estado do sistema, o Backup Server não tem quaisquer requisitos de espaço no computador protegido. A cópia de segurança do Windows Server transfere diretamente as cópias de segurança para o computador 'Servidor de cópia de segurança'. O trabalho de transferência de reserva não aparece na vista Backup Server **Jobs.**

* O Servidor de Backup reserva 30 GB de espaço no volume de réplica seleção para BMR. Pode alterar isto na página de Atribuição de **Discos** no assistente do Grupo de Proteção Modificação ou utilizando os cmdlets de PowerShell get-DatasourceDiske e Set-DatasourceDiskAllocation PowerShell. No volume do ponto de recuperação, a proteção BMR requer cerca de 6 GB para uma retenção de cinco dias.
  * Note que não pode reduzir o tamanho do volume da réplica para menos de 15 GB.
  * O Servidor de Backup não calcula o tamanho da fonte de dados BMR. Assume 30 GB para todos os servidores. Altere o valor com base no tamanho das cópias de segurança BMR que espera no seu ambiente. O tamanho de uma cópia de segurança BMR pode ser calculado aproximadamente como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de arranque + volume do sistema + dados do estado de hospedagem de volume, tais como Diretório Ativo.

* Se mudar da proteção do estado do sistema para a proteção BMR, a proteção BMR requer menos espaço no volume do ponto de *recuperação*. No entanto, o espaço extra no volume não é recuperado. Pode reduzir manualmente o tamanho do volume na página de atribuição de **discos modificadas** do assistente do Grupo de Proteção Modificação ou utilizando os cmdlets get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se mudar da proteção do estado do sistema para a proteção BMR, a proteção BMR requer mais espaço no volume da *réplica*. O volume é automaticamente alargado. Se pretender alterar as dotações de espaço predefinidas, utilize o cmdlet De Modificação-DiskAllocation PowerShell.

* Se mudar da proteção BMR para a proteção do estado do sistema, precisa de mais espaço no volume do ponto de recuperação. O Servidor de Backup pode tentar aumentar automaticamente o volume. Se não houver espaço suficiente na piscina de armazenamento, ocorre um erro.

    Se mudar da proteção BMR para a proteção do estado do sistema, precisa de espaço no computador protegido. Isto porque a proteção do estado do sistema escreve primeiro a réplica para o computador local e, em seguida, transfere-a para o computador Do Servidor de Backup.

## <a name="before-you-begin"></a>Antes de começar

1. **Implementar o servidor de backup Azure**. Verifique se o Servidor de Backup está corretamente implantado. Para obter mais informações, veja:
    * [Requisitos do sistema para o Servidor de Backup Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do Servidor de Backup](backup-mabs-protection-matrix.md)

2. **Configurar o armazenamento**. Pode armazenar dados de backup no disco, na fita e na nuvem com o Azure. Para mais informações, consulte Preparar o [armazenamento de dados.](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage)

3. **Configurar o agente de proteção**. Instale o agente de proteção no computador que pretende fazer o apoio. Para mais informações, consulte [A utilização do agente de proteção DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Estado de back up sistema e metal nu

Criar um grupo de proteção tal como descrito nos grupos de [proteção de implantação](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Note que não pode proteger o BMR e o estado do sistema para o mesmo computador em diferentes grupos. Além disso, quando seleciona BMR, o estado do sistema está ativado automaticamente.

1. Para abrir o assistente do Grupo de Proteção Nova na consola de administrador do servidor de backup, selecione **Protection** > **Actions** > Create **Protection Group**.

2. Na página **Select Protection Group Type,** selecione **Servers**, e, em seguida, selecione **Next**.

3. Na página **Select Group Members,** expanda o computador e, em seguida, selecione **o BMR** ou o estado do **sistema**.

    Lembre-se que não pode proteger tanto a BMR como o estado do sistema para o mesmo computador em diferentes grupos. Além disso, quando seleciona BMR, o estado do sistema está ativado automaticamente. Para mais informações, consulte [Implementar grupos](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)de proteção .

4. Na página **Select Data Protection Method,** selecione como pretende lidar com cópias de segurança a curto e longo prazo. A cópia de segurança de curto prazo é sempre para discor primeiro, com a opção de recuar do disco para a nuvem Azure utilizando o Azure Backup (a curto ou longo prazo). Uma alternativa à cópia de segurança a longo prazo da nuvem é configurar cópias de segurança a longo prazo para um dispositivo de fita autónoma ou uma biblioteca de fitas que esteja ligada ao Backup Server.

5. Na página **Select Short-Term Goals,** selecione como pretende fazer o armazenamento de curto prazo no disco:
    1. Para o **intervalo de retenção,** selecione quanto tempo pretende manter os dados no disco.
    2. Para a **frequência de sincronização,** selecione quantas vezes pretende executar uma cópia de segurança incremental para o disco. Se não quiser definir um intervalo de backup, pode verificar o Just antes de uma opção de ponto de **recuperação.** O Backup Server executará uma cópia de segurança expressa e completa antes de cada ponto de recuperação estar agendado.

6. Se pretender armazenar dados em fita para armazenamento a longo prazo, na página **Especificar Objetivos a Longo Prazo, selecione** quanto tempo pretende manter os dados da fita (1-99 anos).
    1. Para a **frequência da cópia de segurança,** selecione quantas vezes deve ser executada a cópia de segurança. A frequência baseia-se no intervalo de retenção que selecionou:
        * Quando o intervalo de retenção é de 1-99 anos, pode selecionar backups para ocorrer diariamente, semanalmente, quinzenais, mensais, trimestrais, semestrais ou anuais.
        * Quando o intervalo de retenção é de 1 a 11 meses, pode selecionar cópias de segurança para ocorrer diariamente, semanalmente, quinzenalmente ou mensalmente.
        * Quando o intervalo de retenção é de 1 a 4 semanas, pode selecionar cópias de segurança para ocorrer diariamente ou semanalmente.

    2. Na página **Select Tape and Library Details,** selecione a fita e a biblioteca para utilizar e se os dados devem ser comprimidos e encriptados.

7. Na página de Alocação de **Discos de Revisão,** reveja o espaço de disco de armazenamento que é atribuído ao grupo de proteção.

    1. **O tamanho total de dados** é o tamanho dos dados que pretende fazer.
    2. **O espaço do disco a fornecer no Servidor** de Backup Azure é o espaço que o Backup Server recomenda para o grupo de proteção. O Servidor de Backup escolhe o volume de backup ideal com base nas definições. No entanto, pode editar as escolhas de volume de cópia de segurança em detalhes de **atribuição de discos.**
    3. Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. As suas edidas alteram os valores para **armazenamento total** e **armazenamento gratuito** no painel de armazenamento de **discos disponível.** Espaço subprovisionado é a quantidade de armazenamento que o Backup Server sugere que adicione ao volume, para garantir backups suaves.

8. Na página Método de **Criação de Réplicas Escolha,** selecione como pretende lidar com a replicação inicial de dados completos. Se optar por replicar-se sobre a rede, recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou para condições de rede que sejam inferiores às ideais, considere replicar os dados offline utilizando meios amovíveis.

9. Na página Escolha de Opções de Verificação de **Consistência,** selecione como pretende automatizar verificações de consistência. Só pode optar por fazer uma verificação quando os dados da réplica se tornarem inconsistentes ou num horário. Se não quiser configurar a verificação automática de consistência, pode fazer uma verificação manual a qualquer momento. Para efetuar uma verificação manual, na área de **Proteção** da Consola de Administrador do Servidor de Backup, clique no grupo de proteção e, em seguida, selecione **'Verificação de consistência de desempenho**.

10. Se selecionou para fazer backup na nuvem utilizando o Azure Backup, na página De dados de **Proteção Online Especificada,** certifique-se de que seleciona as cargas de trabalho que pretende fazer de backup até ao Azure.

11. Na página **'Agenda', selecione** quantas cópias de segurança incrementais para o Azure ocorrerão. Pode agendar backups para funcionar todos os dias, semana, mês e ano, e selecionar a hora e a data em que devem correr. As cópias de segurança podem ocorrer até duas vezes por dia. Cada vez que uma cópia de segurança é executado, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de backup armazenados no disco 'Backup Server'.

12. Na página 'Política de **Retenção Online Especificação', selecione** como os pontos de recuperação que são criados a partir das cópias de segurança diárias, semanais, mensais e anuais são mantidos no Azure.

13. Na página **de Replicação Online Escolha,** selecione como ocorre a replicação completa inicial dos dados. Pode replicar-se sobre a rede ou fazer uma cópia de segurança offline (sementeção offline). Backup offline utiliza a função De importação de Azure. Para mais informações, consulte o [fluxo de trabalho de backup offline em Azure Backup](offline-backup-azure-data-box.md).

14. Na página **Resumo,** reveja as suas definições. Depois de selecionar o **Grupo Criar,** a replicação inicial dos dados ocorre. Quando a replicação de dados terminar, na página **'Estado',** o estado do grupo de proteção é **OK**. A cópia de segurança ocorre então de acordo com as definições do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar estado do sistema ou BMR

Pode recuperar o Estado BMR ou o sistema para uma localização da rede. Se tiver apoiado o BMR, utilize o Windows Recovery Environment (WinRE) para iniciar o seu sistema e conectá-lo à rede. Em seguida, utilize o Windows Server Backup para recuperar da localização da rede. Se tiver apoiado o estado do sistema, utilize apenas o Windows Server Backup para recuperar da localização da rede.

### <a name="restore-bmr"></a>Restaurar a BMR

Executar a recuperação no computador 'Servidor de cópia de segurança':

1. No painel **recovery,** encontre o computador que pretende recuperar e, em seguida, selecione **A Recuperação de Metais Nus**.

2. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

3. Na página **Select Recovery Type,** selecione **Copiar para uma pasta de rede.**

4. Na página **'Especificar Destino',** selecione para onde pretende copiar os dados. Lembre-se que o destino selecionado precisa de espaço suficiente. Recomendamos que crie uma nova pasta.

5. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar imagens de hardware baseadas em rede de armazenamento (SAN)baseadas em hardware, para uma recuperação mais rápida. (Esta é uma opção apenas se tiver um SAN com esta funcionalidade disponível, e a capacidade de criar e dividir um clone para torná-lo writable. Além disso, o computador protegido e o computador 'Backup Server' devem ser ligados à mesma rede.)

6. Configurar opções de notificação. Na página **de Confirmação,** selecione **Recuperar**.

Configurar o local da partilha:

1. No local de restauro, vá para a pasta que tem a cópia de segurança.

2. Partilhe a pasta que está um nível acima do WindowsImageBackup para que a raiz da pasta partilhada seja a pasta WindowsImageBackup. Se não fizer isto, restaurar não encontrará o reforço. Para se ligar utilizando o Windows Recovery Environment (WinRE), precisa de uma parte a que pode aceder no WinRE com o endereço ip e credenciais corretos.

Restaurar o sistema:

1. Inicie o computador no qual pretende restaurar a imagem utilizando o DVD do Windows para o sistema que está a restaurar.

2. Na primeira página, verifique as definições de idioma e local. Na página **Instalar,** selecione **Reparar o computador**.

3. Na página **Opções** de Recuperação do Sistema, selecione **Restaurar o computador utilizando uma imagem de sistema que criou anteriormente**.

4. Na página **de backup de imagem Select,** selecione **Selecione uma imagem** de sistema > **Advanced** > Search for a system image on **the network**. Se aparecer um aviso, selecione **Sim**. Vá para o caminho da partilha, insira as credenciais e, em seguida, selecione o ponto de recuperação. Isto procura cópias de segurança específicas que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que você deseja usar.

5. No **Escolhido como restaurar a** página de backup, selecione **Formato e discos de repartição**. Na página seguinte, verifique as definições.

6. Para iniciar a restauração, selecione **Terminar**. É necessário reiniciar.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Executar a recuperação no Servidor de Backup:

1. No painel **recovery,** encontre o computador que pretende recuperar e, em seguida, selecione **A Recuperação de Metais Nus**.

2. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

3. Na página **'Selecionar Tipo de Recuperação',** selecione **Copiar para uma pasta de rede**.

4. Na página **'Especificar Destino',** selecione onde pretende copiar os dados. Lembre-se que o destino selecionado precisa de espaço suficiente. Recomendamos que crie uma nova pasta.

5. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar instantâneos de hardware baseados em SAN para uma recuperação mais rápida. (Esta é uma opção apenas se tiver um SAN com esta funcionalidade e a capacidade de criar e dividir um clone para torná-lo writable. Além disso, o computador protegido e o servidor do servidor de backup devem ser ligados à mesma rede.)

6. Configurar opções de notificação. Na página **de Confirmação,** selecione **Recuperar**.

Executar a cópia de segurança do servidor do Windows:

1. Selecione **ações** > **recuperar** > **este servidor** > **seguinte**.

2. Selecione **Outro Servidor,** selecione a página **'Especificar Tipo de Localização'** e, em seguida, selecione **a pasta partilhada remota**. Introduza o caminho para a pasta que contém o ponto de recuperação.

3. Na página **Select Recovery Type,** selecione **system state**.

4. Na **página Select Location for System State Recovery,** selecione **Original Location**.

5. Na página **de Confirmação,** selecione **Recuperar**. Após a restauração, reinicie o servidor.

6. Também pode executar o estado de restauração do sistema num pedido de comando. Para isso, inicie a cópia de segurança do Windows Server no computador que pretende recuperar. Para obter a versão identificador, a uma solicitação de comando, introduza: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Utilize o identificador de versão para iniciar a restauração do estado do sistema. No pedido de comando, entre: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Confirme que quer começar a recuperação. Pode ver o processo na janela Command Prompt. É criado um tronco de restauro. Após a restauração, reinicie o servidor.
