---
title: Configurar o Servidor de Backup Azure para a Solução VMware Azure
description: Confende o ambiente Azure VMware Solution para fazer backup de máquinas virtuais utilizando o Servidor de Backup Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581487"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Configurar o Servidor de Backup Azure para a Solução VMware Azure

O Azure Backup Server contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR). Com a Solução Azure VMware, só é possível configurar uma cópia de segurança de nível virtual (VM) utilizando o Azure Backup Server. 

O Azure Backup Server pode armazenar dados de backup para:

- **Disco**: Para armazenamento de curto prazo, o Azure Backup Server reserva dados para piscinas de discos.
- **Azure**: Para armazenamento de curto e longo prazo fora das instalações, os dados do Azure Backup Server armazenados em piscinas de discos podem ser apoiados até à nuvem microsoft Azure utilizando o Backup Azure.

Utilize o Servidor de Backup Azure para restaurar os dados na fonte ou numa localização alternativa. Desta forma, se os dados originais não estiverem disponíveis devido a problemas planeados ou inesperados, pode restaurar os dados para uma localização alternativa.

Este artigo ajuda-o a preparar o seu ambiente de Solução VMware Azure para fazer backup de VMs utilizando o Azure Backup Server. Nós o levamos através dos degraus para: 

> [!div class="checklist"]
> * Determine o tipo e o tamanho do disco VM recomendados para utilizar.
> * Crie um cofre dos Serviços de Recuperação que armazena os pontos de recuperação.
> * Desempece a replicação de armazenamento para um cofre dos Serviços de Recuperação.
> * Adicione armazenamento ao Servidor de Backup Azure.

## <a name="supported-vmware-features"></a>Funcionalidades de VMware suportadas

- **Cópia de segurança sem agente:** O Azure Backup Server não necessita de um agente para ser instalado no servidor vCenter ou ESXi para fazer o backup do VM. Em vez disso, basta fornecer o endereço IP ou o nome de domínio totalmente qualificado (FQDN) e as credenciais de inscrição utilizadas para autenticar o servidor VMware com O Servidor de Backup Azure.
- **Cópia de segurança integrada na nuvem:** O Azure Backup Server protege as cargas de trabalho do disco e da nuvem. O fluxo de trabalho de backup e recuperação do Azure Backup Server ajuda-o a gerir a retenção a longo prazo e a cópia de segurança fora do local.
- **Detetar e proteger VMs geridos por vCenter:** O Azure Backup Server deteta e protege os VM implantados num servidor vCenter ou ESXi. O Azure Backup Server também deteta VMs geridos pelo vCenter para que possa proteger grandes implementações.
- **Proteção automática ao nível da pasta:** o vCenter permite organizar os seus VMs em pastas VM. O Azure Backup Server deteta estas pastas. Pode usá-lo para proteger os VMs ao nível da pasta, incluindo todas as subpastas. Ao proteger as pastas, o Azure Backup Server protege os VMs nessa pasta e protege os VMs adicionados mais tarde. O Azure Backup Server deteta diariamente novos VMs, protegendo-os automaticamente. Ao organizar os seus VMs em pastas recursivas, o Azure Backup Server deteta e protege automaticamente os novos VMs implantados nas pastas recursivas.
- **O Azure Backup Server continua a proteger vMotioned VMs dentro do cluster:** Uma vez que os VMs são vMotioned para o equilíbrio de carga dentro do cluster, o Azure Backup Server deteta e continua a proteção VM.
- **Recuperar os ficheiros necessários mais rapidamente:** O Azure Backup Server pode recuperar ficheiros ou pastas de um VM do Windows sem recuperar todo o VM.

## <a name="limitations"></a>Limitações

- Atualização O Rollup 1 para O Azure Backup Server v3 deve ser instalado.
- Não é possível fazer cópias de segurança antes da primeira cópia de segurança do Azure Backup Server. Depois de o Azure Backup Server terminar a primeira cópia de segurança, pode fazer cópias de segurança.
- O Azure Backup Server não pode proteger VMware VMs com discos de passagem e mapeamentos de dispositivos crus físicos (pRDMs).
- O Azure Backup Server não consegue detetar ou proteger vApps VMware.

Para configurar o Azure Backup Server para a Solução VMware Azure, tem de terminar os seguintes passos:

- Crie os pré-requisitos e o ambiente.
- Crie um cofre dos Serviços de Recuperação.
- Descarregue e instale o Azure Backup Server.
- Adicione armazenamento ao Servidor de Backup Azure.

### <a name="deployment-architecture"></a>Arquitetura de implantação

O Azure Backup Server é implantado como uma infraestrutura Azure como um VM de serviço (IaaS) para proteger VMs de Solução VMware Azure.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="O Azure Backup Server é implantado como uma infraestrutura Azure como um VM de serviço (IaaS) para proteger VMs de Solução VMware Azure." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente do Servidor de Backup Azure

Considere as recomendações nesta secção quando instalar o Azure Backup Server no seu ambiente Azure.

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Certifique-se de que [configura a rede para a sua nuvem privada VMware em Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>Determinar o tamanho da VM

Siga as instruções no Criar o seu primeiro VM do Windows no tutorial [do portal Azure.](../virtual-machines/windows/quick-create-portal.md)  Irá criar o VM na rede virtual, que criou no passo anterior. Comece com uma imagem de galeria do Centro de Dados do Windows Server 2019 para executar o Servidor de Backup Azure. 

A tabela resume o número máximo de cargas de trabalho protegidas para cada tamanho VM do Servidor de Backup Azure. A informação é baseada no desempenho interno e nos testes de escala com valores canónicos para o tamanho e volume da carga de trabalho. O tamanho real da carga de trabalho pode ser maior, mas deve ser acomodado pelos discos ligados ao VM do Servidor de Backup Azure.

| Cargas máximas de trabalho protegidas | Tamanho médio da carga de trabalho | Volume médio da carga de trabalho (diário) | IOPS de armazenamento mínimo | Tipo/tamanho recomendado do disco      | Tamanho VM recomendado |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Volume a 5%                   | 2.000             | HDD padrão (8 TB ou acima do tamanho por disco)  | A4V2       |
| 40                      | 150 GB                | Volume a 10%                  | 4500             | Premium SSD* (1 TB ou superior por disco) | DS3_V2     |
| 60                      | 200 GB                | Volume a 10%                  | 10,500            | Premium SSD* (8 TB ou acima do tamanho por disco) | DS3_V2     |

*Para obter os IOPs necessários, utilize discos mínimos recomendados ou de tamanho superior. Os discos de tamanho mais pequeno oferecem IOPs mais baixos.

> [!NOTE]
> O Azure Backup Server foi concebido para funcionar num servidor dedicado e de uso único. Não é possível instalar o Servidor de Backup Azure num computador que:
> * Funciona como controlador de domínio.
> * Tem a função 'Servidor de Aplicações' instalada.
> * É um servidor de gestão de operações do System Center Operations Manager.
> * Executa o Servidor de Câmbio.
> * É um nó de um aglomerado.

### <a name="disks-and-storage"></a>Discos e armazenamento

O Azure Backup Server requer discos para instalação. 

| Requisito                      | Tamanho recomendado  |
|----------------------------------|-------------------------|
| Instalação do Servidor de Backup Azure                | Local de instalação: 3 GB<br />Unidade de ficheiros da base de dados: 900 MB<br />Unidade do sistema: 1 GB para instalação do SQL Server<br /><br />Também necessitará de espaço para o Azure Backup Server copiar o catálogo de ficheiros para um local de instalação temporária quando arquivar.      |
| Disco para o agrupamento de armazenamento<br />(Utiliza volumes básicos, não pode estar num disco dinâmico) | Duas a três vezes o tamanho dos dados protegidos.<br />Para um cálculo detalhado do armazenamento, consulte [o Planificador de Capacidade DPM](https://www.microsoft.com/download/details.aspx?id=54301).   |

Para saber como anexar um novo disco de dados gerido a um Azure VM existente, consulte [anexar um disco de dados gerido a um VM do Windows utilizando o portal Azure](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Um único Azure Backup Server tem um limite suave de 120 TB para a piscina de armazenamento.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup no disco local e no Azure

Armazenar dados de backup no Azure reduz a infraestrutura de backup no VM do Servidor de Backup Azure. Para recuperação operacional (backup), o Azure Backup Server armazena dados de backup em discos Azure ligados ao VM. Depois de os discos e o espaço de armazenamento serem ligados ao VM, o Azure Backup Server gere o armazenamento para si. A quantidade de armazenamento depende do número e tamanho dos discos ligados a cada VM Azure. Cada tamanho do Azure VM tem um número máximo de discos que podem ser ligados. Por exemplo, A2 é quatro discos, A3 é oito discos e A4 é de 16 discos. Mais uma vez, o tamanho e o número de discos determinam a capacidade total de armazenamento de backup.

> [!IMPORTANT]
> *Não* deve reter dados de recuperação operacional em discos ligados ao Servidor de Backup Azure durante mais de cinco dias. Se os dados tão velhos 5 dias, guarde-os num cofre dos Serviços de Recuperação.

Para armazenar dados de backup em Azure, crie ou utilize um cofre dos Serviços de Recuperação. Quando se prepara para apoiar a carga de trabalho do Servidor de Backup Azure, [configura o cofre dos Serviços de Recuperação](#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de reserva on-line funciona, um ponto de recuperação é criado no cofre. Cada cofre dos Serviços de Recuperação tem até 9.999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e do tempo mantido, pode manter os dados de backup durante muitos anos. Por exemplo, pode criar pontos de recuperação mensais e mantê-los durante cinco anos.

> [!IMPORTANT]
> Quer envie dados de backup para o Azure ou o mantenha localmente, tem de registar o Azure Backup Server com um cofre dos Serviços de Recuperação.

### <a name="scale-deployment"></a>Implantação de escala

Se quiser escalar a sua implantação, tem as seguintes opções:

- **Escala**: Aumente o tamanho do Azure Backup Server VM de série A para série DS3 e aumente o armazenamento local.
- **Descarregar dados**: Enviar dados mais antigos para o Azure e guardar apenas os dados mais recentes sobre o armazenamento ligado à máquina do Servidor de Backup Azure.
- **Escala para fora**: Adicione mais máquinas do Servidor de Backup Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

O VM deve ter .NET Framework 3.5 SP1 ou superior instalado.

### <a name="join-a-domain"></a>Aderir a um domínio

O VM do Servidor de Backup Azure deve ser associado a um domínio. Um utilizador de domínio com privilégios de administrador no VM deve instalar o Azure Backup Server.

O Azure Backup Server implantado num Azure VM pode fazer backup de cargas de trabalho nos VMs na Solução VMware Azure. As cargas de trabalho devem estar no mesmo domínio para ativar a operação de backup.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de armazenamento que armazena os pontos de recuperação criados ao longo do tempo. Também contém políticas de backup que estão associadas a itens protegidos.

1. Inicie sessão na sua subscrição no [portal do Azure](https://portal.azure.com/).

1. No menu esquerdo, selecione **Todos os serviços**.

   ![No menu esquerdo, selecione Todos os serviços.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **de todos os serviços,** **insira os Serviços de Recuperação** e selecione **os cofres** dos Serviços de Recuperação da lista.

   ![Entre e escolha cofres dos Serviços de Recuperação.](../backup/media/backup-create-rs-vault/all-services.png)

   É apresentada a lista dos cofres dos Serviços de Recuperação na subscrição.

1. No dashboard **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

   ![Adicione um cofre dos Serviços de Recuperação.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   A caixa de diálogo **Cofre dos Serviços de Recuperação** abre-se.

1. Introduza valores para o **Nome,** **Subscrição,** **Grupo de Recursos** e **Localização.**

   ![Configure o cofre dos Serviços de Recuperação.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name** (Nome): Introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome tem de começar com uma letra e ser composto apenas por letras, números e hífenes.
   - **Subscrição**: Escolha a subscrição a utilizar. Se for membro de apenas uma subscrição, vai ver esse nome. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (sugerida). Terá várias escolhas apenas se a sua conta escolar ou profissional estiver associada a mais do que uma subscrição do Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Utilizar existente** e, em seguida, selecione um recurso da lista pendente. Para criar um novo grupo de recursos, selecione **Criar novo** e introduza o nome.
   - **Localização**: Selecione a região geográfica do cofre. Para criar um cofre para proteger as máquinas virtuais Azure VMware Solution, o cofre *deve* estar na mesma região que a nuvem privada Azure VMware Solution.

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

   ![Crie o cofre dos Serviços de Recuperação.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Pode demorar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **de Notificações** no canto superior direito do portal. Depois de criar o seu cofre, está visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Atualizar**.

   ![Refresque a lista de cofres de reserva.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Definir replicação de armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre armazenamento geo-redundante (o padrão) e armazenamento localmente redundante. O armazenamento geo-redundante copia os dados da sua conta de armazenamento para uma região secundária, tornando os seus dados duráveis. Armazenamento localmente redundante é uma opção mais barata que não é tão durável. Para saber mais sobre opções de armazenamento geo-redundantes e localmente redundantes, consulte [a redundância do Azure Storage](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Alterar a configuração da replicação de **armazenamento Localmente redundante/Geo-redundante** para um cofre dos Serviços de Recuperação deve ser feito antes de configurar cópias de segurança no cofre. Depois de configurar cópias de segurança, a opção de modificar é desativada e não pode alterar o tipo de replicação de armazenamento.

1. A partir **de cofres dos Serviços de Recuperação,** selecione o novo cofre. 

1. Em **Definições**, selecione **Propriedades**. Na **configuração de cópia de segurança**, selecione **Update**.

1. Selecione o tipo de replicação de armazenamento e **selecione Guardar**.

## <a name="download-and-install-the-software-package"></a>Descarregue e instale o pacote de software

Siga os passos nesta secção para descarregar, extrair e instalar o pacote de software.

### <a name="download-the-software-package"></a>Descarregue o pacote de software

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se já tem um cofre dos Serviços de Recuperação aberto, continue para o próximo passo. Se não tiver um cofre dos Serviços de Recuperação aberto e estiver no portal Azure, no menu principal, **selecione Procurar**.

   1. Na lista de recursos entram os **Serviços de Recuperação.**

   1. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **os cofres dos Serviços de Recuperação,** selecione-o.

   ![Criar serviços de recuperação passo 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

   O dashboard do cofre selecionado é aberto.

   ![O dashboard do cofre selecionado é aberto.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   A opção **Definições** abre por predefinição. Se estiver fechado, selecione **Definições** para abri-la.

   ![A opção Definições abre por predefinição. Se estiver fechado, selecione Definições para abri-la.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Selecione **Backup** para abrir o assistente **"Iniciar".**

   ![Selecione Backup para abrir o assistente "Iniciar".](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Na janela que se abre:

   1. A partir do menu **Onde está a correr a sua carga de trabalho,** selecione **On-Premis**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Onde está a sua carga de trabalho?":::

   1. A partir do menu **O que pretende fazer para fazer o backup,** selecione as cargas de trabalho que pretende proteger utilizando o Servidor de Backup Azure.

   1. Selecione **Prepare a Infraestrutura** para descarregar e instalar o Azure Backup Server e as credenciais do cofre.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Preparar Infraestruturas":::

1. Na janela **de infraestrutura Prepare-se** que abre:

   1. Selecione o link **descarregar** para instalar o Servidor de Backup Azure.

   1. Selecione **Já descarregue ou utilize a mais recente instalação do Azure Backup Server** e, em seguida, **descarregue** para descarregar as credenciais do cofre. Utilizará estas credenciais quando registar o Servidor de Backup Azure no cofre dos Serviços de Recuperação. Os links levam-no ao Centro de Descarregamento, onde descarrega o pacote de software.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infraestrutura de preparação - Servidor de Backup Azure":::

1. Na página de descarregamento, selecione todos os ficheiros e selecione **Next**.

   > [!NOTE]
   > Tem de descarregar todos os ficheiros para a mesma pasta. Como o tamanho do download dos ficheiros em conjunto é superior a 3 GB, pode levar até 60 minutos para o download ser concluído. 

   ![Na página de descarregamento, selecione todos os ficheiros e selecione Seguinte.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Extrair o pacote de software

Se descarregou o pacote de software para um servidor diferente, copie os ficheiros para o VM que criou para implementar o Azure Backup Server.

> [!WARNING]
> São necessários pelo menos 4 GB de espaço livre para extrair os ficheiros de configuração.

1. Depois de ter descarregado todos os ficheiros, clique duas vezes **MicrosoftAzureBackupInstaller.exe** para abrir o assistente de configuração de backup do **Microsoft Azure** e, em seguida, selecione **Next**.

1. Selecione a localização para extrair os ficheiros e selecione **Next**.

1. **Selecione Extrato** para iniciar o processo de extração.

   ![Selecione Extrato para iniciar o processo de extração.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Uma vez extraído, selecione a opção para **executar setup.exe** e, em seguida, selecione **Terminar**.

> [!TIP]
> Também pode localizar o ficheiro setup.exe da pasta onde extraiu o pacote de software.

### <a name="install-the-software-package"></a>Instale o pacote de software

1. Na janela de configuração em **'Instalar'** selecione **Microsoft Azure Backup** para abrir o assistente de configuração.

   ![Na janela de configuração sob instalação, selecione Microsoft Azure Backup para abrir o assistente de configuração.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. No ecrã **Welcome,** selecione **Seguinte** para continuar na página **'Verificações Prévias'.**

1. Selecione **Verificar novamente** para determinar se o hardware e o software cumprem os requisitos pré-requisitos para o Azure Backup Server. Se for cumprido com sucesso, selecione **Next**.

   ![ Selecione Verificar novamente para determinar se o hardware e o software cumprem os requisitos pré-requisitos para o Azure Backup Server. Se formos cumpridos com sucesso, selecione Next.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. O pacote de instalação do Azure Backup Server vem agregado com os binários apropriados do SQL Server que são necessários. Quando iniciar uma nova instalação do Servidor de Backup Azure, selecione a nova instância do SQL Server com esta opção **de Configuração.** Em seguida, **selecione Verificar e instalar**.

   ![O pacote de instalação do Azure Backup Server vem agregado com os binários apropriados do SQL Server que são necessários.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Se pretender utilizar a sua própria instância SQL Server, as versões suportadas do SQL Server são SQL Server 2014 SP1 ou superior, 2016 e 2017. Todas as versões SQL Server devem ser Standard ou Enterprise 64-bit. O caso utilizado pelo Azure Backup Server deve ser apenas local; Não pode ser remoto. Se utilizar uma instância do Servidor SQL existente para o Azure Backup Server, a configuração suporta apenas a utilização de *instâncias nomeadas* do SQL Server.

   Se ocorrer uma falha com uma recomendação para reiniciar a máquina, faça-o e selecione **Check Again**. Para quaisquer problemas de configuração do SQL Server, reconfigure o SQL Server de acordo com as diretrizes do SQL Server. Em seguida, retentou instalar ou atualizar o Azure Backup Server utilizando a instância existente do SQL Server.

   **Configuração manual**

   Quando utilizar a sua própria instância SQL Server, certifique-se de adicionar incorporados\Administradores à função sysadmin à função sysadmin da base de dados principal.

   **Configure serviços de reporte com o SQL Server 2017**

   Se utilizar o seu exemplo de SQL Server 2017, tem de configurar manualmente os Serviços de Informação do SQL Server 2017 (SSRS). Depois de configurar a SSRS, certifique-se de definir a propriedade **IsInitializada** de SSRS para **True**. Quando definido para **True**, O Azure Backup Server assume que o SSRS já está configurado e ignora a configuração SSRS.

   Para verificar o estado de configuração SSRS, corra:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Utilize os seguintes valores para a configuração SSRS:

   * **Conta de Serviço**: **A utilização da conta incorporada** deve ser serviço de **rede**.
   * **URL do Serviço Web**: **O Diretório Virtual** deve ser **\<SQLInstanceName> ReportServer_**.
   * **Base de dados**: **O nome de base de dados** deve ser **ReportServer$ \<SQLInstanceName>**.
   * **URL do Portal Web**: **O Diretório Virtual** deve ser **\<SQLInstanceName> Reports_**.

   [Saiba mais](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) sobre a configuração SSRS.

   > [!NOTE]
   > [Os Termos de Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) da Microsoft (OST) regem o licenciamento para o SQL Server usado como base de dados para Ozure Backup Server. De acordo com o OST, utilize apenas o SQL Server agregado com o Azure Backup Server como base de dados para Ozure Backup Server.

1. Depois de a instalação ter sido bem sucedida, selecione **Next**.

1. Forneça uma localização para instalar ficheiros do Microsoft Azure Backup Server e selecione **Next**.

   > [!NOTE]
   > A localização do risco é necessária para o backup para Azure. Certifique-se de que a localização do risco é de pelo menos 5% dos dados planeados para o backup até à nuvem. Para a proteção do disco, os discos separados precisam de ser configurados após o fim da instalação. Para obter mais informações sobre piscinas de armazenamento, consulte [as piscinas de armazenamento Configure e o armazenamento em disco.](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))

   ![Forneça uma localização para a instalação de ficheiros do Microsoft Azure Backup Server e selecione Next.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Forneça uma palavra-passe forte para contas de utilizador locais restritas e selecione **Next**.

   ![Forneça uma palavra-passe forte para contas de utilizador locais restritas e selecione Next.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selecione se deseja utilizar o Microsoft Update para verificar se há atualizações e selecione **Next**.

   > [!NOTE]
   > Recomendamos que o Windows Update seja redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos como o Azure Backup Server.

   ![Selecione se deseja utilizar o Microsoft Update para verificar se há atualizações e selecione Next.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Reveja o **Resumo das Definições** e selecione **Instalar**.

   A instalação acontece por fases. 
   - A primeira fase instala o Agente de Serviços de Recuperação do Microsoft Azure.
   - A segunda fase verifica a conectividade da Internet. Se disponível, pode continuar com a instalação. Se não estiver disponível, deve fornecer detalhes de procuração para se conectar à internet. 
   - A fase final verifica o software pré-requisito. Se não for instalado, qualquer software em falta é instalado juntamente com o Agente de Serviços de Recuperação do Microsoft Azure.

1. **Selecione Procurar** para localizar as suas credenciais de cofre para registar a máquina no cofre dos Serviços de Recuperação e, em seguida, selecione **Seguinte**.

1. Selecione uma palavra-passe para encriptar ou desencriptar os dados enviados entre a Azure e as suas instalações.

   > [!TIP]
   > Pode gerar automaticamente uma palavra-passe ou fornecer a sua palavra-passe mínima de 16 caracteres.

1. Introduza o local para guardar a palavra-passe e, em seguida, selecione **Seguinte** para registar o servidor.

   > [!IMPORTANT]
   > Guarde a frase para um local seguro que não seja o servidor local. Recomendamos vivamente a utilização do Cofre da Chave Azure para armazenar a palavra-passe.

   Após o fim da configuração do Agente de Serviços de Recuperação do Microsoft Azure, o passo de instalação passa para a instalação e configuração do SQL Server e dos componentes do Servidor de Backup Azure.

   ![Após o fim da configuração do Agente de Serviços de Recuperação do Microsoft Azure, o passo de instalação passa para a instalação e configuração do SQL Server e dos componentes do Servidor de Backup Azure.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Depois de terminar o passo de instalação, selecione **Close**.

### <a name="install-update-rollup-1"></a>Instalar atualização Rollup 1

A instalação do Rollup 1 de Atualização para O Servidor de Backup Azure v3 é obrigatória antes de poder proteger as cargas de trabalho.  Pode encontrar as correções de erros e instruções de instalação no [artigo base](https://support.microsoft.com/en-us/help/4534062/)de conhecimento .

## <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Azure Backup Server v3 suporta o armazenamento de backup moderno que oferece:

-  Poupança de armazenamento de 50%.
-  Reforços que são três vezes mais rápidos.
-  Armazenamento mais eficiente.
-  Armazenamento consciente da carga de trabalho.

### <a name="volumes-in-azure-backup-server"></a>Volumes no Servidor de Backup Azure

Adicione os discos de dados com a capacidade de armazenamento necessária do Azure Backup Server VM se ainda não for adicionado.

O Azure Backup Server v3 só aceita volumes de armazenamento. Quando adiciona um volume, o Azure Backup Server forma o volume para o Sistema de Ficheiros Resiliente (ReFS), que o armazenamento de backup moderno requer.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Adicione volumes ao armazenamento de discos do Servidor backup Azure

1. No painel **de gestão,** rescanear o armazenamento e, em seguida, selecione **Adicionar**. 

1. Selecione entre os volumes disponíveis para adicionar à piscina de armazenamento. 

1. Depois de adicionar os volumes disponíveis, dê-lhes um nome amigável para ajudá-lo a geri-los. 

1. Selecione **OK** para formatar estes volumes para ReFS para que o Azure Backup Server possa utilizar os benefícios modernos de armazenamento de backup.


## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a forma de configurar o Azure Backup Server para a Solução VMware Azure, talvez queira saber mais sobre:

- [Configurar cópias de segurança para os VMs da solução VMware Azure](backup-azure-vmware-solution-virtual-machines.md).
- [Proteger os seus VMS de Solução VMware Azure com integração do Centro de Segurança Azure](azure-security-integration.md).
