---
title: Tutorial - Alargar os servidores de ficheiros do Windows com a Azure File Sync | Microsoft Docs
description: Saiba como estender os servidores de ficheiros do Windows com o Azure File Sync, do início ao fim.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329466"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Expandir os servidores de ficheiros do Windows com o Azure File Sync

O artigo demonstra os passos básicos para alargar a capacidade de armazenamento de um servidor Windows utilizando o Azure File Sync. Embora o tutorial tenha o Windows Server como uma máquina virtual Azure (VM), normalmente faria este processo para os seus servidores no local. Pode encontrar instruções para a implementação do Azure File Sync no seu próprio ambiente no artigo [Implementar Azure File Sync.](storage-sync-files-deployment-guide.md)

> [!div class="checklist"]
> * Implementar o Serviço de Sincronização de Armazenamento
> * Preparar o Windows Server para ser utilizado com o Azure File Sync
> * Instalar o agente do Azure File Sync
> * Registar o Servidor do Windows com o Serviço de Sincronização de Armazenamento
> * Criar um grupo de sincronização e um ponto final na cloud
> * Criar um ponto final de servidor

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Para este tutorial, tem de fazer o seguinte antes de poder implementar o Azure File Sync:

- Criar uma conta de armazenamento Azure e partilha de ficheiros
- Configurar um VM do Datacenter 2016 do Windows Server 2016
- Prepare o VM do Servidor do Windows para sincronização de ficheiros Azure

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um ficheiro .txt

No computador local, crie uma pasta nova com o nome _FilesToSync_ e adicione um ficheiro de texto denominado _mytestdoc.txt_. Você vai enviar esse ficheiro para a partilha de ficheiros mais tarde neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Depois de implementar uma conta de armazenamento Azure, cria uma partilha de ficheiros.

1. No portal Azure, selecione **Ir para o recurso**.
1. Selecione **Ficheiros** do painel de conta de armazenamento.

    ![Selecione ficheiros](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecione **+ Partilha de Ficheiros**.

    ![Selecione o botão de partilha de ficheiros adicionar](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie o novo ficheiro de partilha _de ações._ Introduza "1" para a **quota** e, em seguida, selecione **Criar**. A quota pode ter um máximo de 5 TiB, mas só precisa de 1 GB neste tutorial.

    ![Indique um nome e uma quota para a partilha de ficheiros nova](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione a nova partilha de ficheiros. Na localização da partilha de ficheiros, **selecione Upload**.

    ![Carregar um ficheiro](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navegue na pasta _FicheirosToSync_ onde criou o ficheiro .txt, selecione _mytestdoc.txt_ e selecione **Upload**.

    ![Navegar para a partilha de ficheiros](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Neste momento, criou uma conta de armazenamento e uma partilha de ficheiros com um ficheiro. Em seguida, implementa um Azure VM com o Windows Server 2016 Datacenter para representar o servidor no local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implementar uma VM e anexar um disco de dados

1. Vá ao portal Azure e expanda o menu à esquerda. Escolha **Criar um recurso** no canto superior esquerdo.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace,** procure o **Datacenter 2016 do Windows Server** e selecione-o nos resultados. Escolha **Criar**.
1. Vá ao separador **Básico.** Em **detalhes do Projeto,** selecione o grupo de recursos que criou para este tutorial.

   ![Introduza informações básicas sobre o seu VM na lâmina do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. De acordo com **os detalhes de exemplo,** forneça um nome VM. Por exemplo, use _o myVM_.
1. Não altere as definições predefinidos para **Região,** **Opções de Disponibilidade,** **Imagem** e **Tamanho**.
1. Em **Conta de administrador**, indique um **Nome de utilizador** e uma **palavra-passe** para a VM.
1. De acordo com **as regras da porta de entrada**, escolha Permitir portas **selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** do menu suspenso.

1. Antes de criar a VM, tem de criar um disco de dados.

   1. Selecione **Seguinte:Discos**.

      ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. No **separador Discos,** nas **opções do Disco,** deixe as predefinições.
   1. Em **DATA DISKS,** selecione **Criar e anexar um novo disco**.

   1. Utilize as definições predefinidos, exceto **o Tamanho (GiB),** que pode alterar para **1 GB** para este tutorial.

      ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecione **OK**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   Pode selecionar o ícone **'Notificações'** para ver o progresso da **Implementação**. Criar um novo VM pode levar alguns minutos a ser concluído.

1. Depois da sua implementação em VM estar concluída, selecione **Ir para o recurso**.

   ![Ir para recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Em seguida, ligue-se ao VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. No portal Azure, selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na página **'Ligar à máquina virtual',** mantenha as opções predefinidos para ligar por **endereço IP** sobre a porta 3389. Selecione **Transferir ficheiro RDP**.

   ![Transferir o ficheiro RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de utilizador como *nome local\username,* introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **Continue** a criar a ligação.

### <a name="prepare-the-windows-server"></a>Preparar o servidor Windows

Relativamente ao servidor Windows Server 2016 Datacenter, desative a Configuração de Segurança Avançada do Internet Explorer. Este passo só é necessário para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

No VM do Datacenter 2016 do Windows Server, o Gestor do Servidor abre automaticamente.  Se o Gestor do Servidor não abrir por predefinição, procure-o no Menu Iniciar.

1. No **Gestor do Servidor**, selecione Local **Server**.

   ![“Servidor Local” no lado esquerdo da IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. No painel **propriedades,** selecione o link para **configuração de segurança melhorada do IE**.  

    ![Painel “Configuração de Segurança Avançada do IE”, na IU do Gestor de Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Avançada do Internet Explorer**, selecione **Desativado** em **Administradores** e **Utilizadores**.

    ![Janela pop-up da Configuração de Segurança Avançada do Internet Explorer com “Desativado” selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Agora, pode adicionar o disco de dados à VM

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Enquanto ainda estiver no VM do **Datacenter 2016** do Windows Server, selecione **Ficheiros e serviços de armazenamento**  >    >  **Volumes Discos**.

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Clique com o botão direito no disco de 1 GB chamado **Msft Virtual Disk** e selecione **Novo volume**.
1. Conclua o assistente. Utilize as definições predefinidas e tome nota da letra de unidade atribuída.
1. Selecione **Criar**.
1. Selecione **Fechar**.

   Nesta fase, pôs o disco online e criou um volume. Abra o Explorador de Ficheiros no Windows Server VM para confirmar a presença do disco de dados recentemente adicionado.

1. No File Explorer no VM, expanda **este PC** e abra a nova unidade. Neste exemplo, a unidade é a F:.
1. Clique à direita e selecione **Nova**  >  **Pasta.** Dê à pasta o nome _FilesToSync_.
1. Abra a pasta **FicheirosToSync.**
1. Clique à direita e selecione **Novo**  >  **Documento de Texto.** Dê ao ficheiro de texto o nome _MyTestFile_.

    ![Adicionar um ficheiro de texto novo](media/storage-sync-files-extend-servers/new-file.png)

1. Fechar o **Explorador de Ficheiros** e **o Gestor do Servidor**.

### <a name="download-the-azure-powershell-module"></a>Descarregue o módulo Azure PowerShell

Em seguida, no VM do Datacenter 2016 do Windows Server, instale o módulo Azure PowerShell no servidor.

1. No VM, abra uma janela PowerShell elevada.
1. Execute o seguinte comando:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Se tiver uma versão NuGet com mais de 2.8.5.201, é solicitado que descarregue e instale a versão mais recente do NuGet.

   Por predefinição, a galeria do PowerShell não está configurada como um repositório fidedigno para o PowerShellGet. A primeira vez que usas a PSGallery, vês o seguinte pedido:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Responda **Sim** ou **Sim a Todos** para continuar com a instalação.

O módulo `Az` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação do mesmo transfere todos os módulos disponíveis do Azure Resource Manager e disponibiliza os respetivos cmdlets para utilização.

Neste momento, preparaste o teu ambiente para o tutorial. Está pronto para implantar o Serviço de Sincronização de Armazenamento.

## <a name="deploy-the-service"></a>Implementar o serviço

Para implementar o Azure File Sync, coloca em primeiro lugar um recurso **do Serviço de Sincronização de Armazenamento** num grupo de recursos para a sua subscrição selecionada. O Serviço de Sincronização de Armazenamento herda permissões de acesso do seu grupo de subscrição e recursos.

1. No portal Azure, **selecione Criar um recurso** e, em seguida, procure o **Azure File Sync**.
1. Nos resultados da pesquisa, selecione **Azure File Sync**.
1. Selecione **Criar** para abrir o separador **Implementar Sincronização de Armazenamento**.

   ![Implementar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No painel que se abre, introduza as informações seguintes:

   | Valor | Descrição |
   | ----- | ----- |
   | **Nome** | Um nome exclusivo (por subscrição) para o Serviço de Sincronização de Armazenamento.<br><br>Use _afssyncservice02_ para este tutorial. |
   | **Subscrição** | A assinatura Azure que utiliza para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.<br><br>Use _o afsresgroup101918_ para este tutorial. |
   | **Localização** | E.U.A. Leste |

1. Quando tiver terminado, selecione **Criar** para implementar o **Serviço de Sincronização do Armazenamento**.
1. Selecione o separador **Notificações** > **Vá ao recurso**.

## <a name="install-the-agent"></a>Instalar o agente

O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure.

1. No **Windows Server 2016 Datacenter** VM, abra o **Internet Explorer**.
1. Aceda ao [Centro de Transferências da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Desloque-se até à secção **Azure File Sync Agent** e selecione **Download**.

   ![Transferência do agente do Sync](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selecione a caixa de verificação para **StorageSyncAgent_V3_WS2016.EXE** e selecione **Seguinte**.

   ![Selecionar o agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selecione **Deixe abrir uma**  >    >  **vez.**
1. Se ainda não tiver fechado a janela do PowerShell, feche-a.
1. Aceite as predefinições do **Assistente de Configuração do Agente de Sincronização de Armazenamento**.
1. Selecione **Instalar**.
1. Selecione **Concluir**.

Implementou o Serviço de Sincronização do Azure e instalou o agente na VM do Windows Server 2016 Datacenter. Agora, tem de registar a VM no Serviço de Sincronização de Armazenamento.

## <a name="register-windows-server"></a>Registar o Windows Server

Registar o seu servidor Windows com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor só pode ser registado num Serviço de Sincronização de Armazenamento. Pode sincronizar-se com outros servidores e ações de ficheiros Azure que estão associadas a esse Serviço de Sincronização de Armazenamento.

O UI de Registo do Servidor deve abrir-se automaticamente depois de instalar o agente Azure File Sync. Se não o fizer, pode abri-lo manualmente a partir da localização do ficheiro: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Quando o UI de Registo do Servidor abrir no VM, selecione **OK**.
1. Selecione Iniciar o **S-In.**
1. Inscreva-se com as suas credenciais de conta Azure e selecione **Iniciar sinagem**.
1. Forneça as seguintes informações:

   ![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | Valor | Descrição |
   | ----- | ----- |
   | **Assinatura Azure** | A subscrição que contém o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Grupo de Recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento. Use _o afsresgroup101918_ para este tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do Serviço de Sincronização de Armazenamento. Use _afssyncservice02_ para este tutorial. |

1. Selecione **Registar-se** para completar o registo do servidor.
1. Como parte do processo de registo, é solicitado um pedido de inscrição adicional. Iniciar s.000 e selecionar **Seguinte**.
1. Selecione **OK**.

## <a name="create-a-sync-group"></a>Criar um grupo de sincronização

Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Um grupo de sincronização deve conter um ponto final em nuvem, que representa uma partilha de ficheiros Azure. Um grupo de sincronização também deve conter um ou mais pontos finais do servidor. Os pontos finais de servidor representam um caminho num servidor registado. Para criar um grupo de sincronização:

1. No [portal Azure](https://portal.azure.com/), selecione **+ Sync group** a partir do Serviço de Sincronização de Armazenamento. Use *afssyncservice02* para este tutorial.

   ![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Introduza as seguintes informações para criar um grupo de sincronização com um ponto final em nuvem:

   | Valor | Descrição |
   | ----- | ----- |
   | **Sync nome de grupo** | Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico. Use *o grupo afssyncgroup* para este tutorial.|
   | **Subscrição** | A subscrição na qual implementou o Serviço de Sincronização de Armazenamento neste tutorial. |
   | **Conta de armazenamento** | Escolha **Selecione a conta de armazenamento**. No painel que aparece, selecione a conta de armazenamento que tem a partilha de ficheiros Azure que criou. Use *afsstoracct101918* para este tutorial. |
   | **Azure partilha de ficheiros** | O nome do ficheiro Azure partilha-o. Use *afsfileshare* para este tutorial. |

1. Selecione **Criar**.

Se selecionar o grupo de sincronização, pode ver que tem agora um **ponto final da cloud**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor

Um ponto final do servidor representa uma localização específica num servidor registado. Por exemplo, uma pasta num volume de servidor. Para adicionar um ponto final do servidor:

1. Selecione o grupo de sincronização recém-criado e, em seguida, **selecione Adicionar ponto final do servidor**.

   ![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. No painel de ponto final do **servidor Adicionar,** introduza as seguintes informações para criar um ponto final do servidor:

   | Valor | Descrição |
   | ----- | ----- |
   | **Servidor registado** | O nome do servidor que criou. Use *afsvm101918* para este tutorial. |
   | **Caminho** | O caminho do Servidor do Windows para a unidade que criou. Utilize *f:\filestosync* neste tutorial. |
   | **Arrumo na Cloud** | Deixe desativado para o tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para o tutorial. |

1. Selecione **Criar**.

Os ficheiros estão agora sincronizados na partilha de ficheiros do Azure e no Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar os recursos que criou neste tutorial, retire primeiro os pontos finais do serviço de sincronização de armazenamento. Em seguida, desinsuse o servidor com o seu serviço de sincronização de armazenamento, remova os grupos de sincronização e elimine o serviço de sincronização.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu os passos básicos para aumentar a capacidade de armazenamento de um servidor Windows utilizando o Azure File Sync. Para uma análise mais aprofundada do planeamento de uma implementação do Azure File Sync, consulte:

> [!div class="nextstepaction"]
> [Plan for Azure File Sync deployment](./storage-sync-files-planning.md) (Planear uma implementação do Azure File Sync)
