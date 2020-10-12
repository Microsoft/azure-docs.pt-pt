---
title: Instalar Azure Backup Server no Azure Stack
description: Neste artigo, aprenda a usar o Azure Backup Server para proteger ou fazer backup de cargas de trabalho em Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 7153e2ff03a4f78ee1cc92ca04054fb2955d11a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970237"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalar Azure Backup Server no Azure Stack

Este artigo explica como instalar o Servidor de Backup Azure no Azure Stack. Com o Azure Backup Server, pode proteger a Infraestrutura como uma carga de trabalho de serviço (IaaS), como máquinas virtuais em funcionamento em Azure Stack. Um benefício da utilização do Azure Backup Server para proteger as suas cargas de trabalho é que pode gerir toda a proteção da carga de trabalho a partir de uma única consola.

> [!NOTE]
> Para saber mais sobre as capacidades de segurança, consulte a [documentação das funcionalidades de segurança do Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Azure Backup Server

O Azure Backup Server protege as seguintes cargas de trabalho de máquina virtual Azure Stack.

| Origem de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Windows Server Semi Canal Anual - Datacenter/Enterprise/Standard | Volumes, ficheiros, pastas |
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

Considere as recomendações nesta secção ao instalar o Azure Backup Server no seu ambiente Azure Stack. O instalador do Servidor de Backup Azure verifica se o seu ambiente possui os pré-requisitos necessários, mas poupará tempo preparando-se antes de instalar.

### <a name="determining-size-of-virtual-machine"></a>Determinação do tamanho da máquina virtual

Para executar o Servidor de Backup Azure numa máquina virtual Azure Stack, utilize o tamanho A2 ou maior. Para obter assistência na escolha de um tamanho de máquina virtual, descarregue a calculadora de [tamanhoS VM Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuais em máquinas virtuais Azure Stack

Todas as máquinas virtuais utilizadas numa carga de trabalho Azure Stack devem pertencer à mesma rede virtual Azure e à Subscrição Azure.

### <a name="azure-backup-server-vm-performance"></a>Desempenho do VM do servidor de backup Azure

Se partilhado com outras máquinas virtuais, o tamanho da conta de armazenamento e os limites do IOPS impactam o desempenho do VM do Servidor de Backup Azure. Por esta razão, deverá utilizar uma conta de armazenamento separada para a máquina virtual Azure Backup Server. O agente Azure Backup em execução no Servidor de Backup Azure necessita de armazenamento temporário para:

- sua própria utilização (uma localização de cache),
- dados restaurados a partir da nuvem (área de preparação local)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar o armazenamento temporário de discos de backup Azure

Cada máquina virtual Azure Stack vem com armazenamento temporário de disco, que está disponível para o utilizador como volume `D:\` . A área de preparação local necessária pelo Azure Backup pode ser configurada para residir em `D:\` , e a localização da cache pode ser colocada em `C:\` . Desta forma, não é necessário esculpir nenhum armazenamento dos discos de dados ligados à máquina virtual do Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup no disco local e em Azure

O Azure Backup Server armazena dados de backup em discos Azure ligados à máquina virtual, para recuperação operacional. Uma vez que os discos e espaço de armazenamento são ligados à máquina virtual, o Azure Backup Server gere o armazenamento para si. A quantidade de armazenamento de dados de backup depende do número e tamanho dos discos ligados a cada [máquina virtual Azure Stack](/azure-stack/user/azure-stack-storage-overview). Cada tamanho de Azure Stack VM tem um número máximo de discos que podem ser ligados à máquina virtual. Por exemplo, A2 são quatro discos. A3 são oito discos. A4 tem 16 discos. Mais uma vez, o tamanho e o número de discos determinam o total do armazenamento de backup.

> [!IMPORTANT]
> **Não** deve reter dados de recuperação operacional (backup) em discos ligados ao Servidor de Backup Azure durante mais de cinco dias.
>

Armazenar dados de backup em Azure reduz a infraestrutura de backup em Azure Stack. Se os dados tão velhos agem com mais de cinco dias, este deve ser armazenado em Azure.

Para armazenar dados de backup em Azure, crie ou utilize um cofre dos Serviços de Recuperação. Ao preparar-se para apoiar a carga de trabalho do Servidor de Backup Azure, [configura o cofre dos Serviços de Recuperação](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de reserva funciona, um ponto de recuperação é criado no cofre. Cada cofre dos Serviços de Recuperação tem até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados, e quanto tempo são retidos, pode reter dados de backup durante muitos anos. Por exemplo, pode criar pontos de recuperação mensais e retê-los durante cinco anos.

### <a name="scaling-deployment"></a>Implantação de escala

Se quiser escalar a sua implantação, tem as seguintes opções:

- Scale up - Aumente o tamanho da máquina virtual Azure Backup Server de série A para série D e aumente o armazenamento local [de acordo com as instruções da máquina virtual Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
- Descarrega dados - envie dados mais antigos para o Azure e retenha apenas os dados mais recentes sobre o armazenamento anexado ao Servidor de Backup Azure.
- Scale out - Adicione mais servidores de backup Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

.NET Quadro 3.5 SP1 ou superior deve ser instalado na máquina virtual.

### <a name="joining-a-domain"></a>Juntando-se a um domínio

A máquina virtual Azure Backup Server deve ser unida a um domínio. Um utilizador de domínio com privilégios de administrador deve instalar o Azure Backup Server na máquina virtual.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Usando um IaaS VM em Azure Stack

Ao escolher um servidor para O Azure Backup Server, comece com uma imagem de datacenter R2 do Windows Server 2012 ou do Windows Server 2016 Datacenter. O artigo, [Crie a sua primeira máquina virtual Windows no portal Azure,](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)fornece um tutorial para começar com a máquina virtual recomendada. Os requisitos mínimos recomendados para a máquina virtual do servidor (VM) devem ser: Norma A2 com dois núcleos e RAM de 3,5 GB.

Proteger cargas de trabalho com o Azure Backup Server tem muitas nuances. A [matriz de proteção do MABS](./backup-mabs-protection-matrix.md) ajuda a explicar estas nuances. Antes de colocar a máquina, leia completamente este artigo.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar numa máquina virtual dedicada e uni única. Não é possível instalar o Servidor de Backup Azure em:
>
> - Um computador em execução como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicações está instalada
> - Um computador no qual o Exchange Server está em execução
> - Um computador que é um nó de um aglomerado

Junte sempre o Servidor de Backup Azure a um domínio. Se precisar de mover o Azure Backup Server para um domínio diferente, instale primeiro o Azure Backup Server e, em seguida, junte-o ao novo domínio. Uma vez implantado O Servidor de Backup Azure, não poderá movê-lo para um novo domínio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação do armazenamento do cofre dos Serviços de Recuperação permite-lhe escolher entre armazenamento geo-redundante e armazenamento localmente redundante. Por padrão, os cofres dos Serviços de Recuperação utilizam armazenamento geo-redundante. Se este cofre for o seu cofre primário, deixe a opção de armazenamento definida para armazenamento geo-redundante. Escolha o armazenamento localmente redundante se quiser uma opção mais barata que seja menos durável. Leia mais sobre opções de armazenamento [geo-redundantes,](../storage/common/storage-redundancy.md#geo-redundant-storage) [localmente redundantes](../storage/common/storage-redundancy.md#locally-redundant-storage)e [redundantes](../storage/common/storage-redundancy.md#zone-redundant-storage) na visão geral da replicação do [Azure Storage](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o seu cofre para abrir o painel de abóbada e o menu Definições. Se o menu **Definições** não abrir, selecione **Todas as definições** no painel de instrumentos do cofre.
2. No menu **Definições,** selecione a configuração de backup de backup da **infraestrutura**  >  **Backup Configuration** para abrir o menu **de configuração de cópia de segurança.** No menu **Configuração de Cópia de Segurança,** escolha a opção de replicação de armazenamento para o seu cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Descarregue o instalador do Servidor de Backup Azure

Existem duas formas de descarregar o instalador do Azure Backup Server. Pode descarregar o instalador Azure Backup Server a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55269). Também pode baixar o instalador do Azure Backup Server enquanto configura um cofre dos Serviços de Recuperação. Os passos seguintes acompanham-no através do download do instalador a partir do portal Azure enquanto configura um cofre dos Serviços de Recuperação.

1. A partir da sua máquina virtual Azure Stack, [inscreva-se na subscrição do Azure no portal Azure.](https://portal.azure.com/)
2. No menu à esquerda, selecione **Todos os Serviços**.

    ![Escolha a opção Todos os Serviços no menu principal](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. No diálogo **de todos os serviços,** tipo *Serviços de Recuperação.* Quando começa a escrever, a sua entrada filtra a lista de recursos. Assim que o vir, selecione **cofres dos Serviços de Recuperação.**

    ![Serviços de recuperação de tipo em todos os diálogos de serviços](./media/backup-mabs-install-azure-stack/all-services.png)

    É apresentada a lista dos cofres dos Serviços de Recuperação na subscrição.

4. A partir da lista de cofres dos Serviços de Recuperação, selecione o seu cofre para abrir o painel de instrumentos.

    ![Selecione o seu cofre para abrir o painel de instrumentos](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. No menu "Começar" do cofre, selecione **Backup** para abrir o assistente "Iniciar".

    ![Backup começando](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    O menu de reserva abre.

    ![Backup-goals-default-open](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. No menu de cópia de segurança, a partir do menu Onde está a **sua carga de trabalho,** selecione **On-ins**. A partir do menu **de backup Do que pretende fazer backup,** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure. Se não tiver a certeza de que cargas de trabalho selecionar, escolha **Máquinas Virtuais Hiper-V** e, em seguida, selecione **Prepare a Infraestrutura**.

    ![no local e cargas de trabalho como objetivos](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    O menu **de infraestrutura Prepare** abre.

7. No menu **de infraestrutura** Prepare,selecione **Descarregue** para abrir uma página web para descarregar ficheiros de instalação do Servidor backup Azure.

    ![Mudança de assistente de início](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A página web da Microsoft que acolhe os ficheiros descarregáveis para O Azure Backup Server, abre.

8. Na página de descarregamento do Microsoft Azure Backup Server, escolha um idioma e selecione **Download**.

    ![O centro de descarregamento abre](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. O instalador do Servidor de Backup Azure é composto por oito ficheiros - um instalador e sete ficheiros .bin. Verifique **o nome do ficheiro** para selecionar todos os ficheiros necessários e selecione **Seguinte**. Descarregue todos os ficheiros para a mesma pasta.

    ![Baixar centro, ficheiros selecionados](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    O tamanho de download de todos os ficheiros de instalação é superior a 3 GB. Num link de descarregamento de 10 Mbps, o download de todos os ficheiros de instalação pode demorar até 60 minutos. Os ficheiros descarregam para o local de descarregamento especificado.

## <a name="extract-azure-backup-server-install-files"></a>Extrair ficheiros de instalação do Servidor de Backup Azure

Depois de ter descarregado todos os ficheiros para a sua máquina virtual Azure Stack, vá para o local de descarregamento. A primeira fase de instalação do Azure Backup Server é extrair os ficheiros.

![Baixar o instalador MABS](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Para iniciar a instalação, a partir da lista de ficheiros descarregados, selecione **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > São necessários pelo menos 4GB de espaço livre para extrair os ficheiros de configuração.
    >

2. No assistente do Servidor de Backup Azure, selecione **Seguinte** para continuar.

    ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Escolha o caminho para os ficheiros Azure Backup Server e selecione **Seguinte**.

   ![Selecione destino para ficheiros](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifique o local de extração e **selecione Extrato**.

   ![Verificar localização de extração](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. O assistente extrai os ficheiros e lê o processo de instalação.

   ![Assistente extrai ficheiros](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Assim que o processo de extração estiver concluído, **selecione Acabamento**. Por predefinição, **execute setup.exe** é selecionado. Quando selecionar **Terminar,** Setup.exe instala o Microsoft Azure Backup Server para a localização especificada.

   ![A configuração extrai ficheiros do Microsoft Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instale o pacote de software

No passo anterior, selecione **Finish** para sair da fase de extração e inicie o assistente de configuração do Servidor de Backup Azure.

![Microsoft Azure Backup Setup Wizard começa](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

O Azure Backup Server partilha código com o Gestor de Proteção de Dados. Verá referências ao Gestor de Proteção de Dados e DPM no instalador do Servidor de Backup Azure. Embora o Azure Backup Server e o Data Protection Manager sejam produtos separados, estes produtos estão intimamente relacionados.

1. Para lançar o assistente de configuração, selecione **Microsoft Azure Backup Server**.

   ![Selecione o Servidor de Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. No ecrã **Welcome,** selecione **Next**.

    ![Azure Backup Server - Bem-vindo](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. No ecrã **de Verificações Pré-Requisitos,** selecione **Verifique** se os pré-requisitos de hardware e software para o Azure Backup Server foram cumpridos.

    ![Azure Backup Server - Verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se o seu ambiente tiver os pré-requisitos necessários, verá uma mensagem indicando que a máquina cumpre os requisitos. Selecione **Seguinte**.  

    ![Azure Backup Server - Verificação de pré-requisitos passado](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se o seu ambiente não cumprir os requisitos necessários, as questões serão especificadas. Os pré-requisitos que não foram cumpridos também estão listados no DpmSetup.log. Resolva os erros pré-requisitos e, em seguida, executar **Check Again**. A instalação não pode continuar até que todos os pré-requisitos sejam cumpridos.

    ![Azure Backup Server - pré-requisitos de instalação não cumpridos](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. O Microsoft Azure Backup Server requer o SQL Server. O pacote de instalação do Azure Backup Server vem agregado com os binários apropriados do SQL Server. Se quiser utilizar a sua própria instalação SQL, pode. No entanto, a escolha recomendada é deixar o instalador adicionar uma nova instância do SQL Server. Para garantir que a sua escolha funciona com o seu ambiente, **selecione Verificar e instalar**.

   > [!NOTE]
   > O Azure Backup Server não funcionará com uma instância remota do SQL Server. O caso usado pelo Azure Backup Server deve ser local.
   >

    ![Servidor de backup Azure - definições DE SQL](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Depois de verificar, se a máquina virtual tiver os pré-requisitos necessários para instalar o Servidor de Backup Azure, selecione **Next**.

    ![Azure Backup Server - requisitos cumpridos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, reinicie a máquina. Depois de reiniciar a máquina, reinicie o instalador e, quando chegar ao ecrã **SQL Settings,** selecione **Check Again**.

5. Nas **Definições de Instalação**, forneça uma localização para a instalação de ficheiros de servidores de backup do Microsoft Azure e selecione **Next**.

    ![Fornecer localização para a instalação de ficheiros](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    A localização do risco é necessária para voltar a Azure. Certifique-se de que o tamanho da localização do risco equivale a pelo menos 5% dos dados previstos para serem apoiados até ao Azure. Para a proteção do disco, os discos separados devem ser configurados uma vez concluída a instalação. Para obter mais informações sobre piscinas de armazenamento, consulte [Prepare o armazenamento de dados.](/system-center/dpm/plan-long-and-short-term-data-storage)

6. No ecrã **Definições de Segurança,** forneça uma palavra-passe forte para contas de utilizador locais restritas e selecione **Next**.

    ![Tela de definições de segurança](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. No ecrã **opt-in da Atualização do Microsoft,** selecione se pretende utilizar o *Microsoft Update* para verificar se há atualizações e selecione **Next**.

   > [!NOTE]
   > Recomendamos que o Windows Update seja redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos como o Microsoft Azure Backup Server.
   >

    ![Ecrã de Opt-In microsoft update](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Reveja o *Resumo das Definições* e selecione **Instalar.**

    ![Resumo das definições](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando o Azure Backup Server termina a instalação, o instalador lança imediatamente o instalador de serviços de recuperação do Microsoft Azure.

9. O instalador do Microsoft Azure Recovery Services Agent abre e verifica a conectividade da Internet. Se a conectividade da Internet estiver disponível, continue com a instalação. Se não houver conectividade, forneça detalhes de procuração para ligar à Internet. Depois de especificar as definições de procuração, selecione **Seguinte**.

    ![Configuração do proxy](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Para instalar o Agente de Serviços de Recuperação do Microsoft Azure, **selecione Instal**.

    ![Instalação do agente](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    O agente microsoft Azure Recovery Services, também chamado de agente de backup Azure, configura o Servidor de Backup Azure para o cofre dos Serviços de Recuperação. Uma vez configurado, o Azure Backup Server irá sempre fazer cópias de segurança para o mesmo cofre dos Serviços de Recuperação.

11. Assim que o agente microsoft Azure Recovery Services terminar a instalação, selecione **Seguinte** para iniciar a fase seguinte: registar o Azure Backup Server com o cofre dos Serviços de Recuperação.

    ![Instalação do agente concluída com sucesso](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    O instalador lança o **Assistente do Servidor do Registo**.

12. Mude para a sua assinatura Azure e para o cofre dos Serviços de Recuperação. No menu **Prepare Infraestruturas,** selecione **Baixar** para descarregar credenciais de cofre. Se o botão **Descarregar** no passo 2 não estiver ativo, selecione **Já descarregado ou utilize a mais recente instalação do Servidor backup do Azure** para ativar o botão. As credenciais do cofre descarregam para o local onde armazena os downloads. Esteja atento a este local porque vai precisar dele para o próximo passo.

    ![Transferir as credenciais do cofre](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. No menu **de Identificação de** **Cofres, selecione Procurar** para encontrar as credenciais de cofre dos Serviços de Recuperação.

    ![Menu de identificação de abóbada](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    No diálogo **Select Vault Credentials,** vá ao local de descarregamento, selecione as credenciais do cofre e selecione **Open**.

    O caminho para as credenciais aparece no menu de Identificação de Cofre. Selecione **Seguinte** para avançar para as **Definições de Encriptação**.

14. No diálogo definição de **encriptação,** forneça uma palavra-passe para a encriptação de backup e uma localização para armazenar a palavra-passe e selecione **Next**.

    ![Definições de Encriptação](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Pode fornecer a sua própria palavra-passe, ou usar o gerador de palavras-passe para criar um para si. A palavra-passe é sua, e a Microsoft não salva ou gere esta palavra-passe. Para se preparar para um desastre, guarde a sua palavra-passe num local acessível.

    Uma vez selecionado **em seguida,** o Azure Backup Server está registado no cofre dos Serviços de Recuperação. O instalador continua a instalar o SQL Server e o Azure Backup Server.

    ![A configuração instala o SQL e o Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Quando o instalador estiver concluído, o **Status** mostra que todo o software foi instalado com sucesso.

    ![O software instalou com sucesso](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Quando a instalação estiver concluída, a consola Azure Backup Server e os ícones Azure Backup Server PowerShell são criados no ambiente de trabalho do servidor.

## <a name="add-backup-storage"></a>Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado à máquina do Servidor de Backup Azure. Para obter mais informações sobre a adição de discos, consulte [o armazenamento de Backup Moderno.](/system-center/dpm/add-storage)

> [!NOTE]
> Tem de adicionar armazenamento de backup mesmo que planeie enviar dados para o Azure. Na arquitetura do Azure Backup Server, o cofre dos Serviços de Recuperação detém a *segunda* cópia dos dados enquanto o armazenamento local detém a primeira cópia de backup (e obrigatória).
>
>

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup Server requer conectividade ao serviço de Backup Azure para que o produto funcione com sucesso. Para validar se a máquina tem a conectividade com o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola PowerShell do servidor de backup Azure. Se a saída do cmdlet for VERDADEIRA, então a conectividade existe, caso contrário não há conectividade.

Ao mesmo tempo, a assinatura Azure precisa de estar num estado saudável. Para saber o estado da sua subscrição e para a gerir, inscreva-se no portal de [subscrição.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

Assim que conhecer o estado da conectividade Azure e da subscrição do Azure, pode utilizar a tabela abaixo para descobrir o impacto na funcionalidade de backup/restauro oferecida.

| Estado de Conectividade | Subscrição do Azure | Criar uma cópia de segurança no Azure | Voltar ao disco | Restauro de Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Desprovisionado |Parada |Parada |Pontos de recuperação parados e Azure eliminados |Parada |
| Conectividade perdida > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Desprovisionado |Parada |Parada |Pontos de recuperação parados e Azure eliminados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação da perda de conectividade

Se uma firewall ou um representante estiver a impedir o acesso ao Azure, adicione os seguintes endereços de domínio na lista de autorizações de firewall/proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Uma vez que a conectividade com o Azure é restaurada para o Servidor de Backup Azure, o estado de subscrição Azure determina as operações que podem ser realizadas. Uma vez que o servidor esteja **ligado,** utilize a tabela na [conectividade da Rede](backup-mabs-install-azure-stack.md#network-connectivity) para ver as operações disponíveis.

### <a name="handling-subscription-states"></a>Estados de subscrição de manuseamento

É possível alterar uma subscrição Azure de *estado expirado* ou *desprovisionado* para estado *ativo.* Enquanto o estado de subscrição não é *Ativo:*

- Enquanto uma subscrição é *deprovisionada,* perde funcionalidade. Restaurar a subscrição do *Ative,* reaviva a funcionalidade de backup/restauro. Se os dados de backup no disco local forem retidos com um período de retenção suficientemente grande, esses dados de backup podem ser recuperados. No entanto, os dados de backup em Azure são irremediavelmente perdidos uma vez que a subscrição entra no estado *Deprovisionado.*
- Enquanto uma subscrição é *expirada,* perde a funcionalidade. As cópias de segurança programadas não são executadas enquanto uma subscrição é *expirada*.

## <a name="troubleshooting"></a>Resolução de problemas

Se o servidor de backup do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte o [documento de códigos de erro](https://support.microsoft.com/kb/3041338).
Também pode consultar [as FAQs relacionadas com o Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes

O artigo, [Preparando o seu ambiente para DPM,](/system-center/dpm/prepare-environment-for-dpm)contém informações sobre configurações suportadas do Servidor de Backup Azure.

Pode utilizar os seguintes artigos para obter uma compreensão mais profunda da proteção da carga de trabalho utilizando o Microsoft Azure Backup Server.

- [Backup do SQL Server](./backup-mabs-sql-azure-stack.md)
- [Backup do servidor SharePoint](./backup-mabs-sharepoint-azure-stack.md)
- [Backup alternativo do servidor](backup-azure-alternate-dpm-server.md)
