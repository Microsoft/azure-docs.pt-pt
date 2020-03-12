---
title: Recipientes de perfil FSLogix NetApp Windows Virtual Desktop - Azure
description: Como criar um recipiente de perfil FSLogix utilizando ficheiros Azure NetApp no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b29dbd7a034123b17b34b1c0fd4c8649e30810bd
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128169"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Crie um recipiente de perfil FSLogix para uma piscina de anfitriões usando ficheiros Azure NetApp

Recomendamos a utilização de recipientes de perfil FSLogix como solução de perfil do utilizador para o serviço de ambiente de [trabalho virtual Windows](overview.md). Os recipientes de perfil FSLogix armazenam um perfil completo de utilizador num único recipiente e são projetados para percorrer perfis em ambientes de computação remota não persistente, como o Windows Virtual Desktop. Quando entra, o recipiente liga-se dinamicamente ao ambiente de computação utilizando um disco rígido virtual (VHD) e disco rígido virtual Hyper-V (VHDX). Estas tecnologias avançadas de filtragem permitem que o perfil do utilizador esteja imediatamente disponível e apareça no sistema exatamente como um perfil de utilizador local. Para saber mais sobre os recipientes de perfil FSLogix, consulte os recipientes de [perfil FSLogix e os ficheiros Azure](fslogix-containers-azure-files.md).

Pode criar recipientes de perfil FSLogix utilizando [ficheiros Azure NetApp,](https://azure.microsoft.com/services/netapp/)um serviço de plataforma nativa Azure de fácil utilização que ajuda os clientes de forma rápida e fiável a fornecer volumes SMB de nível empresarial para os seus ambientes de ambiente de trabalho virtual windows. Para saber mais sobre os Ficheiros Azure NetApp, consulte [o que é o Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

Este guia irá mostrar-lhe como configurar uma conta Azure NetApp Files e criar recipientes de perfil FSLogix no Windows Virtual Desktop.

Este artigo assume que já tem piscinas de [anfitriões](create-host-pools-azure-marketplace.md) configuradas e agrupadas em um ou mais inquilinos no seu ambiente de ambiente de trabalho virtual windows. Para aprender a configurar inquilinos, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md) e na nossa publicação de blog tech [community](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

As instruções deste guia são especificamente para utilizadores do Windows Virtual Desktop. Se procura orientações mais gerais para configurar ficheiros Azure NetApp e criar recipientes de perfil FSLogix fora do Windows Virtual Desktop, consulte o Conjunto de [Ficheiros NetApp do Azure e crie um arranque rápido de volume NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Este artigo não abrange as melhores práticas para garantir o acesso à partilha de Ficheiros Azure NetApp.

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte opções de armazenamento para recipientes de [perfil FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar um recipiente de perfil FSLogix para uma piscina de acolhimento, deve:

- Configurar e configurar o Ambiente de Trabalho Virtual do Windows
- Fornecer uma piscina de anfitriões do Windows Virtual Desktop
- [Ativar a subscrição de Ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Configurar a sua conta de Ficheiros Azure NetApp

Para começar, precisa de configurar uma conta Azure NetApp Files.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Certifique-se de que a sua conta tem permissões contributivas ou administradores.

2. Selecione o **ícone Azure Cloud Shell** à direita da barra de pesquisa para abrir a Azure Cloud Shell.

3. Assim que a Azure Cloud Shell estiver aberta, selecione **PowerShell**.

4. Se esta for a primeira vez que utiliza o Azure Cloud Shell, crie uma conta de armazenamento na mesma subscrição que mantenha os seus Ficheiros Azure NetApp e Windows Virtual Desktop.

   ![A janela da conta de armazenamento com o botão de armazenamento de criação na parte inferior da janela realçada a vermelho.](media/create-storage-button.png)

5. Uma vez que a Casca de Nuvem Azure carregue, corra os seguintes dois cmdlets.

   ```powershell
   az account set --subscription <subscriptionID>
   ```

   ```powershell
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. No lado esquerdo da janela, selecione **Todos os serviços**. Introduza ficheiros **Azure NetApp** na caixa de pesquisa que aparece no topo do menu.

   ![Uma imagem de um utilizador a introduzir "Ficheiros Azure NetApp" na caixa de pesquisa de Todos os serviços. Os resultados da pesquisa mostram o recurso Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Selecione **Ficheiros Azure NetApp** nos resultados da pesquisa e, em seguida, selecione **Criar**.

8. Selecione o botão **Adicionar.**
9. Quando a lâmina da **conta New NetApp** abrir, introduza os seguintes valores:

    - Para **Nome**, insira o nome da sua conta NetApp.
    - Para **subscrição,** selecione a subscrição da conta de armazenamento que configura no passo 4 do menu suspenso.
    - Para **o grupo Recursos,** selecione um grupo de recursos existente a partir do menu suspenso ou crie um novo selecionando **criar novo**.
    - Para **localização**, selecione a região para a sua conta NetApp a partir do menu suspenso. Esta região deve ser a mesma região que a sua sessão anfitrião VMs.

   >[!NOTE]
   >Atualmente, o Azure NetApp Files não suporta a montagem de um volume em todas as regiões.

10. Quando terminar, selecione **Criar** para criar a sua conta NetApp.

## <a name="create-a-capacity-pool"></a>Criar uma piscina de capacidade

Em seguida, crie uma nova piscina de capacidade: 

1. Vá ao menu Deficheiros Azure NetApp e selecione a sua nova conta.
2. No menu da sua conta, selecione **Piscinas de Capacidade** no serviço de Armazenamento.
3. **Selecione Adicionar piscina**.
4. Quando a lâmina da **piscina de nova capacidade** abrir, introduza os seguintes valores:

    - Para **Nome,** insira um nome para a nova piscina de capacidade.
    - Para **o nível**de serviço, selecione o valor desejado a partir do menu suspenso. Recomendamos **Premium** para a maioria dos ambientes.
       >[!NOTE]
       >A definição Premium fornece o rendimento mínimo disponível para um nível de Serviço Premium, que é de 256 MBps. Pode ser necessário ajustar esta produção para um ambiente de produção. A entrada final baseia-se na relação descrita nos [limites de Entrada](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Para **tamanho (TiB)** , insira o tamanho da piscina de capacidade que melhor se adapte às suas necessidades. O tamanho mínimo é 4 TiB.

5. Quando terminar, selecione **OK**.

## <a name="join-an-active-directory-connection"></a>Junte-se a uma ligação de Diretório Ativo

Depois disso, tens de te juntar a uma ligação de Diretório Ativo.

1. Selecione **ligações de Diretório Ativo** no menu do lado esquerdo da página e, em seguida, selecione o botão **'Juntar'** para abrir a página **'Diretório Ativo'.**

   ![Uma imagem do menu de ligações Do Diretório Ativo.](media/active-directory-connections-menu.png)

2. Insira os seguintes valores na página **Do Diretório Ativo de Adque** para se juntar a uma ligação:

    - Para **DNS Primários,** introduza o endereço IP do servidor DNS no seu ambiente que pode resolver o nome de domínio.
    - Para **domínio,** introduza o seu nome de domínio totalmente qualificado (FQDN).
    - Para **prefixo do Servidor SMB (Conta de Computador),** introduza o fio que pretende anexar ao nome da conta do computador.
    - Para **username,** insira o nome da conta com permissões para realizar a adesão ao domínio.
    - Para **obter palavra-passe,** introduza a palavra-passe da conta.

  >[!NOTE]
  >É uma boa prática confirmar que a conta de computador que criou em [Join a Ative Directy foi](create-fslogix-profile-container.md#join-an-active-directory-connection) criada no seu controlador de domínio nos **Computadores** ou no **OU relevante da sua empresa.**

## <a name="create-a-new-volume"></a>Criar um novo volume

Em seguida, terá de criar um novo volume.

1. Selecione **Volumes**e, em seguida, **selecione Adicionar volume**.

2. Quando a **Lâmina criar uma** lâmina de volume se abrir, introduza os seguintes valores:

    - Para **nome Volume,** introduza um nome para o novo volume.
    - Para **pool capacity,** selecione o pool de capacidade que acabou de criar a partir do menu suspenso.
    - Para **quota (GiB)** introduza o tamanho de volume adequado para o seu ambiente.
    - Para **a rede Virtual,** selecione uma rede virtual existente que tenha conectividade com o controlador de domínio a partir do menu suspenso.
    - Em **Subnet,** **selecione Criar novo**. Tenha em mente que esta subnet será delegada nos Ficheiros Azure NetApp.

3.  Selecione **Seguinte: O protocolo \>\>** de abrir o separador Protocolo e configurar os parâmetros de acesso ao volume.

## <a name="configure-volume-access-parameters"></a>Configure parâmetros de acesso ao volume

Depois de criar o volume, configure os parâmetros de acesso ao volume.

1.  Selecione **SMB** como o tipo de protocolo.
2.  Sob configuração no menu de entrega do **Diretório Ativo,** selecione o mesmo diretório que inicialmente ligou em [Join a Ative Directy connection](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenha em mente que há um limite de um Diretório Ativo por subscrição.
3.  Na caixa de **texto de nome Partilhar,** insira o nome da parte utilizada pela piscina anfitriã da sessão e pelos seus utilizadores.

4.  Selecione **Review + criar** na parte inferior da página. Isto abre a página de validação. Depois de o seu volume ser validado com sucesso, selecione **Criar**.

5.  Neste momento, o novo volume começará a ser implantado. Uma vez concluída a implementação, pode utilizar a partilha de Ficheiros Azure NetApp.

6.  Para ver o caminho do suporte, selecione **Vá recorrer** e procure-o no separador Overview.

    ![Uma imagem do ecrã overview com uma seta vermelha apontando para o caminho do suporte.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configure FSLogix nas máquinas virtuais do anfitrião da sessão (VMs)

Esta secção baseia-se em Criar um recipiente de [perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).

1. [Descarregue o ficheiro .zip do agente FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) enquanto ainda estiver remoto na sessão de vm anfitrião.

2. Desaperte o ficheiro descarregado.

3. No ficheiro, aceda a **x64** > **Lançamentos** e execute **FSLogixAppsSetup.exe**. O menu de instalação será aberto.

4.  Se tiver uma chave de produto, introduza-a na caixa de texto da Chave do Produto.

5. Selecione a caixa de verificação ao lado **de eu concordo com os termos e condições da licença**.

6. Selecione **Instalar**.

7. Navegue para **C:\\Ficheiros do Programa\\Aplicações de\\FSLogix** para confirmar o agente instalado.

8. A partir do menu Iniciar, executar **RegEdit** como administrador.

9. Navegue para **o software\\de\\HKEY_LOCAL_MACHINE de\\de computador  FSLogix**.

10. Criar uma chave chamada **Perfis.**

11.  Criar um valor denominado **Habilitado** com **um** REG_DWORD tipo definido para um valor de dados de **1**.

12. Crie um valor chamado **VHDLocations** com um tipo **multi-string** e defina o seu valor de dados para o URI para a partilha de Ficheiros Azure NetApp.

13. Crie um valor chamado **DeleteLocalProfileWhenVHDShouldApply** com um valor DWORD de 1 para evitar problemas com os perfis locais existentes antes de iniciar sessão.

     >[!WARNING]
     >Tenha cuidado ao criar o valor DeleteLocalProfileWhenVHDShouldApply. Quando o sistema de perfis FSLogix determinar que um utilizador deve ter um perfil FSLogix, mas já existe um perfil local, o Contentor de Perfil irá eliminar permanentemente o perfil local. O utilizador será então inscrito com o novo perfil FSLogix.

## <a name="assign-users-to-session-host"></a>Atribuir utilizadores ao anfitrião da sessão

1. Abra **powerShell ISE** como administrador e inscreva-se no Windows Virtual Desktop.

2. Executar os seguintes cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando solicitado para obter credenciais, insira as credenciais para o utilizador com as funções de Criador de Inquilino ou COLABORADOR RDS/RDS no inquilino do Windows Virtual Desktop.

4. Executar os seguintes cmdlets para atribuir um utilizador a um grupo de ambiente de trabalho remoto:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Certifique-se de que os utilizadores podem aceder à partilha de Ficheiros Azure NetApp

1. Abra o seu navegador de Internet e vá para <https://rdweb.wvd.microsoft.com/webclient/index.html>.

2. Inscreva-se com as credenciais de um utilizador atribuído ao grupo Remote Desktop.

3. Depois de ter estabelecido a sessão de utilizador, inscreva-se no portal Azure com uma conta administrativa.

4. Abra **os Ficheiros Azure NetApp,** selecione a sua conta De Ficheiros Azure NetApp e, em seguida, selecione **Volumes**. Assim que o menu Volumes abrir, selecione o volume correspondente.

   ![Uma imagem da conta NetApp que configuraste anteriormente no portal Azure com o botão Volumes selecionado.](media/netapp-account.png)

5. Aceda ao separador **Overview** e confirme que o recipiente de perfil FSLogix está a utilizar o espaço.

6. Conecte-se diretamente a qualquer parte VM da piscina anfitriã utilizando o Remote Desktop e abra o **File Explorer.** Em seguida, navegue até ao caminho do **Monte** (no exemplo seguinte, o caminho do suporte é \\\\anf-SMB-3863.gt1107.onmicrosoft.com anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   Dentro desta pasta, deve haver um perfil VHD (ou VHDX) como o seguinte exemplo.

   ![Uma imagem do conteúdo da pasta no caminho do suporte. Dentro há um único ficheiro VHD chamado "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Passos seguintes

Pode utilizar os recipientes de perfil FSLogix para configurar uma parte do perfil do utilizador. Para aprender a criar partilhas de perfil do utilizador com os seus novos recipientes, consulte Criar um recipiente de [perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).
