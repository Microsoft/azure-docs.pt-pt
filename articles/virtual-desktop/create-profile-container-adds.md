---
title: Criar o contentor de perfil FSLogix Azure Files Ative Directory Domain Services - Azure
description: Este artigo descreve como criar um recipiente de perfil FSLogix com ficheiros Azure e Serviços de Domínio do Diretório Ativo Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 21db49ade3b6727775c10321e10aff2e0bf231f1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447952"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Criar um recipiente de perfil com ficheiros Azure e Azure AD DS

Este artigo irá mostrar-lhe como criar um recipiente de perfil FSLogix com ficheiros Azure e Serviços de Domínio do Diretório Ativo Azure (DS AD).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já criou um caso Azure AD DS. Se ainda não tiver um, siga as instruções em [Criar primeiro um domínio gerido básico](../active-directory-domain-services/tutorial-create-instance.md) e, em seguida, volte para cá.

## <a name="add-azure-ad-ds-admins"></a>Adicionar administradores Azure AD DS

Para adicionar administradores adicionais, cria um novo utilizador e concede-lhes permissões.

Para adicionar um administrador:

1. Selecione **O Diretório Ativo Azure** a partir da barra lateral e, em seguida, selecione **Todos os utilizadores** e, em seguida, selecione **Novo utilizador**.

2.  Introduza os dados do utilizador nos campos.

3. No painel de diretório Azure Ative no lado esquerdo do ecrã, selecione **Grupos**.

4. Selecione o grupo **de administradores AAD DC.**

5. No painel esquerdo, selecione **Deputados,** **selecione Adicione os membros** no painel principal. Isto mostrará uma lista de todos os utilizadores disponíveis no Azure AD. Selecione o nome do perfil de utilizador que acabou de criar.

## <a name="set-up-an-azure-storage-account"></a>Criar uma conta de armazenamento Azure

Agora é hora de ativar a autenticação Azure AD DS sobre o Bloco de Mensagens do Servidor (SMB).

Para ativar a autenticação:

1. Se ainda não o fez, crie e implemente uma conta de armazenamento V2 Azure de uso geral seguindo as instruções na [Create a Azure Storage account](../storage/common/storage-account-create.md).

2. Uma vez terminada a configuração da sua conta, selecione **Ir para o recurso**.

3. Selecione **configuração** a partir do painel no lado esquerdo do ecrã e, em seguida, ative a **autenticação do Azure Ative Directory para ficheiros Azure** no painel principal. Quando tiver terminado, selecione **Guardar**.

4. Selecione **a visão geral** no painel do lado esquerdo do ecrã e, em seguida, selecione **Ficheiros** no painel principal.

5. Selecione **a partilha de ficheiros** e **introduza** o **Nome** e Quota nos campos que aparecem no lado direito do ecrã.

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Outros utilizadores precisarão de permissões de acesso para aceder à sua partilha de ficheiros. Para isso, terá de atribuir a cada utilizador uma função com as permissões de acesso apropriadas.

Para atribuir permissões de acesso aos utilizadores:

1. A partir do portal Azure, abra a partilha de ficheiros que criou na [Configuração de uma conta de Armazenamento Azure](#set-up-an-azure-storage-account).

2. Selecione **Controlo de Acesso (IAM)**.

3. **Selecione Adicionar uma atribuição de função**.

4. No separador **de atribuição de funções Add,** selecione a função incorporada adequada da lista de funções. Terá de selecionar pelo menos o **Ficheiro de Armazenamento SMB Share Contributor** para obter permissões adequadas.

5. Para **atribuir acesso a**, selecione **Azure Ative Directory user, group, or service principal**.

6. Selecione um nome ou endereço de e-mail para a identidade do Diretório Ativo Azure.

7. Selecione **Guardar**.

## <a name="get-the-storage-account-access-key"></a>Obtenha a chave de acesso à Conta de Armazenamento

Em seguida, terá de obter a chave de acesso para a sua Conta de Armazenamento.

Para obter a chave de acesso à Conta de Armazenamento:

1. A partir da barra lateral do portal Azure, selecione **contas de Armazenamento**.

2. A partir da lista de contas de armazenamento, selecione a conta para a qual ativou a Azure AD DS e criou as funções personalizadas em etapas acima.

3. Em **Definições**, selecione **teclas de acesso** e copie a tecla a partir da **tecla1**.

4. Vá ao separador **Máquinas Virtuais** e localize qualquer VM que se torne parte da sua piscina de anfitriões.

5. Selecione o nome da máquina virtual (VM) em **Máquinas Virtuais (adVM)** e selecione **Connect**

    Isto irá descarregar um ficheiro RDP que lhe permitirá iniciar sôm no VM com as suas próprias credenciais.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem do separador RDP da janela da máquina virtual.](media/rdp-tab.png)

6. Quando se inscrever no VM, faça um pedido de comando como administrador.

7. Execute o seguinte comando:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - `<desired-drive-letter>`Substitua-a por uma letra de unidade à sua escolha (por exemplo, `y:` ).
    - Substitua todas as instâncias `<storage-account-name>` com o nome da conta de armazenamento que especificou anteriormente.
    - `<share-name>`Substitua-o pelo nome da parte que criou anteriormente.
    - `<storage-account-key>`Substitua-a pela chave da conta de armazenamento do Azure.

    Por exemplo:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Executar os seguintes comandos para permitir que os utilizadores do Windows Virtual Desktop criem o seu próprio recipiente de perfil enquanto bloqueiam o acesso aos contentores de perfil de outros utilizadores.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - `<mounted-drive-letter>`Substitua-a pela letra da unidade que usou para mapear a unidade.
    - `<user-email>`Substitua-se pela UPN do grupo de utilizador ou Ative Directory que contenha os utilizadores que exigirão acesso à partilha.

    Por exemplo:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Criar um contentor de perfil

Agora que os seus perfis estão prontos para ir, vamos criar um recipiente de perfil FSLogix.

Para configurar um recipiente de perfil FSLogix:

1. Inscreva-se no VM do anfitrião da sessão que configuraste no início deste artigo, depois [descarregue e instale o agente FSLogix](/fslogix/install-ht/).

2. Desaperte o ficheiro do agente FSLogix que descarregou e vá para **x64**  >  **Releases**, em seguida, abra **FSLogixAppsSetup.exe**.

3. Assim que o instalador for lançado, **selecione concordo com os termos e condições da licença.** Se aplicável, forneça uma nova chave.

4. Selecione **Instalar**.

5. Open **Drive C**, em seguida, vá a Program **Files**  >  **FSLogix**  >  **Apps** para se certificar de que o agente FSLogix foi corretamente instalado.

     >[!NOTE]
     > Se houver vários VMs na piscina de anfitrião, terá de repetir os passos 1 a 5 para cada VM.

6. Executar **Editor de Registo** (RegEdit) como administrador.

7. Navegue para o software de HKEY_LOCAL_MACHINE **de computador**  >    >    >  **FSLogix,** clique à direita no **FSLogix**, selecione **New** e, em seguida, selecione **Key**.

8. Crie uma nova chave chamada **Profiles**.

9.  Clique com o botão direito em **Perfis,** selecione **Novo** e, em seguida, selecione **DWORD (32-bit) Valor.** Nomeie o valor **Ativado** e desa um valor **de Dados** para **1**.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da chave perfis. O ficheiro REG_DWORD é realçado e o seu valor de Dados está definido para 1.](media/dword-value.png)

10. Clique com o botão direito em **Perfis**, selecione **Novo** e, em seguida, selecione **Multi-String Value**. Nomeie o valor **VHDLocations e insira** o URI para a partilha de Ficheiros Azure `\\fsprofile.file.core.windows.net\share` como o valor de Dados.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da tecla Perfis mostrando o ficheiro VHDLocations. O seu valor de dados mostra o URI para a partilha de Ficheiros Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Atribuir utilizadores a um anfitrião de sessão

Agora terá de atribuir os utilizadores ao seu anfitrião de sessão.

Para atribuir utilizadores:

1. Executar o Windows PowerShell como administrador e, em seguida, executar o seguinte cmdlet para iniciar serção no Windows Virtual Desktop com PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando solicitado para obter credenciais, insira o mesmo utilizador que foi concedido ao TenantCreator, RDS Owner, ou RDS Contributor no inquilino virtual do Windows Desktop.

2. Executar os seguintes cmdlets para atribuir o utilizador ao grupo de trabalho remoto:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Tal como os cmdlets anteriores, certifique-se de `<your-wvd-tenant>` `<wvd-pool>` substituir, e `<user-principal>` pelos valores relevantes.

    Por exemplo:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Certifique-se de que o seu perfil funciona

Agora tudo o que tem que fazer é certificar-se de que o perfil que criou existe e funciona como pretendido.

Para verificar o seu perfil:

1. Abra um navegador e vá ao [cliente web virtual do Windows Desktop.](https://rdweb.wvd.microsoft.com/arm/webclient)

2. Inscreva-se na conta de utilizador atribuída ao grupo Remote Desktop.

3. Uma vez estabelecida a sessão de utilizador, abra o portal Azure e inscreva-se com uma conta administrativa.

4. A partir da barra lateral, selecione **contas de Armazenamento**.

5. Selecione a conta de armazenamento configurada como a partilha de ficheiros para o seu pool de anfitriões de sessão e ativada com Azure AD DS.

6. Selecione o ícone **Ficheiros** e, em seguida, expanda a sua parte.

    Se tudo estiver bem montado, deve ver um **Diretório** com um nome formatado assim: `<user SID>-<username>` .

## <a name="next-steps"></a>Passos seguintes

Se procura formas alternativas de criar recipientes de perfil FSLogix, confira os seguintes artigos:

- [Crie um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).
- [Crie um recipiente de perfil FSLogix para uma piscina hospedeira utilizando ficheiros Azure NetApp](create-fslogix-profile-container.md)

Pode encontrar informações mais detalhadas sobre conceitos relacionados com recipientes FSlogix para ficheiros Azure em [recipientes de perfil FSLogix e ficheiros Azure](fslogix-containers-azure-files.md).
