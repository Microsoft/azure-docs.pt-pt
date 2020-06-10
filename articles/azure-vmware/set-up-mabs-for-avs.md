---
title: Configurar o Microsoft Azure Backup Server para Azure VMware Solution (AVS)
description: Confende o seu ambiente Azure VMware Solution (AVS) para fazer backup de máquinas virtuais utilizando o Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 23c29f453587ac7a232c21e43fa6fb45193881bc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613111"
---
# <a name="set-up-microsoft-azure-backup-server-for-avs"></a>Configurar o Microsoft Azure Backup Server para AVS

O Microsoft Azure Backup Server é um sistema robusto de backup e recuperação da empresa que contribui para a sua estratégia de Continuidade e Recuperação de Desastres (BCDR). Durante a pré-visualização do AVS, pode configurar apenas a cópia de segurança virtual do nível da máquina utilizando o Servidor de Backup Azure. 

O Azure Backup Server pode armazenar dados de backup para:

- **Disco**: Para armazenamento de curto prazo, o Azure Backup Server reserva dados para piscinas de discos.

- **Azure**: Para armazenamento de curto e longo prazo fora das instalações, os dados do Azure Backup Server armazenados em piscinas de discos podem ser apoiados até à nuvem Microsoft Azure utilizando o serviço Azure Backup.

Quando ocorrerem falhas e os dados de origem não estiverem disponíveis, pode utilizar o Azure Backup Server para restaurar facilmente os dados na fonte ou numa localização alternativa. Desta forma, se os dados originais não estiverem disponíveis devido a problemas planeados ou inesperados, pode facilmente restaurar os dados para uma localização alternativa.

Neste artigo, ajudamo-lo a preparar o seu ambiente AVS para fazer backup de máquinas virtuais (VMs) utilizando o Azure Backup Server.  Nós o levamos através de passos para: 

> [!div class="checklist"]
> * Determinar o tipo e o tamanho do disco VM recomendados para usar
> * Criar um cofre dos Serviços de Recuperação que armazena os pontos de recuperação
> * Desempece a replicação de armazenamento para um cofre dos Serviços de Recuperação
> * Adicionar armazenamento ao Azure Backup Server

## <a name="supported-vmware-features"></a>Funcionalidades de VMware suportadas

- **Cópia de segurança sem agente:** O Azure Backup Server não necessita de um agente para ser instalado no servidor vCenter ou ESXi para fazer a cópia de segurança da máquina virtual. Em vez disso, basta fornecer o endereço IP ou o nome de domínio totalmente qualificado (FQDN) e credenciais de inscrição usadas para autenticar o servidor VMware com O Servidor de Backup Azure.

- **Backup integrado na nuvem:** O Azure Backup Server protege as cargas de trabalho para o disco e para a nuvem. O fluxo de trabalho de backup e recuperação do Azure Backup Server ajuda-o a gerir a retenção a longo prazo e a cópia de segurança fora do local.

- **Detetar e proteger VMs geridos por vCenter:** O Azure Backup Server deteta e protege os VM implantados num servidor vCenter ou ESXi. O Azure Backup Server também deteta VMs geridos pelo vCenter, permitindo-lhe proteger grandes implementações.

- **Proteção automática ao nível da pasta:** vCenter permite organizar os seus VMs em pastas VM. O Azure Backup Server deteta estas pastas e permite-lhe proteger VMs ao nível da pasta e inclui todas as subpastas. Ao proteger as pastas, o Azure Backup Server não só protege os VMs nessa pasta como também protege os VMs adicionados mais tarde. O Azure Backup Server deteta diariamente novos VMs e protege-os automaticamente. Ao organizar os seus VMs em pastas recursivas, o Azure Backup Server deteta e protege automaticamente os novos VMs implantados nas pastas recursivas.

- **O Azure Backup Server continua a proteger vMotioned VMs dentro do cluster:** Uma vez que os VMs são vMotioned para o equilíbrio de carga dentro do cluster, o Azure Backup Server deteta e continua a proteção VM.

- **Recuperar os ficheiros necessários mais rapidamente:** O Azure Backup Server pode recuperar ficheiros/pastas de um VM do Windows sem recuperar todo o VM.

## <a name="limitations"></a>Limitações

- Atualização O Rollup 1 para O Azure Backup Server v3 deve ser instalado.

- Não é possível fazer cópias de segurança antes da primeira cópia de segurança do Azure Backup Server. Assim que o Azure Backup Server completar a primeira cópia de segurança, poderá fazer cópias de segurança.

- O Azure Backup Server não pode proteger VMware VMs com discos de passagem e mapeamentos de dispositivos crus físicos (pRDM).

- O Azure Backup Server não consegue detetar ou proteger vApps VMware.

**Para configurar o Microsoft Azure Backup Server para Azure VMware Solution (AVS), tem de completar os seguintes passos:**

- Configurar os pré-requisitos e o ambiente

- Criar cofre de serviços de recuperação Azure

- Descarregue e instale o Servidor de Backup Azure 

- Adicionar armazenamento ao Azure Backup Server 

**Arquitetura de Implantação**  
O Microsoft Azure Backup Server é implementado como Azure IaaS VM para proteger VMs AVS.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Arquitetura de implantação AVS" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente do Servidor de Backup Azure

Considere as recomendações nesta secção ao instalar o Servidor de Backup Azure no seu ambiente Azure.

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Certifique-se de que [configura a rede para a sua nuvem privada VMWare em Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Determinar o tamanho da máquina virtual

Tem de criar uma máquina virtual Windows na rede virtual que criou no passo acima. Ao escolher um servidor para executar o Azure Backup Server, recomenda-se que comece com uma imagem de galeria do Windows Server 2019 Datacenter. O Criar a sua primeira máquina virtual Windows no tutorial [do portal Azure](../virtual-machines/windows/quick-create-portal.md) começa com o VM recomendado em Azure, mesmo que nunca tenha usado Azure.

A tabela abaixo resume o número máximo de cargas de trabalho protegidas para cada tamanho da máquina virtual do Azure Backup Server. A informação é baseada no desempenho interno e nos testes de escala com valores canónicos para o tamanho e volume da carga de trabalho. O tamanho real da carga de trabalho pode ser maior, mas deve ser acomodado pelos discos ligados à máquina virtual do Azure Backup Server.

| Cargas de trabalho protegidas máximas | Tamanho médio da carga de trabalho | Volume médio da carga de trabalho (diário) | IOPS de armazenamento de min | Tipo /Tamanho recomendado do disco      | Tamanho VM recomendado |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Volume a 5%                   | 2000             | HDD padrão (8 TB ou acima do tamanho por disco)  | A4V2       |
| 40                      | 150 GB                | Volume a 10%                  | 4500             | Premium SSD* (1 TB ou superior por disco) | DS3_V2     |
| 60                      | 200 GB                | Volume a 10%                  | 10500            | Premium SSD* (8 TB ou acima do tamanho por disco) | DS3_V2     |

* Para obter os IOPs necessários, utilize discos mínimos recomendados ou de tamanho superior. O disco de menor tamanho oferece IOPs mais baixos.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar num servidor dedicado e de uso único. Não é possível instalar o Servidor de Backup Azure num computador:
> * Funcionando como controlador de domínio
> * Tem a função do Servidor de Aplicações instalada
> * Este é um servidor de gestão de gestor de operações do System Center
> * Servidor de troca de execução
> * Este é um nó de um aglomerado

### <a name="disks-and-storage"></a>Discos e armazenamento

O Azure Backup Server requer discos para instalação, incluindo ficheiros de sistema, ficheiros de instalação, software pré-requisito, ficheiros de base de dados e discos dedicados para o pool de armazenamento.

| Requisito                      | Tamanho recomendado  |
|----------------------------------|-------------------------|
| Instalação do Servidor de Backup Azure                | Local de instalação: 3 GB<br />Unidade de ficheiros da base de dados: 900 MB<br />Unidade do sistema: 1 GB para instalação SQL<br /><br />Além disso, você precisará de espaço para o Azure Backup Server copiar o catálogo de ficheiros para um local de instalação temporária ao arquivar.      |
| Disco para o agrupamento de armazenamento<br />(Utiliza volumes básicos, não pode estar num disco dinâmico.) | 2 a 3 vezes o tamanho dos dados protegidos<br />Para um cálculo detalhado do armazenamento, consulte [o DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

O [Programar um disco de dados gerido a um VM do Windows utilizando os](../virtual-machines/windows/attach-managed-disk-portal.md) artigos do portal Azure mostra-lhe como anexar um novo disco de dados gerido a um VM Azure existente.

> [!NOTE]
> Um único Azure Backup Server tem um limite suave de 120 TB para a piscina de armazenamento.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup no disco local e no Azure

Armazenar dados de backup no Azure reduz a infraestrutura de backup no Azure Backup Server VM. Para recuperação operacional (backup), o Azure Backup Server armazena dados de backup em discos Azure ligados ao VM. Uma vez que os discos e o espaço de armazenamento são ligados ao VM, o Azure Backup Server gere o armazenamento para si. A quantidade de armazenamento de dados de backup depende do número e tamanho dos discos ligados a cada VM Azure, e cada tamanho do Azure VM tem um número máximo de discos que podem ser anexados. Por exemplo, A2 são quatro discos. A3 são oito discos. A4 tem 16 discos. Mais uma vez, o tamanho e o número de discos determinam a capacidade total de armazenamento de backup.

> [!IMPORTANT]
> **Não** deve reter dados de recuperação operacional em discos ligados ao Servidor de Backup Azure durante mais de cinco dias. Se os dados tão velhos 5 dias, guarde-os no cofre dos Serviços de Recuperação de Azure.

Para armazenar dados de backup em Azure, crie ou utilize um cofre dos Serviços de Recuperação. Ao preparar-se para apoiar a carga de trabalho do Servidor de Backup Azure, [configura o cofre dos Serviços de Recuperação](#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de reserva on-line funciona, um ponto de recuperação é criado no cofre. Cada cofre dos Serviços de Recuperação tem até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e quanto tempo são retidos, pode reter dados de backup durante muitos anos. Por exemplo, pode criar pontos de recuperação mensais e retê-los durante cinco anos.

> [!IMPORTANT]
> Quer envie dados de backup para o Azure ou o mantenha localmente, tem de registar o Azure Backup Server com um cofre dos Serviços de Recuperação.

### <a name="scale-deployment"></a>Implantação de escala

Se quiser escalar a sua implantação, tem as seguintes opções:

- **Scale-up** - Aumente o tamanho do Azure Backup Server VM de série A para série DS3 e aumente o armazenamento local.

- **Descarrega dados** - envie dados mais antigos para o Azure e retenha apenas os dados mais recentes sobre o armazenamento anexado ao Servidor de Backup Azure.

- **Scale-out** - Adicione mais servidores de backup Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

O VM deve ter .NET Framework 3.5 SP1 ou superior instalado.

### <a name="join-a-domain"></a>Aderir a um domínio

O VM do Servidor de Backup Azure deve ser associado a um domínio, e um utilizador de domínio com privilégios de administrador no VM deve instalar o Azure Backup Server.

Embora não suportado no momento da pré-visualização, o Azure Backup Server implantado num VM Azure pode fazer backup de cargas de trabalho nos VMs em AVS, mas devem estar no mesmo domínio para permitir o funcionamento de backup.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de armazenamento que armazena os pontos de recuperação criados ao longo do tempo. Também contém políticas de backup que estão associadas a itens protegidos.

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

1. No menu à esquerda, selecione **Todos os serviços**.

   ![Selecione Todos os serviços](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **de todos os serviços,** *insira os Serviços de Recuperação* e selecione **os cofres** dos Serviços de Recuperação da lista.

   ![Insira e escolha cofres dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/all-services.png)

   A lista de cofres dos Serviços de Recuperação na subscrição aparece.

1. No painel de **cofres dos Serviços de Recuperação,** selecione **Add**.

   ![Adicione um cofre dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   A caixa de diálogo do **cofre dos Serviços de Recuperação** abre.

1. Fornecer valores para o **Nome,** **Assinatura,** **Grupo de Recursos**e **Localização.**

   ![Configure o cofre dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome deve ser exclusivo da assinatura Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes.

   - **Assinatura**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição padrão (sugerida). Só existem múltiplas escolhas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição do Azure.

   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Utilizar os existentes**e, em seguida, selecione um recurso da lista de drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome.

   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger as máquinas virtuais AVS, o cofre *deve* estar na mesma região que a Nuvem Privada AVS.

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

   ![Criar o cofre dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/click-create-button.png)

   Pode levar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **de Notificações** no canto superior direito do portal. Depois do seu cofre ser criado, está visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

   ![Refresque a lista de cofres de reserva](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Definir Replicação de Armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre armazenamento geo-redundante (o padrão) e armazenamento localmente redundante. O armazenamento geo-redundante copia os dados da sua conta de armazenamento para uma região secundária, tornando os seus dados duráveis. Armazenamento localmente redundante é uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento geo-redundantes e locais redundantes no [Azure Storage redundância](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Alterar o tipo de **replicação** de armazenamento (localmente redundante/ geo-redundante) para um cofre de serviços de recuperação deve ser feito antes de configurar cópias de segurança no cofre. Uma vez configurada cópia de segurança, a opção de modificar é desativada e não pode alterar o **tipo de replicação de armazenamento**.

1. A partir **de cofres dos Serviços de Recuperação,** clique no novo cofre. 

1. Em **Definições**, selecione **Propriedades**e em **Configuração de Cópia de Segurança**, clique em **Atualização**.

1. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

   ![Definir a configuração de armazenamento do novo cofre](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-software-package"></a>Pacote de Software de descarregamento e instalação

### <a name="downloading-the-software-package"></a>Descarregar o pacote de software

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se já tem um cofre dos Serviços de Recuperação aberto, continue para o próximo passo. Se não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal Azure, no menu principal, clique em **Procurar**.

   1. Na lista de recursos, escreva **Serviços de Recuperação**.

   1. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

   ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

   O dashboard do cofre selecionado é aberto.

   ![Abrir painel do cofre](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   As **Definições** abrem por defeito. Se estiver fechado, selecione **Definições** para abri-la.

   ![Abrir painel do cofre](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Clique **em Backup** para abrir o assistente "Iniciar".

   ![Backup começando](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Na janela que se abre, faça o seguinte:

   1. A partir do menu onde está a **sua carga de trabalho,** selecione **On-ins**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Onde está a sua carga de trabalho?":::

   1. A partir do menu **What you wanna back up,** selecione as cargas de trabalho que pretende proteger usando o Servidor de Backup Azure.

   1. Clique em **Preparar Infraestruturas** para descarregar e instalar o Azure Backup Server e as credenciais do cofre.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Preparar Infraestruturas":::

1. Na janela **de infraestrutura Prepare-se** que se abre, faça o seguinte:

   1. Clique no link **descarregar** para instalar o Servidor de Backup Azure.

   1. Descarregue as credenciais do cofre selecionando a **caixa de verificação de instalação do Azure Backup Server e,** em seguida, clique em **Download**. Utilize as credenciais do cofre durante o registo do Azure Backup Server no cofre dos serviços de recuperação. Os links levam-no ao Centro de Descarregamento, onde descarrega o pacote de software.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infraestrutura de preparação - Servidor de Backup Azure":::

1. Na página de descarregamento, selecione todos os ficheiros e clique em **Seguinte**.

   > [!NOTE]
   > Tem de descarregar todos os ficheiros para a mesma pasta.  Uma vez que o tamanho do download dos ficheiros em conjunto é > 3GB, pode levar até 60 minutos para o download para ser concluído. 

   ![Baixar centro 1](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extracting-the-software-package"></a>Extrair o pacote de software

Se descarregou o pacote de software para um servidor diferente, copie os ficheiros para a Máquina Virtual que criou para implementar o Servidor de Backup Azure.

> [!WARNING]
> São necessários pelo menos 4 GB de espaço livre para extrair os ficheiros de configuração.

1. Depois de ter descarregado todos os ficheiros, clique duas vezes no **MicrosoftAzureBackupInstaller.exe** para abrir o **Microsoft Azure Backup Setup Wizard** e, em seguida, clique em **Seguinte**.

1. Selecione a localização para extrair os ficheiros e clique em **Seguinte**.

1. Clique **em Extrato** para iniciar o processo de extração.

   ![Microsoft Azure Backup Setup Wizard](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Uma vez extraído, selecione a opção para **Executar setup.exe** e, em seguida, clique em **Terminar**.

> [!TIP]
> Também pode localizar o ficheiro setup.exe a partir da pasta onde extraiu o pacote de software.

### <a name="installing-the-software-package"></a>Instalação do pacote de software

1. Na janela de configuração em instalação, clique em **Microsoft Azure Backup** para abrir o assistente de configuração.

   ![Microsoft Azure Backup Setup Wizard](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. No ecrã Welcome, clique **em Seguinte** para continuar as Verificações Pré-Requisitos.

1. Clique **em Verificar** se os pré-requisitos de hardware e software para O Servidor de Backup Azure estão cumpridos. Se formos cumpridos com sucesso, clique em **Seguinte**.

   ![Azure Backup Server - Bem-vindo e Pré-Requisitos](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. O pacote de instalação do Azure Backup Server vem agregado com os binários sql server apropriados necessários. Ao iniciar uma nova instalação do Servidor de Backup Azure, selecione a **nova instância do SQL Server com esta** opção de Configuração e clique em Verificar e **Instalar**.

   ![Azure Backup Server - verificação DO SQL](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Se desejar utilizar o seu próprio servidor SQL, as versões sql Server suportadas são SQL Server 2014 SP1 ou superior, 2016 e 2017. Todas as versões SQL Server devem ser Standard ou Enterprise 64-bit. O Azure Backup Server não funciona com uma instância remota do SQL Server. O caso usado pelo Azure Backup Server tem de ser local. Se estiver a utilizar um servidor SQL existente para O Servidor de Backup Azure, a configuração suporta apenas a utilização de *instâncias nomeadas* de servidor SQL.

   Se ocorrer uma falha com uma recomendação para reiniciar a máquina, faça-o e clique em **Verificar novamente**. Se existirem problemas de configuração SQL, reconfigure o SQL de acordo com as diretrizes DO SQL e retentou instalar/atualizar o Azure Backup Server utilizando a instância existente do SQL.

   **Configuração manual**

   Quando utilizar a sua própria instância de SQL, certifique-se de adicionar builtin\Administrators à função sysadmin para dominar o DB.

   **Configuração SSRS com SQL 2017**

   Quando estiver a utilizar a sua própria instância do SQL 2017, tem de configurar o SSRS manualmente. Após a configuração SSRS, certifique-se de que a propriedade *isInitializada* de SSRS está definida para *True*. Quando isto é definido para True, o MABS assume que o SSRS já está configurado e saltará a configuração SSRS.

   Para verificar o estado de configuração SSRS, execute o seguinte comando:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Utilize os seguintes valores para a configuração SSRS:
   * Conta de Serviço: "Utilizar conta incorporada" deve ser Serviço de Rede
   * URL do Serviço Web: 'Diretório Virtual' deve ser ReportServer_\<SQLInstanceName>
   * Base de dados: O Nome da Base de Dados deve ser ReportServer$\<SQLInstanceName>
   * URL do Portal Web: 'Diretório Virtual' deve ser Reports_\<SQLInstanceName>

   [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração SSRS.

   > [!NOTE]
   > [Os Termos de Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) da Microsoft (OST) regem o licenciamento para o SQL Server usado como base de dados para Ozure Backup Server. De acordo com o OST, o SQL Server agregado com o Azure Backup Server só pode ser utilizado como base de dados para Ozure Backup Server.

1. Uma vez instalado com sucesso, clique em **Seguinte**.

1. Forneça uma localização para a instalação de ficheiros de servidor de backup do Microsoft Azure e clique em **Seguinte**.

   > [!NOTE]
   > A localização do risco é necessária para voltar a Azure. Certifique-se de que a localização do risco é de pelo menos 5% dos dados planeados para serem apoiados até à nuvem. Para a proteção do disco, os discos separados devem ser configurados uma vez concluída a instalação. Para obter mais informações sobre as piscinas de armazenamento, consulte [as piscinas de armazenamento Configure e o armazenamento em disco.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Forneça uma palavra-passe forte para contas de utilizador locais restritas e clique em **Seguinte**.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selecione se deseja utilizar o Microsoft Update para verificar se há atualizações e clique em **Seguinte**.

   > [!NOTE]
   > Recomendamos que o Windows Update seja redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos como o Microsoft Azure Backup Server.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Reveja o *Resumo das Definições* e clique em **Instalar.**

   A instalação acontece por fases. A primeira fase instala o Microsoft Azure Recovery Services Agent e a segunda fase verifica a conectividade da Internet. Se a conectividade da Internet estiver disponível, pode continuar com a instalação; caso contrário, deve fornecer detalhes de procuração para se ligar à Internet. A fase final verifica o software pré-requisito e, se não for instalado, qualquer software em falta é instalado juntamente com o Agente de Serviços de Recuperação do Microsoft Azure.

1. Clique **em Procurar** para localizar as suas credenciais de cofre para registar a máquina no cofre dos Serviços de Recuperação e, em seguida, clique em **Seguinte**.

1. Escolha uma palavra-passe para encriptar/desencriptar os dados enviados entre a Azure e as suas instalações.

   > [!TIP]
   > Pode gerar automaticamente uma palavra-passe ou fornecer a sua própria palavra-passe mínima de 16 caracteres.

1. Introduza o local para guardar a palavra-passe e, em seguida, clique em **Seguinte** para registar o servidor.

   > [!IMPORTANT]
   > É importante também guardar a palavra-passe para um local seguro que não seja o servidor local. A Microsoft sugere fortemente a utilização de um Cofre de Chave Azure para armazenar a palavra-passe.

   Após a conclusão da configuração do Agente de Serviços de Recuperação do Microsoft Azure, passa para a instalação e configuração do SQL Server e dos componentes do Servidor de Backup Azure.

   ![Servidor do Backup do Azure](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Assim que o passo de instalação estiver concluído, clique em **Fechar**.

### <a name="install-update-rollup-1"></a>Instalar atualização Rollup 1

Instalação da atualização Rollup 1 para o Microsoft Azure Backup Server v3 é obrigatória antes de proteger as cargas de trabalho. Para ver a lista de correções de erros e as instruções de instalação do Microsoft Azure Backup Server V3 UR1, consulte o artigo KB [4534062](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Azure Backup Server V3 suporta o armazenamento de backup moderno que oferece:

-  Economia de armazenamento de 50%

-  Backups que são três vezes mais rápidos

-  Armazenamento mais eficiente

-  Armazenamento consciente da carga de trabalho

### <a name="volumes-in-backup-server"></a>Volumes no Servidor de Backup

Adicione os discos de dados com a capacidade de armazenamento necessária à máquina virtual do servidor Azure Backup se ainda não for adicionada.

O Backup Server V3 só aceita volumes de armazenamento. Quando adiciona um volume, o Backup Server forma o volume para o Sistema de Ficheiros Resiliente (ReFS), que o armazenamento de backup moderno requer.

### <a name="add-volumes-to-backup-server-disk-storage"></a>Adicione volumes ao armazenamento de discos do Servidor de Backup

1. No painel **de gestão,** rescanear o armazenamento e, em seguida, selecione **Adicionar**. 

1. Selecione entre os volumes disponíveis para adicionar à piscina de armazenamento. 

1. Depois de adicionar os volumes disponíveis, dê-lhes um nome amigável para ajudá-lo a geri-los. 

1. Clique **em OK** para formatar estes volumes para ReFS para que o Backup Server possa utilizar os benefícios do armazenamento de backup moderno.

![Adicionar Volumes disponíveis](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Passos Seguintes

Continue até ao próximo tutorial para aprender a configurar o backup de VMware VMs em execução na Solução VMware Azure (AVS) utilizando o Azure Backup Server.

> [!div class="nextstepaction"]
> [Cópia de segurança de configuração de VMS AVS](backup-avs-vms-with-mabs.md)

