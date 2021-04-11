---
title: Recipientes de perfil FSLogix NetApp Windows Virtual Desktop - Azure
description: Como criar um contentor de perfil FSLogix utilizando ficheiros Azure NetApp no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 180d49f52eeb4f9e25a682fbf8f52a0bc307c7ed
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447969"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Criar um contentor de perfil com ficheiros Azure NetApp e DS AD

Recomendamos a utilização de recipientes de perfil FSLogix como solução de perfil de utilizador para o [serviço Windows Virtual Desktop](overview.md). Os recipientes de perfil FSLogix armazenam um perfil de utilizador completo num único recipiente e são projetados para percorrer perfis em ambientes de computação remota não persistentes como o Windows Virtual Desktop. Ao iniciar sposição, o contentor liga-se dinamicamente ao ambiente de computação utilizando um disco rígido virtual (VHD) e um disco rígido virtual Hyper-V (VHDX). Estas tecnologias avançadas de filtragem permitem que o perfil do utilizador esteja imediatamente disponível e apareçam no sistema exatamente como um perfil de utilizador local. Para saber mais sobre os recipientes de perfil FSLogix, consulte [os recipientes de perfil FSLogix e os ficheiros Azure](fslogix-containers-azure-files.md).

Pode criar recipientes de perfil FSLogix utilizando [ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/), um serviço de plataforma nativa Azure de fácil utilização que ajuda os clientes a providenciar de forma rápida e fiável volumes SMB de qualidade empresarial para os seus ambientes de Desktop Virtual Windows. Para saber mais sobre os Ficheiros Azure NetApp, veja [o que é O Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

Este guia irá mostrar-lhe como configurar uma conta Azure NetApp Files e criar recipientes de perfil FSLogix no Windows Virtual Desktop.

Este artigo pressupõe que já tem [piscinas de anfitriões](create-host-pools-azure-marketplace.md) configuradas e agrupadas em um ou mais inquilinos no seu ambiente de desktop virtual windows. Para aprender a configurar inquilinos, consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) e no nosso blog tech [community.](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)

As instruções neste guia são especificamente para utilizadores do Windows Virtual Desktop. Se procura orientações mais gerais para configurar ficheiros Azure NetApp e criar recipientes de perfil FSLogix fora do Windows Virtual Desktop, consulte os [Ficheiros Azure NetApp da Configuração e crie um arranque rápido de volume NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Este artigo não abrange as melhores práticas para garantir o acesso à partilha dos Ficheiros Azure NetApp.

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte [as opções de armazenamento para recipientes de perfil FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um recipiente de perfil FSLogix para uma piscina de anfitrião, deve:

- Configurar e configurar o Ambiente de Trabalho Virtual do Windows
- Provisionar uma piscina de anfitriões virtual do Windows Desktop
- [Ativar a subscrição dos Ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Crie a sua conta Azure NetApp Files

Para começar, é necessário criar uma conta Azure NetApp Files.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Certifique-se de que a sua conta tem permissões de contribuinte ou administrador.

2. Selecione o **ícone Azure Cloud Shell** à direita da barra de pesquisa para abrir a Azure Cloud Shell.

3. Assim que o Azure Cloud Shell estiver aberto, selecione **PowerShell**.

4. Se esta for a sua primeira utilização usando a Azure Cloud Shell, crie uma conta de armazenamento na mesma subscrição que mantém os seus Ficheiros Azure NetApp e o Windows Virtual Desktop.

   > [!div class="mx-imgBorder"]
   > ![A janela da conta de armazenamento com o botão de armazenamento criar na parte inferior da janela realçada a vermelho.](media/create-storage-button.png)

5. Uma vez que a Azure Cloud Shell carregue, executar os dois cmdlets seguintes.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. No lado esquerdo da janela, selecione **Todos os serviços**. Introduza **os Ficheiros Azure NetApp** na caixa de pesquisa que aparece no topo do menu.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem de um utilizador que introduz "Ficheiros Azure NetApp" na caixa de pesquisa de todos os serviços. Os resultados da pesquisa mostram o recurso Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Selecione **ficheiros Azure NetApp** nos resultados da pesquisa e, em seguida, selecione **Criar**.

8. Selecione o botão **Adicionar.**
9. Quando o separador **da conta New NetApp** abrir, insira os seguintes valores:

    - Para **nome,** insira o nome da sua conta NetApp.
    - Para **Subscrição**, selecione a subscrição da conta de armazenamento que criou no passo 4 a partir do menu suspenso.
    - Para **o grupo de recursos**, selecione um grupo de recursos existente a partir do menu suspenso ou crie um novo selecionando Criar **novo**.
    - Para **localização**, selecione a região para a sua conta NetApp a partir do menu suspenso. Esta região deve ser a mesma região que os VMs anfitriões da sua sessão.

   >[!NOTE]
   >Atualmente, o Azure NetApp Files não suporta a montagem de um volume em todas as regiões.

10. Quando terminar, selecione **Criar** para criar a sua conta NetApp.

## <a name="create-a-capacity-pool"></a>Criar uma piscina de capacidade

Em seguida, criar uma nova capacidade de piscina:

1. Vá ao menu Azure NetApp Files e selecione a sua nova conta.
2. No menu da sua conta, selecione **piscinas de capacidade** sob o serviço de Armazenamento.
3. **Selecione Adicionar piscina**.
4. Quando o **separador de piscina de nova capacidade** abrir, insira os seguintes valores:

    - Para **nome,** insira um nome para o novo pool de capacidade.
    - Para **o nível de Serviço,** selecione o valor pretendido no menu suspenso. Recomendamos **Premium** para a maioria dos ambientes.
       >[!NOTE]
       >A definição Premium fornece a produção mínima disponível para um nível de Serviço Premium, que é de 256 MBps. Pode ser necessário ajustar esta produção para um ambiente de produção. A produção final baseia-se na relação descrita nos [limites de Produção](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Para **tamanho (TiB)**, insira o tamanho da piscina de capacidade que melhor se adequa às suas necessidades. O tamanho mínimo é 4 TiB.

5. Quando tiver terminado, selecione **OK**.

## <a name="join-an-active-directory-connection"></a>Junte-se a uma ligação ative diretório

Depois disso, tens de te juntar a uma ligação ao Ative Directory.

1. Selecione **as ligações Ative Directory** no menu no lado esquerdo da página e, em seguida, selecione o botão **'Juntar',** para abrir a página **'Ligar Diretório Activo'.**

   > [!div class="mx-imgBorder"]
   > ![Uma imagem do menu de ligações do Diretório Ativo.](media/active-directory-connections-menu.png)

2. Introduza os seguintes valores na página **'Juntar Diretório Ativo'** para se juntar a uma ligação:

    - Para **DNS Primários,** insira o endereço IP do servidor DNS no seu ambiente que possa resolver o nome de domínio.
    - Para **Domínio,** insira o seu nome de domínio totalmente qualificado (FQDN).
    - Para **prefixo SMB Server (Conta de Computador),** insira a cadeia que pretende anexar ao nome da conta do computador.
    - Para **o nome de utilizador,** insira o nome da conta com permissões para efetuar a junção de domínio.
    - Para **palavra-passe,** introduza a palavra-passe da conta.

## <a name="create-a-new-volume"></a>Criar um novo volume

Em seguida, terá de criar um novo volume.

1. Selecione **Volumes** e, em seguida, **selecione Adicionar volume**.

2. Quando o separador **De volume** abrir, insira os seguintes valores:

    - Para **o nome volume,** insira um nome para o novo volume.
    - Para **o pool de capacidade,** selecione a piscina de capacidade que acabou de criar a partir do menu suspenso.
    - Para **quota (GiB)**, insira o tamanho de volume adequado para o seu ambiente.
    - Para **a rede Virtual**, selecione uma rede virtual existente que tenha conectividade com o controlador de domínio a partir do menu suspenso.
    - Na **sub-rede,** selecione **Criar novo**. Tenha em mente que esta sub-rede será delegada nos Ficheiros Azure NetApp.

3.  Selecione **Seguinte: Protocolo \> \>** para abrir o separador Protocolo e configurar os parâmetros de acesso ao volume.

## <a name="configure-volume-access-parameters"></a>Configurar parâmetros de acesso ao volume

Depois de criar o volume, configuure os parâmetros de acesso ao volume.

1.  Selecione **SMB** como o tipo de protocolo.
2.  Em Configuração no menu suspenso do **Diretório Ativo,** selecione o mesmo diretório que inicialmente ligou [na ligação 'Unir um Diretório Activo'.](create-fslogix-profile-container.md#join-an-active-directory-connection) Lembre-se que há um limite de um Diretório Ativo por subscrição.
3.  Na caixa de texto **do nome partilhar,** insira o nome da parte utilizada pela piscina anfitriã da sessão e pelos seus utilizadores.

4.  Selecione **'Rever + criar'** na parte inferior da página. Isto abre a página de validação. Depois de o seu volume ser validado com sucesso, **selecione Criar**.

5.  Neste momento, o novo volume começará a ser implantado. Uma vez concluída a implementação, pode utilizar a partilha de Ficheiros Azure NetApp.

6.  Para ver o caminho de montagem, selecione **Vá para o recurso** e procure-o no separador Vista Geral.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem do ecrã da visão geral com uma seta vermelha apontando para o caminho de montagem.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configure fSLogix em máquinas virtuais de anfitrião de sessão (VMs)

Esta secção baseia-se na [Criação de um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).

1. [Descarregue o ficheiro .zip do agente FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) enquanto ainda estiver afastado no VM do anfitrião da sessão.

2. Desaperte o ficheiro descarregado.

3. No ficheiro, vá a **x64**  >  **Releases** e execute **FSLogixAppsSetup.exe**. O menu de instalação será aberto.

4.  Se tiver uma chave de produto, insira-a na caixa de texto da Chave de Produto.

5. Selecione a caixa de verificação ao lado **de que concordo com os termos e condições da licença**.

6. Selecione **Instalar**.

7. Navegue para **C: \\ Ficheiros de programa \\ FSLogix \\ Apps** para confirmar o agente instalado.

8. A partir do menu Iniciar, executar **RegEdit** como administrador.

9. Navegue para **o software de HKEY_LOCAL_MACHINE de computador \\ \\ \\ FSLogix**.

10. Crie uma chave chamada **Perfis.**

11.  Criar um valor chamado **Enabled** com um conjunto de tipo **REG_DWORD** para um valor de dados de **1**.

12. Crie um valor chamado **VHDLocations** com um tipo **multi-string** e desloque o seu valor de dados para o URI para a partilha de Ficheiros Azure NetApp.

13. Crie um valor chamado **DeleteLocalProfileWhenVHDShouldAppapply** com um valor DWORD de 1 para evitar problemas com perfis locais existentes antes de iniciar sessão.

     >[!WARNING]
     >Tenha cuidado ao criar o valor DeleteLocalProfileWhenVHDShouldApply. Quando o sistema de perfis FSLogix determina que um utilizador deve ter um perfil FSLogix, mas já existe um perfil local, o Profile Container eliminará permanentemente o perfil local. O utilizador será então inscrito com o novo perfil FSLogix.

## <a name="assign-users-to-session-host"></a>Atribua utilizadores ao anfitrião da sessão

1. Open **PowerShell ISE** como administrador e inscreva-se no Windows Virtual Desktop.

2. Executar os seguintes cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando solicitado para obter credenciais, insira as credenciais para o utilizador com as funções de Proprietário do Inquilino ou RDS Owner/RDS Contributnation no inquilino virtual do Windows Desktop.

4. Executar os seguintes cmdlets para atribuir um utilizador a um grupo de desktop remoto:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Certifique-se de que os utilizadores podem aceder à partilha do Ficheiro Azure NetApp

1. Abra o seu navegador de internet e vá <https://rdweb.wvd.microsoft.com/arm/webclient> para.

2. Inscreva-se com as credenciais de um utilizador atribuído ao grupo Remote Desktop.

3. Assim que tiver estabelecido a sessão de utilizador, inscreva-se no portal Azure com uma conta administrativa.

4. Abra **os Ficheiros Azure NetApp**, selecione a sua conta Azure NetApp Files e, em seguida, selecione **Volumes**. Assim que o menu Volumes abrir, selecione o volume correspondente.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem da conta NetApp que instalou anteriormente no portal Azure com o botão Volumes selecionado.](media/netapp-account.png)

5. Vá ao **separador Visão Geral** e confirme que o recipiente de perfil FSLogix está a utilizar espaço.

6. Ligue-se diretamente a qualquer parte VM da piscina hospedeira utilizando o Desktop Remoto e abra o **Explorador de Ficheiros.** Em seguida, navegue até ao **caminho** da montagem (no exemplo seguinte, o caminho de montagem é \\ \\ anf-SMB-3863.gt1107.onmicrosoft.com \\ anf-VOL).

   Dentro desta pasta, deve haver um perfil VHD (ou VHDX) como o do exemplo seguinte.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem do conteúdo da pasta no caminho de montagem. Dentro está um único ficheiro VHD chamado "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Passos seguintes

Pode utilizar os recipientes de perfil FSLogix para configurar uma partilha de perfil de utilizador. Para aprender a criar partilhas de perfil de utilizador com os seus novos recipientes, consulte [Criar um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).

Também pode criar uma partilha de ficheiros Azure Files para armazenar o seu perfil FSLogix. Para saber mais, consulte [criar um ficheiro Azure Files com um controlador de domínio](create-file-share.md).