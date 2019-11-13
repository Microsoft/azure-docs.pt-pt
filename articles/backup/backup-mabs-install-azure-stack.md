---
title: Instalar Servidor de Backup do Azure em Azure Stack | Microsoft Docs
description: Neste artigo, saiba como usar Servidor de Backup do Azure para proteger ou fazer backup de cargas de trabalho no Azure Stack.
author: dcurwin
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: dacurwin
ms.openlocfilehash: 7716fba7a16b393143ffb9c97e7203e15bc07d82
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012940"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalar Azure Backup Server no Azure Stack

Este artigo explica como instalar o Servidor de Backup do Azure no Azure Stack. Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de IaaS (infraestrutura como serviço), como máquinas virtuais em execução no Azure Stack. Uma vantagem de usar Servidor de Backup do Azure para proteger suas cargas de trabalho é você pode gerenciar toda a proteção de carga de trabalho de um único console.

> [!NOTE]
> Para saber mais sobre os recursos de segurança, consulte a [documentação sobre recursos de segurança de backup do Azure](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Azure Backup Server

O Servidor de Backup do Azure protege as seguintes cargas de trabalho Azure Stack máquina virtual.

| Fonte de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Canal semianual do Windows Server-Datacenter/empresa/Standard | Volumes, arquivos, pastas |
| Windows Server 2016-datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012 R2-Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012-datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2008 R2-Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| SQL Server 2016 | Base de Dados |
| SQL Server 2014 | Base de Dados |
| SQL Server 2012 SP1 | Base de Dados |
| SharePoint 2016 | Farm, banco de dados, front-end, servidor Web |
| SharePoint 2013 | Farm, banco de dados, front-end, servidor Web |
| SharePoint 2010 | Farm, banco de dados, front-end, servidor Web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente de Servidor de Backup do Azure

Considere as recomendações nesta seção ao instalar Servidor de Backup do Azure em seu ambiente de Azure Stack. O instalador do Servidor de Backup do Azure verifica se o seu ambiente tem os pré-requisitos necessários, mas você economizará tempo preparando-se antes de instalar o.

### <a name="determining-size-of-virtual-machine"></a>Determinando o tamanho da máquina virtual

Para executar Servidor de Backup do Azure em uma máquina virtual Azure Stack, use o tamanho a2 ou maior. Para obter ajuda na escolha de um tamanho de máquina virtual, baixe o [Azure Stack calculadora de tamanho de VM](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuais em máquinas virtuais Azure Stack

Todas as máquinas virtuais usadas em uma carga de trabalho de Azure Stack devem pertencer à mesma rede virtual do Azure e à assinatura do Azure.

### <a name="azure-backup-server-vm-performance"></a>Desempenho de VM Servidor de Backup do Azure

Se compartilhado com outras máquinas virtuais, o tamanho da conta de armazenamento e os limites de IOPS afetarão Servidor de Backup do Azure desempenho da VM. Por esse motivo, você deve usar uma conta de armazenamento separada para a máquina virtual Servidor de Backup do Azure. O agente de backup do Azure em execução na Servidor de Backup do Azure precisa de armazenamento temporário para:

- seu próprio uso (um local de cache),
- dados restaurados da nuvem (área de preparo local)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurando o armazenamento em disco temporário do backup do Azure

Cada máquina virtual Azure Stack vem com armazenamento temporário em disco, que está disponível para o usuário como `D:\`de volume. A área de preparação local necessária para o backup do Azure pode ser configurada para residir no `D:\`, e o local do cache pode ser colocado em `C:\`. Dessa forma, nenhum armazenamento precisa ser retirado dos discos de dados anexados à máquina virtual Servidor de Backup do Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenando dados de backup no disco local e no Azure

O Servidor de Backup do Azure armazena dados de backup em discos do Azure anexados à máquina virtual, para recuperação operacional. Depois que os discos e o espaço de armazenamento são anexados à máquina virtual, Servidor de Backup do Azure gerencia o armazenamento para você. A quantidade de armazenamento de dados de backup depende do número e do tamanho dos discos anexados a cada [máquina virtual Azure Stack](/azure-stack/user/azure-stack-storage-overview). Cada tamanho de Azure Stack VM tem um número máximo de discos que podem ser anexados à máquina virtual. Por exemplo, a2 é de quatro discos. A3 é de oito discos. A4 é 16 discos. Novamente, o tamanho e o número de discos determinam o pool de armazenamento de backup total.

> [!IMPORTANT]
> Você **não** deve manter os dados de recuperação operacional (backup) em discos anexados servidor de backup do Azure por mais de cinco dias.
>

O armazenamento de dados de backup no Azure reduz a infraestrutura de backup no Azure Stack. Se os dados tiverem mais de cinco dias, eles deverão ser armazenados no Azure.

Para armazenar dados de backup no Azure, crie ou use um cofre dos serviços de recuperação. Ao se preparar para fazer backup da carga de trabalho do Servidor de Backup do Azure, você [configura o cofre dos serviços de recuperação](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de backup é executado, um ponto de recuperação é criado no cofre. Cada cofre dos serviços de recuperação contém até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e por quanto tempo eles são mantidos, você pode reter os dados de backup por muitos anos. Por exemplo, você pode criar pontos de recuperação mensais e remantê-los por cinco anos.

### <a name="scaling-deployment"></a>Implantação em escala

Se você quiser dimensionar sua implantação, terá as seguintes opções:

- Escalar verticalmente – aumente o tamanho da máquina virtual Servidor de Backup do Azure de uma série para a D Series e aumente o armazenamento local de [acordo com as instruções da máquina virtual Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
- Descarregar dados – enviar dados mais antigos para o Azure e manter somente os dados mais recentes no armazenamento anexado ao Servidor de Backup do Azure.
- Escalar horizontalmente-adicione mais servidores de backup do Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3,5 SP1 ou superior deve estar instalado na máquina virtual.

### <a name="joining-a-domain"></a>Ingressando em um domínio

O Servidor de Backup do Azure máquina virtual deve ser ingressado em um domínio. Um usuário de domínio com privilégios de administrador deve instalar Servidor de Backup do Azure na máquina virtual.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Usando uma VM IaaS no Azure Stack

Ao escolher um servidor para Servidor de Backup do Azure, comece com uma imagem da galeria do Windows Server 2012 R2 Datacenter ou do Windows Server 2016 datacenter. O artigo [criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para introdução à máquina virtual recomendada. Os requisitos mínimos recomendados para a VM (máquina virtual) do servidor devem ser: a2 padrão com dois núcleos e 3,5 GB de RAM.

A proteção de cargas de trabalho com Servidor de Backup do Azure tem muitas nuances. O artigo [instalar o DPM como uma máquina virtual do Azure](https://technet.microsoft.com/library/jj852163.aspx), ajuda a explicar essas nuances. Antes de implantar o computador, leia este artigo completamente.

> [!NOTE]
> O Servidor de Backup do Azure foi projetado para ser executado em uma máquina virtual dedicada de finalidade única. Não é possível instalar o Servidor de Backup do Azure em:
>
> - Um computador em execução como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicações está instalada
> - Um computador no qual o Exchange Server está em execução
> - Um computador que é um nó de um cluster

Sempre ingresse Servidor de Backup do Azure em um domínio. Se você precisar mover Servidor de Backup do Azure para um domínio diferente, primeiro instale o Servidor de Backup do Azure e, em seguida, ingresse-o no novo domínio. Depois de implantar Servidor de Backup do Azure, você não poderá movê-lo para um novo domínio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento do cofre dos serviços de recuperação permite que você escolha entre o armazenamento com redundância geográfica e o armazenamento com redundância local. Por padrão, os cofres dos serviços de recuperação usam o armazenamento com redundância geográfica. Se esse cofre for seu cofre principal, deixe a opção de armazenamento definida como armazenamento com redundância geográfica. Escolha armazenamento com redundância local se desejar uma opção mais barata que seja menos durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione seu cofre para abrir o painel do cofre e o menu configurações. Se o menu **configurações** não abrir, clique em **todas as configurações** no painel do cofre.
2. No menu **configurações** , clique em **infraestrutura de backup** > **configuração de backup** para abrir o menu **configuração de backup** . No menu **configuração de backup** , escolha a opção de replicação de armazenamento para seu cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Baixar o instalador do Servidor de Backup do Azure

Há duas maneiras de baixar o instalador do Servidor de Backup do Azure. Você pode baixar o instalador do Servidor de Backup do Azure no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55269). Você também pode baixar Servidor de Backup do Azure Installer enquanto estiver configurando um cofre dos serviços de recuperação. As etapas a seguir orientarão você no download do instalador do portal do Azure ao configurar um cofre dos serviços de recuperação.

1. Em sua máquina virtual Azure Stack, [entre na sua assinatura do Azure no portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **todos os serviços**.

    ![Escolha a opção todos os serviços no menu principal](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Na caixa de diálogo **todos os serviços** , digite *serviços de recuperação*. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Depois de vê-lo, selecione **cofres dos serviços de recuperação**.

    ![Digite serviços de recuperação na caixa de diálogo todos os serviços](./media/backup-mabs-install-azure-stack/all-services.png)

    A lista de cofres dos serviços de recuperação na assinatura é exibida.

4. Na lista de cofres dos serviços de recuperação, selecione seu cofre para abrir seu painel.

    ![Digite serviços de recuperação na caixa de diálogo todos os serviços](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. No menu Introdução do cofre, clique em **backup** para abrir o assistente de introdução.

    ![Introdução ao backup](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    O menu backup é aberto.

    ![Backup-metas-padrão-abertas](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. No menu backup, no menu **onde está sua carga de trabalho em execução** , selecione **local**. No menu suspenso do **que você deseja fazer backup?** , selecione as cargas de trabalho que você deseja proteger usando servidor de backup do Azure. Se você não tiver certeza de quais cargas de trabalho selecionar, escolha **máquinas virtuais Hyper-V** e clique em **preparar infraestrutura**.

    ![locais e cargas de trabalho como metas](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    O menu **preparar infraestrutura** é aberto.

7. No menu **preparar infraestrutura** , clique em **baixar** para abrir uma página da Web para baixar servidor de backup do Azure arquivos de instalação.

    ![Alteração do assistente de Introdução](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A página da Web da Microsoft que hospeda os arquivos baixáveis para Servidor de Backup do Azure, é aberta.

8. Na página de download do Backup do Microsoft Azure Server, selecione um idioma e clique em **baixar**.

    ![O centro de download é aberto](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. O instalador do Servidor de Backup do Azure é composto por oito arquivos – um instalador e sete arquivos. bin. Verifique o **nome do arquivo** para selecionar todos os arquivos necessários e clique em **Avançar**. Baixe todos os arquivos na mesma pasta.

    ![Centro de download 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    O tamanho do download de todos os arquivos de instalação é maior que 3 GB. Em um link de download de 10 Mbps, o download de todos os arquivos de instalação pode levar até 60 minutos. Os arquivos são baixados para o local de download especificado.

## <a name="extract-azure-backup-server-install-files"></a>Extrair Servidor de Backup do Azure arquivos de instalação

Depois de baixar todos os arquivos para sua máquina virtual Azure Stack, vá para o local de download. A primeira fase de instalação do Servidor de Backup do Azure é extrair os arquivos.

![Centro de download 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Para iniciar a instalação, na lista de arquivos baixados, clique em **MicrosoftAzureBackupserverInstaller. exe**.

    > [!WARNING]
    > Pelo menos 4 GB de espaço livre é necessário para extrair os arquivos de instalação.
    >

2. No assistente de Servidor de Backup do Azure, clique em **Avançar** para continuar.

    ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Escolha o caminho para os arquivos de Servidor de Backup do Azure e clique em **Avançar**.

   ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifique o local de extração e clique em **extrair**.

   ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. O assistente extrai os arquivos e lê o processo de instalação.

   ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Quando o processo de extração for concluído, clique em **concluir**. Por padrão, **Execute setup. exe** é selecionado. Quando você clica em **concluir**, o setup. exe instala o backup do Microsoft Azure Server no local especificado.

   ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalar o pacote de software

Na etapa anterior, você clicou em **concluir** para sair da fase de extração e iniciar o assistente de instalação do servidor de backup do Azure.

![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Servidor de Backup do Azure compartilha código com Data Protection Manager. Você verá referências a Data Protection Manager e ao DPM no instalador do Servidor de Backup do Azure. Embora Servidor de Backup do Azure e Data Protection Manager sejam produtos separados, esses produtos estão fortemente relacionados.

1. Para iniciar o assistente de instalação, clique em **backup do Microsoft Azure Server**.

   ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na tela de **boas-vindas** , clique em **Avançar**.

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na tela **verificações de pré-requisitos** , clique em **verificar** para determinar se os pré-requisitos de hardware e software para servidor de backup do Azure foram atendidos.

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se o seu ambiente tiver os pré-requisitos necessários, você verá uma mensagem indicando que o computador atende aos requisitos. Clique em **Seguinte**.  

    ![Servidor de Backup do Azure-verificação de pré-requisitos aprovada](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se o seu ambiente não atender aos pré-requisitos necessários, os problemas serão especificados. Os pré-requisitos que não foram atendidos também estão listados no DpmSetup. log. Resolva os erros de pré-requisito e execute **verificar novamente**. A instalação não pode continuar até que todos os pré-requisitos sejam atendidos.

    ![Servidor de Backup do Azure-pré-requisitos de instalação não atendidos](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. O Backup do Microsoft Azure Server requer SQL Server. O pacote de instalação do Servidor de Backup do Azure vem agrupado com os binários de SQL Server apropriados. Se você quiser usar sua própria instalação do SQL, poderá. No entanto, a opção recomendada é permitir que o instalador adicione uma nova instância do SQL Server. Para garantir que sua escolha funcione com seu ambiente, clique em **verificar e instalar**.

   > [!NOTE]
   > Servidor de Backup do Azure não funcionará com uma instância de SQL Server remota. A instância usada pelo Servidor de Backup do Azure deve ser local.
   >

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Após a verificação, se a máquina virtual tiver os pré-requisitos necessários para instalar Servidor de Backup do Azure, clique em **Avançar**.

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se ocorrer uma falha com uma recomendação para reiniciar o computador, reinicie o computador. Depois de reiniciar o computador, reinicie o instalador e, quando chegar à tela de **configurações do SQL** , clique em **verificar novamente**.

5. Nas **configurações de instalação**, forneça um local para a instalação de arquivos do backup do Microsoft Azure Server e clique em **Avançar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    O local de rascunho é necessário para fazer backup no Azure. Verifique se o tamanho do local de rascunho é equivalente a pelo menos 5% dos dados planejados para backup no Azure. Para proteção de disco, discos separados precisam ser configurados após a conclusão da instalação. Para obter mais informações sobre pools de armazenamento, consulte [configurar pools de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

6. Na tela **configurações de segurança** , forneça uma senha forte para contas de usuário locais restritas e clique em **Avançar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na tela **Microsoft Update aceitar** , selecione se deseja usar *Microsoft Update* para verificar se há atualizações e clique em **Avançar**.

   > [!NOTE]
   > É recomendável ter Windows Update redirecionar para Microsoft Update, que oferece atualizações de segurança e importantes para o Windows e outros produtos, como o Backup do Microsoft Azure Server.
   >

    ![Backup do Microsoft Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Examine o *Resumo das configurações* e clique em **instalar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando Servidor de Backup do Azure concluir a instalação, o instalador iniciará imediatamente o instalador do agente de Serviços de Recuperação do Microsoft Azure.

9. O instalador do agente de Serviços de Recuperação do Microsoft Azure é aberto e verifica a conectividade com a Internet. Se a conectividade com a Internet estiver disponível, prossiga com a instalação. Se não houver conectividade, forneça detalhes de proxy para se conectar à Internet. Depois de especificar as configurações de proxy, clique em **Avançar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Para instalar o agente de Serviços de Recuperação do Microsoft Azure, clique em **instalar**.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    O agente de Serviços de Recuperação do Microsoft Azure, também chamado de agente de backup do Azure, configura o Servidor de Backup do Azure para o cofre dos serviços de recuperação. Uma vez configurado, Servidor de Backup do Azure sempre fará backup dos dados no mesmo cofre dos serviços de recuperação.

11. Depois que o agente de Serviços de Recuperação do Microsoft Azure concluir a instalação, clique em **Avançar** para iniciar a próxima fase: registrar servidor de backup do Azure com o cofre dos serviços de recuperação.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    O instalador inicia o **Assistente para registrar servidor**.

12. Alterne para sua assinatura do Azure e seu cofre de serviços de recuperação. No menu **preparar infraestrutura** , clique em **baixar** para baixar as credenciais do cofre. Se o botão **baixar** na etapa 2 não estiver ativo, selecione **já baixado ou usando a instalação mais recente do servidor de backup do Azure** para ativar o botão. As credenciais do cofre são baixadas para o local onde você armazena downloads. Lembre-se desse local porque você precisará dele para a próxima etapa.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. No menu de **identificação do cofre** , clique em **procurar** para localizar as credenciais do cofre dos serviços de recuperação.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Na caixa de diálogo **selecionar credenciais do cofre** , vá para o local de download, selecione as credenciais do cofre e clique em **abrir**.

    O caminho para as credenciais é exibido no menu de identificação do cofre. Clique em **Avançar** para avançar para a configuração de criptografia.

14. Na caixa de diálogo **configuração de criptografia** , forneça uma senha para a criptografia de backup e um local para armazenar a senha e clique em **Avançar**.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Você pode fornecer sua própria senha ou usar o gerador de frase secreta para criar uma para você. A frase secreta é sua e a Microsoft não salva nem gerencia essa frase secreta. Para se preparar para um desastre, salve sua senha em um local acessível.

    Depois de clicar em **Avançar**, a servidor de backup do Azure será registrada no cofre dos serviços de recuperação. O instalador continua instalando SQL Server e o Servidor de Backup do Azure.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Quando o instalador for concluído, o status mostrará que todo o software foi instalado com êxito.

    ![Servidor de Backup do Azure PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Quando a instalação for concluída, o console do Servidor de Backup do Azure e os ícones do Servidor de Backup do Azure PowerShell serão criados na área de trabalho do servidor.

## <a name="add-backup-storage"></a>Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado à máquina Servidor de Backup do Azure. Para obter mais informações sobre como adicionar discos, consulte [Adicionar armazenamento de backup moderno](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Você precisa adicionar armazenamento de backup mesmo se planeja enviar dados para o Azure. Na arquitetura de Servidor de Backup do Azure, o cofre dos serviços de recuperação mantém a *segunda* cópia dos dados, enquanto o armazenamento local mantém a primeira cópia (e obrigatória) de backup.
>
>

## <a name="network-connectivity"></a>Conectividade de rede

Servidor de Backup do Azure requer conectividade com o serviço de backup do Azure para que o produto funcione com êxito. Para validar se o computador tem a conectividade com o Azure, use o cmdlet ```Get-DPMCloudConnection``` no console do Servidor de Backup do Azure PowerShell. Se a saída do cmdlet for TRUE, a conectividade existirá, caso contrário, não haverá conectividade.

Ao mesmo tempo, a assinatura do Azure precisa estar em um estado íntegro. Para descobrir o estado da sua assinatura e gerenciá-la, entre no portal de [assinatura](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Depois de saber o estado da conectividade do Azure e da assinatura do Azure, você pode usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Subscrição do Azure | Fazer backup no Azure | Fazer backup em disco | Restaurar do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Desprovisionada |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |
| Perda de conectividade > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Desprovisionada |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperando da perda de conectividade

Se um firewall ou um proxy estiver impedindo o acesso ao Azure, adicione os seguintes endereços de domínio na lista de permissões do perfil de firewall/proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Quando a conectividade com o Azure é restaurada para o Servidor de Backup do Azure, o estado de assinatura do Azure determina as operações que podem ser executadas. Quando o servidor estiver **conectado**, use a tabela em [conectividade de rede](backup-mabs-install-azure-stack.md#network-connectivity) para ver as operações disponíveis.

### <a name="handling-subscription-states"></a>Manipulando Estados de assinatura

É possível alterar uma assinatura do Azure do estado *expirado* ou *desprovisionado* para o estado *ativo* . Enquanto o estado da assinatura não estiver *ativo*:

- Embora uma assinatura seja *desprovisionada*, ela perde a funcionalidade. A restauração da assinatura no *Active, ativa*a funcionalidade de backup/restauração. Se os dados de backup no disco local tiverem sido retidos com um período de retenção suficientemente grande, os dados de backup poderão ser recuperados. No entanto, os dados de backup no Azure ficam irrecuperáveis quando a assinatura entra no estado *desprovisionado* .
- Enquanto uma assinatura *expira*, ela perde a funcionalidade. Os backups agendados não são executados enquanto uma assinatura *expira*.

## <a name="troubleshooting"></a>Resolução de problemas

Se Backup do Microsoft Azure servidor falhar com erros durante a fase de instalação (ou backup ou restauração), consulte o [documento códigos de erro](https://support.microsoft.com/kb/3041338).
Você também pode consultar as [perguntas frequentes relacionadas ao backup do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes

O artigo [preparando seu ambiente para o DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)contém informações sobre as configurações de servidor de backup do Azure com suporte.

Você pode usar os artigos a seguir para obter uma compreensão mais profunda da proteção de carga de trabalho usando o Backup do Microsoft Azure Server.

- [SQL Server Backup](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Backup do SharePoint Server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Backup de servidor alternativo](backup-azure-alternate-dpm-server.md)
