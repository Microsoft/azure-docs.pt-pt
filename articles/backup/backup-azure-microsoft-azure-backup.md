---
title: Use o Servidor de Backup Azure para fazer backup de cargas de trabalho
description: Neste artigo, aprenda a preparar o seu ambiente para proteger e fazer backup de cargas de trabalho utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 2cf6d88ad37ec1368e53c7213ea771c028a56643
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247279"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instale e atualize o Servidor de Backup do Azure

> [!div class="op_single_selector"]
>
> * [Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Aplica-se a: MABS v3. (MABS v2 já não é suportado. Se estiver a utilizar uma versão mais cedo do que o MABS v3, por favor atualize para a versão mais recente.)

Este artigo explica como preparar o seu ambiente para fazer backup de cargas de trabalho usando o Microsoft Azure Backup Server (MABS). Com o Azure Backup Server, pode proteger as cargas de trabalho da aplicação tais como Hiper-V VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows a partir de uma única consola.

> [!NOTE]
> O Azure Backup Server pode agora proteger VMware VMs e fornecer capacidades de segurança melhoradas. Instale o produto conforme explicado nas secções abaixo e no mais recente Agente de Backup Azure. Para saber mais sobre como fazer backup de servidores VMware com O Servidor de Backup Azure, consulte o artigo, Use o [Azure Backup Server para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md). Para saber mais sobre as capacidades de segurança, consulte a [documentação das funcionalidades de segurança de backup da Azure](backup-azure-security-feature.md).
>
>

O MABS implantado num VM Azure pode fazer backup de VMs em Azure, mas devem estar no mesmo domínio para permitir o funcionamento de backup. O processo de apoio a um VM Azure permanece o mesmo que apoiar VMs nas instalações, no entanto a implantação de MABS em Azure tem algumas limitações. Para obter mais informações sobre a limitação, consulte [o DPM como uma máquina virtual Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> A Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gestor de Recursos e clássico.](../azure-resource-manager/management/deployment-models.md) Este artigo fornece as informações e procedimentos para restaurar os VMs implantados utilizando o modelo gestor de recursos.
>
>

O Azure Backup Server herda grande parte da funcionalidade de backup de carga de trabalho do Gestor de Proteção de Dados (DPM). Este artigo liga-se à documentação do DPM para explicar algumas das funcionalidades partilhadas. Embora o Azure Backup Server partilhe grande parte da mesma funcionalidade que o DPM, o Azure Backup Server não faz backup de fita, nem se integra com o System Center.

## <a name="choose-an-installation-platform"></a>Escolha uma plataforma de instalação

O primeiro passo para pôr o Servidor de Backup Azure a funcionar é configurar um Servidor Windows. O seu servidor pode estar no Azure ou no local.

* Para proteger as cargas de trabalho no local, o servidor MABS deve estar localizado no local.
* Para proteger as cargas de trabalho em funcionamento em VMs Azure, o servidor MABS deve estar localizado em Azure, funcionando como um VM Azure.

### <a name="using-a-server-in-azure"></a>Usando um servidor em Azure

Ao escolher um servidor para executar o Azure Backup Server, recomenda-se que comece com uma imagem de galeria do Datacenter do Windows Server 2016 ou do Centro de Dados do Windows Server 2019. O artigo, [Crie a sua primeira máquina virtual Windows no portal Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar com a máquina virtual recomendada em Azure, mesmo que nunca tenha usado o Azure antes. Os requisitos mínimos recomendados para a máquina virtual do servidor (VM) devem ser: Standard_A4_v2 com quatro núcleos e 8 GB de RAM.

Proteger cargas de trabalho com o Azure Backup Server tem muitas nuances. A [matriz de proteção do MABS](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix) ajuda a explicar estas nuances. Antes de colocar a máquina, leia completamente este artigo.

### <a name="using-an-on-premises-server"></a>Usando um servidor no local

Se não quiser executar o servidor base em Azure, pode executar o servidor num VM Hiper-V, num VM VM VM v ou num hospedeiro físico. Os requisitos mínimos recomendados para o hardware do servidor são dois núcleos e 8 GB de RAM. Os sistemas operativos suportados estão listados no quadro seguinte:

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 e os mais recentes SPs |64 bits |Standard, Datacenter, Essentials  |

Pode desduplicar o armazenamento DPM utilizando a deduplica do Servidor do Windows. Saiba mais sobre como [o DPM e a deduplica](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) funcionam em conjunto quando implantados em Hiper-VMs.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar num servidor dedicado e de uso único. Não é possível instalar o Servidor de Backup Azure em:
>
> * Um computador em execução como um controlador de domínio
> * Um computador no qual a função de Servidor de Aplicações está instalada
> * Um computador que é um servidor de gestão do System Center Operations Manager
> * Um computador no qual o Exchange Server está em execução
> * Um computador que é um nó de um cluster
>
> A instalação do Azure Backup Server não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

Junte sempre o Servidor de Backup Azure a um domínio. Se planeia mover o servidor para um domínio diferente, instale primeiro o Azure Backup Server e, em seguida, junte o servidor ao novo domínio. Mover uma máquina de servidor de backup Azure existente para um novo domínio após a implementação não ser *suportada*.

Quer envie dados de backup para o Azure, ou o mantenha localmente, o Azure Backup Server deve ser registado num cofre dos Serviços de Recuperação.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por padrão, os cofres dos Serviços de Recuperação utilizam armazenamento geo-redundante. Se este cofre for o seu cofre primário, deixe a opção de armazenamento definida para armazenamento geo-redundante. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre opções de armazenamento [geo-redundantes](../storage/common/storage-redundancy-grs.md) e [localmente redundantes](../storage/common/storage-redundancy-lrs.md) na visão geral da [replicação do Azure Storage](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
2. Em **Propriedades**, em **Configuração de Cópia de Segurança,** clique em **Atualização**.

3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pacote de software

### <a name="downloading-the-software-package"></a>Descarregar o pacote de software

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Se já tem um cofre dos Serviços de Recuperação aberto, prossiga para o passo 3. Se não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal Azure, no menu principal, clique em **Procurar**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

     ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     É apresentada a lista dos cofres dos Serviços de Recuperação.
   * Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.

     ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A lâmina **de definições** abre-se por defeito. Se estiver fechado, clique em **Definições** para abrir a lâmina de definições.

    ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Clique **em Backup** para abrir o assistente "Iniciar".

    ![Backup começando](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    No **Início com** a lâmina de reserva que abre, **os Backup Goals** serão selecionados automaticamente.

    ![Backup-goals-default-open](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Na lâmina **Backup Goal,** a partir do menu onde está a **sua carga de trabalho,** selecione **On-ins**.

    ![no local e cargas de trabalho como objetivos](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A partir do menu **de retirada do que pretende fazer,** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure e, em seguida, clique em **OK**.

    O **"Getting Start" com o** assistente de backup muda a opção de **infraestrutura Prepare** para fazer backup das cargas de trabalho para a Azure.

   > [!NOTE]
   > Se apenas pretender fazer cópias de segurança de ficheiros e pastas, recomendamos a utilização do agente Azure Backup e seguindo as orientações do artigo, [Primeiro procure: faça cópias de segurança de ficheiros e pastas](backup-try-azure-backup-in-10-mins.md). Se vai proteger mais do que ficheiros e pastas, ou planeia expandir as necessidades de proteção no futuro, selecione essas cargas de trabalho.
   >
   >

    ![Mudança de assistente de início](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Na lâmina de **infraestrutura Prepare-se** que se abre, clique nos links **de descarregamento** para instalar o Servidor de Backup Azure e baixar as credenciais de cofre. Utilize as credenciais do cofre durante o registo do Azure Backup Server no cofre dos serviços de recuperação. Os links levam-no ao Centro de Descarregamentos onde o pacote de software pode ser descarregado.

    ![Preparar infraestruturas para o Servidor de Backup Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecione todos os ficheiros e clique em **Seguinte**. Descarregue todos os ficheiros que chegam a partir da página de descarregamento do Microsoft Azure Backup e coloque todos os ficheiros na mesma pasta.

    ![Baixar centro 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Uma vez que o tamanho de download de todos os ficheiros em conjunto é > 3G, num link de descarregamento de 10 Mbps pode demorar até 60 minutos para o download ser concluído.

### <a name="extracting-the-software-package"></a>Extrair o pacote de software

Depois de ter descarregado todos os ficheiros, clique no **MicrosoftAzureBackupInstaller.exe**. Isto iniciará o **Microsoft Azure Backup Setup Wizard** para extrair os ficheiros de configuração para uma localização especificada por si. Continue através do assistente e clique no botão **Extrair** para iniciar o processo de extração.

> [!WARNING]
> São necessários pelo menos 4 GB de espaço livre para extrair os ficheiros de configuração.
>
>

![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Uma vez concluído o processo de extração, verifique a caixa para lançar a *configuração.exe* recentemente extraída para começar a instalar o Microsoft Azure Backup Server e clicar no botão **Terminar.**

### <a name="installing-the-software-package"></a>Instalação do pacote de software

1. Clique em **Microsoft Azure Backup** para lançar o assistente de configuração.

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. No ecrã Welcome, clique no botão **Seguinte.** Isto leva-o à secção *de Verificações Prévias.* Neste ecrã, clique em **Verificar** se os pré-requisitos de hardware e software para O Azure Backup Server foram cumpridos. Se todos os pré-requisitos forem cumpridos com sucesso, verá uma mensagem indicando que a máquina satisfaz os requisitos. Clique no botão **Seguinte.**

    ![Azure Backup Server - Bem-vindo e Pré-Requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. O pacote de instalação do Azure Backup Server vem agregado com os binários sql server apropriados necessários. Ao iniciar uma nova instalação do Servidor de Backup Azure, escolha a opção **Instale a nova instância do SQL Server com esta Configuração** e clique no botão **Verificar e Instalar.** Uma vez instalados os pré-requisitos com sucesso, clique em **Seguinte**.

    >[!NOTE]
    >Se desejar utilizar o seu próprio servidor SQL, as versões sql Server suportadas são SQL Server 2014 SP1 ou superior, 2016 e 2017.  Todas as versões SQL Server devem ser Standard ou Enterprise 64-bit.
    >O Azure Backup Server não funcionará com uma instância remota do SQL Server. O caso que está a ser usado pelo Azure Backup Server tem de ser local. Se estiver a utilizar um servidor SQL existente para MABS, a configuração MABS apenas suporta a utilização de *instâncias nomeadas* de servidor SQL.

    ![Azure Backup Server - verificação DO SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, faça-o e clique em **Verificar novamente**. Se existirem problemas de configuração SQL, reconfigure o SQL de acordo com as diretrizes SQL e refaça a instalação/atualização do MABS utilizando a instância existente do SQL.

   **Configuração manual**

   Quando utilizar a sua própria instância de SQL, certifique-se de adicionar builtin\Administrators para sysadmin função para dominar dB.

    **Configuração SSRS com SQL 2017**

    Quando estiver a utilizar a sua própria instância de SQL 2017, tem de configurar manualmente o SSRS. Após a configuração SSRS, certifique-se de que a propriedade *isInitializada* de SSRS está definida para *True*. Quando isto é definido para True, o MABS assume que o SSRS já está configurado e saltará a configuração SSRS.

    Utilize os seguintes valores para a configuração SSRS:
    * Conta de Serviço: "Utilizar conta incorporada" deve ser Serviço de Rede
    * URL do Serviço Web: 'Diretório Virtual' deve ser ReportServer_\<SQLInstanceName>
    * Base de dados: O Nome da Base de Dados deve ser ReportServer$\<SQLInstanceName>
    * URL do Portal Web: 'Diretório Virtual' deve ser Reports_\<SQLInstanceName>

    [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração SSRS.

    > [!NOTE]
    > O licenciamento para o SQL Server utilizado como base de dados para MABS é regido pelos [Termos de Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) da Microsoft (OST). De acordo com o OST, o SQL Server agregado com mABS só pode ser usado como base de dados para MABS.

4. Forneça uma localização para a instalação de ficheiros de servidor de backup do Microsoft Azure e clique em **Seguinte**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A localização do risco é um requisito para voltar a Azure. Certifique-se de que a localização do risco é de pelo menos 5% dos dados planeados para serem apoiados até à nuvem. Para a proteção do disco, os discos separados devem ser configurados uma vez concluída a instalação. Para obter mais informações sobre os pools de armazenamento, consulte [prepare o armazenamento de dados.](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019)
5. Forneça uma palavra-passe forte para contas de utilizador locais restritas e clique em **Seguinte**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se deseja utilizar o *Microsoft Update* para verificar se há atualizações e clique em **Seguinte**.

   > [!NOTE]
   > Recomendamos que o Windows Update seja redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos como o Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Reveja o *Resumo das Definições* e clique em **Instalar.**

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação acontece por fases. Na primeira fase, o Agente de Serviços de Recuperação do Microsoft Azure é instalado no servidor. O assistente também verifica a conectividade da Internet. Se a conectividade da Internet estiver disponível, pode prosseguir com a instalação, caso contrário, tem de fornecer detalhes de procuração para se ligar à Internet.

    O próximo passo é configurar o Agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, terá de fornecer as suas credenciais de cofre para registar a máquina no cofre dos serviços de recuperação. Também irá fornecer uma palavra-passe para encriptar/desencriptar os dados enviados entre a Azure e as suas instalações. Pode gerar automaticamente uma palavra-passe ou fornecer a sua própria palavra-passe mínima de 16 caracteres. Continue com o assistente até que o agente tenha sido configurado.

    ![Azure Backup Servidor PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Uma vez concluído o registo do servidor de backup do Microsoft Azure, o assistente de configuração geral procede à instalação e configuração do SQL Server e dos componentes do Servidor de Backup Azure. Uma vez concluída a instalação do componente SQL Server, os componentes do Servidor de Backup Azure são instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando o passo de instalação tiver terminado, os ícones do ambiente de trabalho do produto também terão sido criados. Basta clicar duas vezes no ícone para lançar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado à máquina do Servidor de Backup Azure. Para obter mais informações sobre a adição de discos, consulte [as piscinas de armazenamento Configure e o armazenamento de discos.](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)

> [!NOTE]
> Tem de adicionar armazenamento de backup mesmo que planeie enviar dados para o Azure. Na arquitetura atual do Azure Backup Server, o cofre Azure Backup detém a *segunda* cópia dos dados enquanto o armazenamento local detém a primeira cópia de backup (e obrigatória).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instale e atualize o agente de proteção de dados

O MABS utiliza o agente de proteção de dados do System Center Data Protection Manager. [Aqui estão os passos](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) para instalar o Agente de Proteção nos seus Servidores de Proteção.

As seguintes secções descrevem como atualizar agentes de proteção para computadores clientes.

1. Na consola de administrador do servidor de cópia de segurança, selecione Agentes **de Gestão**  >  **Agents**.

2. No painel de visualização, selecione os computadores clientes para os quais pretende atualizar o agente de proteção.

   > [!NOTE]
   > A coluna **Atualizações do Agente** indica quando está disponível uma atualização do agente de proteção para cada computador protegido. No painel **de Ações,** a ação **Update** só está disponível quando um computador protegido é selecionado e as atualizações estão disponíveis.
   >
   >

3. Para instalar agentes de proteção atualizados nos computadores selecionados, no painel **de Ações,** selecione **Update**.

4. Para um computador cliente que não esteja ligado à rede, até que o computador esteja ligado à rede, a coluna **'Estado do Agente'** apresenta um estado de **atualização pendente**.

   Depois de um computador cliente estar ligado à rede, a coluna **Atualizações** de Agente para o computador cliente mostra um estado de **Atualização**.

## <a name="move-mabs-to-a-new-server"></a>Mover o MABS para um novo servidor

Aqui estão os passos se precisar de mover o MABS para um novo servidor, mantendo o armazenamento. Isto só pode ser feito se todos os dados estiverem no Armazenamento de Backup Moderno.

  > [!IMPORTANT]
  >
  > * O nome do novo servidor deve ter o mesmo nome que a instância original do Servidor de Backup Azure. Não é possível alterar o nome da nova instância do Servidor de Backup Azure se pretender utilizar o pool de armazenamento anterior e a Base de Dados MABS (DPMDB) para manter pontos de recuperação.
  > * Deve ter uma cópia de segurança da Base de Dados MABS (DPMDB). Precisa restaurar a base de dados.

1. No painel de visualização, selecione os computadores clientes para os quais pretende atualizar o agente de proteção.
2. Desligue o servidor de reserva original do Azure ou tire-o do fio.
3. Repôs a conta da máquina no diretório ativo.
4. Instale o Servidor 2016 em nova máquina e nomeie-o com o mesmo nome da máquina que o servidor original de Backup Azure.
5. Junte-se ao Domínio
6. Instale o servidor de backup V3 ou mais tarde (mova os discos de piscina MABS storage do antigo servidor e importe)
7. Restaurar o DPMDB dado no passo 1.
8. Prenda o armazenamento do servidor de reserva original ao novo servidor.
9. Do SQL Restaurar o DPMDB
10. Da linha de comando de administração no novo cd do servidor até ao Microsoft Azure Backup instalar localização e pasta de contentores

    Exemplo do caminho: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Para a azure backup, executar DPMSYNC -SYNC

    Se adicionou discos NOVOS à piscina de armazenamento DPM em vez de mover os antigos, em seguida, executar DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup Server requer conectividade ao serviço de Backup Azure para que o produto funcione com sucesso. Para validar se a máquina tem a conectividade com o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola PowerShell do servidor de backup Azure. Se a saída do cmdlet for VERDADEIRA, então a conectividade existe, caso contrário não existe conectividade.

Ao mesmo tempo, a assinatura Azure precisa de estar num estado saudável. Para saber o estado da sua subscrição e para a gerir, inscreva-se no portal de [subscrição.](https://account.windowsazure.com/Subscriptions)

Assim que conhecer o estado da conectividade Azure e da subscrição do Azure, pode utilizar a tabela abaixo para descobrir o impacto na funcionalidade de backup/restauro oferecida.

| Estado de Conectividade | Subscrição do Azure | Criar uma cópia de segurança no Azure | Voltar ao disco | Restauro de Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativo |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Desprovisionado |Parada |Parada |Pontos de recuperação parados e Azure eliminados |Parada |
| Conectividade perdida > 15 dias |Ativo |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Desprovisionado |Parada |Parada |Pontos de recuperação parados e Azure eliminados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação da perda de conectividade

Se tiver uma firewall ou um representante que esteja a impedir o acesso ao Azure, tem de permitir os seguintes endereços de domínio no perfil de firewall/proxy:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Se estiver a utilizar o olho do ExpressRoute Microsoft, selecione os seguintes serviços/regiões:

* Diretório Ativo Azure (12076:5060)
* Região microsoft Azure (de acordo com a localização do cofre dos Serviços de Recuperação)
* Azure Storage (de acordo com a localização do cofre dos Serviços de Recuperação)

Para mais detalhes, visite [os requisitos de encaminhamento ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Uma vez que a conectividade com o Azure foi restaurada para a máquina do Servidor de Backup Azure, as operações que podem ser realizadas são determinadas pelo estado de subscrição Azure. A tabela acima tem detalhes sobre as operações permitidas uma vez que a máquina está "Conectada".

### <a name="handling-subscription-states"></a>Estados de subscrição de manuseamento

É possível levar uma subscrição Azure de um estado *expirado* ou *desprovisionado* para o estado *Ativo.* No entanto, isto tem algumas implicações no comportamento do produto enquanto o estado não é *Ativo*:

* Uma *subscrição deprovisionada* perde a funcionalidade durante o período em que é desprovisionada. Ao *ativar,* a funcionalidade do produto de backup/restauro é reativada. Os dados de backup no disco local também podem ser recuperados se forem mantidos com um período de retenção suficientemente grande. No entanto, os dados de backup em Azure são irremediavelmente perdidos uma vez que a subscrição entra no estado *Deprovisionado.*
* Uma subscrição *expirada* só perde a funcionalidade até que tenha sido *novamente* ativa. Quaisquer backups programados para o período em que a subscrição foi expirada não serão *executados.*

## <a name="upgrade-mabs"></a>Upgrade MABS

Utilize os seguintes procedimentos para atualizar o MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade de MABS V2 para V3

> [!NOTE]
>
> O MABS V2 não é um pré-requisito para a instalação do MABS V3. No entanto, só pode fazer upgrade para MABS V3 a partir de MABS V2.

Utilize os seguintes passos para atualizar o MABS:

1. Para atualizar de MABS V2 para MABS V3, atualize o seu SISTEMA para Windows Server 2016 ou Windows Server 2019, se necessário.

2. Atualize o seu servidor. Os degraus são semelhantes à [instalação.](#install-and-upgrade-azure-backup-server) No entanto, para as definições DE SQL, você terá a opção de atualizar o seu exemplo SQL para SQL 2017, ou para usar o seu próprio exemplo de servidor SQL 2017.

   > [!NOTE]
   >
   > Não saia enquanto a sua instância SQL estiver a ser atualizada, a saída irá desinstalar a instância de reporte do SQL e, por isso, uma tentativa de retualização do MABS falhará.

   > [!IMPORTANT]
   >
   >  Como parte da atualização do SQL 2017, apoiamos as chaves de encriptação SQL e desinstalamos os serviços de reporte. Após a atualização do servidor SQL, o serviço de reporte (14.0.6827.4788) é instalado & chaves de encriptação são restauradas.
   >
   > Ao configurar o SQL 2017 manualmente, consulte a *configuração SSRS com secção SQL 2017* sob as instruções de instalação.

3. Atualize os agentes de proteção nos servidores protegidos.
4. As cópias de segurança devem continuar sem a necessidade de reiniciar os seus servidores de produção.
5. Pode começar a proteger os seus dados agora. Se estiver a atualizar para o Modern Backup Storage, enquanto protege, também pode escolher os volumes que deseja armazenar as cópias de segurança e verificar se está em espaço a provisionado. [Saiba mais](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se o servidor de backup do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte este [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para obter mais informações.
Também pode consultar [as FAQs relacionadas com o Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes

Pode obter informações detalhadas aqui sobre [a preparação do seu ambiente para o DPM.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019) Também contém informações sobre configurações suportadas nas quais o Azure Backup Server pode ser implantado e utilizado. Pode utilizar uma série de [cmdlet PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) para realizar várias operações.

Pode utilizar estes artigos para obter uma compreensão mais profunda da proteção da carga de trabalho utilizando o servidor De backup do Microsoft Azure.

* [Backup do SQL Server](backup-azure-backup-sql.md)
* [Backup do servidor SharePoint](backup-azure-backup-sharepoint.md)
* [Backup alternativo do servidor](backup-azure-alternate-dpm-server.md)
