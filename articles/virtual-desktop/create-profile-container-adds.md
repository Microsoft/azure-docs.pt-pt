---
title: Criar o contentor de perfil FSLogix Azure Files Ative Directory Domain Services - Azure
description: Este artigo descreve como criar um recipiente de perfil FSLogix com Ficheiros Azure e Serviços de Domínio de Diretório Ativo Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 916d34abfaf8223e3cf29977e13dfddf15a3fbf9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607287"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Criar um recipiente de perfil FSLogix com Ficheiros Azure

Este artigo irá mostrar-lhe como criar um recipiente de perfil FSLogix com Ficheiros Azure e Serviços de Domínio ativo do Diretório Azure (AD DS).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já criou uma instância Azure AD DS. Se ainda não tiver uma, siga as instruções em [Criar um domínio gerido básico](../active-directory-domain-services/tutorial-create-instance.md) primeiro, edepois volte aqui.

## <a name="add-azure-ad-ds-admins"></a>Adicionar administrações Azure AD DS

Para adicionar administradores adicionais, cria um novo utilizador e concede-lhes permissões.

Para adicionar um administrador:

1. Selecione **O Diretório Ativo Azure** a partir da barra lateral, depois selecione **Todos os utilizadores**, e, em seguida, selecione Novo **utilizador**.

2.  Introduza os detalhes do utilizador nos campos.

3. No painel de diretório ativo Azure no lado esquerdo do ecrã, selecione **Grupos**.

4. Selecione o grupo **de administradores aAD DC.**

5. No painel esquerdo, selecione **Membros,** em seguida, **selecione Adicionar membros** no painel principal. Isto mostrará uma lista de todos os utilizadores disponíveis em Azure AD. Selecione o nome do perfil de utilizador que acabou de criar.

## <a name="set-up-an-azure-storage-account"></a>Criar uma conta de Armazenamento Azure

Agora é hora de ativar a autenticação Azure AD DS sobre o Bloco de Mensagens do Servidor (SMB). 

Para permitir a autenticação:

1. Se ainda não o fez, crie e implemente uma conta de armazenamento v2 Azure de uso geral, seguindo as instruções em Criar uma conta de [Armazenamento Azure](../storage/common/storage-account-create.md).

2. Depois de terminar a configuração da sua conta, selecione **Ir para o recurso**.

3. **Selecione configuração** a partir do painel no lado esquerdo do ecrã e, em seguida, ative a **autenticação do Diretório Ativo Azure para Ficheiros Azure** no painel principal. Quando tiver terminado, selecione **Guardar**.

4. Selecione **a visão geral** no painel do lado esquerdo do ecrã e, em seguida, selecione **Ficheiros** no painel principal.

5. Selecione **a partilha de ficheiros** e introduza o **Nome** e **a Quota** nos campos que aparecem no lado direito do ecrã.

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Outros utilizadores precisarão de permissões de acesso para aceder à sua partilha de ficheiros. Para isso, terá de atribuir a cada utilizador uma função com as permissões de acesso adequadas.

Para atribuir aos utilizadores permissões de acesso:

1. A partir do portal Azure, abra a partilha de ficheiros que criou na [Criação de uma conta de Armazenamento Azure](#set-up-an-azure-storage-account).

2. Selecione **Controlo de Acesso (IAM)**.

3. Selecione **Adicionar uma atribuição de funções**.

4. No separador de atribuição de **funções Add,** selecione a função incorporada adequada da lista de papéis. Terá de selecionar pelo menos o Colaborador de Partilha de **Dados de Ficheiros** de Armazenamento Para obter permissões adequadas.

5. Para **atribuir acesso a**, selecione Utilizador, grupo ou diretor de serviço **Azure Ative**.

6. Selecione um nome ou endereço de e-mail para a identidade do Diretório Ativo Azure.

7. Selecione **Guardar**.

## <a name="get-the-storage-account-access-key"></a>Obtenha a chave de acesso à Conta de Armazenamento

Em seguida, terá de obter a chave de acesso para a sua Conta de Armazenamento.

Para obter a chave de acesso à Conta de Armazenamento:

1. A partir da barra lateral do portal Azure, selecione **contas de armazenamento**.

2. A partir da lista de contas de armazenamento, selecione a conta para a qual ativou o Azure AD DS e criou as funções personalizadas em passos acima.

3. Em **Definições,** selecione **as teclas de acesso** e copie a tecla a partir do **teclado1**.

4. Vá ao separador **Máquinas Virtuais** e localize qualquer VM que se torne parte da sua piscina anfitriã.

5. Selecione o nome da máquina virtual (VM) em **máquinas virtuais (adVM)** e selecione **Connect**

    Isto irá descarregar um ficheiro RDP que lhe permitirá iniciar sessão no VM com as suas próprias credenciais.

    ![Uma imagem do separador RDP da Janela da Ligação à janela da máquina virtual.](media/rdp-tab.png)

6. Quando tiver assinado o VM, faça um pedido de comando como administrador.

7. Execute o seguinte comando:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Substitua-a `<desired-drive-letter>` por uma letra de `y:`unidade à sua escolha (por exemplo, ).
    - Substitua todas `<storage-account-name>` as instâncias com o nome da conta de armazenamento que especificou anteriormente.
    - Substitua-o `<share-name>` pelo nome da parte que criou anteriormente.
    - Substitua `<storage-account-key>` com a chave da conta de armazenamento do Azure.

    Por exemplo:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Executar o seguinte comando para conceder ao utilizador acesso total à partilha de Ficheiros Azure.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Substitua-a `<mounted-drive-letter>` pela letra da unidade que pretende utilizar pelo utilizador.
    - Substitua-a `<user-email>` pela UPN do utilizador que utilizará este perfil para aceder aos VMs anfitriões da sessão.

    Por exemplo:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Criar um contentor de perfil

Agora que os seus perfis estão prontos para ir, vamos criar um recipiente de perfil FSLogix.

Para configurar um recipiente de perfil FSLogix:

1. Inicie sessão no VM do anfitrião da sessão configurado no início deste artigo, em seguida, [descarregue e instale o agente FSLogix](/fslogix/install-ht/).

2. Desbloqueie o ficheiro do agente FSLogix que descarregou e vá para **x64** > **Lançamentos**e abra **fSLogixAppsSetup.exe**.

3. Assim que o instalador for lançado, selecione, **concordo com os termos e condições da licença.** Se aplicável, forneça uma nova chave.

4. Selecione **Instalar**.

5. Abra a **Unidade C**e, em **seguida,** > vá às**Aplicações** **FSLogix** > do Programa para se certificar de que o agente FSLogix estava corretamente instalado.

     >[!NOTE]
     > Se houver vários VMs na piscina anfitriã, terá de repetir os passos 1 a 5 para cada VM.

6. Executar **Editor de Registo** (RegEdit) como administrador.

7. Navegue para **computador** > **HKEY_LOCAL_MACHINE** > **software** > **FSLogix,** clique à direita no **FSLogix,** selecione **New**, e, em seguida, selecione **Chave**.

8. Criar uma nova chave chamada **Perfis.**

9.  Clique à direita nos **Perfis,** selecione **New**, e, em seguida, selecione **DWORD (32 bits) Valor.** Nomeie o valor **Ativado** e detete o valor **dos Dados** para **1**.

    ![Uma imagem da chave perfis. O ficheiro REG_DWORD é destacado e o seu valor de Dados está definido para 1.](media/dword-value.png)

10. Clique à direita nos **Perfis,** selecione **New**, e, em seguida, selecione **Multi-String Value**. Nomeie o valor **VHDLocations** e introduza `\\fsprofile.file.core.windows.net\share` o URI para a partilha de Ficheiros Azure como valor de Dados.

    ![Uma imagem da tecla Perfis mostrando o ficheiro VHDLocations. O seu valor de Dados mostra o URI para a partilha de Ficheiros Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Atribuir utilizadores a um anfitrião de sessão

Agora terá de atribuir utilizadores ao seu anfitrião da sessão.

Para atribuir aos utilizadores:

1. Executar o Windows PowerShell como administrador e, em seguida, executar o seguinte cmdlet para iniciar sessão no Windows Virtual Desktop com powerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando solicitado para credenciais, insira o mesmo utilizador a quem foi concedido o papel de TenantCreator, RDS Owner ou RDS Contributor no inquilino do Windows Virtual Desktop.

2. Executar os seguintes cmdlets para atribuir o utilizador ao grupo de ambiente de trabalho remoto:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Tal como os cmdlets anteriores, certifique-se de substituir, `<your-wvd-tenant>` `<wvd-pool>`e `<user-principal>` com os valores relevantes.

    Por exemplo:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Certifique-se de que o seu perfil funciona

Agora tudo o que tens de fazer é certificar-te que o perfil que criaste existe e funciona como pretendido.

Para verificar o seu perfil:

1. Abra um navegador e vá ao [cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Inscreva-se na conta de utilizador atribuída ao grupo Remote Desktop.

3. Uma vez estabelecida a sessão de utilizador, abra o portal Azure e inscreva-se com uma conta administrativa.

4. A partir da barra lateral, selecione **contas de armazenamento**.

5. Selecione a conta de armazenamento configurada como a partilha de ficheiros para a sua piscina de anfitriões de sessão e ativada com O DS Azure.

6. Selecione o ícone **Ficheiros** e, em seguida, expanda a sua parte.

    Se tudo estiver bem montado, deve ver um **Diretório** com um nome `<user SID>-<username>`que seja formatado assim: .

## <a name="next-steps"></a>Passos seguintes

Se procura formas alternativas de criar recipientes de perfil FSLogix, consulte os seguintes artigos:

- [Crie um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).
- [Crie um recipiente de perfil FSLogix para uma piscina de anfitriões usando ficheiros Azure NetApp](create-fslogix-profile-container.md)

Pode encontrar informações mais detalhadas sobre conceitos relacionados com contentores FSlogix para ficheiros Azure em [contentores de perfil FSLogix e ficheiros Azure](fslogix-containers-azure-files.md).
