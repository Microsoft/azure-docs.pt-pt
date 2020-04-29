---
title: Instalar Azure Backup Server no Azure Stack
description: Neste artigo, aprenda a usar o Servidor de Backup Azure para proteger ou fazer backup de cargas de trabalho em Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77583440"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalar Azure Backup Server no Azure Stack

Este artigo explica como instalar o Servidor de Backup Azure no Azure Stack. Com o Servidor de Backup Azure, pode proteger a Infraestrutura como um Serviço (IaaS) cargas de trabalho, como máquinas virtuais em funcionamento em Azure Stack. Um benefício de utilizar o Servidor de Backup Azure para proteger as suas cargas de trabalho é que pode gerir toda a proteção de carga de trabalho a partir de uma única consola.

> [!NOTE]
> Para saber mais sobre as capacidades de segurança, consulte a documentação de funcionalidades de [segurança do Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Azure Backup Server

O Azure Backup Server protege as seguintes cargas de trabalho da máquina virtual Azure Stack.

| Origem de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Canal Semi Anual do Servidor do Windows - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| SQL Server 2016 | Base de Dados |
| SQL Server 2014 | Base de Dados |
| SQL Server 2012 SP1 | Base de Dados |
| SharePoint 2016 | Quinta, base de dados, frontend, servidor web |
| SharePoint 2013 | Quinta, base de dados, frontend, servidor web |
| SharePoint 2010 | Quinta, base de dados, frontend, servidor web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente do Servidor de Backup Azure

Considere as recomendações nesta secção ao instalar o Servidor de Backup Azure no seu ambiente Azure Stack. O instalador do Servidor de Backup Azure verifica se o seu ambiente tem os pré-requisitos necessários, mas poupará tempo preparando-se antes de instalar.

### <a name="determining-size-of-virtual-machine"></a>Determinar o tamanho da máquina virtual

Para executar o Servidor de Backup Azure numa máquina virtual Azure Stack, utilize o tamanho A2 ou maior. Para obter assistência na escolha de um tamanho de máquina virtual, descarregue a calculadora de [tamanho VM Do Empilh A Pino.](https://www.microsoft.com/download/details.aspx?id=56832)

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes Virtuais em Máquinas virtuais Azure Stack

Todas as máquinas virtuais utilizadas numa carga de trabalho do Azure Stack devem pertencer à mesma rede virtual Azure e à Subscrição Azure.

### <a name="azure-backup-server-vm-performance"></a>Desempenho vM do servidor de backup Azure

Se partilhada com outras máquinas virtuais, o tamanho da conta de armazenamento e os limites do IOPS impactam o desempenho do VM do Servidor de Backup Azure. Por esta razão, deve utilizar uma conta de armazenamento separada para a máquina virtual do Servidor de Backup Azure. O agente de backup Azure que funciona no Servidor de Backup Azure precisa de armazenamento temporário para:

- sua própria utilização (uma localização cache),
- dados restaurados a partir da nuvem (área de paragem local)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar o armazenamento temporário de disco de backup azure

Cada máquina virtual Azure Stack vem com armazenamento temporário de `D:\`disco, que está disponível para o utilizador como volume . A área de paragem local necessária pelo Azure `D:\`Backup pode ser configurada `C:\`para residir em , e a localização da cache pode ser colocada em . Desta forma, nenhum armazenamento precisa de ser esculpido longe dos discos de dados ligados à máquina virtual do Servidor de Backup Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup no disco local e em Azure

O Azure Backup Server armazena dados de backup em discos Azure ligados à máquina virtual, para recuperação operacional. Uma vez que os discos e o espaço de armazenamento estão ligados à máquina virtual, o Azure Backup Server gere o armazenamento para si. A quantidade de armazenamento de dados de backup depende do número e tamanho dos discos ligados a cada [máquina virtual Azure Stack](/azure-stack/user/azure-stack-storage-overview). Cada tamanho de Azure Stack VM tem um número máximo de discos que podem ser ligados à máquina virtual. Por exemplo, A2 é quatro discos. A3 são oito discos. A4 são 16 discos. Mais uma vez, o tamanho e o número de discos determinam o total de armazenamento de reserva.

> [!IMPORTANT]
> Não **deverá** reter dados de recuperação operacional (backup) em discos ligados ao Servidor de Backup Azure durante mais de cinco dias.
>

Armazenar dados de backup no Azure reduz a infraestrutura de backup no Azure Stack. Se os dados forem com mais de cinco dias, devem ser armazenados em Azure.

Para armazenar dados de backup em Azure, crie ou use um cofre de Serviços de Recuperação. Ao preparar-se para fazer backup a carga de trabalho do Servidor de Backup Azure, [configura o cofre dos Serviços](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)de Recuperação . Uma vez configurado, cada vez que um trabalho de reserva corre, um ponto de recuperação é criado no cofre. Cada cofre dos Serviços de Recuperação tem até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e do tempo que são retidos, pode reter dados de backup durante muitos anos. Por exemplo, pode criar pontos de recuperação mensais e retê-los por cinco anos.

### <a name="scaling-deployment"></a>Implantação de escala

Se quiser escalar a sua implementação, tem as seguintes opções:

- Escala para cima - Aumente o tamanho da máquina virtual do Servidor de Backup Azure de série A para Série D, e aumente o armazenamento local [de acordo com as instruções](/azure-stack/user/azure-stack-manage-vm-disks)da máquina virtual Azure Stack .
- Descarregue os dados - envie dados mais antigos para o Azure e guarde apenas os dados mais recentes sobre o armazenamento anexado ao Servidor de Backup Azure.
- Scale out - Adicione mais Servidores de Backup Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

.NET A estrutura 3.5 SP1 ou superior deve ser instalada na máquina virtual.

### <a name="joining-a-domain"></a>Aderir a um domínio

A máquina virtual do Servidor de Backup Azure deve ser unida a um domínio. Um utilizador de domínio com privilégios de administrador deve instalar o Servidor de Backup Azure na máquina virtual.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Usando um VM IaaS em Azure Stack

Ao escolher um servidor para o Servidor de Backup Azure, comece com um Datacenter Do Windows Server 2012 R2 ou imagem da galeria DoCenter Do Windows Server 2016. O artigo, [Create your first Windows virtual machine in the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar com a máquina virtual recomendada. Os requisitos mínimos recomendados para a máquina virtual do servidor (VM) devem ser: Padrão A2 com dois núcleos e RAM de 3,5-GB.

Proteger cargas de trabalho com o Azure Backup Server tem muitas nuances. O artigo, [Instalar dPM como uma máquina virtual Azure,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))ajuda a explicar estas nuances. Antes de colocar a máquina, leia completamente este artigo.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar numa máquina virtual dedicada e unifamiliar. Não é possível instalar o Servidor de Backup Azure em:
>
> - Um computador em execução como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicações está instalada
> - Um computador no qual o Exchange Server está em execução
> - Um computador que é um nó de um cluster

Junte-se sempre ao Azure Backup Server para um domínio. Se precisar de mover o Azure Backup Server para um domínio diferente, instale primeiro o Servidor de Backup Azure e, em seguida, junte-o ao novo domínio. Uma vez implementado o Servidor de Backup Azure, não pode movê-lo para um novo domínio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento de cofre saqueada de Serviços de Recuperação permite-lhe escolher entre armazenamento geo-redundante e armazenamento localmente redundante. Por padrão, os cofres dos Serviços de Recuperação utilizam armazenamento geo-redundante. Se este cofre for o seu cofre principal, deixe a opção de armazenamento definida para armazenamento geo-redundante. Escolha armazenamento localmente redundante se quiser uma opção mais barata que seja menos durável. Leia mais sobre opções de armazenamento [georedundante](../storage/common/storage-redundancy-grs.md) e [localmente redundantes](../storage/common/storage-redundancy-lrs.md) na visão geral da replicação do [Armazenamento Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o seu cofre para abrir o painel do cofre e o menu Definições. Se o menu **Definições** não abrir, clique em **todas as definições** no painel do cofre.
2. No menu **Definições,** clique na**configuração** de backup da **infraestrutura** > de backup para abrir o menu de **configuração de backup.** No menu **de configuração de backup,** escolha a opção de replicação de armazenamento para o seu cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Baixe o instalador do Servidor de Backup Azure

Existem duas formas de descarregar o instalador do Servidor de Backup Azure. Pode descarregar o instalador do Servidor de Backup Azure a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55269). Também pode baixar o instalador do Servidor de Backup Azure, uma vez que está a configurar um cofre de Serviços de Recuperação. Os seguintes passos passam por você através do download do instalador do portal Azure enquanto configura um cofre de Serviços de Recuperação.

1. A partir da sua máquina virtual Azure Stack, [inscreva-se na sua assinatura Azure no portal Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **Todos os Serviços.**

    ![Escolha a opção Todos os Serviços no menu principal](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. No diálogo **de todos os serviços,** digite Serviços de *Recuperação.* À medida que começa a escrever, a sua entrada filtra a lista de recursos. Assim que o vir, selecione **cofres dos Serviços de Recuperação.**

    ![Serviços de Recuperação de Tipos no diálogo de todos os serviços](./media/backup-mabs-install-azure-stack/all-services.png)

    A lista de cofres dos Serviços de Recuperação na subscrição aparece.

4. A partir da lista de cofres dos Serviços de Recuperação, selecione o seu cofre para abrir o painel de instrumentos.

    ![Serviços de Recuperação de Tipos no diálogo de todos os serviços](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. No menu "Getting Started" do cofre, clique em **Backup** para abrir o assistente de início de partida.

    ![Backup começando](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    O menu de reserva abre.

    ![Backup-goals-default-open](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. No menu de reserva, a partir do menu onde está o menu de funcionamento da **sua carga de trabalho,** selecione **On-premises**. A partir do menu de reserva **What deseja fazer backup,** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure. Se não tiver a certeza de quais as cargas de trabalho a selecionar, escolha **máquinas virtuais Hyper-V** e, em seguida, clique em **Preparar infraestruturas**.

    ![no local e cargas de trabalho como objetivos](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Abre o menu **de infraestruturas Prepare.**

7. No menu **de infraestrutura Prepare,** clique em **Baixar** para abrir uma página web para descarregar ficheiros de instalação do Servidor de Backup Azure.

    ![Começando a mudança de feiticeiro](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A página web da Microsoft que acolhe os ficheiros transferíveis para o Azure Backup Server abre.

8. Na página de descarregamento do Microsoft Azure Backup Server, selecione um idioma e clique em **Baixar**.

    ![Centro de descarregamento abre](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. O instalador do Servidor de Backup Azure é composto por oito ficheiros - um instalador e sete ficheiros .bin. Verifique **o Nome do Ficheiro** para selecionar todos os ficheiros necessários e clique em **Next**. Descarregue todos os ficheiros para a mesma pasta.

    ![Centro de descarregamento 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    O tamanho de download de todos os ficheiros de instalação é superior a 3 GB. Num link de descarregamento de 10 Mbps, o download de todos os ficheiros de instalação pode demorar até 60 minutos. Os ficheiros descarregam para o local de descarregamento especificado.

## <a name="extract-azure-backup-server-install-files"></a>Extrato de Servidor de Backup Azure instala ficheiros

Depois de ter descarregado todos os ficheiros para a sua máquina virtual Azure Stack, vá ao local de descarregamento. A primeira fase de instalação do Servidor de Backup Azure é extrair os ficheiros.

![Centro de descarregamento 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Para iniciar a instalação, a partir da lista de ficheiros descarregados, clique em **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > São necessários pelo menos 4GB de espaço livre para extrair os ficheiros de configuração.
    >

2. No assistente do Servidor de Backup Azure, clique em **Next** para continuar.

    ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Escolha o caminho para os ficheiros do Servidor de Backup Azure e clique em **Next**.

   ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifique a localização da extração e clique em **Extrair**.

   ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. O assistente extrai os ficheiros e prepara o processo de instalação.

   ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Assim que o processo de extração estiver concluído, clique em **Terminar**. Por predefinição, **execute configuração.exe** é selecionado. Quando clicar em **Terminar,** Configurar.exe instala o Microsoft Azure Backup Server no local especificado.

   ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instale o pacote de software

No passo anterior, clicou em **Terminar** para sair da fase de extração e iniciar o assistente de configuração do Servidor de Backup Azure.

![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

O Azure Backup Server partilha o código com o Gestor de Proteção de Dados. Verá referências ao Gestor de Proteção de Dados e DPM no instalador do Servidor de Backup Azure. Embora o Azure Backup Server e o Gestor de Proteção de Dados sejam produtos separados, estes produtos estão intimamente relacionados.

1. Para lançar o assistente de configuração, clique no **Microsoft Azure Backup Server**.

   ![Assistente de configuração de backup microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. No ecrã de **Boas-Vindas**, clique em **Seguinte**.

    ![Servidor de backup Azure - Verificação de boas-vindas e pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. No ecrã **de Verificações Pré-Requisitos,** clique em **Verificar** se os pré-requisitos de hardware e software para o Servidor de Backup Azure foram cumpridos.

    ![Servidor de backup Azure - Verificação de boas-vindas e pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se o seu ambiente tiver os pré-requisitos necessários, verá uma mensagem indicando que a máquina satisfaz os requisitos. Clique em **Seguinte**.  

    ![Servidor de backup Azure - Verificação de pré-requisitos passado](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se o seu ambiente não cumprir os pré-requisitos necessários, as questões serão especificadas. Os pré-requisitos que não foram cumpridos também estão listados no DpmSetup.log. Resolva os erros pré-requisitos e, em seguida, executar **Check Again**. A instalação não pode prosseguir até que todos os pré-requisitos sejam cumpridos.

    ![Azure Backup Server - pré-requisitos de instalação não cumpridos](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. O Microsoft Azure Backup Server requer o Servidor SQL. O pacote de instalação do Servidor de Backup Azure vem agregado com os binários adequados do Servidor SQL. Se quiser utilizar a sua própria instalação SQL, pode. No entanto, a escolha recomendada é deixar o instalador adicionar uma nova instância de SQL Server. Para garantir que a sua escolha funciona com o seu ambiente, clique em **Verificar e Instalar**.

   > [!NOTE]
   > O Servidor de Backup Azure não funcionará com uma instância remota do Servidor SQL. A instância utilizada pelo Azure Backup Server deve ser local.
   >

    ![Servidor de backup Azure - Verificação de boas-vindas e pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Depois de verificar, se a máquina virtual tiver os pré-requisitos necessários para instalar o Servidor de Backup Azure, clique **em Next**.

    ![Servidor de backup Azure - Verificação de boas-vindas e pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, reinicie a máquina. Depois de reiniciar a máquina, reiniciar o instalador e, quando chegar ao ecrã de **Definições SQL,** clique **em 'Verificar' Novamente**.

5. Nas **Definições de Instalação,** forneça uma localização para a instalação de ficheiros do servidor de backup do Microsoft Azure e clique em **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    A localização do risco é necessária para voltar até Azure. Certifique-se de que o tamanho da localização do risco é equivalente a pelo menos 5% dos dados previstos para serem apoiados até ao Azure. Para a proteção do disco, os discos separados devem ser configurados assim que a instalação estiver concluída. Para mais informações sobre piscinas de armazenamento, consulte [piscinas de armazenamento configure e armazenamento em disco](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

6. No ecrã definições de **segurança,** forneça uma senha forte para contas restritas de utilizadores locais e clique em **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. No ecrã **Opt-In** da Microsoft Update, selecione se pretende utilizar o *Microsoft Update* para verificar se há atualizações e clica **em Next**.

   > [!NOTE]
   > Recomendamos que o Windows Update redirecione para o Microsoft Update, que oferece segurança e atualizações importantes para windows e outros produtos como o Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Reveja o *resumo das Definições* e clique em **Instalar**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando o Azure Backup Server terminar a instalação, o instalador lança imediatamente o instalador do agente microsoft Azure Recovery Services.

9. O instalador de serviços de recuperação do Microsoft Azure abre e verifica a conectividade da Internet. Se a conectividade da Internet estiver disponível, proceda à instalação. Se não houver conectividade, forneça detalhes proxy para ligar à Internet. Depois de especificar as definições de procuração, clique em **Seguinte**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Para instalar o Agente de Serviços de Recuperação do Microsoft Azure, clique em **Instalar**.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    O agente dos Serviços de Recuperação do Microsoft Azure, também chamado de agente de backup Azure, confunde o Servidor de Backup Azure para o cofre dos Serviços de Recuperação. Uma vez configurado, o Azure Backup Server irá sempre fazer backup de dados para o mesmo cofre dos Serviços de Recuperação.

11. Assim que o agente dos Serviços de Recuperação do Microsoft Azure terminar a instalação, clique em **Next** para iniciar a próxima fase: registar o Servidor de Backup Azure com o cofre dos Serviços de Recuperação.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    O instalador lança o **Assistente do Servidor de Registo**.

12. Mude para a sua subscrição Azure e para o seu cofre de Serviços de Recuperação. No menu **Prepare infraestruturas,** clique em **Baixar** para descarregar credenciais de cofre. Se o botão **Descarregamento** no passo 2 não estiver ativo, selecione **Já descarregado ou utilizando a mais recente instalação** do Servidor de Backup Azure para ativar o botão. As credenciais do cofre descarregam para o local onde armazena downloads. Esteja atento a este local porque vai precisar dele para o próximo passo.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. No menu **de Identificação do Cofre,** clique em **Navegar** para encontrar as credenciais do cofre dos Serviços de Recuperação.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    No diálogo **Select Vault Credentials,** vá ao local de descarregamento, selecione as credenciais do cofre e clique em **Open**.

    O caminho para as credenciais aparece no menu de Identificação do Cofre. Clique em **seguida** para avançar para a Definição de Encriptação.

14. No diálogo de Definição de **Encriptação,** forneça uma frase de passe para a encriptação de cópia de segurança e uma localização para armazenar a frase de passe e clique em **Next**.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Pode fornecer a sua própria frase de passe, ou usar o gerador de frases-passe para criar um para si. A palavra-passe é sua, e a Microsoft não guarda nem gere esta frase-passe. Para se preparar para um desastre, guarde a sua palavra-passe para um local acessível.

    Assim que clicar em **Seguinte,** o Servidor de Backup Azure está registado no cofre dos Serviços de Recuperação. O instalador continua a instalar o SQL Server e o Servidor de Backup Azure.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Quando o instalador estiver concluído, o Status mostra que todo o software foi instalado com sucesso.

    ![Servidor de backup Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Quando a instalação estiver concluída, a consola do Servidor de Backup Azure e os ícones PowerShell do Servidor de Backup Azure são criados no ambiente de trabalho do servidor.

## <a name="add-backup-storage"></a>Adicione armazenamento de reserva

A primeira cópia de reserva é mantida no armazenamento anexada à máquina do Servidor de Backup Azure. Para mais informações sobre a adição de discos, consulte [Adicionar armazenamento de backup moderno](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Tem de adicionar armazenamento de reserva mesmo que planeie enviar dados para o Azure. Na arquitetura Azure Backup Server, o cofre dos Serviços de Recuperação detém a *segunda* cópia dos dados enquanto o armazenamento local detém a primeira cópia de backup (e obrigatória).
>
>

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup Server requer conectividade com o serviço de backup Azure para que o produto funcione com sucesso. Para validar se a máquina tem a conectividade ```Get-DPMCloudConnection``` com o Azure, utilize o cmdlet na consola PowerShell do Servidor de Backup Azure. Se a saída do cmdlet for TRUE, então existe conectividade, caso contrário não há conectividade.

Ao mesmo tempo, a subscrição do Azure tem de estar num estado saudável. Para saber o estado da sua subscrição e geri-la, inscreva-se no portal de [subscrição](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Assim que souber o estado da conectividade Azure e da subscrição do Azure, pode utilizar a tabela abaixo para saber o impacto na funcionalidade de backup/restauro oferecida.

| Estado de Conectividade | Subscrição do Azure | Criar uma cópia de segurança no Azure | Voltar ao disco | Restaurar de Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Deprovisionado |Parada |Parada |Pontos de recuperação parados e Azure apagados |Parada |
| A conectividade perdida > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| A conectividade perdida > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| A conectividade perdida > 15 dias |Deprovisionado |Parada |Parada |Pontos de recuperação parados e Azure apagados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação da perda de conectividade

Se uma firewall ou um proxy estiver a impedir o acesso ao Azure, adicione os seguintes endereços de domínio na lista de permitir o perfil firewall/proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Uma vez que a conectividade com o Azure é restaurada ao Servidor de Backup Azure, o estado de subscrição do Azure determina as operações que podem ser realizadas. Uma vez **ligado**o servidor, utilize a tabela na [conectividade da Rede](backup-mabs-install-azure-stack.md#network-connectivity) para ver as operações disponíveis.

### <a name="handling-subscription-states"></a>Manipulação de estados de subscrição

É possível alterar uma subscrição Azure do estado *expirado* ou *dedprovisionado* para o estado *ativo.* Enquanto o estado de subscrição não estiver *ativo:*

- Enquanto uma subscrição é *dedprovisionada,* perde funcionalidade. Restaurar a subscrição do *Ative,* reaviva a funcionalidade de backup/restauro. Se os dados de backup no disco local foram retidos com um período de retenção suficientemente grande, esses dados de backup podem ser recuperados. No entanto, os dados de backup em Azure são irremediavelmente perdidos assim que a subscrição entra no estado *dedprovisionado.*
- Enquanto uma subscrição é *Expirada,* perde funcionalidade. As cópias de segurança programadas não funcionam enquanto uma subscrição é *expirada*.

## <a name="troubleshooting"></a>Resolução de problemas

Se o servidor de backup do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte o documento dos [códigos](https://support.microsoft.com/kb/3041338)de erro .
Também pode consultar [as FAQs relacionadas](backup-azure-backup-faq.md) com o Azure Backup

## <a name="next-steps"></a>Passos seguintes

O artigo, [Preparando o seu ambiente para DPM,](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)contém informações sobre configurações suportadas do Servidor de Backup Azure.

Pode utilizar os seguintes artigos para obter uma compreensão mais profunda da proteção da carga de trabalho utilizando o Microsoft Azure Backup Server.

- [Backup do Servidor SQL](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Backup do servidor SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Backup alternativo do servidor](backup-azure-alternate-dpm-server.md)
