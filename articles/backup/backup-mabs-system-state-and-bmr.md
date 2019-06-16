---
title: O Azure Backup Server protege o estado do sistema e restaura a bare-metal
description: Utilize o Azure Backup Server para fazer backup de estado do sistema e fornecer proteção de recuperação bare-metal (BMR).
services: backup
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: 35ab150670cdc27efcedca233928e0c2184aeca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116180"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Criar cópias de segurança do Estado do sistema e restaurar para bare-metal com o Azure Backup Server

O Azure Backup Server cria uma cópia de segurança do Estado do sistema e fornece proteção de recuperação bare-metal (BMR).

*   **Cópia de segurança de estado de sistema**: Faz backup de arquivos de sistema operacional, para que possa recuperar quando um computador é iniciado, mas os arquivos de sistema e do Registro são perdidos. Uma cópia de segurança do Estado do sistema inclui:
    * Membro de domínio: Ficheiros de arranque, base de dados de registo de classe COM+, registo
    * Controlador de domínio: Windows Server Active Directory (NTDS), ficheiros de arranque, base de dados de registo de classe COM+, registo, volume de sistema (SYSVOL)
    * Computador que executa serviços de cluster: Metadados do servidor de cluster
    * Computador que executa serviços de certificados: Dados de certificado
* **Cópia de segurança bare-metal**: Faz uma cópia de segurança de ficheiros de sistema operativo e todos os dados em volumes críticos (exceto os dados de utilizador). Por definição, uma cópia de segurança de BMR inclui uma cópia de segurança do Estado do sistema. Fornece proteção quando um computador não é iniciado e for necessário recuperar tudo.

A tabela seguinte resume o que pode criar cópias de segurança e recuperar. Para obter informações detalhadas sobre as versões de aplicações que podem ser protegidos com o estado do sistema e a BMR, consulte [o que faz o servidor de cópia de segurança do Azure backup?](backup-mabs-protection-matrix.md).

|Cópia de segurança|Problema|Recuperar a partir de cópia de segurança do servidor de cópia de segurança do Azure|Recuperar a partir de cópia de segurança de estado de sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de ficheiros**<br /><br />Cópia de segurança de dados regulares<br /><br />Backup de estado do sistema/BMR|Dados de ficheiro perdidos|S|N|N|
|**Dados de ficheiros**<br /><br />O Azure backup do servidor de cópia de segurança de dados de ficheiro<br /><br />Backup de estado do sistema/BMR|Sistema de operativo perdido ou danificado|N|S|S|
|**Dados de ficheiros**<br /><br />O Azure backup do servidor de cópia de segurança de dados de ficheiro<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de ficheiros**<br /><br />O Azure backup do servidor de cópia de segurança de dados de ficheiro<br /><br />Backup de estado do sistema/BMR|Servidor perdido (volumes de dados perdidos)|S|Não|Sim (BMR, seguida de recuperação normal de dados de ficheiro de cópia de segurança)|
|**Dados do SharePoint**:<br /><br />O Azure backup do servidor de cópia de segurança dos dados do farm<br /><br />Backup de estado do sistema/BMR|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**:<br /><br />O Azure backup do servidor de cópia de segurança dos dados do farm<br /><br />Backup de estado do sistema/BMR|Sistema de operativo perdido ou danificado|N|S|S|
|**Dados do SharePoint**:<br /><br />O Azure backup do servidor de cópia de segurança dos dados do farm<br /><br />Backup de estado do sistema/BMR|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />O Azure backup do servidor de cópia de segurança de anfitrião Hyper-V ou do convidado<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|VM perdida|S|N|N|
|Hyper-V<br /><br />O Azure backup do servidor de cópia de segurança de anfitrião Hyper-V ou do convidado<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Sistema de operativo perdido ou danificado|N|S|S|
|Hyper-V<br /><br />O Azure backup do servidor de cópia de segurança de anfitrião Hyper-V ou do convidado<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Anfitrião de Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />O Azure backup do servidor de cópia de segurança de anfitrião Hyper-V ou do convidado<br /><br />Cópia de segurança do sistema/BMR estado do anfitrião|Anfitrião de Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação normal de servidor de cópia de segurança do Azure|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do Azure Backup Server<br /><br />Backup de estado do sistema/BMR|Dados da aplicação perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do Azure Backup Server<br /><br />Backup de estado do sistema/BMR|Sistema de operativo perdido ou danificado|N|Y|S|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do Azure Backup Server<br /><br />Backup de estado do sistema/BMR|Servidor perdido (base de dados/registos de transações intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Cópia de segurança de aplicação do Azure Backup Server<br /><br />Backup de estado do sistema/BMR|Servidor perdido (base de dados/registos de transações perdidos)|N|N|S<br /><br />Recuperação de BMR, seguida de recuperação normal de servidor de cópia de segurança do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona a cópia de segurança de estado de sistema

Quando uma cópia de segurança do Estado do sistema é executado, cópia de segurança do servidor se comunica com cópia de segurança do Windows Server para pedir uma cópia de segurança do Estado do sistema do servidor. Por predefinição, o servidor de cópia de segurança e de cópia de segurança do Windows Server utilizam a unidade que tenha o mais espaço livre. Informações sobre esta unidade são guardadas no ficheiro Psdatasourceconfig. Esta é a unidade que utiliza a cópia de segurança do Windows Server para cópias de segurança.

Pode personalizar a unidade que utiliza o servidor de cópia de segurança para a cópia de segurança do Estado do sistema. No servidor protegido, aceda a C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Abra o ficheiro Psdatasourceconfig para edição. Alteração da \<FilesToProtect\> valor para a letra de unidade. Guarde e feche o ficheiro. Se houver um conjunto de grupos de proteção para proteger o estado do sistema do computador, execute uma verificação de consistência. Se for gerado um alerta, selecione **Modificar grupo de proteção** no alerta e, em seguida, conclua o assistente. Em seguida, execute outra verificação de consistência.

Tenha em atenção que, se o servidor de proteção estiver num cluster, é possível que seja selecionada uma unidade de cluster como a unidade com mais espaço livre. Se a propriedade dessa unidade tiver sido mudada para outro nó e um execuções de cópia de segurança de estado de sistema, a unidade não está disponível e a cópia de segurança falhará. Neste cenário, modificar o XML para apontar para uma unidade local.

Em seguida, a cópia de segurança do Windows Server cria uma pasta designada WindowsImageBackup na raiz da pasta de restauro. Como a cópia de segurança do Windows Server cria a cópia de segurança, todos os dados é colocados nesta pasta. Quando a cópia de segurança estiver concluída, o ficheiro é transferido para o computador do servidor de cópia de segurança. Tenha em atenção as seguintes informações:

* Esta pasta e os respetivos conteúdos não são limpos quando a cópia de segurança ou a transferência estiver concluída. A melhor maneira de pensar nisso é que o espaço está a ser reservado para a próxima vez que uma cópia de segurança estiver concluída.
* A pasta é criada sempre que for feita uma cópia de segurança. O carimbo de data e hora indicar a hora de seu último backup de estado do sistema.

## <a name="bmr-backup"></a>Cópia de segurança de BMR

Para a BMR (incluindo uma cópia de segurança do Estado do sistema), a tarefa de cópia de segurança é guardada diretamente para uma partilha no computador do servidor de cópia de segurança. Não é guardada para uma pasta no servidor protegido.

Servidor de cópia de segurança chama a cópia de segurança do Windows Server e partilha o volume de réplica para essa cópia de segurança de BMR. Neste caso, ele não indica a cópia de segurança do Windows Server para utilizar a unidade com mais espaço livre. Em vez disso, ele usa o compartilhamento que foi criado para a tarefa.

Quando a cópia de segurança estiver concluída, o ficheiro é transferido para o computador do servidor de cópia de segurança. Os registos são armazenados em c:\windows\logs\windowsserverbackup.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

-   A BMR não é suportada para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operativo cliente.

-   Não é possível proteger a BMR e o sistema de estado para o mesmo computador em grupos de proteção diferentes.

-   Um computador do servidor de cópia de segurança não pode proteger-se para a BMR.

-   Proteção de curta duração em banda (disco para banda ou D2T) não é suportada para a BMR. Armazenamento de longa duração em banda (disco para disco para banda ou D2D2T) é suportado.

-   Para a proteção de BMR, a cópia de segurança do Windows Server tem de ser instalada no computador protegido.

-   Para a proteção de BMR, ao contrário de para proteção do Estado do sistema, o servidor de cópia de segurança não tem requisitos de espaço no computador protegido. Cópia de segurança do Windows Server transfere diretamente as cópias de segurança para o computador do servidor de cópia de segurança. A tarefa de cópia de segurança de transferência não aparece no servidor de cópia de segurança **tarefas** vista.

-   Servidor de cópia de segurança reserva 30 GB de espaço no volume de réplica para a BMR. Pode alterá-lo sobre a **atribuição do disco** página no assistente Modificar grupo de proteção ou utilizando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation do PowerShell. No volume de ponto de recuperação, a proteção BMR requer cerca de 6 GB para uma retenção de cinco dias.
    * Tenha em atenção que não é possível reduzir o tamanho do volume de réplica para menos de 15 GB.
    * Servidor de cópia de segurança não calcula o tamanho da origem de dados da BMR. Parte do princípio de 30 GB para todos os servidores. Altere o valor com base no tamanho das cópias de segurança de BMR que esperava no seu ambiente. O tamanho de uma cópia de segurança de BMR pode ser aproximadamente calculado como a soma do espaço utilizado em todos os volumes críticos. Volumes críticos = volume de arranque + volume de sistema + volume que aloja os dados de estado do sistema, como o Active Directory.

-   Se alterar de proteção do Estado do sistema para a proteção de BMR, a proteção BMR requer menos espaço no *volume de pontos de recuperação*. No entanto, o espaço extra no volume não será recuperado. Pode diminuir manualmente o tamanho do volume no **modificar alocação do disco** página do assistente Modificar grupo de proteção ou utilizando os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation do PowerShell.

    Se alterar de proteção do Estado do sistema para a proteção de BMR, a proteção BMR requer mais espaço no *volume de réplica*. O volume foi estendido automaticamente. Se pretender alterar as alocações de espaço predefinidas, utilize o cmdlet do PowerShell Modify-DiskAllocation.

-   Se alterar da proteção de BMR para a proteção de estado do sistema, precisa de mais espaço no volume do ponto de recuperação. Servidor de cópia de segurança poderá tentar aumentar automaticamente o volume. Se houver espaço insuficiente no agrupamento de armazenamento, ocorre um erro.

    Se alterar da proteção de BMR para a proteção de estado do sistema, precisa de espaço no computador protegido. Isto acontece porque a proteção do Estado do sistema guarda primeiro a réplica no computador local e, em seguida, transfere-o para o computador do servidor de cópia de segurança.

## <a name="before-you-begin"></a>Antes de começar

1.  **Implementar o Azure Backup Server**. Certifique-se de que o servidor de cópia de segurança está implantado corretamente. Para obter mais informações, consulte:
    * [Requisitos de sistema do servidor de cópia de segurança do Azure](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do servidor de cópia de segurança](backup-mabs-protection-matrix.md)

2.  **Configurar o armazenamento**. Pode armazenar dados de cópia de segurança em disco, em banda e na cloud com o Azure. Para obter mais informações, consulte [preparar o armazenamento de dados](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Configurar o agente de proteção**. Instale o agente de proteção no computador que pretende criar cópias de segurança. Para obter mais informações, consulte [implementar o agente de proteção do DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Cópia de segurança do Estado do sistema e bare-metal
Configurar um grupo de proteção, conforme descrito em [implementar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Tenha em atenção que não é possível proteger a BMR e o sistema de estado para o mesmo computador em grupos diferentes. Além disso, ao selecionar a BMR, o estado do sistema é ativado automaticamente.


1.  Para abrir o Assistente Criar novo grupo de proteção na consola do administrador do servidor de cópia de segurança, selecione **proteção** > **ações** > **criar grupo de proteção** .

2.  Sobre o **selecionar tipo de grupo de proteção** , selecione **servidores**e, em seguida, selecione **seguinte**.

3.  Sobre o **selecionar membros do grupo** página, expanda o computador e, em seguida, selecione **BMR** ou **estado do sistema**.

    Lembre-se de que não é possível proteger a BMR e o sistema de estado para o mesmo computador em grupos diferentes. Além disso, ao selecionar a BMR, o estado do sistema é ativado automaticamente. Para obter mais informações, consulte [implementar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Sobre o **selecionar método de proteção de dados** , selecione como pretende processar a cópia de segurança de curta e longa duração. Backup de curto prazo é sempre efetuadas para disco em primeiro lugar, com a opção de criar cópias de segurança do disco do Azure na cloud com o Azure Backup (curta ou longa duração). É uma alternativa à cópia de segurança de longo prazo para a cloud configurar a cópia de segurança de longo prazo para uma biblioteca de banda ou de dispositivo de banda de autónomo que está ligada ao servidor de cópia de segurança.

5.  Sobre o **selecionar objetivos a curto prazo** , selecione como pretende criar cópias de segurança para armazenamento de curta duração no disco:
    1. Para **período de retenção**, selecione o período de tempo que pretende manter os dados no disco. 
    2. Para **frequência de sincronização**, selecione a frequência com que pretende executar uma cópia de segurança incremental para o disco. Se não quiser definir um intervalo de cópia de segurança, consulte a **apenas antes de um ponto de recuperação** opção. Servidor de cópia de segurança será executada uma cópia de segurança rápida completa imediatamente antes de cada ponto de recuperação está agendado.

6.  Se pretende armazenar os dados em banda para armazenamento a longo prazo no **especificar objetivos de longo prazo** , selecione o período de tempo que pretende manter os dados da banda (1 a 99 anos). 
    1. Para **frequência da cópia de segurança**, selecione a frequência com que cópia de segurança em banda deve ser executado. A frequência baseia-se no período de retenção que selecionou:
        * Quando o período de retenção for de 1 a 99 anos, pode selecionar as cópias de segurança ocorrer diárias, semanais, mensais, trimestrais quinzenal, semestrais ou anuais.
        * Quando o período de retenção for de 1 a 11 meses, pode selecionar as cópias de segurança ocorrem diárias, semanais, bissemanais ou mensais.
        * Quando o período de retenção for de 1 a 4 semanas, pode selecionar as cópias de segurança diárias ou semanais.

    2. Sobre o **selecionar da banda e os detalhes da biblioteca** página, selecione a banda e biblioteca a utilizar e se os dados devem ser comprimidos e encriptados.

7.  Sobre o **rever atribuição do disco** , reveja o espaço de disco do agrupamento de armazenamento atribuída ao grupo de proteção.

    1. **Tamanho de dados total** é o tamanho dos dados que pretende criar cópias de segurança.
    2. **Espaço no disco a ser aprovisionado no Azure Backup Server** é o espaço que o servidor de cópia de segurança recomenda para o grupo de proteção. Servidor de cópia de segurança escolhe o volume de cópia de segurança ideal com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume na **detalhes da alocação de disco**. 
    3. Para cargas de trabalho, no menu pendente, selecione o armazenamento preferido. As edições alteram os valores para **armazenamento Total** e **de armazenamento livre** no **armazenamento em disco disponível** painel. O espaço com aprovisionamento a menos é a quantidade de armazenamento que o servidor de cópia de segurança sugere que adicione ao volume, para garantir cópias de segurança fluídas.

8.  Sobre o **Choose Replica Creation Method** , selecione como pretende processar a replicação de dados completa inicial. Se optar por replicar na rede, recomendamos que escolha uma hora de ponta. Para grandes quantidades de dados ou condições de rede que estão abaixo do ideal, considere replicar os dados offline usando mídia removível.

9. Sobre o **escolher opções de verificação de consistência** , selecione como pretende automatizar as verificações de consistência. Pode optar por executar uma verificação apenas quando os dados de réplica ficarem inconsistentes, ou com base numa agenda. Se não quiser configurar a verificação de consistência automática, pode executar uma verificação manual em qualquer altura. Para executar uma verificação manual, além da **proteção** área da consola do administrador do servidor de cópia de segurança, clique com o botão direito do grupo de proteção e, em seguida, selecione **efetuar verificação de consistência**.

10. Se tiver selecionado para criar cópias de segurança para a cloud utilizando a cópia de segurança do Azure, no **especificar dados da proteção Online** página, certifique-se de que seleciona as cargas de trabalho que pretende criar cópias de segurança para o Azure.

11. Sobre o **Especificar agenda de cópia de segurança Online** página, selecionadas a frequência com que incrementais para o Azure irão ocorrer. Pode agendar cópias de segurança para executar cada dia, semana, mês e ano e selecione a data e hora em que deve executar. As cópias de segurança podem ocorrer até duas vezes por dia. Sempre que for executada uma cópia de segurança, um ponto de recuperação de dados é criado no Azure partir da cópia dos dados de cópia de segurança armazenados no disco do servidor de cópia de segurança.

12. Sobre o **especificar política de retenção Online** , selecione a forma como os pontos de recuperação criados a partir de cópias de segurança diárias, semanais, mensais e anuais são retidos no Azure.

13. Sobre o **escolher replicação Online** , selecione como ocorre a replicação de dados completa inicial. Pode replicar através da rede ou fazer uma offline (propagação offline) de cópia de segurança. Cópia de segurança offline utiliza a funcionalidade de importação do Azure. Para obter mais informações, consulte [Offline fluxo de trabalho de cópia de segurança no Azure Backup](backup-azure-backup-import-export.md).

14. Sobre o **resumo** , reveja as suas definições. Depois de selecionar **criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados for concluída, no **Status** página, o estado do grupo de proteção é **OK**. Cópia de segurança, em seguida, iniciar pela proteção de definições de grupo.

## <a name="recover-system-state-or-bmr"></a>Recuperar estado do sistema ou da BMR
Pode recuperar a BMR ou estado do sistema para uma localização de rede. Se criou uma segurança de BMR, utilize o ambiente de recuperação do Windows (WinRE) para iniciar o seu sistema e ligá-lo à rede. Em seguida, utilize a cópia de segurança do Windows Server para recuperar a partir da localização de rede. Se criou uma cópia de segurança do Estado do sistema, utilize apenas a cópia de segurança do Windows Server para recuperar a partir da localização de rede.

### <a name="restore-bmr"></a>Restaurar BMR
Execute uma recuperação no computador do servidor de cópia de segurança:

1.  Na **recuperação** painel, encontre o computador que pretende recuperar e, em seguida, selecione **recuperação bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e hora para o ponto de recuperação que pretende utilizar.

3.  Sobre o **selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede.**

4.  Sobre o **especificar destino** página, selecione onde pretende copiar os dados. Lembre-se de que o destino selecionado tem de ter espaço suficiente. Recomendamos que crie uma nova pasta.

5.  Sobre o **especificar opções de recuperação** página, selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar a rede de armazenamento (SAN)-com base em instantâneos de hardware, para acelerar a recuperação. (Esta é uma opção apenas se tiver uma SAN com esta funcionalidade disponível e a capacidade de criar e dividir um clone para o tornar gravável. Além disso, o computador protegido e o computador do servidor de cópia de segurança tem de estar ligados à mesma rede.)

6.  Configure as opções de notificação. Sobre o **confirmação** página, selecione **recuperar**.

Configure a localização da partilha:

1.  Na localização de restauro, vá para a pasta que contém a cópia de segurança.

2.  Partilhe a pasta que é um nível acima de WindowsImageBackup para que a raiz da pasta partilhada seja a pasta WindowsImageBackup. Se não fizer isso, o restauro não conseguirá localizar a cópia de segurança. Para ligar utilizando o ambiente de recuperação do Windows (WinRE), terá de um compartilhamento que possa aceder no WinRE com as credenciais e o endereço IP correto.

Restaure o sistema:

1.  Inicie o computador no qual pretende restaurar a imagem usando o DVD do Windows para o sistema que está a restaurar.

2.  Na primeira página, verifique as definições de idioma e região. Sobre o **instale** página, selecione **reparar o seu computador**.

3.  Sobre o **System Recovery Options** página, selecione **restaurar o computador utilizando uma imagem do sistema que criou anteriormente**.

4.  Sobre o **selecionar uma cópia de segurança de imagem do sistema** , selecione **selecionar uma imagem de sistema** > **avançadas** > **procurar uma imagem de sistema na rede**. Se for apresentado um aviso, selecione **Sim**. Vá para o caminho da partilha, introduza as credenciais e, em seguida, selecione o ponto de recuperação. Esta ação procura cópias de segurança específicas disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que pretende utilizar.

5.  Sobre o **escolha como restaurar a cópia de segurança** página, selecione **formatar e dividir discos**. Na página seguinte, verifique as definições. 

6.  Para iniciar o restauro, selecione **concluir**. É necessário um reinício.

### <a name="restore-system-state"></a>Restaurar estado do sistema

Execute uma recuperação no servidor de cópia de segurança:

1.  Na **recuperação** painel, encontre o computador que pretende recuperar e, em seguida, selecione **recuperação bare-Metal**.

2.  Pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e hora para o ponto de recuperação que pretende utilizar.

3.  Sobre o **selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede**.

4.  Sobre o **especificar destino** página, selecione onde pretende copiar os dados. Lembre-se de que o destino selecionado tem espaço suficiente. Recomendamos que crie uma nova pasta.

5.  Sobre o **especificar opções de recuperação** página, selecione as definições de segurança a aplicar. Em seguida, selecione se pretende utilizar instantâneos de hardware baseada em SAN para uma recuperação mais rápida. (Esta é uma opção apenas se tiver uma SAN com esta funcionalidade e a capacidade de criar e dividir um clone para o tornar gravável. Além disso, o computador protegido e o servidor de servidor de cópia de segurança tem de estar ligados à mesma rede.)

6.  Configure as opções de notificação. Sobre o **confirmação** página, selecione **recuperar**.

Execute a cópia de segurança do Windows Server:

1.  Selecione **ações** > **recuperar** > **neste servidor** > **seguinte**.

2.  Selecione **outro servidor**, selecione a **especificar tipo de localização** página e, em seguida, selecione **pasta remota partilhada**. Introduza o caminho para a pasta que contém o ponto de recuperação.

3.  Sobre o **selecionar tipo de recuperação** , selecione **estado do sistema**. 

4. Sobre o **selecionar localização para recuperação do Estado do sistema** , selecione **localização Original**.

5.  Sobre o **confirmação** página, selecione **recuperar**. Após o restauro, reinicie o servidor.

6.  Também pode executar a restauração de estado do sistema num prompt de comando. Para tal, inicie a cópia de segurança do Windows Server no computador que pretende recuperar. Para obter o identificador de versão, uma linha de comandos, introduza: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Utilize o identificador de versão para iniciar o restauro de estado do sistema. Na linha de comandos, introduza: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Confirme que pretende iniciar a recuperação. Pode ver o processo na janela da linha de comando. É criado um registo do restauro. Após o restauro, reinicie o servidor.

