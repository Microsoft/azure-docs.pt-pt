---
title: Usar Servidor de Backup do Azure para fazer backup de cargas de trabalho no Azure
description: Use Servidor de Backup do Azure para proteger ou fazer backup de cargas de trabalho para o portal do Azure.
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: kasinh
ms.openlocfilehash: bf0e964c46088947fa50d1eadbcc12b78978251f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466381"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalar e atualizar Servidor de Backup do Azure
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Este artigo explica como preparar seu ambiente para fazer backup de cargas de trabalho usando o Backup do Microsoft Azure Server (MABS). Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de aplicativos, como VMs do Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows, a partir de um único console.

> [!NOTE]
> O Servidor de Backup do Azure agora pode proteger VMs VMware e fornece recursos de segurança aprimorados. Instale o produto, conforme explicado nas seções abaixo e o agente de backup do Azure mais recente. Para saber mais sobre como fazer backup de servidores VMware com Servidor de Backup do Azure, consulte o artigo [usar servidor de backup do Azure para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md). Para saber mais sobre os recursos de segurança, consulte a [documentação sobre recursos de segurança de backup do Azure](backup-azure-security-feature.md).
>
>

O MABS implantado em uma VM do Azure pode fazer backup de VMs no Azure, mas deve estar no mesmo domínio para habilitar a operação de backup. O processo de back-up de uma VM do Azure permanece o mesmo que fazer backup de VMs no local, no entanto, a implantação de MABS no Azure tem algumas limitações. Para obter mais informações sobre limitações [, consulte DPM como uma máquina virtual do Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e os procedimentos para restaurar as VMs implantadas usando o modelo do Resource Manager.
>
>

Servidor de Backup do Azure herda grande parte da funcionalidade de backup de carga de trabalho do Data Protection Manager (DPM). Este artigo fornece links para a documentação do DPM para explicar algumas das funcionalidades compartilhadas. Embora Servidor de Backup do Azure Compartilhe grande parte da mesma funcionalidade que o DPM, o Servidor de Backup do Azure não faz backup em fita nem se integra ao System Center.

## <a name="choose-an-installation-platform"></a>Escolher uma plataforma de instalação
A primeira etapa para colocar o Servidor de Backup do Azure em funcionamento é configurar um Windows Server. O servidor pode estar no Azure ou no local.

### <a name="using-a-server-in-azure"></a>Usando um servidor no Azure
Ao escolher um servidor para executar Servidor de Backup do Azure, é recomendável começar com uma imagem da galeria do Windows Server 2012 R2 Datacenter, do Windows Server 2016 Datacenter ou do Windows Server 2019 datacenter. O artigo [criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para introdução à máquina virtual recomendada no Azure, mesmo que você nunca tenha usado o Azure antes. Os requisitos mínimos recomendados para a VM (máquina virtual) do servidor devem ser: Standard_A4_v2 com quatro núcleos e 8 GB de RAM.

A proteção de cargas de trabalho com Servidor de Backup do Azure tem muitas nuances. O artigo [instalar o DPM como uma máquina virtual do Azure](https://technet.microsoft.com/library/jj852163.aspx), ajuda a explicar essas nuances. Antes de implantar o computador, leia este artigo completamente.

### <a name="using-an-on-premises-server"></a>Usando um servidor local
Se você não quiser executar o servidor base no Azure, poderá executar o servidor em uma VM do Hyper-V, uma VM do VMware ou um host físico. Os requisitos mínimos recomendados para o hardware do servidor são dois núcleos e 8 GB de RAM. Os sistemas operacionais com suporte são listados na tabela a seguir:

| Sistema operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows Server de 2019 |64 bits |Standard, Datacenter, Essentials (MABS v3 e posterior) |
| Windows Server 2016 e SPs mais recentes |64 bits |Standard, Datacenter, Essentials (MABS V2 e posterior) |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Standard, Datacenter, Foundation |
| Windows Storage Server 2012 R2 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup |

Você pode eliminar a duplicação do armazenamento do DPM usando a eliminação de duplicação do Windows Server. Saiba mais sobre como [o DPM e a eliminação](https://technet.microsoft.com/library/dn891438.aspx) de duplicação funcionam juntos quando implantados em VMs do Hyper-V.

> [!NOTE]
> O Servidor de Backup do Azure foi projetado para ser executado em um servidor dedicado e de finalidade única. Não é possível instalar o Servidor de Backup do Azure em:
> - Um computador em execução como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicações está instalada
> - Um computador que é um servidor de gestão do System Center Operations Manager
> - Um computador no qual o Exchange Server está em execução
> - Um computador que é um nó de um cluster

Sempre ingresse Servidor de Backup do Azure em um domínio. Se você planeja mover o servidor para um domínio diferente, instale Servidor de Backup do Azure primeiro e, em seguida, ingresse o servidor no novo domínio. *Não há suporte*para a movimentação de um computador servidor de backup do Azure existente para um novo domínio após a implantação.

Se você enviar dados de backup para o Azure ou mantê-los localmente, Servidor de Backup do Azure deve ser registrado com um cofre de serviços de recuperação.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento
A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por padrão, os cofres dos serviços de recuperação usam o armazenamento com redundância geográfica. Se esse cofre for seu cofre principal, deixe a opção de armazenamento definida como armazenamento com redundância geográfica. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione seu cofre para abrir o painel do cofre e o menu configurações. Se o menu **configurações** não abrir, clique em **todas as configurações** no painel do cofre.
2. No menu **configurações** , clique em **infraestrutura** > de backup**configuração de backup** para abrir a folha **configuração de backup** . No menu **configuração de backup** , escolha a opção de replicação de armazenamento para seu cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

## <a name="software-package"></a>Pacote de software
### <a name="downloading-the-software-package"></a>Baixando o pacote de software
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Se você já tiver um cofre dos serviços de recuperação aberto, vá para a etapa 3. Se você não tiver um cofre dos serviços de recuperação aberto, mas estiver na portal do Azure, no menu principal, clique em **procurar**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

     ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     É apresentada a lista dos cofres dos Serviços de Recuperação.
   * Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.

     ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A folha **configurações** abre por padrão. Se ele estiver fechado, clique em **configurações** para abrir a folha configurações.

    ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Clique em **backup** para abrir o assistente de introdução.

    ![Introdução ao backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Na folha **introdução com backup** que é aberta, os **objetivos de backup** serão selecionados automaticamente.

    ![Backup-metas-padrão-abertas](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Na folha **meta de backup** , no menu **onde está sua carga de trabalho em execução** , selecione **local**.

    ![locais e cargas de trabalho como metas](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    No menu suspenso do **que você deseja fazer backup?** , selecione as cargas de trabalho que você deseja proteger usando servidor de backup do Azure e clique em **OK**.

    O **introdução com** o assistente de backup alterna a opção **preparar infraestrutura** para fazer backup de cargas de trabalho no Azure.

   > [!NOTE]
   > Se você quiser apenas fazer backup de arquivos e pastas, é recomendável usar o agente de backup do Azure e seguir as orientações no artigo [primeiro, veja: fazer backup de arquivos e pastas](backup-try-azure-backup-in-10-mins.md). Se você for proteger mais de arquivos e pastas, ou se estiver planejando expandir as necessidades de proteção no futuro, selecione essas cargas de trabalho.
   >
   >

    ![Alteração do assistente de Introdução](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Na folha **preparar infraestrutura** que é aberta, clique no link **baixar** links para instalar servidor de backup do Azure e baixar as credenciais do cofre. Você usa as credenciais do cofre durante o registro de Servidor de Backup do Azure para o cofre dos serviços de recuperação. Os links levam você ao centro de download onde o pacote de software pode ser baixado.

    ![Preparar a infraestrutura para Servidor de Backup do Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecione todos os arquivos e clique em **Avançar**. Baixe todos os arquivos provenientes da página de download do Backup do Microsoft Azure e coloque todos os arquivos na mesma pasta.

    ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Como o tamanho do download de todos os arquivos juntos é > 3G, em um link de download do 10 Mbps, pode levar até 60 minutos para que o download seja concluído.

### <a name="extracting-the-software-package"></a>Extraindo o pacote de software
Depois de baixar todos os arquivos, clique em **MicrosoftAzureBackupInstaller. exe**. Isso iniciará o **Assistente de instalação backup do Microsoft Azure** para extrair os arquivos de instalação para um local especificado por você. Continue com o assistente e clique no botão **extrair** para iniciar o processo de extração.

> [!WARNING]
> Pelo menos 4 GB de espaço livre são necessários para extrair os arquivos de instalação.
>
>

![Assistente de instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Quando o processo de extração for concluído, marque a caixa para iniciar o *Setup. exe* que foi extraído de novo para iniciar a instalação do backup do Microsoft Azure Server e clique no botão **concluir** .

### <a name="installing-the-software-package"></a>Instalando o pacote de software
1. Clique em **backup do Microsoft Azure** para iniciar o assistente de instalação.

    ![Assistente de instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na tela de boas-vindas, clique no botão **Avançar** . Isso levará você para a seção *verificações de pré-requisitos* . Nessa tela, clique em **verificar** para determinar se os pré-requisitos de hardware e software para servidor de backup do Azure foram atendidos. Se todos os pré-requisitos forem atendidos com êxito, você verá uma mensagem indicando que o computador atende aos requisitos. Clique no botão **Avançar** .

    ![Servidor de Backup do Azure-bem-vindo e verificação de pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. O Backup do Microsoft Azure Server requer SQL Server Enterprise. Além disso, o pacote de instalação Servidor de Backup do Azure é fornecido com os binários de SQL Server apropriados necessários se você não quiser usar seu próprio SQL. Ao iniciar com uma nova instalação do Servidor de Backup do Azure, você deve escolher a opção **Instalar nova instância do SQL Server com essa configuração** e clicar no botão **verificar e instalar** . Depois que os pré-requisitos forem instalados com êxito, clique em **Avançar**.

    ![Verificação de Servidor de Backup do Azure-SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar o computador, faça isso e clique em **verificar novamente**. No caso de qualquer problema de configuração do SQL, reconfigure o SQL de acordo com as diretrizes do SQL e tente instalar/atualizar MABS usando a instância existente do SQL.

   > [!NOTE]
   > Servidor de Backup do Azure não funcionará com uma instância de SQL Server remota. A instância que está sendo usada pelo Servidor de Backup do Azure precisa ser local. Caso você esteja usando um SQL Server existente para MABS, a instalação do MABS só dá suporte ao uso de *instâncias nomeadas* do SQL Server.

   **Configuração manual**

   Ao usar sua própria instância do SQL, certifique-se de adicionar Builtin\administradores à função sysadmin ao BD mestre.

    **Configuração do SSRS com o SQL 2017**

    Ao usar a própria instância do SQL 2017, você precisa configurar manualmente o SSRS. Após a configuração do SSRS,  Verifique se a propriedade IsInitialized do SSRS está definida como *true*. Quando definido como true, MABS pressupõe que o SSRS já está configurado e ignorará a configuração do SSRS.

    Use os seguintes valores para a configuração do SSRS:

        - Service Account: ‘Use built-in account’ should be Network Service
        - Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>
        - Database: DatabaseName should be ReportServer$<SQLInstanceName>
        - Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração do SSRS.

4. Forneça um local para a instalação de arquivos do Backup do Microsoft Azure Server e clique em **Avançar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    O local de rascunho é um requisito para fazer backup no Azure. Verifique se o local de rascunho é de pelo menos 5% dos dados planejados para backup na nuvem. Para proteção de disco, discos separados precisam ser configurados após a conclusão da instalação. Para obter mais informações sobre pools de armazenamento, consulte [configurar pools de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Forneça uma senha forte para contas de usuário locais restritas e clique em **Avançar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se deseja usar *Microsoft Update* para verificar se há atualizações e clique em **Avançar**.

   > [!NOTE]
   > É recomendável ter Windows Update redirecionar para Microsoft Update, que oferece atualizações de segurança e importantes para o Windows e outros produtos, como o Backup do Microsoft Azure Server.
   >
   >

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Examine o *Resumo das configurações* e clique em **instalar**.

    ![Backup do Microsoft Azure PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação ocorre em fases. Na primeira fase, o agente de Serviços de Recuperação do Microsoft Azure está instalado no servidor. O assistente também verifica a conectividade com a Internet. Se a conectividade com a Internet estiver disponível, você poderá prosseguir com a instalação, se não, você precisa fornecer detalhes de proxy para se conectar à Internet.

    A próxima etapa é configurar o agente de Serviços de Recuperação do Microsoft Azure. Como parte da configuração, você precisará fornecer suas credenciais do cofre para registrar o computador no cofre dos serviços de recuperação. Você também fornecerá uma senha para criptografar/descriptografar os dados enviados entre o Azure e seu local. Você pode gerar automaticamente uma frase secreta ou fornecer sua própria frase secreta de 16 caracteres no mínimo. Continue com o assistente até que o agente tenha sido configurado.

    ![Servidor de Backup do Azure PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Depois que o registro do Backup do Microsoft Azure Server for concluído com êxito, o assistente de instalação geral continuará a instalação e a configuração do SQL Server e dos componentes do Servidor de Backup do Azure. Depois que a instalação do componente do SQL Server for concluída, os componentes do Servidor de Backup do Azure serão instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando a etapa de instalação for concluída, os ícones da área de trabalho do produto também serão criados. Basta clicar duas vezes no ícone para iniciar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de backup
A primeira cópia de backup é mantida no armazenamento anexado à máquina Servidor de Backup do Azure. Para obter mais informações sobre como adicionar discos, consulte [configurar pools de armazenamento e armazenamento em disco](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Você precisa adicionar armazenamento de backup mesmo se planeja enviar dados para o Azure. Na arquitetura atual do Servidor de Backup do Azure, o cofre de backup do Azure mantém a *segunda* cópia dos dados, enquanto o armazenamento local mantém a primeira cópia (e obrigatória) de backup.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalar e atualizar o agente de proteção do Data Protection Manager

O MABS usa o agente de proteção do System Center Data Protection Manager. [Aqui estão as etapas](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) para instalar o agente de proteção em seus servidores de proteção.

As seções a seguir descrevem como atualizar agentes de proteção para computadores cliente.

1. Na console do administrador servidor de backup, selecione**agentes**de **Gerenciamento** > .

2. No painel de exibição, selecione os computadores cliente para os quais você deseja atualizar o agente de proteção.

   > [!NOTE]
   > A coluna **atualizações do agente** indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **ações** , a ação **Atualizar** está disponível somente quando um computador protegido está selecionado e as atualizações estão disponíveis.
   >
   >

3. Para instalar os agentes de proteção atualizados nos computadores selecionados, no painel **ações** , selecione **Atualizar**.

4. Para um computador cliente que não está conectado à rede, até que o computador esteja conectado à rede, a coluna **status do agente** mostra o status **atualização pendente**.

   Depois que um computador cliente é conectado à rede, a coluna **atualizações do agente** do computador cliente mostra o status **atualizando**.

## <a name="move-mabs-to-a-new-server"></a>Mover MABS para um novo servidor

Aqui estão as etapas se você precisar mover MABS para um novo servidor, mantendo o armazenamento. Isso só poderá ser feito se todos os dados estiverem em Armazenamento de Backup Moderno.


  > [!IMPORTANT]
  > - O nome do novo servidor deve ter o mesmo nome que a instância de Servidor de Backup do Azure original. Você não poderá alterar o nome da nova instância de Servidor de Backup do Azure se quiser usar o pool de armazenamento anterior e o DPMDB (banco de dados MABS) para manter os pontos de recuperação.
  > - Você deve ter um backup do banco de dados MABS (DPMDB). Você precisará restaurar o banco de dados.

1. No painel de exibição, selecione os computadores cliente para os quais você deseja atualizar o agente de proteção.
2. Desligue o servidor de backup do Azure original ou retire-o da conexão.
3. Redefina a conta do computador no Active Directory.
4. Instale o servidor 2016 no novo computador e nomeie-o com o mesmo nome de computador do servidor de backup do Azure original.
5. Ingressar no domínio
6. Instalar o servidor de backup do Azure v2 ou posterior (mover discos do pool de armazenamento MABS do servidor antigo e importar)
7. Restaure o DPMDB feito na etapa 1.
8. Anexe o armazenamento do servidor de backup original ao novo servidor.
9. Do SQL restaurar o DPMDB
10. Na linha de comando do administrador no novo CD do servidor para Backup do Microsoft Azure local de instalação e pasta bin

    Exemplo de caminho: C:\Windows\System32 > CD "C:\Arquivos de Programas\microsoft Azure Backup\DPM\DPM\bin\"

11. Para o backup do Azure, execute DPMSYNC-SYNC

    Se você tiver adicionado novos discos ao pool de armazenamento do DPM em vez de mover os antigos, execute DPMSYNC-Reallocatereplica

## <a name="network-connectivity"></a>Conectividade de rede
Servidor de Backup do Azure requer conectividade com o serviço de backup do Azure para que o produto funcione com êxito. Para validar se o computador tem a conectividade com o Azure, use ```Get-DPMCloudConnection``` o cmdlet no console do servidor de backup do Azure PowerShell. Se a saída do cmdlet for TRUE, a conectividade existirá, caso contrário, não haverá conectividade.

Ao mesmo tempo, a assinatura do Azure precisa estar em um estado íntegro. Para descobrir o estado da sua assinatura e gerenciá-la, entre no portal de [assinatura](https://account.windowsazure.com/Subscriptions).

Depois de saber o estado da conectividade do Azure e da assinatura do Azure, você pode usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Subscrição do Azure | Fazer backup no Azure | Fazer backup em disco | Restaurar do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligado |Active |Permitido |Permitido |Permitido |Permitido |
| Ligado |Fora do prazo |Parada |Parada |Permitido |Permitido |
| Ligado |Desprovisionada |Parada |Parada |Parado e pontos de recuperação do Azure excluídos |Parada |
| Perda de conectividade > 15 dias |Active |Parada |Parada |Permitido |Permitido |
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
É possível fazer uma assinatura do Azure de um estado  expirado  ou desprovisionado para o estado *ativo* . No entanto, isso tem algumas implicações no comportamento do produto enquanto o estado não está *ativo*:

* Uma  assinatura desprovisionada perde a funcionalidade para o período em que é desprovisionada. Na ativação *ativa*, a funcionalidade do produto de backup/restauração é reativada. Os dados de backup no disco local também poderão ser recuperados se forem mantidos com um período de retenção suficientemente grande. No entanto, os dados de backup no Azure ficam irrecuperáveis quando a  assinatura entra no estado desprovisionado.
* Uma  assinatura expirada perde apenas a funcionalidade para até que ela tenha sido *ativada* novamente. Todos os backups agendados para o período em que a assinatura *expirou* não serão executados.

## <a name="upgrade-mabs"></a>Atualizar MABS
Use os procedimentos a seguir para atualizar o MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Atualização do MABS v2 para v3

> [!NOTE]
>
> MABS v2 não é um pré-requisito para instalar o MABS v3. No entanto, você pode atualizar para o MABS v3 somente do MABS v2.

Use as seguintes etapas para atualizar o MABS:

1. Para atualizar do MABS v2 para o MABS v3, atualize seu sistema operacional para o Windows Server 2016 ou o Windows Server 2019, se necessário.

2. Atualize seu servidor. As etapas são semelhantes à [instalação](#install-and-upgrade-azure-backup-server). No entanto, para as configurações do SQL, você obterá uma opção para atualizar sua instância do SQL para o SQL 2017 ou para usar sua própria instância do SQL Server 2017.

   > [!NOTE]
   >
   > Não sair enquanto a instância do SQL estiver sendo atualizada, a saída desinstalará a instância de relatórios SQL e, portanto, uma tentativa de atualizar novamente o MABS falhará.

   Coisas importantes a observar:

   > [!IMPORTANT]
   >
   >  Como parte da atualização do SQL 2017, fazemos backup das chaves de criptografia do SQL e desinstalamos o Reporting Services. Após a atualização do SQL Server, o serviço de relatório (14.0.6827.4788) é instalado & chaves de criptografia são restauradas.
   >
   > Ao configurar o SQL 2017 manualmente, consulte a seção *configuração do SSRS com o sql 2017* em instruções de instalação.

3. Atualize os agentes de proteção nos servidores protegidos.
4. Os backups devem continuar sem a necessidade de reiniciar os servidores de produção.
5. Você pode começar a proteger seus dados agora. Se você estiver atualizando para Armazenamento de Backup Moderno, ao mesmo tempo em que protege, também poderá escolher os volumes nos quais deseja armazenar os backups e verificar em um espaço provisionado. [Saiba mais](backup-mabs-add-storage.md).

> [!NOTE]
>
> Se você estiver atualizando do MABS v1 para o v2, verifique se o sistema operacional é o Windows Server 2016 ou o Windows Server 2012 R2. Para aproveitar os novos recursos como o System Center 2016 Data Protection Manager Armazenamento de Backup Moderno, você deve instalar o servidor de backup V2 no Windows Server 2016. Antes de atualizar para o ou instalar o servidor de backup v2, leia sobre os [pré-requisitos de instalação](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) aplicáveis ao mAbs.

## <a name="troubleshooting"></a>Resolução de problemas
Se Backup do Microsoft Azure servidor falhar com erros durante a fase de configuração (ou backup ou restauração), consulte este [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para obter mais informações.
Você também pode consultar as [perguntas frequentes relacionadas ao backup do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes
Você pode obter informações detalhadas sobre como [preparar seu ambiente para o DPM](https://technet.microsoft.com/library/hh758176.aspx) no site do Microsoft TechNet. Ele também contém informações sobre as configurações com suporte nas quais Servidor de Backup do Azure podem ser implantadas e usadas. Você pode usar uma série de [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) para executar várias operações.

Você pode usar esses artigos para obter uma compreensão mais profunda da proteção de carga de trabalho usando o Backup do Microsoft Azure Server.

* [SQL Server Backup](backup-azure-backup-sql.md)
* [Backup do SharePoint Server](backup-azure-backup-sharepoint.md)
* [Backup de servidor alternativo](backup-azure-alternate-dpm-server.md)
