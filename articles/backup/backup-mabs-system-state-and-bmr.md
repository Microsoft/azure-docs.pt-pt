---
title: Proteção contra o estado do sistema e a recuperação de metais nus
description: Utilize o Servidor de Backup Azure para fazer backup do estado do sistema e fornecer proteção de recuperação de metais nus (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021627"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Faça backup do estado do sistema e restaure para o metal nu, utilizando o Servidor de Backup Azure

O Azure Backup Server confirma o estado do sistema e fornece uma proteção de recuperação de metais nus (BMR).

* **Backup do estado do sistema**: Retrossitiva os ficheiros do sistema operativo. Esta cópia de segurança permite-lhe recuperar quando um computador começa, mas os ficheiros do sistema e o registo perdem-se. Uma cópia de segurança do estado do sistema inclui os seguintes elementos:
  * Membro de domínio: ficheiros de arranque, base de dados de registo de classe COM+, registo
  * Controlador de domínio: Diretor ativo do Windows Server (NTDS), ficheiros de arranque, base de dados de registo de classes COM+, registo, volume do sistema (SYSVOL)
  * Computador que executa serviços de cluster: Metadados de servidor de cluster
  * Computador que executa serviços de certificado: Dados de certificados
* **Cópia de segurança de metal nu:** Faça backup dos ficheiros do sistema operativo e de todos os dados relativos a volumes críticos, com exceção dos dados do utilizador. Por definição, uma cópia de segurança BMR inclui uma cópia de segurança do estado do sistema. Fornece proteção quando um computador não arranca e tem que recuperar tudo.

A tabela que se segue resume o que pode recuar e recuperar. Para obter informações sobre as versões de aplicações que o estado do sistema e o BMR podem proteger, veja [o que o Azure Backup Server faz de volta?](backup-mabs-protection-matrix.md)

|Backup|Problema|Recuperar do backup do Servidor de Backup do Azure|Recuperar a partir de uma cópia de segurança do estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de ficheiros**<br /><br />Cópia de segurança de dados normal<br /><br />Cópia de segurança do estado do sistema/BMR|Dados de ficheiro perdidos|Y|N|N|
|**Dados de ficheiros**<br /><br />Backup Backup Backup backup de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|Y|Y|
|**Dados de ficheiros**<br /><br />Backup Backup Backup backup de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (volumes de dados intactos)|N|N|Y|
|**Dados de ficheiros**<br /><br />Backup Backup Backup backup de dados de ficheiros<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (volumes de dados perdidos)|Y|N|Y<br /><br />BMR, seguido de recuperação regular de dados de ficheiros com apoio|
|**Dados do SharePoint**<br /><br />Backup Backup backup do Azure Server de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Site perdido, listas, listas de itens, documentos|Y|N|N|
|**Dados do SharePoint**<br /><br />Backup Backup backup do Azure Server de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|Y|Y|
|**Dados do SharePoint**<br /><br />Backup Backup backup do Azure Server de dados agrícolas<br /><br />Cópia de segurança do estado do sistema/BMR|Recuperação após desastre|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup Backup Backup backup do anfitrião hiper-V ou convidado<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|VM perdida|Y|N|N|
|Hyper-V<br /><br />Backup Backup Backup backup do anfitrião hiper-V ou convidado<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|Y|Y|
|Hyper-V<br /><br />Backup Backup Backup backup do anfitrião hiper-V ou convidado<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Anfitrião de Hyper-V perdido (VMs intactas)|N|N|Y|
|Hyper-V<br /><br />Backup Backup Backup backup do anfitrião hiper-V ou convidado<br /><br />Cópia de segurança de anfitrião do estado do sistema/BMR|Anfitrião de Hyper-V perdido (VMs perdidas)|N|N|Y<br /><br />BMR, seguido da recuperação regular do Servidor de Backup do Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo Azure Backup Server<br /><br />Cópia de segurança do estado do sistema/BMR|Dados da aplicação perdidos|Y|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo Azure Backup Server<br /><br />Cópia de segurança do estado do sistema/BMR|Sistema operativo perdido ou danificado|N|Y|Y|
|SQL Server/Exchange<br /><br />Backup de aplicativo Azure Backup Server<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (base de dados/registos de transações intactos)|N|N|Y|
|SQL Server/Exchange<br /><br />Backup de aplicativo Azure Backup Server<br /><br />Cópia de segurança do estado do sistema/BMR|Servidor perdido (base de dados/registos de transações perdidos)|N|N|Y<br /><br />Recuperação de BMR, seguida de recuperação regular do Servidor de Backup do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona a cópia de segurança do estado do sistema

Quando uma cópia de segurança do estado do sistema é executado, o Backup Server comunica com o Windows Server Backup para solicitar uma cópia de segurança do estado do sistema do servidor. Por predefinição, o Backup Server e o Windows Server Backup utilizam a unidade que tem o espaço livre mais disponível. As informações sobre esta unidade são guardadas no ficheiro *PSDataSourceConfig.xml.*

Pode personalizar a unidade que o Backup Server utiliza para a cópia de segurança do estado do sistema:

1. No servidor protegido, aceda a *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*.
1. Abra o ficheiro *PSDataSourceConfig.xml* para edição.
1. Altere o valor \<FilesToProtect\> para a letra da unidade.
1. Guarde e feche o ficheiro.

Se um grupo de proteção estiver preparado para proteger o estado do sistema do computador, então verifique a consistência. Se for gerado um alerta, selecione Modificar o **grupo de proteção** no alerta e, em seguida, completar as páginas no assistente. Em seguida, execute outra verificação de consistência.

Se o servidor de proteção estiver num cluster, poderá ser selecionada uma unidade de cluster como a unidade que tem mais espaço livre. Se essa propriedade de unidade for mudada para outro nó e um backup do estado do sistema for executado, então a unidade está indisponível e a cópia de segurança falha. Neste cenário, modifique *PSDataSourceConfig.xml* apontar para uma unidade local.

Em seguida, o Windows Server Backup cria uma pasta chamada *WindowsImageBackup* na raiz da pasta de restauro. À medida que o Windows Server Backup cria a cópia de segurança, todos os dados são colocados nesta pasta. Quando a cópia de segurança terminar, o ficheiro é transferido para o computador backup Server. Tenha em atenção as seguintes informações:

* Esta pasta e o seu conteúdo não são limpos quando a cópia de segurança ou transferência termina. A melhor maneira de pensar nisto é que o espaço está reservado para a próxima vez que um backup terminar.
* A pasta é criada para cada cópia de segurança. O carimbo de hora e data reflete a hora do seu último sistema de reserva.

## <a name="how-bmr-backup-works"></a>Como funciona a cópia de segurança da BMR

Para o BMR (incluindo uma cópia de segurança do estado do sistema), a tarefa de cópia de segurança é guardada diretamente para uma partilha no computador Backup Server. Não é guardado para uma pasta no servidor protegido.

O Backup Server chama cópia de segurança do Windows Server e partilha o volume de réplica para a cópia de segurança do BMR. Neste caso, não requer cópia de segurança do Windows Server para utilizar a unidade que tem mais espaço livre. Em vez disso, usa a parte que foi criada para o trabalho.

Quando a cópia de segurança terminar, o ficheiro é transferido para o computador backup Server. Os registos são armazenados em *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* O BMR não é suportado para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operativo de clientes.

* Não pode proteger a BMR e o estado do sistema do mesmo computador em grupos de proteção diferentes.

* Um computador backup Server não pode proteger-se para bMR.

* A proteção a curto prazo para fita (disco a fita ou D2T) não é suportada para BMR. O armazenamento a longo prazo para fita (disco para disco para fita, ou D2D2T) é suportado.

* Para a proteção BMR, a cópia de segurança do Servidor do Windows deve ser instalada no computador protegido.

* Para a proteção do BMR, ao contrário da proteção do estado do sistema, o Backup Server não tem requisitos de espaço no computador protegido. O Windows Server Backup transfere diretamente cópias de segurança para o computador Backup Server. O trabalho de transferência de cópias de segurança não aparece na vista backup Server **Jobs.**

* Backup Server reserva 30 GB de espaço no volume de réplica para BMR. Pode alterar este loteamento de espaço na página de atribuição de **discos** no Assistente do Grupo de Proteção modificar. Ou pode usar os Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation comandantes PowerShell. No volume do ponto de recuperação, a proteção do BMR requer cerca de 6 GB para uma retenção de cinco dias.
  * Não é possível reduzir o tamanho do volume da réplica para menos de 15 GB.
  * O Backup Server não calcula o tamanho da fonte de dados do BMR. Assume 30 GB para todos os servidores. Altere o valor com base no tamanho das cópias de segurança BMR que espera no seu ambiente. Pode calcular aproximadamente o tamanho de uma cópia de segurança BMR como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de arranque + volume do sistema + volume de hospedagem de dados do sistema, tais como Ative Directory.

* Se mudar da proteção do estado do sistema para a proteção BMR, a proteção BMR requer menos espaço no volume do *ponto de recuperação*. No entanto, o espaço extra no volume não é recuperado. Pode reduzir manualmente o tamanho do volume na página de atribuição de **disco de modificação** do assistente do grupo de proteção modificar. Ou pode usar os Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation comandantes PowerShell.

    Se mudar da proteção do estado do sistema para a proteção BMR, a proteção BMR requer mais espaço no volume de *réplica*. O volume é automaticamente estendido. Se quiser alterar as alocações de espaço predefinidos, utilize o cmdlet powershell Modify-DiskAllocation.

* Se mudar de proteção BMR para proteção do estado do sistema, então precisa de mais espaço no volume do ponto de recuperação. O Backup Server pode tentar aumentar automaticamente o volume. Se a piscina de armazenamento não tiver espaço suficiente, ocorre um erro.

    Se mudar de proteção BMR para proteção do estado do sistema, então precisa de espaço no computador protegido. Precisa do espaço porque a proteção do estado do sistema primeiro escreve a réplica para o computador local e depois transfere a réplica para o computador Backup Server.

## <a name="before-you-begin"></a>Antes de começar

1. **Implementar o servidor de backup Azure**. Verifique se o Servidor de Cópia de Segurança está corretamente implantado. Para obter mais informações, consulte:
    * [Requisitos do sistema para O Servidor de Backup Azure](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do servidor de backup](backup-mabs-protection-matrix.md)

1. **Configurar o armazenamento.** Pode armazenar dados de backup no disco, na fita e na nuvem com o Azure. Para obter mais informações, consulte [Preparar o armazenamento de dados.](/system-center/dpm/plan-long-and-short-term-data-storage)

1. **Configurar o agente de proteção.** Instale o agente de proteção no computador que pretende fazer o back-up. Para obter mais informações, consulte [implementar o agente de proteção DPM](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Cópia de segurança do estado do sistema e bare-metal

Para apoiar o estado do sistema e o metal nu:

1. Para abrir o Assistente do Grupo de Proteção Nova, na consola de administrador do servidor de backup, selecione  >  **Protection Actions** Create Protection  >  **Group**.

1. Na página **'Tipo de Grupo de Proteção de Protecção' selecione,** selecione **Servidores** e, em seguida, selecione **Seguinte**.

1. Na página **'Selecionar membros do grupo',** expandir o computador e, em seguida, selecionar o **BMR** ou **o estado do sistema**.

    Lembre-se que não pode proteger tanto o BMR como o estado do sistema para o mesmo computador em diferentes grupos. Além disso, quando selecionar O BMR, o estado do sistema é automaticamente ativado. Para obter mais informações, consulte [grupos de proteção implementar](/system-center/dpm/create-dpm-protection-groups).

1. Na página **'Selecionar método de proteção de dados',** escolha como lidar com a cópia de segurança a curto prazo e a cópia de segurança a longo prazo.

    A cópia de segurança a curto prazo é sempre para dissixar primeiro, com a opção de fazer backup do disco para o Azure utilizando o Azure Backup (a curto ou longo prazo). Uma alternativa à cópia de segurança a longo prazo da nuvem é configurar uma cópia de segurança a longo prazo para um dispositivo de fita autónomo ou biblioteca de fitas que esteja ligada ao Backup Server.

1. Na página **'Selecionar Short-Term Objetivos',** escolha como voltar ao armazenamento de curto prazo no disco:
    * Para **o intervalo de retenção,** escolha quanto tempo para manter os dados no disco.
    * Para **a frequência de sincronização,** escolha com que frequência executar uma cópia de segurança incremental no disco. Se não quiser definir um intervalo de backup, pode selecionar **antes de um ponto de recuperação**. O Backup Server executará uma cópia de segurança completa expressa pouco antes de cada ponto de recuperação ser programado.

1. Se pretender armazenar dados em fita para armazenamento a longo prazo, então na página **'Especificar Long-Term Objetivos',** escolha quanto tempo para manter os dados da fita (1 a 99 anos).
    1. Para **a frequência de cópia de segurança,** escolha a frequência com que executar a cópia de segurança para a fita. A frequência baseia-se na gama de retenção selecionada:
        * Quando a faixa de retenção for de 1 a 99 anos, pode apoiar-se diariamente, semanalmente, quinzenalmente, mensal, trimestral, semesear ou anualmente.
        * Quando a gama de retenção for de 1 a 11 meses, pode voltar a subir diariamente, semanalmente, quinzenalmente ou mensalmente.
        * Quando o intervalo de retenção for de 1 a 4 semanas, pode voltar atrás diariamente ou semanalmente.

    1. Na página **'Selecione' Tape and Library Details,** selecione a fita e a biblioteca para utilizar. Escolha também se os dados devem ser comprimidos e encriptados.

1. Na página **de Atribuição do Disco de Revisão,** reveja o espaço do disco de armazenamento disponível para o grupo de proteção.

    * **O tamanho total** dos dados é o tamanho dos dados que pretende fazer.
    * **O espaço do disco a ser a provisionado no Azure Backup Server** é o espaço que o Backup Server recomenda para o grupo de proteção. O Backup Server utiliza estas definições para escolher o volume de backup ideal. Pode editar as opções de volume de cópia de segurança nos **detalhes de atribuição do disco.**
    * Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o Backup Server sugere que adicione ao volume para garantir cópias de segurança suaves.

1. Na página Escolha método **de criação de réplica,** selecione como lidar com a replicação inicial de dados completos.

    Se optar por replicar-se sobre a rede, recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou para condições de rede que não sejam ideais, considere replicar os dados offline utilizando suportes amovíveis.

1. Na página **Escolha Opções de Verificação de Consistência,** selecione como automatizar verificações de consistência.

    Pode optar por executar uma verificação apenas quando os dados de réplica se tornarem inconsistentes, ou num horário. Se não quiser configurar a verificação automática de consistência, então pode verificar manualmente a qualquer momento. Para executar uma verificação manual, na área de **Proteção** da Consola de Administrador do Servidor de Cópia de Segurança, clique com o botão direito no grupo de proteção e, em seguida, selecione **'Executar verificação de consistência'**.

1. Se optar por fazer backup de segurança na nuvem utilizando o Azure Backup, na página **De Dados de Proteção Online,** selecione as cargas de trabalho que pretende fazer até ao Azure.

1. Na página **'Especificar agendamento' de backup on-line,** selecione com que frequência de volta gradual até Azure.

    Pode agendar backups para executar todos os dias, semana, mês e ano. Também pode selecionar a hora e a data em que as cópias de segurança devem ser executadas. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que uma cópia de segurança é executada, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de cópia que são armazenados no disco backup Server.

1. Na página **'Política de Retenção Online Especificar',** selecione como os pontos de recuperação que são criados a partir das cópias de segurança diárias, semanais, mensais e anuals são mantidos em Azure.

1. Na página **Escolha Replicação Online,** selecione como ocorre a replicação completa inicial dos dados.

    Pode replicar-se sobre a rede ou fazer o back-up offline (sementeira offline). Uma cópia de segurança offline utiliza a função Azure Import. Para obter mais informações, consulte [o fluxo de trabalho de backup offline no Azure Backup](offline-backup-azure-data-box.md).

1. Na página  **Resumo,** reveja as suas definições. Depois de selecionar **Criar Grupo,** ocorre a replicação inicial dos dados. Quando a replicação de dados terminar, na página **'Estado',** o estado do grupo de proteção está **OK**. As cópias de segurança acontecem de acordo com as definições do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou a BMR

Pode recuperar a BMR ou o estado do sistema numa localização de rede. Se tiver apoiado o BMR, utilize o Ambiente de Recuperação do Windows (WinRE) para iniciar o seu sistema e conectá-lo à rede. Em seguida, utilize a Cópia de Segurança do Windows Server para recuperar a partir da localização de rede. Se tiver estado de backup do sistema, utilize apenas o Windows Server Backup para recuperar da localização da rede.

### <a name="restore-bmr"></a>Restaurar BMR

Para executar a recuperação no computador Backup Server:

1. No painel **recovery,** encontre o computador que pretende recuperar. Em seguida, selecione **Bare Metal Recovery**.

1. Os pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

1. Na página  **'Tipo de Recuperação' selecione** **Copy para uma pasta de rede**.

1. Na página **'Destino Especificar',** selecione o destino para os dados copiados.

    Lembre-se, o destino precisa ter espaço suficiente para os dados. Recomendamos que crie uma nova pasta para o destino.

1. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança. Em seguida, selecione se deve utilizar imagens de hardware baseadas em área de armazenamento (SAN), para uma recuperação mais rápida. Esta opção só está disponível se:
    * Você tem uma SAN que fornece esta funcionalidade.
    * Pode criar e dividir um clone para torná-lo credível.
    * O computador protegido e o computador backup Server estão ligados à mesma rede.

1. Configurar opções de notificação.
1. Na página **Confirmação,** selecione **Recuperar**.

Para configurar a localização da partilha:

1. No local de restauro, vá à pasta que tem a cópia de segurança.

1. Partilhe a pasta que está um nível acima do *WindowsImageBackup* para que a raiz da pasta partilhada seja a pasta *WindowsImageBackup.*

    Se não partilhar esta pasta, a restauração não encontrará a cópia de segurança. Para se conectar utilizando o WinRE, precisa de uma partilha a que possa aceder no WinRE com o endereço IP e credenciais IP corretos.

Para restaurar o sistema:

1. Inicie o computador no qual pretende restaurar a imagem utilizando o DVD do Windows para o sistema que está a restaurar.

1. Na primeira página, verifique as definições de idioma e local. Na página **'Instalar',** **selecione Reparar o seu computador**.

1. Na página **Opções de Recuperação** do Sistema, selecione **Restaurar o computador utilizando uma imagem do sistema que criou anteriormente**.

1. Na página de backup de imagem do **sistema selecione** **selecione uma imagem** do sistema Procura uma imagem  >  **avançada**  >  **na rede**. Se aparecer um aviso, selecione **Sim**. Vá para o caminho da partilha, introduza as credenciais e, em seguida, selecione o ponto de recuperação. O sistema procura cópias de segurança específicas que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que pretende utilizar.

1. Na escolha como restaurar a página **de cópia de segurança,** selecione Formato e discos de **repartição**. Na página seguinte, verifique as definições.

1. Para iniciar a restauração, **selecione Terminar**. É necessário reiniciar.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Para executar a recuperação no Servidor de Backup:

1. No painel **recovery,** encontre o computador que pretende recuperar e, em seguida, selecione **A Recuperação de Metais Nus**.

1. Os pontos de recuperação disponíveis são indicados a negrito no calendário. Selecione a data e a hora para o ponto de recuperação que pretende utilizar.

1. Na página **'Tipo de Recuperação' selecione** **Copy para uma pasta de rede**.

1. Na página **'Saber',** selecione onde copiar os dados.

    Lembre-se, o destino que seleciona precisa de ter espaço suficiente para os dados. Recomendamos que crie uma nova pasta para o destino.

1. Na página **'Especificar Opções de Recuperação',** selecione as definições de segurança. Em seguida, selecione se deve usar fotos de hardware baseadas em SAN, para uma recuperação mais rápida. Esta opção só está disponível se:
    * Você tem uma SAN que fornece esta funcionalidade.
    * Pode criar e dividir um clone para torná-lo credível.
    * O servidor de computador protegido e servidor de backup estão ligados à mesma rede.

1. Configurar opções de notificação.
1. Na página **Confirmação,** selecione **Recuperar**.

Para executar a cópia de segurança do Servidor do Windows:

1. Selecione **Ações**  >  **Para recuperar** este  >  **servidor**  >  **em seguida**.

1. Selecione **Outro Servidor**, selecione a página **'Localizar',** e, em seguida, selecione **a pasta partilhada remotamente**. Introduza o caminho para a pasta que contém o ponto de recuperação.

1. Na página **'Tipo de Recuperação' Selecione,** selecione **Estado do Sistema**.

1. Na **página Select Location for System State Recovery,** selecione  **Original Location**.

1. Na página **Confirmação,** selecione **Recuperar**.

1. Após a restauração, reinicie o servidor.

Também pode executar a restauração do estado do sistema numa solicitação de comando:

1. Inicie a cópia de segurança do Servidor do Windows no computador que pretende recuperar.

1. Para obter o identificador de versão, num pedido de comando, insira:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Utilize o identificador de versão para iniciar a restauração do estado do sistema. Na linha de comandos, escreva:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Confirme que quer começar a recuperação. Pode ver o processo na janela 'Ordem de comando'. É criado um registo do restauro.

1. Após a restauração, reinicie o servidor.
