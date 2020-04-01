---
title: Tutorial - Alargar servidores de ficheiros Windows com Sincronização de Ficheiros Azure [ Microsoft Docs
description: Saiba como alargar os servidores de ficheiros do Windows com o Azure File Sync, do início ao fim.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: eb00234fb7522c763dbaa910bee99cf327bebaf1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77597903"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Expandir os servidores de ficheiros do Windows com o Azure File Sync

O artigo demonstra os passos básicos para alargar a capacidade de armazenamento de um servidor Windows utilizando o Azure File Sync. Embora o tutorial apresente o Windows Server como uma máquina virtual Azure (VM), normalmente faria este processo para os seus servidores no local. Pode encontrar instruções para implementar o Azure File Sync no seu próprio ambiente no artigo [Deploy Azure File Sync.](storage-sync-files-deployment-guide.md)

> [!div class="checklist"]
> * Implementar o Serviço de Sincronização de Armazenamento
> * Preparar o Windows Server para ser utilizado com o Azure File Sync
> * Instalar o agente do Azure File Sync
> * Registe o Windows Server com o Serviço de Sincronização de Armazenamento
> * Criar um grupo de sincronização e um ponto final na cloud
> * Criar um ponto final de servidor

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Para este tutorial, tem de fazer o seguinte antes de poder implementar o Azure File Sync:

- Criar uma conta de armazenamento Azure e uma partilha de ficheiros
- Configurar um VM do Datacenter Do Windows Server 2016
- Prepare o VM do Servidor do Windows para sincronização de ficheiros Azure

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um ficheiro .txt

No computador local, crie uma pasta nova com o nome _FilesToSync_ e adicione um ficheiro de texto denominado _mytestdoc.txt_. Vais enviar esse ficheiro para a partilha de ficheiros mais tarde neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Depois de implementar uma conta de armazenamento Azure, cria uma partilha de ficheiros.

1. No portal Azure, selecione **Ir para recurso**.
1. Selecione **Ficheiros** do painel da conta de armazenamento.

    ![Selecione Ficheiros](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecione **+ Partilha de Ficheiros**.

    ![Selecione o botão de partilha de ficheiros adicionais](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie o novo ficheiro share _afsfileshare_. Introduza "1" para a **Quota**e, em seguida, selecione **Criar**. A quota pode ter um máximo de 5 TiB, mas só precisa de 1 GB neste tutorial.

    ![Indique um nome e uma quota para a partilha de ficheiros nova](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione a nova partilha de ficheiros. Na localização da partilha de ficheiros, selecione **Upload**.

    ![Carregar um ficheiro](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navegue na pasta _FilesToSync_ onde criou o ficheiro .txt, selecione _mytestdoc.txt_ e selecione **Upload**.

    ![Navegar para a partilha de ficheiros](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Neste momento, criou uma conta de armazenamento e uma partilha de ficheiros com um ficheiro. Em seguida, implementa um Azure VM com o Windows Server 2016 Datacenter para representar o servidor no local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implementar uma VM e anexar um disco de dados

1. Vá ao portal Azure e expanda o menu à esquerda. Escolha **Criar um recurso** no canto superior esquerdo.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace,** procure o **Windows Server 2016 Datacenter** e selecione-o nos resultados. Escolha **Criar**.
1. Vá ao separador **Basics.** Sob os detalhes do **Projeto,** selecione o grupo de recursos que criou para este tutorial.

   ![Introduza informações básicas sobre o seu VM na lâmina do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Em **detalhes de Instância,** forneça um nome VM. Por exemplo, use _myVM_.
1. Não altere as definições predefinidas para **Região,** **Opções de Disponibilidade,** **Imagem**e **Tamanho**.
1. Em **Conta de administrador**, indique um **Nome de utilizador** e uma **palavra-passe** para a VM.
1. De acordo com as regras da **porta de entrada,** escolha **permitir portas selecionadas** e, em seguida, selecionar **RDP (3389)** e **HTTP** do menu suspenso.

1. Antes de criar a VM, tem de criar um disco de dados.

   1. Selecione **Next:Disks**.

      ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. No separador **Discos,** sob **as opções do Disco,** deixe as predefinições.
   1. Em **DISCDS DE DADOS,** selecione **Criar e fixe um novo disco**.

   1. Utilize as definições predefinidas exceto o **Tamanho (GiB)**, que pode alterar para **1 GB** para este tutorial.

      ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecione **OK**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   Pode selecionar o ícone **Notificações** para ver o progresso da **Implantação**. Criar um novo VM pode levar alguns minutos para ser concluído.

1. Depois da implementação do VM estar concluída, selecione **Ir para o recurso**.

   ![Ir para recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Em seguida, ligue-se ao VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. No portal Azure, selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na página **Connect to virtual machine,** mantenha as opções predefinidas para ligar por **endereço IP** sobre a porta 3389. Selecione **Transferir ficheiro RDP**.

   ![Transferir o ficheiro RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de utilizador como *localhost\username*, introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **Continue** a criar a ligação.

### <a name="prepare-the-windows-server"></a>Preparar o servidor Windows

Relativamente ao servidor Windows Server 2016 Datacenter, desative a Configuração de Segurança Avançada do Internet Explorer. Este passo só é necessário para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

No Windows Server 2016 Datacenter VM, o Server Manager abre automaticamente.  Se o Gestor do Servidor não abrir por padrão, procure-o no Menu Iniciar.

1. No **Gestor do Servidor,** selecione **Servidor Local**.

   ![“Servidor Local” no lado esquerdo da IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. No painel **Propriedades,** selecione o link para **Configuração de Segurança Melhorada IE**.  

    ![Painel “Configuração de Segurança Avançada do IE”, na IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Avançada do Internet Explorer**, selecione **Desativado** em **Administradores** e **Utilizadores**.

    ![Janela pop-up da Configuração de Segurança Avançada do Internet Explorer com “Desativado” selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Agora, pode adicionar o disco de dados à VM

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Ainda no **Windows Server 2016 Datacenter** VM, selecione **Ficheiros e serviços** > de armazenamento**Volumes Disks** > **Disks**.

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Clique no disco de 1 GB chamado **Msft Virtual Disk** e selecione **Novo volume**.
1. Conclua o assistente. Utilize as definições predefinidas e tome nota da letra de unidade atribuída.
1. Selecione **Criar**.
1. Selecione **Fechar**.

   Nesta fase, pôs o disco online e criou um volume. Abra o Explorador de Ficheiros no VM do Servidor do Windows para confirmar a presença do disco de dados recentemente adicionado.

1. No File Explorer no VM, expanda **este PC** e abra a nova unidade. Neste exemplo, a unidade é a F:.
1. Clique à direita e selecione **Nova** > **Pasta**. Dê à pasta o nome _FilesToSync_.
1. Abra a pasta **FilesToSync.**
1. Clique à direita e selecione **Novo** > **Documento de Texto**. Dê ao ficheiro de texto o nome _MyTestFile_.

    ![Adicionar um ficheiro de texto novo](media/storage-sync-files-extend-servers/new-file.png)

1. Fechar o Explorador de **Ficheiros** e **o Gestor do Servidor.**

### <a name="download-the-azure-powershell-module"></a>Descarregue o módulo Azure PowerShell

Em seguida, no Windows Server 2016 Datacenter VM, instale o módulo PowerShell Azure no servidor.

1. No VM, abra uma janela powerShell elevada.
1. Execute o seguinte comando:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Se tiver uma versão NuGet com mais de 2.8.5.201, é-lhe pedido que descarregue e instale a versão mais recente do NuGet.

   Por predefinição, a galeria do PowerShell não está configurada como um repositório fidedigno para o PowerShellGet. A primeira vez que utiliza a GALERIA PS, vê o seguinte pedido:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Resposta **Sim** ou **Sim a Todos** para continuar com a instalação.

O módulo `Az` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação do mesmo transfere todos os módulos disponíveis do Azure Resource Manager e disponibiliza os respetivos cmdlets para utilização.

Neste momento, preparaste o teu ambiente para o tutorial. Está pronto para implantar o Serviço de Sincronização de Armazenamento.

## <a name="deploy-the-service"></a>Implementar o serviço

Para implementar o Azure File Sync, coloca-se pela primeira vez um recurso **do Serviço de Sincronização** de Armazenamento num grupo de recursos para a sua subscrição selecionada. O Serviço de Sincronização de Armazenamento herda permissões de acesso do seu grupo de subscrição e recursos.

1. No portal Azure, selecione **Criar um recurso** e, em seguida, procurar o **Azure File Sync**.
1. Nos resultados da pesquisa, selecione **Azure File Sync**.
1. Selecione **Criar** para abrir o separador **Implementar Sincronização de Armazenamento**.

   ![Implementar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No painel que se abre, introduza as informações seguintes:

   | Valor | Descrição |
   | ----- | ----- |
   | **Nome** | Um nome exclusivo (por subscrição) para o Serviço de Sincronização de Armazenamento.<br><br>Use _afssyncservice02_ para este tutorial. |
   | **Subscrição** | A subscrição Azure que utiliza para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.<br><br>Use _afsresgroup101918_ para este tutorial. |
   | **Localização** | E.U.A. Leste |

1. Quando tiver terminado, selecione **Criar** para implementar o **Serviço de Sincronização do Armazenamento**.
1. Selecione o separador **Notificações** > **vá para o recurso**.

## <a name="install-the-agent"></a>Instalar o agente

O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure.

1. No **Windows Server 2016 Datacenter** VM, abra **o Internet Explorer.**
1. Aceda ao [Centro de Transferências da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Desloque-se até à secção **De sincronia de ficheiros Azure** e selecione **Download**.

   ![Transferência do agente do Sync](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selecione a caixa de verificação para **StorageSyncAgent_V3_WS2016. EXE** e selecione **Next**.

   ![Selecionar o agente](media/storage-sync-files-extend-servers/select-agent.png)

1. **Selecione Permitir uma vez** > **Run** > **aberto**.
1. Se ainda não tiver fechado a janela do PowerShell, feche-a.
1. Aceite as predefinições do **Assistente de Configuração do Agente de Sincronização de Armazenamento**.
1. Selecione **Instalar**.
1. Selecione **Concluir**.

Implementou o Serviço de Sincronização do Azure e instalou o agente na VM do Windows Server 2016 Datacenter. Agora, tem de registar a VM no Serviço de Sincronização de Armazenamento.

## <a name="register-windows-server"></a>Registar o Windows Server

Registar o seu servidor Windows com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor só pode ser registado num Serviço de Sincronização de Armazenamento. Pode sincronizar com outros servidores e partilhas de ficheiros Azure que estão associadas a esse Serviço de Sincronização de Armazenamento.

O UI de Registo do Servidor deve ser aberto automaticamente após a instalação do agente Dessincronização de ficheiros Azure. Se não o fizer, pode abri-lo manualmente a partir da sua localização de ficheiro:`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Quando o UI de Registo do Servidor abrir no VM, selecione **OK**.
1. Selecione Iniciar o **'Iniciar' o 'Iniciar' o 'Iniciar' por 'Iniciar' o 'Sign**
1. Inscreva-se com as credenciais da sua conta Azure e selecione **Iniciar sessão**.
1. Forneça as seguintes informações:

   ![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valor | Descrição |
   | **Assinatura Azure** | A subscrição que contém o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Grupo de Recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento. Use _afsresgroup101918_ para este tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do Serviço de Sincronização de Armazenamento. Use _afssyncservice02_ para este tutorial. |

1. Selecione **Registar** para completar o registo do servidor.
1. Como parte do processo de registo, é solicitado um registo adicional. Iniciar sessão e selecionar **Seguinte**.
1. Selecione **OK**.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização

Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Um grupo de sincronização deve conter um ponto final em nuvem, o que representa uma partilha de ficheiros Azure. Um grupo de sincronização também deve conter um ou mais pontos finais do servidor. Os pontos finais de servidor representam um caminho num servidor registado. Para criar um grupo de sincronização:

1. No [portal Azure,](https://portal.azure.com/)selecione **+ Sync do** Serviço de Sincronização de Armazenamento. Use *afssyncservice02* para este tutorial.

   ![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Introduza as seguintes informações para criar um grupo de sincronização com um ponto final em nuvem:

   | Valor | Descrição |
   | ----- | ----- |
   | **Nome do grupo de sincronização** | Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico. Use *afssyncgroup* para este tutorial.|
   | **Subscrição** | A subscrição na qual implementou o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Conta de armazenamento** | Escolha **a conta de armazenamento Select**. No painel que aparece, selecione a conta de armazenamento que tem a partilha de ficheiros Azure que criou. Use *afsstoracct101918* para este tutorial. |
   | **Partilha de ficheiros Azure** | O nome da partilha de ficheiros Azure que criou. Use *afsfileshare* para este tutorial. |

1. Selecione **Criar**.

Se selecionar o grupo de sincronização, pode ver que tem agora um **ponto final da cloud**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor

Um ponto final do servidor representa uma localização específica num servidor registado. Por exemplo, uma pasta no volume do servidor. Para adicionar um ponto final do servidor:

1. Selecione o grupo de sincronização recém-criado e, em seguida, selecione **Adicionar ponto final**do servidor .

   ![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. No painel de ponto final do **servidor Adicionar,** introduza as seguintes informações para criar um ponto final do servidor:

   | | |
   | ----- | ----- |
   | Valor | Descrição |
   | **Servidor registado** | O nome do servidor que criou. Use *afsvm101918* para este tutorial. |
   | **Caminho** | O caminho do Windows Server para a unidade que criou. Utilize *f:\filestosync* neste tutorial. |
   | **Arrumo na Cloud** | Deixe desativado para o tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para o tutorial. |

1. Selecione **Criar**.

Os ficheiros estão agora sincronizados na partilha de ficheiros do Azure e no Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu os passos básicos para alargar a capacidade de armazenamento de um servidor Windows utilizando o Azure File Sync. Para uma análise mais aprofundada do planeamento de uma implementação de Sincronização de Ficheiros Azure, consulte:

> [!div class="nextstepaction"]
> [Plan for Azure File Sync deployment](./storage-sync-files-planning.md) (Planear uma implementação do Azure File Sync)
