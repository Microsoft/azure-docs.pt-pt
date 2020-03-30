---
title: Sistema e proteção de recuperação de metal nu
description: Utilize o Servidor de Backup Azure para fazer backup o seu estado de sistema e fornecer proteção de recuperação de metais nus (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505874"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Back up system state and restore to bare metal, usando o Azure Backup Server

O Azure Backup Server apoia o estado do sistema e fornece proteção de recuperação de metal nu (BMR).

* **Backup do estado do sistema**: Backup ficheiros do sistema operativo. Esta cópia de segurança permite-lhe recuperar quando um computador começa, mas os ficheiros do sistema e o registo perdem-se. Uma cópia de segurança do estado do sistema inclui os seguintes elementos:
  * Membro de domínio: ficheiros de arranque, base de dados de registo de classe COM+, registo
  * Controlador de domínio: Diretório Ativo do Servidor do Windows (NTDS), ficheiros de boot, base de dados de registo de classe COM+, registo, volume do sistema (SYSVOL)
  * Computador que executa serviços de cluster: Metadados de servidores cluster
  * Computador que executa serviços de certificado: Dados de certificados
* **Backup bare-metal**: Cópia de segurança dos ficheiros do sistema operativo e de todos os dados em volumes críticos, com exceção dos dados do utilizador. Por definição, uma cópia de segurança BMR inclui uma cópia de segurança do estado do sistema. Fornece proteção quando um computador não começa e tem que recuperar tudo.

A tabela que se segue resume o que pode recuar e recuperar. Para obter informações sobre as versões de aplicações que o estado do sistema e o BMR podem proteger, veja o que é que o [Azure Backup Server faz](backup-mabs-protection-matrix.md)cópia de segurança?

|Cópia de segurança|Problema|Recuperar da cópia de segurança do Servidor de Backup Azure|Recuperar a partir de uma cópia de segurança do estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de ficheiros**<br /><br />Cópia de segurança de dados normal<br /><br />Cópia de segurança do estado do sistema/BMR|Dados de ficheiro perdidos|S|N|N|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|S|S|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de ficheiros**<br /><br />Backup do Servidor de Backup Azure de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (volumes de dados perdidos)|S|N|S<br /><br />BMR, seguida de recuperação regular de dados de ficheiros de back-up|
|**Dados do SharePoint**<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|S|S|
|**Dados do SharePoint**<br /><br />Backup do Servidor de Backup Azure de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|VM perdida|S|N|N|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Anfitrião de Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />Backup do Servidor de Backup Azure do anfitrião ou hóspede do Hyper-V<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Anfitrião de Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguido de recuperação regular do Servidor de Backup Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Cópia de segurança do estado do sistema/BMR|Dados da aplicação perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|S|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (base de dados/registos de transações intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo sintetizar O Servidor de Backup Azure<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (base de dados/registos de transações perdidos)|N|N|S<br /><br />Recuperação bMR, seguida de recuperação regular do Servidor de Backup Azure|

## <a name="how-system-state-backup-works"></a>Como funciona a cópia de segurança do estado do sistema

Quando uma cópia de segurança do estado do sistema é executado, o Backup Server comunica com o Windows Server Backup para solicitar uma cópia de segurança do estado do sistema do servidor. Por predefinição, o Backup Server e o Windows Server Backup utilizam a unidade que possui o espaço livre mais disponível. As informações sobre esta unidade são guardadas no ficheiro *PSDataSourceConfig.xml.* 

Pode personalizar a unidade que o Backup Server utiliza para a cópia de segurança do estado do sistema: 

1. No servidor protegido, vá a *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*. 
1. Abra o ficheiro *PSDataSourceConfig.xml* para edição. 
1. Altere \<o\> valor do FilesToProtect para a letra de unidade. 
1. Guarde e feche o ficheiro. 

Se um grupo de proteção estiver definido para proteger o estado do sistema do computador, então faça uma verificação de consistência. Se for gerado um alerta, selecione modificar o **grupo de proteção** no alerta e, em seguida, completar as páginas do assistente. Em seguida, execute outra verificação de consistência.

Se o servidor de proteção estiver num cluster, pode ser selecionada uma unidade de cluster como a unidade que tem mais espaço livre. Se a propriedade da unidade for mudada para outro nó e uma cópia de segurança do estado do sistema corre, então a unidade não está disponível e a cópia de segurança falha. Neste cenário, modifique *PSDataSourceConfig.xml* para apontar para uma unidade local.

Em seguida, o Windows Server Backup cria uma pasta chamada *WindowsImageBackup* na raiz da pasta de restauro. À medida que o Windows Server Backup cria a cópia de segurança, todos os dados são colocados nesta pasta. Quando a cópia de segurança terminar, o ficheiro é transferido para o computador 'Servidor de cópia de segurança'. Tenha em atenção as seguintes informações:

* Esta pasta e o seu conteúdo não são limpos quando a cópia de segurança ou transferência terminarem. A melhor maneira de pensar nisto é que o espaço está reservado para a próxima vez que um backup terminar.
* A pasta é criada para cada cópia de segurança. A hora e a data refletem a hora do seu último sistema de reserva.

## <a name="how-bmr-backup-works"></a>Como funciona o backup bMR

Para a BMR (incluindo uma cópia de segurança do estado do sistema), o trabalho de backup é guardado diretamente para uma parte no computador 'Backup Server'. Não é guardado para uma pasta no servidor protegido.

O Servidor de Backup chama o Windows Server backup e partilha o volume de réplica seleção para a cópia de segurança BMR. Neste caso, não é necessário que o Windows Server Backup utilize a unidade que tem mais espaço livre. Em vez disso, usa a parte que foi criada para o trabalho.

Quando a cópia de segurança terminar, o ficheiro é transferido para o computador 'Servidor de cópia de segurança'. Os registos são armazenados em *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* A BMR não é suportada para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operativo de clientes.

* Não pode proteger a BMR e o estado do sistema do mesmo computador em grupos de proteção diferentes.

* Um computador do Servidor de Backup não pode proteger-se para a BMR.

* A proteção a curto prazo para fita (disco para fita, ou D2T) não é suportada para BMR. O armazenamento a longo prazo para fita (disco para disco para fita, ou D2D2T) é suportado.

* Para a proteção BMR, a Cópia de Segurança do Servidor do Windows deve ser instalada no computador protegido.

* Para a proteção BMR, ao contrário da proteção do estado do sistema, o Backup Server não dispõe de requisitos de espaço no computador protegido. A cópia de segurança do Windows Server transfere diretamente as cópias de segurança para o computador 'Servidor de cópia de segurança'. O trabalho de transferência de reserva não aparece na vista Backup Server **Jobs.**

* O Servidor de Backup reserva 30 GB de espaço no volume de réplica seleção para BMR. Pode alterar este loteamento de espaço na página de Atribuição de **Discos** no Assistente do Grupo de Proteção Modificação. Ou pode utilizar os cmdlets get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell. No volume do ponto de recuperação, a proteção BMR requer cerca de 6 GB para uma retenção de cinco dias.
  * Não se pode reduzir o tamanho do volume da réplica para menos de 15 GB.
  * O Servidor de Backup não calcula o tamanho da fonte de dados BMR. Assume 30 GB para todos os servidores. Altere o valor com base no tamanho das cópias de segurança BMR que espera no seu ambiente. Pode calcular aproximadamente o tamanho de uma cópia de segurança BMR como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de arranque + volume do sistema + dados do estado de hospedagem de volume, tais como Diretório Ativo.

* Se mudar da proteção do estado do sistema para a proteção BMR, então a proteção BMR requer menos espaço no volume do ponto de *recuperação*. No entanto, o espaço extra no volume não é recuperado. Pode reduzir manualmente o tamanho do volume na página de alocação de **discos modificadas** do Assistente do Grupo de Proteção Modificação. Ou pode utilizar os cmdlets get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se mudar da proteção do estado do sistema para a proteção BMR, então a proteção BMR requer mais espaço no volume da *réplica*. O volume é automaticamente alargado. Se pretender alterar as dotações de espaço predefinidas, utilize o cmdlet De Modificação-DiskAllocation PowerShell.

* Se mudar da proteção BMR para a proteção do estado do sistema, então precisa de mais espaço no volume do ponto de recuperação. O Servidor de Backup pode tentar aumentar automaticamente o volume. Se o depósito não tiver espaço suficiente, ocorre um erro.

    Se mudar da proteção BMR para a proteção do estado do sistema, então precisa de espaço no computador protegido. Você precisa do espaço porque a proteção do estado do sistema primeiro escreve a réplica para o computador local, e depois transfere a réplica para o computador Do Servidor de Backup.

## <a name="before-you-begin"></a>Antes de começar

1. **Implementar o servidor de backup Azure**. Verifique se o Servidor de Backup está corretamente implantado. Para obter mais informações, consulte:
    * [Requisitos do sistema para o Servidor de Backup Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do Servidor de Backup](backup-mabs-protection-matrix.md)

1. **Configurar o armazenamento**. Pode armazenar dados de backup no disco, na fita e na nuvem com o Azure. Para mais informações, consulte Preparar o [armazenamento de dados.](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage)

1. **Configurar o agente de proteção**. Instale o agente de proteção no computador que pretende fazer o apoio. Para mais informações, consulte [A utilização do agente de proteção DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Cópia de segurança do estado do sistema e bare-metal

Para apoiar o estado do sistema e o metal nu:

1. Para abrir o Assistente do Grupo de Proteção Nova, na consola de administrador do servidor de backup, selecione **Protection** > **Actions** > **Create Protection Group**.

1. Na página **Select Protection Group Type,** selecione **Servers**, e, em seguida, selecione **Next**.

1. Na página **Select Group Members,** expanda o computador e, em seguida, selecione **o BMR** ou o estado do **sistema**.

    Lembre-se que não pode proteger tanto a BMR como o estado do sistema para o mesmo computador em diferentes grupos. Além disso, quando seleciona BMR, o estado do sistema está ativado automaticamente. Para mais informações, consulte [Implementar grupos](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)de proteção .

1. Na página **Select Data Protection Method,** escolha como lidar com a cópia de segurança a curto prazo e a cópia de segurança a longo prazo. 

    A cópia de segurança de curto prazo é sempre para discor primeiro, com a opção de recuar do disco para o Azure utilizando o Azure Backup (a curto ou longo prazo). Uma alternativa à cópia de segurança a longo prazo da nuvem é configurar cópias de segurança a longo prazo para um dispositivo de fita autónoma ou uma biblioteca de fitas que esteja ligada ao Backup Server.

1. Na página **Select Short-Term Goals,** escolha como fazer o armazenamento de curto prazo no disco:
    * Para a **gama Retenção,** escolha quanto tempo para manter os dados no disco.
    * Para a **frequência de sincronização,** escolha com que frequência executar uma cópia de segurança incremental para o disco. Se não quiser definir um intervalo de backup, pode selecionar Antes de um ponto de **recuperação**. O Backup Server executará uma cópia de segurança expressa antes de cada ponto de recuperação estar agendado.

1. Se pretender armazenar dados em fita para armazenamento a longo prazo, então na página **Especificar Objetivos de Longo Prazo,** escolha quanto tempo para manter os dados da fita (1 a 99 anos).
    1. Para a **frequência de cópia de segurança,** escolha com que frequência fazer cópias de segurança para gravar. A frequência baseia-se no intervalo de retenção selecionado:
        * Quando o intervalo de retenção é de 1 a 99 anos, pode recuar diariamente, semanalmente, quinzenalmente, mensalmente, trimestral, semestralmente ou anualmente.
        * Quando o intervalo de retenção é de 1 a 11 meses, pode recuar diariamente, semanalmente, quinzenalmente ou mensalmente.
        * Quando o intervalo de retenção é de 1 a 4 semanas, pode recuar diariamente ou semanalmente.

    1. Na página **Select Tape and Library Details,** selecione a fita e a biblioteca para utilizar. Escolha também se os dados devem ser comprimidos e encriptados.

1. Na página de Alocação de **Discos de Revisão,** reveja o espaço de disco de armazenamento que está disponível para o grupo de proteção.

    * **O tamanho total de dados** é o tamanho dos dados que pretende fazer.
    * **O espaço do disco a fornecer no Servidor** de Backup Azure é o espaço que o Backup Server recomenda para o grupo de proteção. O Servidor de Backup utiliza estas definições para escolher o volume de cópia de segurança ideal. Pode editar as escolhas de volume de cópia de cópia de segurança em detalhes de **atribuição de discos.**
    * Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o Backup Server sugere que adicione ao volume para garantir backups suaves.

1. Na página Método de Criação de **Réplicas Escolha,** selecione como lidar com a replicação inicial de dados completos. 

    Se optar por replicar-se sobre a rede, recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou para condições de rede que sejam inferiores às ideais, considere replicar os dados offline utilizando meios amovíveis.

1. Na página Escolha de Opções de Verificação de **Consistência,** selecione como automatizar verificações de consistência. 

    Só pode optar por fazer uma verificação quando os dados da réplica se tornarem inconsistentes ou num horário. Se não quiser configurar a verificação automática de consistência, então pode fazer uma verificação manual a qualquer momento. Para efetuar uma verificação manual, na área de **Proteção** da Consola de Administrador do Servidor de Backup, clique no grupo de proteção e, em seguida, selecione **'Verificação de consistência de desempenho**.

1. Se optou por voltar à nuvem utilizando o Azure Backup, na página De dados de **Proteção Online Especificar,** selecione as cargas de trabalho que pretende fazer de reserva até ao Azure.

1. Na página **'Agenda', selecione** com que frequência deve voltar a azure. 

    Pode agendar reforços para funcionar todos os dias, semana, mês e ano. Também pode selecionar a hora e a data em que as cópias de segurança devem ser executadas. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que uma cópia de segurança é executado, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de backup que são armazenados no disco 'Backup Server'.

1. Na página 'Política de **Retenção Online Especificação', selecione** como os pontos de recuperação que são criados a partir das cópias de segurança diárias, semanais, mensais e anuais são mantidos em Azure.

1. Na página **de Replicação Online Escolha,** selecione como ocorre a replicação completa inicial dos dados. 

    Pode replicar-se sobre a rede ou voltar a estar offline (sementeção offline). Uma cópia de segurança offline utiliza a função De importação de Azure. Para mais informações, consulte o [fluxo de trabalho de backup offline em Azure Backup](offline-backup-azure-data-box.md).

1. Na página **Resumo,** reveja as suas definições. Depois de selecionar o **Grupo Criar,** a replicação inicial dos dados ocorre. Quando a replicação de dados termina, na página **'Estado',** o estado do grupo de proteção é **OK**. As cópias de segurança acontecem então de acordo com as definições do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou a BMR

Pode recuperar a BMR ou o estado do sistema numa localização de rede. Se tiver apoiado o BMR, utilize o Windows Recovery Environment (WinRE) para iniciar o seu sistema e ligá-lo à rede. Em seguida, utilize a Cópia de Segurança do Windows Server para recuperar a partir da localização de rede. Se tiver apoiado o estado do sistema, utilize apenas o Windows Server Backup para recuperar da localização da rede.

### <a name="restore-bmr"></a>Restaurar BMR

Para executar a recuperação no computador 'Servidor de Backup':

1. No painel **recovery,** encontre o computador que pretende recuperar. Em seguida, selecione **Bare Metal Recovery**.

1. Os pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

1. Na página **'Selecionar Tipo de Recuperação',** selecione **Copiar para uma pasta de rede**.

1. Na página **'Especificar Destino',** selecione o destino para os dados copiados. 

    Lembre-se, o destino precisa ter espaço suficiente para os dados. Recomendamos que crie uma nova pasta para o destino.

1. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança. Em seguida, selecione se utiliza imagens de hardware baseadas em área de armazenamento (SAN), para uma recuperação mais rápida. Esta opção só está disponível se:
    * Tem um SAN que fornece esta funcionalidade. 
    * Pode criar e dividir um clone para torná-lo consumado.
    * O computador protegido e o computador 'Backup Server' estão ligados à mesma rede.

1. Configurar opções de notificação. 
1. Na página **de Confirmação,** selecione **Recuperar**.

Para configurar o local da partilha:

1. No local de restauro, vá para a pasta que tem a cópia de segurança.

1. Partilhe a pasta que está um nível acima do *WindowsImageBackup* para que a raiz da pasta partilhada seja a pasta *WindowsImageBackup.* 

    Se não partilhar esta pasta, restaurar não encontrará a cópia de segurança. Para se ligar utilizando o WinRE, precisa de uma parte a que pode aceder no WinRE com o endereço IP e credenciais corretos.

Para restaurar o sistema:

1. Inicie o computador no qual pretende restaurar a imagem utilizando o DVD do Windows para o sistema que está a restaurar.

1. Na primeira página, verifique as definições para idioma e local. Na página **Instalar,** selecione **Reparar o computador**.

1. Na página **Opções** de Recuperação do Sistema, selecione **Restaurar o computador utilizando uma imagem de sistema que criou anteriormente**.

1. Na página **de backup de imagem Select,** selecione **Selecione uma imagem** > de sistema**Advanced** > **Search para obter uma imagem de sistema na rede**. Se aparecer um aviso, selecione **Sim**. Vá para o caminho da partilha, insira as credenciais e, em seguida, selecione o ponto de recuperação. O sistema procura cópias de segurança específicas que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que pretende utilizar.

1. No **Escolhido como restaurar a** página de backup, selecione **Formato e discos de repartição**. Na página seguinte, verifique as definições.

1. Para iniciar a restauração, selecione **Terminar**. É necessário reiniciar.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Para executar a recuperação no Servidor de Backup:

1. No painel **recovery,** encontre o computador que pretende recuperar e, em seguida, selecione **A Recuperação de Metais Nus**.

1. Os pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

1. Na página **'Selecionar Tipo de Recuperação',** selecione **Copiar para uma pasta de rede**.

1. Na página **'Especificar Destino',** selecione onde copiar os dados. 

    Lembre-se, o destino que selecionar precisa de ter espaço suficiente para os dados. Recomendamos que crie uma nova pasta para o destino.

1. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança. Em seguida, selecione se utiliza imagens de hardware baseadas em SAN, para uma recuperação mais rápida. Esta opção só está disponível se: 
    * Tem um SAN que fornece esta funcionalidade.
    * Pode criar e dividir um clone para torná-lo consumado. 
    * O computador protegido e o servidor do servidor de backup estão ligados à mesma rede.

1. Configurar opções de notificação. 
1. Na página **de Confirmação,** selecione **Recuperar**.

Para executar a cópia de segurança do Servidor do Windows:

1. Selecione **ações** > **para recuperar** > **este servidor** > **a seguir**.

1. Selecione **Outro Servidor,** selecione a página **'Especificar Tipo de Localização'** e, em seguida, selecione **a pasta partilhada remota**. Introduza o caminho para a pasta que contém o ponto de recuperação.

1. Na página **Select Recovery Type,** selecione **system state**.

1. Na **página Select Location for System State Recovery,** selecione **Original Location**.

1. Na página **de Confirmação,** selecione **Recuperar**. 

1. Após a restauração, reinicie o servidor.

Também pode executar a restauração do estado do sistema a uma solicitação de comando: 

1. Inicie a cópia de segurança do Windows Server no computador que pretende recuperar. 

1. Para obter o identificador da versão, a uma solicitação de comando, introduza:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Utilize o identificador de versão para iniciar a restauração do estado do sistema. Na linha de comandos, escreva: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Confirme que quer começar a recuperação. Pode ver o processo na janela Command Prompt. É criado um registo do restauro. 

1. Após a restauração, reinicie o servidor.
