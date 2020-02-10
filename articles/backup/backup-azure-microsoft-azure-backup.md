---
title: Usar Servidor de Backup do Azure para fazer backup de cargas de trabalho
description: Neste artigo, saiba como preparar seu ambiente para proteger e fazer backup de cargas de trabalho usando o Backup do Microsoft Azure Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: ff5df19d3e2d42af9a45fbc1b71980cee1cdb8a0
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111592"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalar e atualizar Servidor de Backup do Azure

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Aplica-se a: MABS v3. (Não há mais suporte para o MABS v2. Se você estiver usando uma versão anterior à MABS v3, atualize para a versão mais recente.)

Este artigo explica como preparar seu ambiente para fazer backup de cargas de trabalho usando o Backup do Microsoft Azure Server (MABS). Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de aplicativos, como VMs do Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows, a partir de um único console.

> [!NOTE]
> O Servidor de Backup do Azure agora pode proteger VMs VMware e fornece recursos de segurança aprimorados. Instale o produto, conforme explicado nas seções abaixo e o agente de backup do Azure mais recente. Para saber mais sobre o backup de servidores VMware com o Servidor de Backup Azure, consulte o artigo, [Use o Servidor de Backup Azure para fazer backup num servidor VMware](backup-azure-backup-server-vmware.md). Para saber mais sobre as capacidades de segurança, consulte a documentação de funcionalidades de [segurança de backup Azure](backup-azure-security-feature.md).
>
>

O MABS implantado em uma VM do Azure pode fazer backup de VMs no Azure, mas deve estar no mesmo domínio para habilitar a operação de backup. O processo de back-up de uma VM do Azure permanece o mesmo que fazer backup de VMs no local, no entanto, a implantação de MABS no Azure tem algumas limitações. Para mais informações sobre limitação, consulte [o DPM como uma máquina virtual Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gestor de Recursos e clássico.](../azure-resource-manager/management/deployment-models.md) Este artigo fornece as informações e os procedimentos para restaurar as VMs implantadas usando o modelo do Resource Manager.
>
>

Servidor de Backup do Azure herda grande parte da funcionalidade de backup de carga de trabalho do Data Protection Manager (DPM). Este artigo fornece links para a documentação do DPM para explicar algumas das funcionalidades compartilhadas. Embora Servidor de Backup do Azure Compartilhe grande parte da mesma funcionalidade que o DPM, o Servidor de Backup do Azure não faz backup em fita nem se integra ao System Center.

## <a name="choose-an-installation-platform"></a>Escolher uma plataforma de instalação

A primeira etapa para colocar o Servidor de Backup do Azure em funcionamento é configurar um Windows Server. O servidor pode estar no Azure ou no local.

### <a name="using-a-server-in-azure"></a>Usando um servidor no Azure

Ao escolher um servidor para executar Servidor de Backup do Azure, é recomendável começar com uma imagem da galeria do Windows Server 2016 Datacenter ou do Windows Server 2019 datacenter. O artigo, [Create your first Windows virtual machine in the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar com a máquina virtual recomendada em Azure, mesmo que nunca tenha usado Azure antes. Os requisitos mínimos recomendados para a máquina virtual do servidor (VM) devem ser: Standard_A4_v2 com quatro núcleos e RAM de 8-GB.

A proteção de cargas de trabalho com Servidor de Backup do Azure tem muitas nuances. O artigo, [Instalar dPM como uma máquina virtual Azure,](https://technet.microsoft.com/library/jj852163.aspx)ajuda a explicar estas nuances. Antes de implantar o computador, leia este artigo completamente.

### <a name="using-an-on-premises-server"></a>Usando um servidor local

Se você não quiser executar o servidor base no Azure, poderá executar o servidor em uma VM do Hyper-V, uma VM do VMware ou um host físico. Os requisitos mínimos recomendados para o hardware do servidor são dois núcleos e 8 GB de RAM. Os sistemas operacionais com suporte são listados na tabela a seguir:

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 e SPs mais recentes |64 bits |Standard, Datacenter, Essentials  |

Você pode eliminar a duplicação do armazenamento do DPM usando a eliminação de duplicação do Windows Server. Saiba mais sobre como o DPM e a [desduplicação](https://technet.microsoft.com/library/dn891438.aspx) funcionam em conjunto quando implantados em VMs Hiper-V.

> [!NOTE]
> O Servidor de Backup do Azure foi projetado para ser executado em um servidor dedicado e de finalidade única. Não é possível instalar o Servidor de Backup do Azure em:
>
> * Um computador em execução como um controlador de domínio
> * Um computador no qual a função de Servidor de Aplicações está instalada
> * Um computador que é um servidor de gestão do System Center Operations Manager
> * Um computador no qual o Exchange Server está em execução
> * Um computador que é um nó de um cluster
>
> A instalação do Servidor de Backup Azure não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

Sempre ingresse Servidor de Backup do Azure em um domínio. Se você planeja mover o servidor para um domínio diferente, instale Servidor de Backup do Azure primeiro e, em seguida, ingresse o servidor no novo domínio. Mover uma máquina de servidor de backup Azure existente para um novo domínio após a implementação não é *suportada*.

Se você enviar dados de backup para o Azure ou mantê-los localmente, Servidor de Backup do Azure deve ser registrado com um cofre de serviços de recuperação.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por padrão, os cofres dos serviços de recuperação usam o armazenamento com redundância geográfica. Se esse cofre for seu cofre principal, deixe a opção de armazenamento definida como armazenamento com redundância geográfica. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
2. Em **Propriedades**, sob **configuração de backup,** clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pacote de software

### <a name="downloading-the-software-package"></a>Baixando o pacote de software

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Se você já tiver um cofre dos serviços de recuperação aberto, vá para a etapa 3. Se não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal Azure, no menu principal, clique em **Navegar**.

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

    ![Introdução ao backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    No **Getting Started com** lâmina de reserva que abre, **os Backup Goals** serão selecionados automaticamente.

    ![Backup-metas-padrão-abertas](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Na lâmina **de backup Goal,** a partir do menu **de funcionamento** da sua carga de trabalho, selecione **On-premises**.

    ![locais e cargas de trabalho como metas](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A partir do **menu "O que quer fazer backup",** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure e, em seguida, clique em **OK**.

    O **Getting Started com o** assistente de reserva muda a opção de infraestrutura **prepare** para fazer backup das cargas de trabalho para o Azure.

   > [!NOTE]
   > Se pretender apenas fazer cópias de segurança de ficheiros e pastas, recomendamos a utilização do agente De backup Azure e seguindo a orientação no artigo, [primeiro olhar: cópia de ficheiros e pastas](backup-try-azure-backup-in-10-mins.md). Se você for proteger mais de arquivos e pastas, ou se estiver planejando expandir as necessidades de proteção no futuro, selecione essas cargas de trabalho.
   >
   >

    ![Alteração do assistente de Introdução](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Na lâmina de **infraestrutura Prepare** que se abre, clique nos links **de descarregamento** para instalar o Servidor de Backup Azure e descarregue as credenciais do cofre. Você usa as credenciais do cofre durante o registro de Servidor de Backup do Azure para o cofre dos serviços de recuperação. Os links levam você ao centro de download onde o pacote de software pode ser baixado.

    ![Preparar a infraestrutura para Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecione todos os ficheiros e clique em **Next**. Baixe todos os arquivos provenientes da página de download do Backup do Microsoft Azure e coloque todos os arquivos na mesma pasta.

    ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Como o tamanho do download de todos os arquivos juntos é > 3G, em um link de download de 10 Mbps, pode levar até 60 minutos para que o download seja concluído.

### <a name="extracting-the-software-package"></a>Extraindo o pacote de software

Depois de ter descarregado todos os ficheiros, clique em **MicrosoftAzureBackupInstaller.exe**. Isto irá iniciar o Assistente de Configuração de **Backup Microsoft Azure** para extrair os ficheiros de configuração para um local especificado por si. Continue através do assistente e clique no botão **Extrato** para iniciar o processo de extração.

> [!WARNING]
> Pelo menos 4 GB de espaço livre são necessários para extrair os arquivos de instalação.
>
>

![Assistente de instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Uma vez concluído o processo de extração, verifique a caixa para lançar a *configuração.exe* recém-extraída para começar a instalar o Microsoft Azure Backup Server e clicar no botão **'Terminar'.**

### <a name="installing-the-software-package"></a>Instalando o pacote de software

1. Clique em **Microsoft Azure Backup** para lançar o assistente de configuração.

    ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. No ecrã Welcome, clique no botão **Seguinte.** Isto leva-o à secção *de Verificações Pré-Requisitos.* Neste ecrã, clique em **Verificar** se os pré-requisitos de hardware e software para o Servidor de Backup Azure foram cumpridos. Se todos os pré-requisitos forem atendidos com êxito, você verá uma mensagem indicando que o computador atende aos requisitos. Clique no botão **Seguinte.**

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. O Backup do Microsoft Azure Server requer SQL Server Enterprise. Além disso, o pacote de instalação Servidor de Backup do Azure é fornecido com os binários de SQL Server apropriados necessários se você não quiser usar seu próprio SQL. Ao começar com uma nova instalação do Servidor de Backup Azure, deve escolher a opção Instale nova **instância do Servidor SQL com esta Configuração** e clique no botão **'Verificar e Instalar'.** Assim que os pré-requisitos forem instalados com sucesso, clique em **Seguinte**.

    ![Verificação de Servidor de Backup do Azure-SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, faça-o e clique em **'Verificar' Novamente**. Se houver algum problema de configuração do SQL, reconfigure o SQL de acordo com as diretrizes do SQL e tente novamente instalar/atualizar MABS usando a instância existente do SQL.

   > [!NOTE]
   > Servidor de Backup do Azure não funcionará com uma instância de SQL Server remota. A instância que está sendo usada pelo Servidor de Backup do Azure precisa ser local. Caso esteja a utilizar um servidor SQL existente para MABS, a configuração do MABS apenas suporta a utilização de *instâncias nomeadas* do servidor SQL.

   **Configuração manual**

   Ao usar sua própria instância do SQL, certifique-se de adicionar Builtin\administradores à função sysadmin ao BD mestre.

    **Configuração SSRS com SQL 2017**

    Ao usar sua própria instância do SQL 2017, você precisa configurar manualmente o SSRS. Após a configuração do SSRS, certifique-se de que a propriedade *isinitializada* de SSRS está definida para *True*. Quando definido como true, MABS pressupõe que o SSRS já está configurado e ignorará a configuração do SSRS.

    Use os seguintes valores para a configuração do SSRS:
    * Conta de serviço: ' usar conta interna ' deve ser serviço de rede
    * URL do Serviço Web: 'Diretório Virtual' deve ser ReportServer_\<SQLInstanceName>
    * Base de Dados: DatabaseName deve ser ReportServer$\<SQLInstanceName>
    * URL do Portal Web: 'Diretório Virtual' deve ser Reports_\<SQLInstanceName>

    [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração ssrs.

    > [!NOTE]
    > O licenciamento para o SQL Server usado como base de dados para MABS é regido pelos Termos de [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) da Microsoft (OST). De acordo com o OST, SQL Server agrupadas com MABS podem ser usadas somente como o banco de dados para MABS.

4. Forneça uma localização para a instalação de ficheiros do servidor de backup do Microsoft Azure e clique em **Next**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    O local de rascunho é um requisito para fazer backup no Azure. Certifique-se de que a localização do risco é de pelo menos 5% dos dados previstos para serem apoiados até à nuvem. Para proteção de disco, discos separados precisam ser configurados após a conclusão da instalação. Para mais informações sobre piscinas de armazenamento, consulte [piscinas de armazenamento configure e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Forneça uma senha forte para contas restritas de utilizadores locais e clique em **Next**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se pretende utilizar o *Microsoft Update* para verificar se há atualizações e clica **em Next**.

   > [!NOTE]
   > É recomendável ter Windows Update redirecionar para Microsoft Update, que oferece atualizações de segurança e importantes para o Windows e outros produtos, como o Backup do Microsoft Azure Server.
   >
   >

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Reveja o *resumo das Definições* e clique em **Instalar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação ocorre em fases. Na primeira fase, o agente de Serviços de Recuperação do Microsoft Azure está instalado no servidor. O assistente também verifica a conectividade com a Internet. Se a conectividade com a Internet estiver disponível, você poderá prosseguir com a instalação, se não, você precisa fornecer detalhes de proxy para se conectar à Internet.

    A próxima etapa é configurar o agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, você precisará fornecer suas credenciais do cofre para registrar o computador no cofre dos serviços de recuperação. Você também fornecerá uma senha para criptografar/descriptografar os dados enviados entre o Azure e seu local. Você pode gerar automaticamente uma frase secreta ou fornecer sua própria frase secreta de 16 caracteres no mínimo. Continue com o assistente até que o agente tenha sido configurado.

    ![Servidor de Backup do Azure PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Depois que o registro do Backup do Microsoft Azure Server for concluído com êxito, o assistente de instalação geral continuará a instalação e a configuração do SQL Server e dos componentes do Servidor de Backup do Azure. Depois que a instalação do componente do SQL Server for concluída, os componentes do Servidor de Backup do Azure serão instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando a etapa de instalação for concluída, os ícones da área de trabalho do produto também serão criados. Basta clicar duas vezes no ícone para iniciar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado à máquina Servidor de Backup do Azure. Para mais informações sobre a adição de discos, consulte [piscinas de armazenamento configure e armazenamento em disco](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Você precisa adicionar armazenamento de backup mesmo se planeja enviar dados para o Azure. Na arquitetura atual do Azure Backup Server, o cofre de backup Azure detém a *segunda* cópia dos dados enquanto o armazenamento local detém a primeira cópia de backup (e obrigatória).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalar e atualizar o agente de proteção do Data Protection Manager

O MABS usa o agente de proteção do System Center Data Protection Manager. [Aqui estão os passos](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) para instalar o Agente de Proteção nos seus Servidores de Proteção.

As seções a seguir descrevem como atualizar agentes de proteção para computadores cliente.

1. Na consola de administrador do servidor de backup, selecione **Management** > **Agentes**.

2. No painel de exibição, selecione os computadores cliente para os quais você deseja atualizar o agente de proteção.

   > [!NOTE]
   > A coluna **Atualizações** do Agente indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **De Ações,** a ação **Update** só está disponível quando um computador protegido é selecionado e as atualizações estão disponíveis.
   >
   >

3. Para instalar agentes de proteção atualizados nos computadores selecionados, no painel **Ações,** selecione **Update**.

4. Para um computador cliente que não esteja ligado à rede, até que o computador esteja ligado à rede, a coluna Estado do **Agente** mostra um estado de **Atualização Pendente**.

   Depois de um computador cliente estar ligado à rede, a coluna **Agent Updates** para o computador cliente mostra um estado de **Atualização**.

## <a name="move-mabs-to-a-new-server"></a>Mover MABS para um novo servidor

Aqui estão as etapas se você precisar mover MABS para um novo servidor, mantendo o armazenamento. Isso só poderá ser feito se todos os dados estiverem em Armazenamento de Backup Moderno.

  > [!IMPORTANT]
  >
  > * O nome do novo servidor deve ter o mesmo nome que a instância de Servidor de Backup do Azure original. Você não poderá alterar o nome da nova instância de Servidor de Backup do Azure se quiser usar o pool de armazenamento anterior e o DPMDB (banco de dados MABS) para manter os pontos de recuperação.
  > * Você deve ter um backup do banco de dados MABS (DPMDB). Você precisará restaurar o banco de dados.

1. No painel de exibição, selecione os computadores cliente para os quais você deseja atualizar o agente de proteção.
2. Desligue o servidor de backup do Azure original ou retire-o da conexão.
3. Redefina a conta do computador no Active Directory.
4. Instale o servidor 2016 no novo computador e nomeie-o com o mesmo nome de computador do servidor de backup do Azure original.
5. Aderir ao Domínio
6. Instalar o servidor de backup do Azure V3 ou posterior (mover os discos do pool de armazenamento do MABS do servidor antigo e importar)
7. Restaure o DPMDB feito na etapa 1.
8. Anexe o armazenamento do servidor de backup original ao novo servidor.
9. Do SQL restaurar o DPMDB
10. Na linha de comando do administrador no novo CD do servidor para Backup do Microsoft Azure local de instalação e pasta bin

    Exemplo de percurso: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Para o backup do Azure, execute DPMSYNC-SYNC

    Se você tiver adicionado novos discos ao pool de armazenamento do DPM em vez de mover os antigos, execute DPMSYNC-Reallocatereplica

## <a name="network-connectivity"></a>Conectividade da rede

Servidor de Backup do Azure requer conectividade com o serviço de backup do Azure para que o produto funcione com êxito. Para validar se a máquina tem a conectividade com o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola PowerShell do Servidor de Backup Azure. Se a saída do cmdlet for TRUE, a conectividade existirá, caso contrário, não haverá conectividade.

Ao mesmo tempo, a assinatura do Azure precisa estar em um estado íntegro. Para saber o estado da sua subscrição e geri-la, inscreva-se no portal de [subscrição](https://account.windowsazure.com/Subscriptions).

Depois de saber o estado da conectividade do Azure e da assinatura do Azure, você pode usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Subscrição do Azure | De volta a Azure | Fazer backup em disco | Restaurar do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligada |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligada |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligada |Desprovisionada |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |
| Perda de conectividade > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Perda de conectividade > 15 dias |Desprovisionada |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperando da perda de conectividade

Se você tiver um firewall ou um proxy que está impedindo o acesso ao Azure, você precisará permitir os seguintes endereços de domínio no perfil de firewall/proxy:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Depois que a conectividade com o Azure tiver sido restaurada no computador Servidor de Backup do Azure, as operações que podem ser executadas serão determinadas pelo estado da assinatura do Azure. A tabela acima tem detalhes sobre as operações permitidas quando o computador está "conectado".

### <a name="handling-subscription-states"></a>Manipulando Estados de assinatura

É possível levar uma assinatura Azure de um estado *expirado* ou *dedprovisionado* para o estado *Ativo.* No entanto, isto tem algumas implicações no comportamento do produto enquanto o Estado não está *Ativo:*

* Uma subscrição *dedprovisionada* perde a funcionalidade para o período em que é dedprovisionada. Ao ligar *ative,* a funcionalidade do produto de backup/restauro é reativada. Os dados de backup no disco local também poderão ser recuperados se forem mantidos com um período de retenção suficientemente grande. No entanto, os dados de backup em Azure são irremediavelmente perdidos assim que a subscrição entra no estado *dedprovisionado.*
* Uma subscrição *Expirada* só perde funcionalidade até que tenha sido novamente tornada *Ativa.* Quaisquer cópias de segurança programadas para o período em que a subscrição foi *Expirada* não serão executadas.

## <a name="upgrade-mabs"></a>Atualizar MABS

Use os procedimentos a seguir para atualizar o MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Atualização do MABS v2 para v3

> [!NOTE]
>
> MABS v2 não é um pré-requisito para instalar o MABS v3. No entanto, você pode atualizar para o MABS v3 somente do MABS v2.

Use as seguintes etapas para atualizar o MABS:

1. Para atualizar do MABS v2 para o MABS v3, atualize seu sistema operacional para o Windows Server 2016 ou o Windows Server 2019, se necessário.

2. Atualize seu servidor. Os passos são semelhantes à [instalação.](#install-and-upgrade-azure-backup-server) No entanto, para as configurações do SQL, você obterá uma opção para atualizar sua instância do SQL para o SQL 2017 ou para usar sua própria instância do SQL Server 2017.

   > [!NOTE]
   >
   > Não sair enquanto a instância do SQL estiver sendo atualizada, a saída desinstalará a instância de relatórios SQL e, portanto, uma tentativa de atualizar novamente o MABS falhará.

   > [!IMPORTANT]
   >
   >  Como parte da atualização do SQL 2017, fazemos backup das chaves de criptografia do SQL e desinstalamos o Reporting Services. Após a atualização do SQL Server, o serviço de relatório (14.0.6827.4788) é instalado & chaves de criptografia são restauradas.
   >
   > Ao configurar manualmente o SQL 2017, consulte a configuração SSRS com a secção *SQL 2017* sob instruções de instalação.

3. Atualize os agentes de proteção nos servidores protegidos.
4. Os backups devem continuar sem a necessidade de reiniciar os servidores de produção.
5. Você pode começar a proteger seus dados agora. Se você estiver atualizando para Armazenamento de Backup Moderno, ao mesmo tempo em que protege, também poderá escolher os volumes nos quais deseja armazenar os backups e verificar em um espaço provisionado. [Saiba mais](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se o servidor de backup do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte este documento de [códigos](https://support.microsoft.com/kb/3041338) de erro para obter mais informações.
Também pode consultar [as FAQs relacionadas](backup-azure-backup-faq.md) com o Azure Backup

## <a name="next-steps"></a>Passos seguintes

Pode obter informações detalhadas sobre [a preparação do seu ambiente para DPM](https://technet.microsoft.com/library/hh758176.aspx) no site da Microsoft TechNet. Ele também contém informações sobre as configurações com suporte nas quais Servidor de Backup do Azure podem ser implantadas e usadas. Pode utilizar uma série de [cmdlet PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) para realizar várias operações.

Você pode usar esses artigos para obter uma compreensão mais profunda da proteção de carga de trabalho usando o Backup do Microsoft Azure Server.

* [Backup do Servidor SQL](backup-azure-backup-sql.md)
* [Backup do servidor SharePoint](backup-azure-backup-sharepoint.md)
* [Backup alternativo do servidor](backup-azure-alternate-dpm-server.md)
