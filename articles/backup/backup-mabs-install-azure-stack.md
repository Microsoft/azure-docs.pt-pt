---
title: Instalar o Azure Backup Server no Azure Stack | Documentos da Microsoft
description: Utilize o Azure Backup Server para proteger ou fazer cópia de segurança de cargas de trabalho no Azure Stack.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d3a2ffdedda7f541fb1a3f37a8b40bc7af3dcb57
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996514"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalar Azure Backup Server no Azure Stack

Este artigo explica como instalar o Azure Backup Server no Azure Stack. Com o servidor de cópia de segurança do Azure, pode proteger a infraestrutura como um cargas de trabalho do serviço (IaaS), como máquinas virtuais em execução no Azure Stack. Uma vantagem de utilizar o Azure Backup Server para proteger as cargas de trabalho é que pode gerenciar a proteção de todas as cargas de trabalho de um único console.

> [!NOTE]
> Para saber mais sobre as capacidades de segurança, consulte [documentação de funcionalidades de segurança do Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Azure Backup Server
O servidor de cópia de segurança do Azure protege as cargas de trabalho de máquina virtual seguintes do Azure Stack.

| Origem de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Windows Server via Semianuais - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
| SQL Server 2016 | Base de Dados |
| SQL Server 2014 | Base de Dados |
| SQL Server 2012 SP1 | Base de Dados |
| SharePoint 2016 | Farm, base de dados, front-end, servidor web |
| SharePoint 2013 | Farm, base de dados, front-end, servidor web |
| SharePoint 2010 | Farm, base de dados, front-end, servidor web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente de servidor de cópia de segurança do Azure

Ao instalar o servidor de cópia de segurança do Azure no seu ambiente do Azure Stack, considere as recomendações nesta secção. O instalador do servidor de cópia de segurança do Azure verifica que o seu ambiente tem os pré-requisitos necessários, mas economizará tempo com a preparação antes de instalar.

### <a name="determining-size-of-virtual-machine"></a>Determinar o tamanho da máquina virtual
Para executar o servidor de cópia de segurança do Azure numa máquina virtual do Azure Stack, utilize o tamanho A2 ou superior. Para obter assistência na escolha um tamanho de máquina virtual, transfira o [Calculadora do tamanho da VM do Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuais em máquinas de virtuais do Azure Stack
Todas as máquinas virtuais utilizadas numa carga de trabalho do Azure Stack tem de pertencer à mesma rede virtual do Azure e subscrição do Azure.

### <a name="azure-backup-server-vm-performance"></a>Desempenho da VM do servidor de cópia de segurança do Azure
Se forem partilhados com outras máquinas virtuais, a conta de armazenamento tamanho e o impacto de limites IOPS o desempenho da VM de servidor de cópia de segurança do Azure. Por esse motivo, deve utilizar uma conta de armazenamento separada para a máquina virtual do servidor de cópia de segurança do Azure. O agente de cópia de segurança do Azure em execução no servidor de cópia de segurança do Azure precisa de armazenamento temporário para:
- uso próprio (uma localização da cache),
- dados restaurados a partir da cloud (área de transição local)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar o armazenamento de disco temporário de cópia de segurança do Azure
Cada máquina virtual do Azure Stack é fornecido com o armazenamento de disco temporário, que está disponível para o utilizador como volume `D:\`. A área de transição local necessária pelo Azure Backup pode ser configurada para estar situada em `D:\`, e a localização da cache pode ser colocada em `C:\`. Dessa forma, sem armazenamento tem de ser criadas na direção oposta os discos de dados ligados à máquina virtual do Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenamento de dados de cópia de segurança no disco local e no Azure
Servidor de cópia de segurança do Azure armazena dados de cópia de segurança nos discos Azure ligados à máquina virtual, para recuperação operacional. Assim que os discos e o espaço de armazenamento estão anexados à máquina virtual, o servidor de cópia de segurança do Azure gere o armazenamento para si. A quantidade de armazenamento de dados de cópia de segurança depende do número e tamanho dos discos anexados a cada [máquinas de virtuais do Azure Stack](/azure-stack/user/azure-stack-storage-overview). Cada tamanho de VM do Azure Stack tem um número máximo de discos que podem ser anexados à máquina virtual. Por exemplo, A2 é quatro discos. A3 é oito discos. A4 é 16 discos. Novamente, o tamanho e número de discos determina o agrupamento de armazenamento de cópia de segurança total.

> [!IMPORTANT]
> Deve **não** manter os dados de recuperação operacional (cópia de segurança) em discos ligado ao servidor de cópia de segurança do Azure por mais de cinco dias.
>

Armazenar dados de cópia de segurança no Azure reduz a infraestrutura de cópia de segurança no Azure Stack. Se os dados são mais de cinco dias, devem ser armazenado no Azure.

Para armazenar dados de cópia de segurança no Azure, criar ou utilizar um cofre dos serviços de recuperação. Ao se preparar para criar cópias de segurança da carga de trabalho do servidor de cópia de segurança do Azure, [configurar o Cofre dos serviços de recuperação](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Uma vez configurado, sempre que for executada uma tarefa de cópia de segurança, é criado um ponto de recuperação no cofre. Cada Cofre de serviços de recuperação contém até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e o período de tempo são mantidos, pode manter os dados de cópia de segurança por muitos anos. Por exemplo, pode criar pontos de recuperação mensal e mantê-los durante cinco anos.
 
### <a name="scaling-deployment"></a>Dimensionar implementação
Se quiser dimensionar a sua implementação, tem as seguintes opções:
  - Aumentar verticalmente – aumente o tamanho da máquina virtual do Azure Backup Server de uma série de série D e aumentar o armazenamento local [acordo com as instruções de máquina virtual do Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
  - Descarregar dados – envia os dados antigos para o Azure e mantém apenas os dados mais recentes no armazenamento ligado para o servidor de cópia de segurança do Azure.
  - Aumente horizontalmente – adicionar mais servidores de cópia de segurança do Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 SP1 ou superior tem de estar instalado na máquina virtual.

### <a name="joining-a-domain"></a>Associação a um domínio

A máquina virtual do servidor de cópia de segurança do Azure têm de ser associada a um domínio. Um utilizador de domínio com privilégios de administrador tem de instalar o servidor de cópia de segurança do Azure na máquina virtual.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Utilizar uma VM de IaaS no Azure Stack

Ao escolher um servidor para servidor de cópia de segurança do Azure, comece com uma imagem de galeria do Datacenter do Windows Server 2012 R2 ou Windows Server 2016 Datacenter. O artigo [criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar a trabalhar com o recomendado da máquina virtual. Os requisitos mínimos recomendados para a máquina do servidor virtual (VM) devem ser: Standard a2 com dois núcleos e 3.5 GB de RAM.

Proteger cargas de trabalho com o Azure Backup Server tem muitos nuances. O artigo [instalar o DPM como máquina virtual do Azure](https://technet.microsoft.com/library/jj852163.aspx), ajuda a explica tais nuances. Antes de implementar a máquina, leia este artigo completamente.

> [!NOTE]
> O Azure Backup Server foi concebido para ser executado numa máquina virtual dedicada de finalidade única. Não é possível instalar o servidor de cópia de segurança do Azure em:
> - Um computador em execução como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicações está instalada
> - Um computador no qual o Exchange Server está em execução
> - Um computador que é um nó de um cluster

Sempre associe o servidor de cópia de segurança do Azure a um domínio. Se precisar de mover o servidor de cópia de segurança do Azure para um domínio diferente, primeiro instalar o servidor de cópia de segurança do Azure, em seguida, associe-o para o novo domínio. Depois de implementar o servidor de cópia de segurança do Azure, não é possível movê-lo para um novo domínio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento de Cofre de serviços de recuperação permite-lhe escolher entre o armazenamento com redundância geográfica e armazenamento localmente redundante. Por predefinição, os cofres dos serviços de recuperação utilizam o armazenamento georredundante. Se este cofre é o Cofre primário, deixe a opção de armazenamento definida para armazenamento georredundante. Escolha armazenamento localmente redundante se pretender que uma opção mais barata que é menos durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o cofre para abrir o dashboard do cofre e o menu de definições. Se o **configurações** menu não abre, clique em **todas as definições** no dashboard do cofre.
2. Na **definições** menu, clique em **infraestrutura de cópia de segurança** > **configuração de cópia de segurança** para abrir o **configuração de cópia de segurança**menu. Sobre o **configuração de cópia de segurança** menu, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Transferir o instalador do servidor de cópia de segurança do Azure

Existem duas formas para transferir o instalador do servidor de cópia de segurança do Azure. Pode transferir o instalador de servidor de cópia de segurança do Azure a partir da [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Também pode transferir o instalador do servidor de cópia de segurança do Azure à medida que está a configurar um cofre dos serviços de recuperação. Os seguintes passos guiá-lo por meio de transferir o instalador do portal do Azure ao configurar um cofre dos serviços de recuperação.

1. Da sua máquina virtual do Azure Stack, [iniciar sessão na sua subscrição do Azure no portal do Azure](https://portal.azure.com/).
2. No menu da esquerda, selecione **todos os serviços**.

    ![Escolha a opção de todos os serviços no menu principal](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Na **todos os serviços** caixa de diálogo, escreva *dos serviços de recuperação*. Quando começar a escrever, sua entrada filtra a lista de recursos. Quando o vir, selecione **cofres dos serviços de recuperação**.

    ![Escreva os serviços de recuperação na caixa de diálogo todos os serviços](./media/backup-mabs-install-azure-stack/all-services.png)

    É apresentada a lista de cofres de serviços de recuperação na subscrição.

4. Na lista de cofres dos serviços de recuperação, selecione o cofre para abrir o dashboard.

    ![Escreva os serviços de recuperação na caixa de diálogo todos os serviços](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. No menu do Cofre de introdução, clique em **cópia de segurança** para abrir o Assistente de introdução.

    ![Cópia de segurança de introdução](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    É aberto o menu de cópia de segurança.

    ![Aberto de cópia de segurança objetivos padrão](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. No menu de cópia de segurança, do **em que a sua carga de trabalho é executado** menu, selecione **locais**. Do **o que fazer quiser a cópia de segurança?** menu pendente, selecione as cargas de trabalho que pretende proteger ao utilizar o servidor de cópia de segurança do Azure. Se não tiver a certeza que cargas de trabalho para selecionar, escolha **máquinas de virtuais de Hyper-V** e, em seguida, clique em **preparar infraestrutura**.

    ![cargas de trabalho como objetivos e no local](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    O **preparar a infraestrutura** é aberto o menu.

7. Na **preparar infraestrutura** menu, clique em **transferir** para abrir uma página da web para transferir ficheiros de instalação do servidor de cópia de segurança do Azure.

    ![Introdução a alteração do Assistente de introdução](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A página web da Microsoft que aloja os ficheiros que pode ser baixados para o servidor de cópia de segurança do Azure, é aberto.

8. Na página de download do Microsoft Azure Backup Server, selecione um idioma e clique em **transferir**.

    ![Abre-se do Centro de download](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. O instalador do servidor de cópia de segurança do Azure é composta por oito ficheiros - um instalador e sete arquivos. bin. Verifique **nome de ficheiro** para selecionar todos os ficheiros necessários e clique em **próxima**. Baixe todos os ficheiros na mesma pasta.

    ![1 do Centro de transferências](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    O tamanho do download de todos os ficheiros de instalação é mais de 3 GB. Num download de 10 Mbps link, download de todos os arquivos de instalação pode demorar até 60 minutos. Transferem os ficheiros para a sua localização de transferência especificada.

## <a name="extract-azure-backup-server-install-files"></a>Extrair os ficheiros de instalação do servidor de cópia de segurança do Azure

Depois de baixar todos os ficheiros à máquina virtual do Azure Stack, vá para a localização de transferência. É a primeira fase de instalação do servidor de cópia de segurança do Azure extrair os ficheiros.

![1 do Centro de transferências](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Para iniciar a instalação, na lista de ficheiros transferidos, clique em **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Pelo menos 4GB de espaço livre é necessário para extrair os ficheiros de configuração.
    >

2. No Assistente do servidor de cópia de segurança do Azure, clique em **seguinte** para continuar.

    ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Escolha o caminho para os ficheiros do servidor de cópia de segurança do Azure e clique em **seguinte**.

   ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifique se a localização de extração e clique em **extrair**.

   ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. O assistente extrai os ficheiros e prepara o processo de instalação.

   ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Assim que tiver concluído o processo de extração, clique em **concluir**. Por predefinição, **executar setup.exe** está selecionada. Quando clica em **concluir**, Setup.exe instala o Microsoft Azure Backup Server para a localização especificada.

   ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalar o pacote de software

No passo anterior, clicou **concluir** para sair da fase de extração e iniciar o Assistente de configuração do servidor de cópia de segurança do Azure.

![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

O Azure Backup Server compartilha o código com o Data Protection Manager. Verá referências a Data Protection Manager e o DPM no instalador do servidor de cópia de segurança do Azure. Embora o Azure Backup Server e Data Protection Manager são produtos separados, esses produtos estão intimamente relacionados.

1. Para iniciar o Assistente de configuração, clique em **Microsoft Azure Backup Server**.

   ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Sobre o **bem-vindo** ecrã, clique em **próxima**.

    ![Servidor de cópia de segurança do Azure - pré-requisitos e bem-vindo de verificação](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Sobre o **verificações de pré-requisitos** ecrã, clique em **verificar** para determinar se foram cumpridos os pré-requisitos de hardware e software para o servidor de cópia de segurança do Azure.

    ![Servidor de cópia de segurança do Azure - pré-requisitos e bem-vindo de verificação](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se o ambiente tiver os pré-requisitos necessários, verá uma mensagem a indicar que o computador cumpre os requisitos. Clique em **Seguinte**.  

    ![Servidor de cópia de segurança do Azure - verificação de pré-requisitos passada](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se o seu ambiente não cumpre os pré-requisitos necessários, os problemas serão especificados. Os pré-requisitos que não foram cumpridos também estão listados no Dpmsetup. Resolva os erros de pré-requisitos e, em seguida, execute **verificar novamente**. Não é possível continuar a instalação até que todos os pré-requisitos são cumpridos.

    ![Servidor de cópia de segurança do Azure - pré-requisitos de instalação não cumpridos](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server requer o SQL Server. O pacote de instalação do servidor de cópia de segurança do Azure vem agrupado com os binários do SQL Server apropriados. Se pretender utilizar a sua própria instalação do SQL, pode. No entanto, a opção recomendada é permitir que o instalador adicionar uma nova instância do SQL Server. Para garantir a sua escolha funciona com o seu ambiente, clique em **verificar e instalar**.

   > [!NOTE]
   > O Azure Backup Server não irá funcionar com uma instância remota do SQL Server. A instância utilizada pelo servidor de cópia de segurança do Azure tem de ser local.
   >

    ![Servidor de cópia de segurança do Azure - pré-requisitos e bem-vindo de verificação](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Depois de verificar, se a máquina virtual tem os pré-requisitos necessários para instalar o servidor de cópia de segurança do Azure, clique em **seguinte**.

    ![Servidor de cópia de segurança do Azure - pré-requisitos e bem-vindo de verificação](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, em seguida, reinicie a máquina. Depois de reiniciar a máquina, reinicie o instalador, e quando vai para o **definições do SQL** ecrã, clique em **verificar novamente**.

5. Na **definições de instalação**, forneça uma localização para a instalação dos ficheiros do servidor de cópia de segurança do Microsoft Azure e clique em **próxima**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    A localização de rascunho é necessário para criar cópias de segurança para o Azure. Certifique-se de que o tamanho da localização de rascunho é equivalente a, pelo menos, 5% dos dados em planeadas a cópia de segurança para o Azure. Para a proteção de disco, discos separados tem de ser configurado depois de concluída a instalação. Para obter mais informações sobre agrupamentos de armazenamento, consulte [configurar agrupamentos de armazenamento e armazenamento no disco](https://technet.microsoft.com/library/hh758075.aspx).

6. Sobre o **definições de segurança** ecrã, forneça uma palavra-passe forte para contas de usuário local restritas e clique em **próxima**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na **Microsoft Update participação ativa-In** ecrã, selecione se pretende utilizar *Microsoft Update* para procurar atualizações e clique em **seguinte**.

   > [!NOTE]
   > Recomendamos ter a atualização do Windows redirecionar para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos, como o Microsoft Azure Backup Server.
   >

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Reveja os *resumo de definições* e clique em **instalar**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando o servidor de cópia de segurança do Azure conclui a instalação, o instalador inicia imediatamente o instalador do agente dos serviços de recuperação do Microsoft Azure.

9. O abre-se de instalador de agente de serviços de recuperação do Microsoft Azure e verifica a existência de conectividade à Internet. Se a conectividade à Internet está disponível, continue com a instalação. Se não há nenhuma conectividade, forneça detalhes do proxy para estabelecer ligação à Internet. Depois de especificar as definições de proxy, clique em **seguinte**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Para instalar o agente de serviços de recuperação do Microsoft Azure, clique em **instalar**.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    O agente dos serviços de recuperação do Microsoft Azure, também chamado do agente de cópia de segurança do Azure, configura o servidor de cópia de segurança do Azure para o Cofre dos serviços de recuperação. Uma vez configurado, servidor de cópia de segurança do Azure será sempre cópia de segurança de dados no mesmo cofre dos serviços de recuperação.

11. Assim que o agente dos serviços de recuperação do Microsoft Azure conclui a instalação, clique em **seguinte** para iniciar a próxima fase: registar o servidor de cópia de segurança do Azure com o Cofre dos serviços de recuperação.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    O instalador é iniciada a **Assistente de registo do servidor**.

12. Mude para a sua subscrição do Azure e o seu Cofre dos serviços de recuperação. Na **preparar infraestrutura** menu, clique em **transferir** para transferir as credenciais do cofre. Se o **baixe** botão no passo 2 não é o Active Directory, selecione **já transferido ou a utilizar a instalação de servidor de cópia de segurança do Azure mais recente** para ativar o botão. Transferem as credenciais do cofre para a localização onde armazenar downloads. Tenha em atenção nesta localização porque ele será necessário para a próxima etapa.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Na **identificação do cofre** menu, clique em **procurar** para encontrar as credenciais do cofre dos serviços de recuperação.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Na **Selecione as credenciais do cofre** caixa de diálogo, vá para a localização de transferência, selecione as suas credenciais do cofre e clique em **aberto**.

    O caminho para as credenciais é apresentada no menu de identificação do cofre. Clique em **seguinte** para avançar para a definição de encriptação.

14. Na **definição de encriptação** caixa de diálogo, forneça uma frase de acesso para a encriptação de cópia de segurança e uma localização para armazenar a frase de acesso e clique em **próxima**.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Pode fornecer seu próprio frase de acesso, ou usar o gerador de frase de acesso para criar um para si. A frase de acesso é sua, e a Microsoft não guardar nem gerir esta frase de acesso. Para preparar um desastre, guarde a frase de acesso numa localização acessível.

    Assim que clicar em **seguinte**, o servidor de cópia de segurança do Azure está registrado com o Cofre dos serviços de recuperação. O instalador continua a instalação do SQL Server e o servidor de cópia de segurança do Azure.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Quando o instalador concluir, o estado mostra de que todo o software foi instalado com êxito.

    ![O Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Quando concluir a instalação, a consola do servidor de cópia de segurança do Azure e os ícones do PowerShell de servidor de cópia de segurança do Azure são criados no ambiente de trabalho de servidor.

## <a name="add-backup-storage"></a>Adicionar armazenamento de cópia de segurança

A primeira cópia de segurança é mantida no armazenamento ligado à máquina do servidor de cópia de segurança do Azure. Para obter mais informações sobre como adicionar discos, consulte [armazenamento de cópia de segurança moderno adicionar](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Tem de adicionar armazenamento de cópia de segurança, mesmo que pretenda enviar dados para o Azure. A arquitetura de servidor de cópia de segurança do Azure, os serviços de recuperação cofre retenções a *segundo* cópia dos dados enquanto o armazenamento local contém a cópia de segurança do primeira (e obrigatória).
>
>

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup Server precisa de conectividade para o serviço de cópia de segurança do Azure para o produto a funcionar com êxito. Para validar se o computador tem conectividade para o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola do PowerShell de servidor de cópia de segurança do Azure. Se a saída do cmdlet é TRUE, então existe conectividade, caso contrário não existe nenhuma conectividade.

Ao mesmo tempo, a subscrição do Azure tem de estar em bom estado de funcionamento. Para obter informações sobre o estado da sua subscrição e geri-lo, inicie sessão para o [portal de subscrições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Sabe o estado da conetividade do Azure e da subscrição do Azure, pode utilizar a tabela abaixo para obter informações sobre o impacto sobre a funcionalidade de cópia de segurança/restauro fornecida.

| Estado de conectividade | Subscrição do Azure | Criar uma cópia de segurança do Azure | Criar cópias de segurança em disco | Restaurar a partir do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligado |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligado |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligado |Desaprovisionamento |Parada |Parada |Pontos de recuperação de parada e do Azure eliminados |Parada |
| Perda de conectividade > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Desaprovisionamento |Parada |Parada |Pontos de recuperação de parada e do Azure eliminados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperar da perda de conectividade

Se uma firewall ou um proxy está a impedir acesso para o Azure, aborda o domínio seguinte da lista de permissões no perfil de firewall/proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Depois de conectividade para o Azure é restaurada para o servidor de cópia de segurança do Azure, o estado da subscrição do Azure determina as operações que podem ser executadas. Assim que o servidor estiver **ligado**, utilize a tabela na [conectividade de rede](backup-mabs-install-azure-stack.md#network-connectivity) para ver as operações disponíveis.

### <a name="handling-subscription-states"></a>Manipulação de Estados de subscrição

É possível alterar uma subscrição do Azure partir *expirado* ou *desaprovisionada* estado para *Active* estado. Embora o estado da subscrição não seja *Active Directory*:

- Embora seja uma subscrição *desaprovisionada*, ele perde a funcionalidade. Restaurar a subscrição que pretende *Active Directory*, revives a funcionalidade de cópia de segurança/restauro. Se os dados de cópia de segurança no disco local foi mantidos com um período de retenção suficientemente grande, que podem ser obtidos dados de cópia de segurança. No entanto, os dados de cópia de segurança no Azure serão irremediavelmente perdidos depois de insere a subscrição do *desaprovisionada* estado.
- Embora seja uma subscrição *expirado*, ele perde a funcionalidade. Cópias de segurança agendadas não são executados enquanto a subscrição for *expirado*.

## <a name="troubleshooting"></a>Resolução de problemas

Se o Microsoft Azure Backup server falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte a [documento de códigos de erro](https://support.microsoft.com/kb/3041338).
Também pode consultar [FAQs relacionados com a cópia de segurança do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos Seguintes

O artigo [preparar o ambiente para o DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), contém informações sobre configurações suportadas do servidor de cópia de segurança do Azure.

Pode utilizar os seguintes artigos para obter uma compreensão mais aprofundada de proteção de carga de trabalho com o Microsoft Azure Backup Server.

- [Cópia de segurança do SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Cópia de segurança do SharePoint server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Cópia de segurança do servidor alternativo](backup-azure-alternate-dpm-server.md)
