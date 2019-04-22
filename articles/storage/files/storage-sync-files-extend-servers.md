---
title: Tutorial - ampliar servidores de ficheiros do Windows com o Azure File Sync | Documentos da Microsoft
description: Saiba como ampliar servidores de ficheiros do Windows com o Azure File Sync, do início ao fim.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9d7162eca3c2979b1dd333bdaf95c7c43e875b9d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049154"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Expandir servidores de ficheiros do Windows com o Azure File Sync

O artigo demonstra os passos básicos para expandir a capacidade de armazenamento de um servidor de Windows através da utilização do Azure File Sync. Embora o tutorial apresenta o Windows Server como uma máquina virtual do Azure (VM), faria normalmente, este processo para os seus servidores no local. Pode encontrar instruções para implementar o Azure File Sync em seu próprio ambiente no [implementar Azure File Sync](storage-sync-files-deployment-guide.md) artigo.

> [!div class="checklist"]
> * Implementar o Serviço de Sincronização de Armazenamento
> * Preparar o Windows Server para ser utilizado com o Azure File Sync
> * Instalar o agente do Azure File Sync
> * Registar o servidor do Windows com o serviço de sincronização de armazenamento
> * Criar um grupo de sincronização e um ponto final na cloud
> * Criar um ponto final de servidor

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Para este tutorial, terá de fazer o seguinte antes de poder implementar Azure File Sync:

- Criar uma partilha de ficheiro e da conta de armazenamento do Azure
- Configurar uma VM do Windows Server 2016 Datacenter
- Preparar a VM do Windows Server para a sincronização de ficheiros do Azure

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um ficheiro .txt

No computador local, crie uma pasta nova com o nome _FilesToSync_ e adicione um ficheiro de texto denominado _mytestdoc.txt_. deverá carregar esse ficheiro para a partilha de ficheiros mais tarde neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Depois de implementar uma conta de armazenamento do Azure, criar uma partilha de ficheiros.

1. No portal do Azure, selecione **Ir para recurso**.
1. Selecione **ficheiros** partir do painel de conta de armazenamento.

    ![Selecionar ficheiros](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecione **+ partilha de ficheiros**.

    ![Selecione o botão Adicionar uma partilha de ficheiros](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie a nova partilha de ficheiros _afsfileshare_. Introduza "1" para o **Quota**e, em seguida, selecione **criar**. A quota pode ter um máximo de 5 TiB, mas só precisa de 1 GB neste tutorial.

    ![Indique um nome e uma quota para a partilha de ficheiros nova](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione a nova partilha de ficheiros. Na localização de partilha de ficheiros, selecione **carregar**.

    ![Carregar um ficheiro](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navegue para o _FilesToSync_ pasta onde criou o seu ficheiro. txt, selecione _mytestdoc.txt_ e selecione **carregar**.

    ![Navegar para a partilha de ficheiros](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Neste momento, criou uma conta de armazenamento e uma partilha de ficheiros com um arquivo no mesmo. Em seguida, implementar uma VM do Azure com o Windows Server 2016 Datacenter para representar o servidor no local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implementar uma VM e anexar um disco de dados

1. Aceda ao portal do Azure e expanda o menu à esquerda. Escolher **criar um recurso** no canto superior esquerdo.
1. Na caixa de pesquisa acima da lista de **do Azure Marketplace** recursos, procure **do Windows Server 2016 Datacenter** e selecione-o nos resultados. Escolha **Criar**.
1. Vá para o **Noções básicas** separador. Sob **detalhes do projeto**, selecione o grupo de recursos que criou para este tutorial.

   ![Introduza as informações básicas sobre a VM no painel do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Sob **detalhes de instância**, forneça um nome VM. Por exemplo, usar _myVM_.
1. Não altere as definições predefinidas para **região**, **opções de disponibilidade**, **imagem**, e **tamanho**.
1. Em **Conta de administrador**, indique um **Nome de utilizador** e uma **palavra-passe** para a VM.
1. Sob **regras de porta de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (. 3389)** e **HTTP** no menu pendente.

1. Antes de criar a VM, tem de criar um disco de dados.

   1. Selecione **próxima: discos**.

      ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Sobre o **discos** separador, em **opções de disco**, deixe as predefinições.
   1. Sob **discos de dados**, selecione **criar e anexar um disco novo**.

   1. Utilizar as predefinições, exceto para **tamanho (GiB)**, que pode alterar para **1GB** para este tutorial.

      ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecione **OK**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   Pode selecionar o **notificações** ícone para ver a **progresso da implementação**. Criar uma nova VM, poderá demorar alguns minutos a concluir.

1. Depois de concluída a implementação de VM, selecione **Ir para recurso**.

   ![Ir para recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Em seguida, ligar à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. No portal do Azure, selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Sobre o **ligar à máquina virtual** página, mantenha as opções predefinidas para ligar ao **endereço IP** pela porta 3389. Selecione **Transferir ficheiro RDP**.

   ![Transferir o ficheiro RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o ficheiro RDP transferido e selecione **Connect** quando lhe for pedido.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Escreva o nome de utilizador como *localhost\username*, introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **continuar** para criar a ligação.

### <a name="prepare-the-windows-server"></a>Preparar o servidor do Windows

Para o servidor do Windows Server 2016 Datacenter, desative a configuração de segurança avançada do Internet Explorer. Este passo só é necessário para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

Na VM de Datacenter do Windows Server 2016, o Gestor de servidor abre automaticamente.  Se o Gestor de servidores não abrir por predefinição, pesquisá-la no Explorador de ficheiros.

1. Na **Gestor de servidores**, selecione **servidor Local**.

   ![“Servidor Local” no lado esquerdo da IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Sobre o **propriedades** painel, selecione a ligação para **configuração de segurança avançada do IE**.  

    ![Painel “Configuração de Segurança Avançada do IE”, na IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Avançada do Internet Explorer**, selecione **Desativado** em **Administradores** e **Utilizadores**.

    ![Janela pop-up da Configuração de Segurança Avançada do Internet Explorer com “Desativado” selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Agora, pode adicionar o disco de dados à VM

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Enquanto estiver da **Windows Server 2016 Datacenter** VM, selecione **ficheiros e serviços de armazenamento** > **Volumes** > **discos** .

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Com o botão direito do disco de 1 GB denominado **disco Virtual da Msft** e selecione **novo volume**.
1. Conclua o assistente. Utilizar as predefinições e anote a letra de unidade atribuída.
1. Selecione **Criar**.
1. Selecione **fechar**.

   Nesta fase, pôs o disco online e criou um volume. Abra o Explorador de ficheiros na VM do Windows Server para confirmar a presença do disco de dados adicionados recentemente.

1. No Explorador de ficheiros na VM, expanda **este PC** e abra a nova unidade. Neste exemplo, a unidade é a F:.
1. Clique com botão direito do rato e selecione **Novo** > **Pasta**. Dê à pasta o nome _FilesToSync_.
1. Abra o **FilesToSync** pasta.
1. Faça duplo clique e selecione **Novo** > **Documento de Texto**. Dê ao ficheiro de texto o nome _MyTestFile_.

    ![Adicionar um ficheiro de texto novo](media/storage-sync-files-extend-servers/new-file.png)

1. Fechar **Explorador de ficheiros** e **Gestor de servidor**.

### <a name="download-the-azure-powershell-module"></a>Transferir o módulo Azure PowerShell

Em seguida, na VM de Datacenter do Windows Server 2016, instale o módulo Azure PowerShell no servidor.

1. Na VM, abra uma janela elevada do PowerShell.
1. Execute o seguinte comando:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Se tiver uma versão de NuGet que é mais antiga do que a 2.8.5.201, lhe for pedido para transferir e instalar a versão mais recente do NuGet.

   Por predefinição, a galeria do PowerShell não está configurada como um repositório fidedigno para o PowerShellGet. Na primeira vez que utilizar o PSGallery verá a seguinte linha de comandos:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Resposta **Sim** ou **Sim para todos** para continuar com a instalação.

O módulo `Az` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação do mesmo transfere todos os módulos disponíveis do Azure Resource Manager e disponibiliza os respetivos cmdlets para utilização.

Neste momento, configurou o ambiente para o tutorial. Está pronto para implementar o serviço de sincronização de armazenamento.

## <a name="deploy-the-service"></a>Implementar o serviço

Para implementar o Azure File Sync, primeiro a colocar uma **serviço de sincronização de armazenamento** recursos num grupo de recursos para a sua subscrição selecionada. O serviço de sincronização de armazenamento herda as permissões de acesso da sua subscrição e grupo de recursos.

1. No portal do Azure, selecione **criar um recurso** e, em seguida, procure **Azure File Sync**.
1. Nos resultados da pesquisa, selecione **Azure File Sync**.
1. Selecione **Criar** para abrir o separador **Implementar Sincronização de Armazenamento**.

   ![Implementar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No painel que se abre, introduza as informações seguintes:

   | Valor | Descrição |
   | ----- | ----- |
   | **Nome** | Um nome exclusivo (por subscrição) para o Serviço de Sincronização de Armazenamento.<br><br>Uso _afssyncservice02_ para este tutorial. |
   | **Subscrição** | A subscrição do Azure que utilizar para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o serviço de sincronização de armazenamento.<br><br>Uso _afsresgroup101918_ para este tutorial. |
   | **Localização** | EUA Leste |

1. Quando tiver terminado, selecione **Criar** para implementar o **Serviço de Sincronização do Armazenamento**.
1. Selecione o **notificações** separador > **Ir para recurso**.

## <a name="install-the-agent"></a>Instalar o agente

O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure.

1. Na **Windows Server 2016 Datacenter** VM, abra **Internet Explorer**.
1. Aceda ao [Centro de Transferências da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Desloque para baixo para o **do Azure File Sync Agent** secção e selecione **transferir**.

   ![Transferência do agente do Sync](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selecione a caixa de verificação **StorageSyncAgent_V3_WS2016. EXE** e selecione **próxima**.

   ![Selecionar o agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selecione **permitir que uma vez** > **executar** > **aberto**.
1. Se ainda não tiver fechado a janela do PowerShell, feche-a.
1. Aceite as predefinições do **Assistente de Configuração do Agente de Sincronização de Armazenamento**.
1. Selecione **Instalar**.
1. Selecione **Concluir**.

Implementou o serviço de sincronização do Azure e instalado o agente da VM do Windows Server 2016 Datacenter. Agora precisa de registar a VM com o serviço de sincronização de armazenamento.

## <a name="register-windows-server"></a>Registar o Windows Server

Registar o servidor do Windows com um serviço de sincronização de armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e o serviço de sincronização de armazenamento. Um servidor só pode ser registrado para um serviço de sincronização de armazenamento. Este pode sincronizar com outros servidores e partilhas de ficheiros do Azure que estão associadas esse serviço de sincronização de armazenamento.

A interface do Usuário de registo do servidor deverá abrir automaticamente depois de instalar o agente de sincronização de ficheiros do Azure. Se isso não acontecer, pode abri-lo manualmente da localização do ficheiro: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Quando abrir a interface do Usuário de registo do servidor na VM, selecione **OK**.
1. Selecione **início de sessão** para começar.
1. Inicie sessão com as credenciais da conta do Azure e selecione **início de sessão**.
1. Forneça as seguintes informações:

   ![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valor | Descrição |
   | **Subscrição do Azure** | A subscrição que contém o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Grupo de Recursos** | O grupo de recursos que contém o serviço de sincronização de armazenamento. Uso _afsresgroup101918_ para este tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do serviço de sincronização de armazenamento. Uso _afssyncservice02_ para este tutorial. |

1. Selecione **registar** para concluir o registo do servidor.
1. Como parte do processo de registo, lhe for pedido um adicional para início de sessão. Iniciar sessão e selecione **seguinte**.
1. Selecione **OK**.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização

Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Um grupo de sincronização tem de conter um ponto final da cloud, que representa uma partilha de ficheiros do Azure. Um grupo de sincronização também tem de conter um ou mais pontos finais do servidor. Os pontos finais de servidor representam um caminho num servidor registado. Para criar um grupo de sincronização:

1. Na [portal do Azure](https://portal.azure.com/), selecione **+ grupo de sincronização** do serviço de sincronização de armazenamento. Uso *afssyncservice02* para este tutorial.

   ![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Introduza as seguintes informações para criar um grupo de sincronização com um ponto final da cloud:

   | Valor | Descrição |
   | ----- | ----- |
   | **Nome do grupo de sincronização** | Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico. Uso *afssyncgroup* para este tutorial.|
   | **Subscrição** | A subscrição na qual implementou o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Conta de armazenamento** | Escolher **selecione a conta de armazenamento**. No painel que aparece, selecione a conta de armazenamento que tenha a partilha de ficheiros do Azure que criou. Uso *afsstoracct101918* para este tutorial. |
   | **Partilha de ficheiros do Azure** | O nome da partilha de ficheiros do Azure que criou. Uso *afsfileshare* para este tutorial. |

1. Selecione **Criar**.

Se selecionar o grupo de sincronização, pode ver que tem agora um **ponto final da cloud**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor

Um ponto de final de servidor representa uma localização específica num servidor registado. Por exemplo, uma pasta num volume do servidor. Para adicionar um ponto final do servidor:

1. Selecione o grupo de sincronização recém-criado e, em seguida, selecione **adicionar ponto final de servidor**.

   ![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Sobre o **adicionar ponto final de servidor** painel, introduza as seguintes informações para criar um ponto final do servidor:

   | | |
   | ----- | ----- |
   | Valor | Descrição |
   | **Servidor registado** | O nome do servidor que criou. Uso *afsvm101918* para este tutorial. |
   | **Caminho** | O caminho do Windows Server para a unidade que criou. Uso *f:\filestosync* neste tutorial. |
   | **Arrumo na Cloud** | Deixe desativado para o tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para o tutorial. |

1. Selecione **Criar**.

Os ficheiros estão agora sincronizados na partilha de ficheiros do Azure e no Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu as etapas básicas para expandir a capacidade de armazenamento de um servidor Windows utilizando o Azure File Sync. Para obter uma visão mais completa ao planear uma implementação de sincronização de ficheiros do Azure, consulte:

> [!div class="nextstepaction"]
> [Plan for Azure File Sync deployment](./storage-sync-files-planning.md) (Planear uma implementação do Azure File Sync)
