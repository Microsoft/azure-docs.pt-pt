---
title: Tutorial-estender servidores de arquivos do Windows com Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como estender os servidores de arquivos do Windows com Sincronização de Arquivos do Azure, do início ao fim.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f71a27ea4da6bce5832287e948e0731672280196
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699491"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Expandir servidores de ficheiros do Windows com o Azure File Sync

O artigo demonstra as etapas básicas para estender a capacidade de armazenamento de um Windows Server usando Sincronização de Arquivos do Azure. Embora o tutorial retenha o Windows Server como uma VM (máquina virtual) do Azure, você normalmente faria esse processo para seus servidores locais. Você pode encontrar instruções para implantar Sincronização de Arquivos do Azure em seu próprio ambiente no artigo [implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md) .

> [!div class="checklist"]
> * Implementar o Serviço de Sincronização de Armazenamento
> * Preparar o Windows Server para ser utilizado com o Azure File Sync
> * Instalar o agente do Azure File Sync
> * Registrar o Windows Server com o serviço de sincronização de armazenamento
> * Criar um grupo de sincronização e um ponto final na cloud
> * Criar um ponto final de servidor

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Para este tutorial, você precisa fazer o seguinte para poder implantar Sincronização de Arquivos do Azure:

- Criar uma conta de armazenamento do Azure e um compartilhamento de arquivos
- Configurar uma VM do Windows Server 2016 datacenter
- Preparar a VM do Windows Server para Sincronização de Arquivos do Azure

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um ficheiro .txt

No computador local, crie uma pasta nova com o nome _FilesToSync_ e adicione um ficheiro de texto denominado _mytestdoc.txt_. Você carregará esse arquivo no compartilhamento de arquivos posteriormente neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Depois de implantar uma conta de armazenamento do Azure, você cria um compartilhamento de arquivos.

1. Na portal do Azure, selecione **ir para o recurso**.
1. Selecione **arquivos** no painel da conta de armazenamento.

    ![Selecionar arquivos](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecione **+ compartilhamento de arquivos**.

    ![Selecione o botão Adicionar compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie o novo compartilhamento de arquivo _afsfileshare_. Insira "1" para a **cota**e, em seguida, selecione **criar**. A quota pode ter um máximo de 5 TiB, mas só precisa de 1 GB neste tutorial.

    ![Indique um nome e uma quota para a partilha de ficheiros nova](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione o novo compartilhamento de arquivos. No local do compartilhamento de arquivos, selecione **carregar**.

    ![Carregar um ficheiro](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navegue até a pasta _FilesToSync_ em que você criou o arquivo. txt, selecione _mytestdoc. txt_ e selecione **carregar**.

    ![Navegar para a partilha de ficheiros](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Neste ponto, você criou uma conta de armazenamento e um compartilhamento de arquivos com um arquivo. Em seguida, implante uma VM do Azure com o Windows Server 2016 datacenter para representar o servidor local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implementar uma VM e anexar um disco de dados

1. Vá para a portal do Azure e expanda o menu à esquerda. Escolha **criar um recurso** no canto superior esquerdo.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace** , pesquise por **Windows Server 2016 datacenter** e selecione-o nos resultados. Selecione **Criar**.
1. Vá para a guia **noções básicas** . Em **detalhes do projeto**, selecione o grupo de recursos que você criou para este tutorial.

   ![Insira informações básicas sobre sua VM na folha do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Em **detalhes da instância**, forneça um nome de VM. Por exemplo, use _myVM_.
1. Não altere as configurações padrão para **região**, **Opções de disponibilidade**, **imagem**e **tamanho**.
1. Em **Conta de administrador**, indique um **Nome de utilizador** e uma **palavra-passe** para a VM.
1. Em **regras de porta de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **http** no menu suspenso.

1. Antes de criar a VM, tem de criar um disco de dados.

   1. Selecione **Avançar: discos**.

      ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na guia **discos** , em **Opções de disco**, deixe os padrões.
   1. Em **discos de dados**, selecione **criar e anexar um novo disco**.

   1. Use as configurações padrão, exceto **tamanho (GIB)** , que você pode alterar para **1 GB** para este tutorial.

      ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecione **OK**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   Você pode selecionar o ícone de **notificações** para observar o **progresso da implantação**. A criação de uma nova VM pode levar alguns minutos para ser concluída.

1. Depois que a implantação da VM for concluída, selecione **ir para o recurso**.

   ![Ir para recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Em seguida, você se conecta à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. Na portal do Azure, selecione **conectar** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na página **conectar à máquina virtual** , mantenha as opções padrão para se conectar pelo **endereço IP** pela porta 3389. Selecione **Transferir ficheiro RDP**.

   ![Transferir o ficheiro RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o arquivo RDP baixado e selecione **conectar** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de usuário como *localhost\username*, insira a senha que você criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **continuar** para criar a conexão.

### <a name="prepare-the-windows-server"></a>Preparar o Windows Server

Para o Windows Server 2016 Datacenter Server, desabilite a configuração de segurança reforçada do Internet Explorer. Este passo só é necessário para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

Na VM do Windows Server 2016 datacenter, o Gerenciador do Servidor é aberto automaticamente.  Se Gerenciador do Servidor não abrir por padrão, pesquise-o no explorador de arquivos.

1. Em **Gerenciador do servidor**, selecione **servidor local**.

   ![“Servidor Local” no lado esquerdo da IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. No painel **Propriedades** , selecione o link para **configuração de segurança aprimorada do IE**.  

    ![Painel “Configuração de Segurança Avançada do IE”, na IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Avançada do Internet Explorer**, selecione **Desativado** em **Administradores** e **Utilizadores**.

    ![Janela pop-up da Configuração de Segurança Avançada do Internet Explorer com “Desativado” selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Agora, pode adicionar o disco de dados à VM

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Ainda na VM do **Windows Server 2016 datacenter**  > , selecione **arquivos e serviços de armazenamento** **discos**de**volumes** > .

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Clique com o botão direito do mouse no disco de 1 GB denominado **MSFT Virtual Disk** e selecione **novo volume**.
1. Conclua o assistente. Use as configurações padrão e anote a letra da unidade atribuída.
1. Selecione **Criar**.
1. Selecione **fechar**.

   Nesta fase, pôs o disco online e criou um volume. Abra o explorador de arquivos na VM do Windows Server para confirmar a presença do disco de dados adicionado recentemente.

1. No explorador de arquivos da VM, expanda **este computador** e abra a nova unidade. Neste exemplo, a unidade é a F:.
1. Clique com botão direito do rato e selecione **Novo** > **Pasta**. Dê à pasta o nome _FilesToSync_.
1. Abra a pasta **FilesToSync** .
1. Faça duplo clique e selecione **Novo** > **Documento de Texto**. Dê ao ficheiro de texto o nome _MyTestFile_.

    ![Adicionar um ficheiro de texto novo](media/storage-sync-files-extend-servers/new-file.png)

1. Feche o **Explorador de arquivos** e **Gerenciador do servidor**.

### <a name="download-the-azure-powershell-module"></a>Baixar o módulo Azure PowerShell

Em seguida, na VM do Windows Server 2016 datacenter, instale o módulo Azure PowerShell no servidor.

1. Na VM, abra uma janela do PowerShell com privilégios elevados.
1. Execute o seguinte comando:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Se você tiver uma versão do NuGet que seja mais antiga que 2.8.5.201, será solicitado que você baixe e instale a versão mais recente do NuGet.

   Por predefinição, a galeria do PowerShell não está configurada como um repositório fidedigno para o PowerShellGet. Na primeira vez que você usar o PSGallery, você verá o seguinte prompt:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Responda **Sim** ou **Sim para todos** para continuar a instalação.

O módulo `Az` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação do mesmo transfere todos os módulos disponíveis do Azure Resource Manager e disponibiliza os respetivos cmdlets para utilização.

Neste ponto, você configurou seu ambiente para o tutorial. Você está pronto para implantar o serviço de sincronização de armazenamento.

## <a name="deploy-the-service"></a>Implementar o serviço

Para implantar Sincronização de Arquivos do Azure, primeiro você coloca um recurso de **serviço de sincronização de armazenamento** em um grupo de recursos para sua assinatura selecionada. O serviço de sincronização de armazenamento herda as permissões de acesso de sua assinatura e grupo de recursos.

1. Na portal do Azure, selecione **criar um recurso** e procure **sincronização de arquivos do Azure**.
1. Nos resultados da pesquisa, selecione **Azure File Sync**.
1. Selecione **Criar** para abrir o separador **Implementar Sincronização de Armazenamento**.

   ![Implementar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No painel que se abre, introduza as informações seguintes:

   | Value | Descrição |
   | ----- | ----- |
   | **Name** | Um nome exclusivo (por subscrição) para o Serviço de Sincronização de Armazenamento.<br><br>Use _afssyncservice02_ para este tutorial. |
   | **Subscrição** | A assinatura do Azure que você usa para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o serviço de sincronização de armazenamento.<br><br>Use _afsresgroup101918_ para este tutorial. |
   | **Location** | East US |

1. Quando tiver terminado, selecione **Criar** para implementar o **Serviço de Sincronização do Armazenamento**.
1. Selecione a guia **notificações** > **vá para recurso**.

## <a name="install-the-agent"></a>Instalar o agente

O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure.

1. Na VM do **Windows Server 2016 datacenter** , abra o **Internet Explorer**.
1. Aceda ao [Centro de Transferências da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Role para baixo até a seção **agente de sincronização de arquivos do Azure** e selecione **baixar**.

   ![Transferência do agente do Sync](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Marque a caixa de seleção para **StorageSyncAgent_V3_WS2016. EXE** e selecione **Avançar**.

   ![Selecionar o agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selecione **permitir após** > a**execução** > **abrir**.
1. Se ainda não tiver fechado a janela do PowerShell, feche-a.
1. Aceite as predefinições do **Assistente de Configuração do Agente de Sincronização de Armazenamento**.
1. Selecione **Instalar**.
1. Selecione **Concluir**.

Você implantou o Serviço de Sincronização do Azure e instalou o agente na VM do Windows Server 2016 datacenter. Agora, você precisa registrar a VM com o serviço de sincronização de armazenamento.

## <a name="register-windows-server"></a>Registar o Windows Server

Registrar seu Windows Server com um serviço de sincronização de armazenamento estabelece uma relação de confiança entre seu servidor (ou cluster) e o serviço de sincronização de armazenamento. Um servidor só pode ser registrado em um serviço de sincronização de armazenamento. Ele pode sincronizar com outros servidores e compartilhamentos de arquivos do Azure associados a esse serviço de sincronização de armazenamento.

A interface do usuário de registro do servidor deve ser aberta automaticamente após a instalação do agente de Sincronização de Arquivos do Azure. Se não estiver, você poderá abri-lo manualmente de seu local de arquivo:`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Quando a interface do usuário de registro do servidor abrir na VM, selecione **OK**.
1. Selecione **entrar** para começar.
1. Entre com suas credenciais de conta do Azure e selecione **entrar**.
1. Forneça as seguintes informações:

   ![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Value | Descrição |
   | **Subscrição do Azure** | A subscrição que contém o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Grupo de Recursos** | O grupo de recursos que contém o serviço de sincronização de armazenamento. Use _afsresgroup101918_ para este tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do serviço de sincronização de armazenamento. Use _afssyncservice02_ para este tutorial. |

1. Selecione **registrar** para concluir o registro do servidor.
1. Como parte do processo de registro, você será solicitado a fornecer uma entrada adicional. Entre e selecione **Avançar**.
1. Selecione **OK**.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização

Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Um grupo de sincronização deve conter um ponto de extremidade de nuvem, que representa um compartilhamento de arquivos do Azure. Um grupo de sincronização também deve conter um ou mais pontos de extremidade do servidor. Os pontos finais de servidor representam um caminho num servidor registado. Para criar um grupo de sincronização:

1. Na [portal do Azure](https://portal.azure.com/), selecione **+ grupo de sincronização** do serviço de sincronização de armazenamento. Use *afssyncservice02* para este tutorial.

   ![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Insira as informações a seguir para criar um grupo de sincronização com um ponto de extremidade de nuvem:

   | Value | Descrição |
   | ----- | ----- |
   | **Nome do grupo de sincronização** | Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico. Use *afssyncgroup* para este tutorial.|
   | **Subscrição** | A subscrição na qual implementou o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Conta de armazenamento** | Escolha **selecionar conta de armazenamento**. No painel que aparece, selecione a conta de armazenamento que tem o compartilhamento de arquivos do Azure que você criou. Use *afsstoracct101918* para este tutorial. |
   | **Partilha de ficheiros do Azure** | O nome do compartilhamento de arquivos do Azure que você criou. Use *afsfileshare* para este tutorial. |

1. Selecione **Criar**.

Se selecionar o grupo de sincronização, pode ver que tem agora um **ponto final da cloud**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor

Um ponto de extremidade do servidor representa um local específico em um servidor registrado. Por exemplo, uma pasta em um volume do servidor. Para adicionar um ponto de extremidade do servidor:

1. Selecione o grupo de sincronização criado recentemente e, em seguida, selecione **Adicionar ponto de extremidade do servidor**.

   ![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. No painel **Adicionar ponto de extremidade do servidor** , insira as informações a seguir para criar um ponto de extremidade do servidor:

   | | |
   | ----- | ----- |
   | Value | Descrição |
   | **Servidor registado** | O nome do servidor que você criou. Use *afsvm101918* para este tutorial. |
   | **Caminho** | O caminho do Windows Server para a unidade que você criou. Use *f:\filestosync* neste tutorial. |
   | **Arrumo na Cloud** | Deixe desativado para o tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para o tutorial. |

1. Selecione **Criar**.

Os ficheiros estão agora sincronizados na partilha de ficheiros do Azure e no Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu as etapas básicas para estender a capacidade de armazenamento de um Windows Server usando Sincronização de Arquivos do Azure. Para obter uma visão mais detalhada do planejamento de uma implantação de Sincronização de Arquivos do Azure, consulte:

> [!div class="nextstepaction"]
> [Plan for Azure File Sync deployment](./storage-sync-files-planning.md) (Planear uma implementação do Azure File Sync)
