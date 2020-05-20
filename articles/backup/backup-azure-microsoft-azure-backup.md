---
title: Use o Servidor de Backup Azure para fazer backup das cargas de trabalho
description: Neste artigo, aprenda a preparar o seu ambiente para proteger e fazer backup de cargas de trabalho utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 7a442cb094f87852c9d4f781d378f5886f3a4a42
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652127"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalar e atualizar o Servidor de Backup Azure

> [!div class="op_single_selector"]
>
> * [Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Aplica-se a: MABS v3. (O MABS v2 já não é suportado. Se estiver a utilizar uma versão mais cedo do que o MABS v3, por favor atualize para a versão mais recente.)

Este artigo explica como preparar o seu ambiente para fazer backup de cargas de trabalho utilizando o Microsoft Azure Backup Server (MABS). Com o Servidor de Backup Azure, pode proteger as cargas de trabalho da aplicação, tais como VMs Hiper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e Windows clientes a partir de uma única consola.

> [!NOTE]
> O Azure Backup Server pode agora proteger vMware VMs e fornecer capacidades de segurança melhoradas. Instale o produto conforme explicado nas secções abaixo e no mais recente Agente de Backup Azure. Para saber mais sobre o backup de servidores VMware com o Servidor de Backup Azure, consulte o artigo, [Use o Servidor de Backup Azure para fazer backup num servidor VMware](backup-azure-backup-server-vmware.md). Para saber mais sobre as capacidades de segurança, consulte a documentação de funcionalidades de [segurança de backup Azure](backup-azure-security-feature.md).
>
>

Os MABS implantados num VM Azure podem fazer backup em VMs em Azure, mas devem estar no mesmo domínio para permitir a operação de backup. O processo de apoio a um VM Azure permanece o mesmo que apoiar os VMs nas instalações, no entanto a implantação de MABS em Azure tem algumas limitações. Para mais informações sobre limitação, consulte [o DPM como uma máquina virtual Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gestor de Recursos e clássico.](../azure-resource-manager/management/deployment-models.md) Este artigo fornece as informações e procedimentos para restaurar os VMs implantados usando o modelo de Gestor de Recursos.
>
>

O Azure Backup Server herda grande parte da funcionalidade de backup da carga de trabalho do Gestor de Proteção de Dados (DPM). Este artigo liga-se à documentação do DPM para explicar algumas das funcionalidades partilhadas. Embora o Azure Backup Server partilhe grande parte da mesma funcionalidade que o DPM, o Azure Backup Server não faz backup, nem se integra no System Center.

## <a name="choose-an-installation-platform"></a>Escolha uma plataforma de instalação

O primeiro passo para pôr o Servidor de Backup Azure a funcionar é configurar um Servidor windows. O seu servidor pode estar no Azure ou no local.

* Para proteger as cargas de trabalho no local, o servidor MABS deve estar localizado no local.
* Para proteger as cargas de trabalho em VMs Azure, o servidor MABS deve estar localizado em Azure, funcionando como um VM Azure.

### <a name="using-a-server-in-azure"></a>Usando um servidor em Azure

Ao escolher um servidor para executar o Servidor de Backup Azure, recomenda-se que comece com uma imagem de galeria do Datacenter Do Windows Server 2016 ou do Windows Server 2019 Datacenter. O artigo, [Create your first Windows virtual machine in the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar com a máquina virtual recomendada em Azure, mesmo que nunca tenha usado Azure antes. Os requisitos mínimos recomendados para a máquina virtual do servidor (VM) devem ser: Standard_A4_v2 com quatro núcleos e RAM de 8-GB.

Proteger cargas de trabalho com o Azure Backup Server tem muitas nuances. O artigo, [Instalar dPM como uma máquina virtual Azure,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))ajuda a explicar estas nuances. Antes de colocar a máquina, leia completamente este artigo.

### <a name="using-an-on-premises-server"></a>Usando um servidor no local

Se não quiser executar o servidor base em Azure, pode executar o servidor num VM Hiper-V, num VM VM ou num hospedeiro físico. Os requisitos mínimos recomendados para o hardware do servidor são dois núcleos e RAM de 8 GB. Os sistemas operativos suportados estão listados no quadro seguinte:

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 e os mais recentes SPs |64 bits |Standard, Datacenter, Essentials  |

Pode duplicar o armazenamento de DPM utilizando a Deduplicação do Servidor windows. Saiba mais sobre como o DPM e a [desduplicação](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) funcionam em conjunto quando implantados em VMs Hiper-V.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar num servidor dedicado e unipropósito. Não é possível instalar o Servidor de Backup Azure em:
>
> * Um computador em execução como um controlador de domínio
> * Um computador no qual a função de Servidor de Aplicações está instalada
> * Um computador que é um servidor de gestão do System Center Operations Manager
> * Um computador no qual o Exchange Server está em execução
> * Um computador que é um nó de um cluster
>
> A instalação do Servidor de Backup Azure não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

Junte-se sempre ao Azure Backup Server para um domínio. Se planeia mover o servidor para um domínio diferente, instale primeiro o Servidor de Backup Azure e, em seguida, junte-se ao servidor para o novo domínio. Mover uma máquina de servidor de backup Azure existente para um novo domínio após a implementação não é *suportada*.

Quer envie dados de backup para o Azure ou guarde-os localmente, o Azure Backup Server deve ser registado com um cofre dos Serviços de Recuperação.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por padrão, os cofres dos Serviços de Recuperação utilizam armazenamento geo-redundante. Se este cofre for o seu cofre principal, deixe a opção de armazenamento definida para armazenamento geo-redundante. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre opções de armazenamento [georedundante](../storage/common/storage-redundancy-grs.md) e [localmente redundantes](../storage/common/storage-redundancy-lrs.md) na visão geral da replicação do [Armazenamento Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
2. Em **Propriedades**, sob **configuração de backup,** clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pacote de software

### <a name="downloading-the-software-package"></a>Descarregar o pacote de software

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Se já tem um cofre dos Serviços de Recuperação aberto, proceda ao passo 3. Se não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal Azure, no menu principal, clique em **Navegar**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

     ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     É apresentada a lista dos cofres dos Serviços de Recuperação.
   * Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.

     ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A lâmina **Definições** abre-se por defeito. Se estiver fechado, clique em **Definições** para abrir a lâmina de definições.

    ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Clique em **Backup** para abrir o assistente Iniciando.

    ![Backup começando](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    No **Getting Started com** lâmina de reserva que abre, **os Backup Goals** serão selecionados automaticamente.

    ![Backup-goals-default-open](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Na lâmina **de backup Goal,** a partir do menu **de funcionamento** da sua carga de trabalho, selecione **On-premises**.

    ![no local e cargas de trabalho como objetivos](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A partir do **menu "O que quer fazer backup",** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure e, em seguida, clique em **OK**.

    O **Getting Started com o** assistente de reserva muda a opção de infraestrutura **prepare** para fazer backup das cargas de trabalho para o Azure.

   > [!NOTE]
   > Se pretender apenas fazer cópias de segurança de ficheiros e pastas, recomendamos a utilização do agente De backup Azure e seguindo a orientação no artigo, [primeiro olhar: cópia de ficheiros e pastas](backup-try-azure-backup-in-10-mins.md). Se vai proteger mais do que ficheiros e pastas, ou planeia expandir as necessidades de proteção no futuro, selecione essas cargas de trabalho.
   >
   >

    ![Começando a mudança de feiticeiro](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Na lâmina de **infraestrutura Prepare** que se abre, clique nos links **de descarregamento** para instalar o Servidor de Backup Azure e descarregue as credenciais do cofre. Usa as credenciais do cofre durante o registo do Azure Backup Server no cofre dos serviços de recuperação. Os links levam-no ao Centro de Descarregamento onde o pacote de software pode ser descarregado.

    ![Prepare infraestrutura para o Servidor de Backup Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecione todos os ficheiros e clique em **Next**. Faça o download de todos os ficheiros da página de descarregamento do Microsoft Azure Backup e coloque todos os ficheiros na mesma pasta.

    ![Centro de descarregamento 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Uma vez que o tamanho de download de todos os ficheiros em conjunto é > 3G, num link de descarregamento de 10 Mbps pode demorar até 60 minutos para o download ser concluído.

### <a name="extracting-the-software-package"></a>Extração do pacote de software

Depois de ter descarregado todos os ficheiros, clique em **MicrosoftAzureBackupInstaller.exe**. Isto irá iniciar o Assistente de Configuração de **Backup Microsoft Azure** para extrair os ficheiros de configuração para um local especificado por si. Continue através do assistente e clique no botão **Extrato** para iniciar o processo de extração.

> [!WARNING]
> São necessários pelo menos 4 GB de espaço livre para extrair os ficheiros de configuração.
>
>

![Assistente de configuração de backup microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Uma vez concluído o processo de extração, verifique a caixa para lançar a *configuração.exe* recém-extraída para começar a instalar o Microsoft Azure Backup Server e clicar no botão **'Terminar'.**

### <a name="installing-the-software-package"></a>Instalação do pacote de software

1. Clique em **Microsoft Azure Backup** para lançar o assistente de configuração.

    ![Assistente de configuração de backup microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. No ecrã Welcome, clique no botão **Seguinte.** Isto leva-o à secção *de Verificações Pré-Requisitos.* Neste ecrã, clique em **Verificar** se os pré-requisitos de hardware e software para o Servidor de Backup Azure foram cumpridos. Se todos os pré-requisitos forem cumpridos com sucesso, verá uma mensagem indicando que a máquina satisfaz os requisitos. Clique no botão **Seguinte.**

    ![Servidor de backup Azure - Verificação de boas-vindas e pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. O pacote de instalação do Servidor de Backup Azure vem agregado com os binários adequados do Servidor SQL necessários. Ao iniciar uma nova instalação do Servidor de Backup Azure, escolha a opção **Instale uma nova instância do Servidor SQL com esta Configuração** e clique no botão **'Verificar e Instalar'.** Assim que os pré-requisitos forem instalados com sucesso, clique em **Seguinte**.

    >[!NOTE]
    >Se desejar utilizar o seu próprio servidor SQL, as versões SQL Server suportadas são SQL Server 2014 SP1 ou superior, 2016 e 2017.  Todas as versões Do SQL Server devem ser Standard ou Enterprise 64-bit.
    >O Servidor de Backup Azure não funcionará com uma instância remota do Servidor SQL. A instância usada pelo Azure Backup Server tem de ser local. Se estiver a utilizar um servidor SQL existente para MABS, a configuração Do MABS apenas suporta a utilização de *instâncias nomeadas* do servidor SQL.

    ![Servidor de backup Azure - Verificação SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, faça-o e clique em **'Verificar' Novamente**. Se existirem problemas de configuração SQL, reconfigure o SQL de acordo com as diretrizes SQL e tente novamente instalar/atualizar o MABS utilizando a instância existente de SQL.

   **Configuração manual**

   Quando utilizar a sua própria instância de SQL, certifique-se de adicionar administradores incorporados ao papel de sysadmin a dominar o DB.

    **Configuração SSRS com SQL 2017**

    Quando estiver a usar a sua própria instância de SQL 2017, precisa de configurar manualmente a SSRS. Após a configuração do SSRS, certifique-se de que a propriedade *isinitializada* de SSRS está definida para *True*. Quando isto está definido para True, o MABS assume que a SSRS já está configurada e irá saltar a configuração SSRS.

    Utilize os seguintes valores para a configuração ssrs:
    * Conta de Serviço: 'Use a conta incorporada' deve ser serviço de rede
    * URL do Serviço Web: 'Diretório Virtual' deve ser ReportServer_ \< SQLInstanceName>
    * Base de Dados: Nome da base de dados deve ser ReportServer$ \< SQLInstanceName>
    * URL do Portal Web: 'Diretório Virtual' deve ser Reports_ \< SQLInstanceName>

    [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração ssrs.

    > [!NOTE]
    > O licenciamento para o SQL Server usado como base de dados para MABS é regido pelos Termos de [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) da Microsoft (OST). De acordo com o OST, o Servidor SQL agregado com MABS só pode ser utilizado como base de dados para MABS.

4. Forneça uma localização para a instalação de ficheiros do servidor de backup do Microsoft Azure e clique em **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A localização do risco é um requisito para voltar a Azure. Certifique-se de que a localização do risco é de pelo menos 5% dos dados previstos para serem apoiados até à nuvem. Para a proteção do disco, os discos separados devem ser configurados assim que a instalação estiver concluída. Para mais informações sobre piscinas de armazenamento, consulte [piscinas de armazenamento configure e armazenamento em disco](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Forneça uma senha forte para contas restritas de utilizadores locais e clique em **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se pretende utilizar o *Microsoft Update* para verificar se há atualizações e clica **em Next**.

   > [!NOTE]
   > Recomendamos que o Windows Update redirecione para o Microsoft Update, que oferece segurança e atualizações importantes para windows e outros produtos como o Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Reveja o *resumo das Definições* e clique em **Instalar**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação acontece por fases. Na primeira fase, o Agente de Serviços de Recuperação do Microsoft Azure está instalado no servidor. O assistente também verifica a conectividade da Internet. Se a conectividade da Internet estiver disponível, pode proceder à instalação, caso contrário, terá de fornecer detalhes de procuração para se ligar à Internet.

    O próximo passo é configurar o Agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, terá de fornecer credenciais ao seu cofre para registar a máquina no cofre dos serviços de recuperação. Também fornecerá uma palavra-passe para encriptar/desencriptar os dados enviados entre o Azure e as suas instalações. Pode gerar automaticamente uma frase de passe ou fornecer a sua própria frase mínima de 16 caracteres. Continue com o assistente até que o agente tenha sido configurado.

    ![Servidor de backup Azure PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Uma vez que o registo do servidor de backup Microsoft Azure complete com sucesso, o assistente de configuração geral procede à instalação e configuração do Servidor SQL e dos componentes do Servidor de Backup Azure. Uma vez concluída a instalação do componente SQL Server, os componentes do Servidor de Backup Azure são instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando o passo de instalação estiver concluído, os ícones do ambiente de trabalho do produto também terão sido criados. Basta clicar duas vezes no ícone para lançar o produto.

### <a name="add-backup-storage"></a>Adicione armazenamento de reserva

A primeira cópia de reserva é mantida no armazenamento anexada à máquina do Servidor de Backup Azure. Para mais informações sobre a adição de discos, consulte [piscinas de armazenamento configure e armazenamento em disco](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Tem de adicionar armazenamento de reserva mesmo que planeie enviar dados para o Azure. Na arquitetura atual do Azure Backup Server, o cofre de backup Azure detém a *segunda* cópia dos dados enquanto o armazenamento local detém a primeira cópia de backup (e obrigatória).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalar e atualizar o agente de proteção do Gestor de Proteção de Dados

O MABS utiliza o agente de proteção de dados do System Center. [Aqui estão os passos](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) para instalar o Agente de Proteção nos seus Servidores de Proteção.

As seguintes secções descrevem como atualizar os agentes de proteção para computadores clientes.

1. Na consola de administrador do servidor de backup, selecione Agentes de **Gestão**  >  **Agents**.

2. No painel de visualização, selecione os computadores clientes para os quais pretende atualizar o agente de proteção.

   > [!NOTE]
   > A coluna **Atualizações** do Agente indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **De Ações,** a ação **Update** só está disponível quando um computador protegido é selecionado e as atualizações estão disponíveis.
   >
   >

3. Para instalar agentes de proteção atualizados nos computadores selecionados, no painel **Ações,** selecione **Update**.

4. Para um computador cliente que não esteja ligado à rede, até que o computador esteja ligado à rede, a coluna Estado do **Agente** mostra um estado de **Atualização Pendente**.

   Depois de um computador cliente estar ligado à rede, a coluna **Agent Updates** para o computador cliente mostra um estado de **Atualização**.

## <a name="move-mabs-to-a-new-server"></a>Mover o MABS para um novo servidor

Aqui estão os passos se precisar de mover o MABS para um novo servidor, mantendo o armazenamento. Isto só pode ser feito se todos os dados estiverem no Armazenamento de Backup Moderno.

  > [!IMPORTANT]
  >
  > * O novo nome do servidor deve ter o mesmo nome da instância original do Servidor de Backup Azure. Não é possível alterar o nome da nova instância do Servidor de Backup Azure se pretender utilizar o conjunto de armazenamento anterior e a Base de Dados MABS (DPMDB) para reter pontos de recuperação.
  > * Deve ter uma cópia de segurança da Base de Dados MABS (DPMDB). Tens de restaurar a base de dados.

1. No painel de visualização, selecione os computadores clientes para os quais pretende atualizar o agente de proteção.
2. Desligue o servidor de reserva original do Azure ou tire-o do fio.
3. Redefinir a conta da máquina em diretório ativo.
4. Instale o Server 2016 em nova máquina e nomeie-o com o mesmo nome de máquina que o servidor original de Backup Azure.
5. Junte-se ao Domínio
6. Instale o servidor de backup Azure V3 ou posteriormente (mova os discos de piscina de armazenamento MABS do servidor antigo e importe)
7. Restaurar o DPMDB tomado no passo 1.
8. Fixe o armazenamento do servidor de reserva original ao novo servidor.
9. A partir do SQL Restaurar o DPMDB
10. Desde a linha de comando de administração no novo cd do servidor até ao Microsoft Azure Backup instalar localização e pasta de lixo

    Exemplo de percurso: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Para backup Azure, Executar DPMSYNC -SYNC

    Se tiver adicionado discos novos à piscina de armazenamento DPM em vez de mover os antigos, então execute a réplica DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup Server requer conectividade com o serviço de backup Azure para que o produto funcione com sucesso. Para validar se a máquina tem a conectividade com o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola PowerShell do Servidor de Backup Azure. Se a saída do cmdlet for TRUE, então existe conectividade, caso contrário não há conectividade.

Ao mesmo tempo, a subscrição do Azure tem de estar num estado saudável. Para saber o estado da sua subscrição e geri-la, inscreva-se no portal de [subscrição](https://account.windowsazure.com/Subscriptions).

Assim que souber o estado da conectividade Azure e da subscrição do Azure, pode utilizar a tabela abaixo para saber o impacto na funcionalidade de backup/restauro oferecida.

| Estado de Conectividade | Subscrição do Azure | Criar uma cópia de segurança no Azure | Voltar ao disco | Restaurar de Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativo |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Deprovisionado |Parada |Parada |Pontos de recuperação parados e Azure apagados |Parada |
| A conectividade perdida > 15 dias |Ativo |Parada |Parada |Permitido |Permitido |
| A conectividade perdida > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| A conectividade perdida > 15 dias |Deprovisionado |Parada |Parada |Pontos de recuperação parados e Azure apagados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação da perda de conectividade

Se tiver uma firewall ou um proxy que esteja a impedir o acesso ao Azure, tem de permitir os seguintes endereços de domínio no perfil firewall/proxy:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Se estiver a utilizar o peering expressRoute Microsoft, selecione os seguintes serviços/regiões:

* Diretório Ativo Azure (12076:5060)
* Região do Microsoft Azure (de acordo com a localização do seu cofre de Serviços de Recuperação)
* Armazenamento Azure (de acordo com a localização do seu cofre de Serviços de Recuperação)

Para mais detalhes, visite os requisitos de [encaminhamento expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Uma vez que a conectividade com o Azure tenha sido restaurada à máquina do Servidor de Backup Azure, as operações que podem ser realizadas são determinadas pelo estado de subscrição do Azure. A tabela acima tem detalhes sobre as operações permitidas uma vez que a máquina está "Conectada".

### <a name="handling-subscription-states"></a>Manipulação de estados de subscrição

É possível levar uma assinatura Azure de um estado *expirado* ou *dedprovisionado* para o estado *Ativo.* No entanto, isto tem algumas implicações no comportamento do produto enquanto o Estado não está *Ativo:*

* Uma subscrição *dedprovisionada* perde a funcionalidade para o período em que é dedprovisionada. Ao ligar *ative,* a funcionalidade do produto de backup/restauro é reativada. Os dados de backup do disco local também podem ser recuperados se forem mantidos com um período de retenção suficientemente grande. No entanto, os dados de backup em Azure são irremediavelmente perdidos assim que a subscrição entra no estado *dedprovisionado.*
* Uma subscrição *Expirada* só perde funcionalidade até que tenha sido novamente tornada *Ativa.* Quaisquer cópias de segurança programadas para o período em que a subscrição foi *Expirada* não serão executadas.

## <a name="upgrade-mabs"></a>Upgrade MABS

Utilize os seguintes procedimentos para atualizar o MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade de MABS V2 para V3

> [!NOTE]
>
> O MABS V2 não é um pré-requisito para a instalação de MABS V3. No entanto, pode fazer upgrade para MABS V3 apenas a partir de MABS V2.

Utilize os seguintes passos para atualizar o MABS:

1. Para fazer o upgrade de MABS V2 para MABS V3, atualize o seu SISTEMA para Windows Server 2016 ou Windows Server 2019, se necessário.

2. Atualize o seu servidor. Os passos são semelhantes à [instalação.](#install-and-upgrade-azure-backup-server) No entanto, para as definições sQL, você terá a opção de atualizar a sua instância SQL para SQL 2017, ou usar a sua própria instância do servidor SQL 2017.

   > [!NOTE]
   >
   > Não saia enquanto a sua instância SQL estiver a ser atualizada, a saída desinstalará a instância de reporte SQL e, portanto, uma tentativa de reactualização do MABS falhará.

   > [!IMPORTANT]
   >
   >  Como parte da atualização SQL 2017, reservamos as chaves de encriptação SQL e desinstalamos os serviços de reporte. Após a atualização do servidor SQL, o serviço de reporte (14.0.6827.4788) é instalado & chaves de encriptação são restauradas.
   >
   > Ao configurar manualmente o SQL 2017, consulte a configuração SSRS com a secção *SQL 2017* sob instruções de instalação.

3. Atualize os agentes de proteção nos servidores protegidos.
4. As cópias de segurança devem continuar sem a necessidade de reiniciar os seus servidores de produção.
5. Pode começar a proteger os seus dados agora. Se estiver a atualizar para o Armazenamento de Backup Moderno, enquanto protege, também pode escolher os volumes em que pretende armazenar as cópias de segurança e verificar se está sob o espaço previsto. [Saiba mais](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se o servidor de backup do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte este documento de [códigos](https://support.microsoft.com/kb/3041338) de erro para obter mais informações.
Também pode consultar [as FAQs relacionadas](backup-azure-backup-faq.md) com o Azure Backup

## <a name="next-steps"></a>Passos seguintes

Pode obter informações detalhadas sobre [a preparação do seu ambiente para o DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019). Também contém informações sobre configurações suportadas nas quais o Servidor de Backup Do Azure pode ser implantado e utilizado. Pode utilizar uma série de [cmdlet PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) para realizar várias operações.

Pode utilizar estes artigos para obter uma compreensão mais profunda da proteção da carga de trabalho utilizando o servidor de backup do Microsoft Azure.

* [Backup do Servidor SQL](backup-azure-backup-sql.md)
* [Backup do servidor SharePoint](backup-azure-backup-sharepoint.md)
* [Backup alternativo do servidor](backup-azure-alternate-dpm-server.md)
