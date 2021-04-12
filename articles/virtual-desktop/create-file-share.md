---
title: Criar uma partilha de ficheiros Azure Files com um controlador de domínio - Azure
description: Crie um recipiente de perfil FSLogix numa partilha de ficheiros Azure num conjunto de anfitriões virtual do Windows com o seu domínio Ative Directory.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e859da6b3ac38ddb89c998d172c39f2549455aaa
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447935"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Criar um recipiente de perfil com ficheiros Azure e DS AD

Neste artigo, você vai aprender como criar uma partilha de ficheiros Azure autenticada por um controlador de domínio num pool de anfitriões virtual do Windows Virtual. Pode utilizar esta partilha de ficheiros para armazenar perfis de armazenamento.

Este processo utiliza serviços de domínio de diretório ativo (DS AD), que é um serviço de diretório on-prem. Se estiver à procura de informações sobre como criar um recipiente de perfil FSLogix com DS AD Azure, consulte [Criar um recipiente de perfil FSLogix com ficheiros Azure](create-profile-container-adds.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que o seu controlador de domínio está sincronizado com o Azure e que é resolúvel a partir da rede virtual Azure (VNET) a que os anfitriões da sessão estão ligados.

## <a name="set-up-a-storage-account"></a>Criar uma conta de armazenamento

Primeiro, terá de criar uma conta de armazenamento do Azure Files.

Para criar uma conta de armazenamento:

1. Inicie sessão no portal do Azure.

2. Procure uma **conta de armazenamento** na barra de pesquisa.

3. Selecione **+Adicionar**.

4. Introduza as seguintes informações na página **de conta de armazenamento Criar:**

    - Criar um novo grupo de recursos.
    - Introduza um nome único para a conta de armazenamento.
    - Para **localização**, recomendamos que escolha o mesmo local que a piscina de anfitriões virtual do Windows Desktop.
    - Em **Desempenho**, selecione **Standard**. (Dependendo dos seus requisitos de IOPS. Para obter mais informações, consulte [as opções de Armazenamento para recipientes de perfil FSLogix no Windows Virtual Desktop](store-fslogix-profile.md).)
    - Para **o tipo de conta**, selecione **StorageV2** ou **FileStorage** (só disponível se o nível de desempenho for Premium).
    - Para **replicação,** selecione **armazenamento localmente redundante (LRS)**.

5. Quando terminar, selecione **'Rever + criar'** e, em seguida, selecione **Criar**.

Se necessitar de instruções de configuração mais detalhadas, consulte [a disponibilidade regional](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Em seguida, terá de criar uma partilha de ficheiros Azure.

Para criar uma partilha de ficheiros:

1. Selecione **Ir para recurso**.

2. Na página Descrição geral, selecione **Partilhas de ficheiros**.

3. Selecione **+Ações de arquivo,** crie uma nova partilha de **ficheiros** com perfil nomeado , em seguida, introduza uma quota apropriada ou deixe o campo em branco sem quota.

4. Selecione **Criar**.

## <a name="enable-active-directory-authentication"></a>Ativar a autenticação do Diretório Ativo

Em seguida, você precisará ativar a autenticação do Ative Directory (AD). Para ativar esta política, terá de seguir as instruções desta secção numa máquina que já se juntou ao domínio. Para ativar a autenticação, siga estas instruções sobre o VM que executa o controlador de domínio:

1. Protocolo remoto de ambiente de trabalho no VM de domínio.

2. Siga as instruções em [Ativar a autenticação AD DS para as suas ações de ficheiroS Azure](../storage/files/storage-files-identity-ad-ds-enable.md) para instalar o módulo AzFilesHybrid e ativar a autenticação.

3.  Abra o portal Azure, abra a sua conta de armazenamento, **selecione Configuração** e confirme que **o Ative Directory (AD)** está definido como **Ativado**.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página de Configuração com diretório ativo Azure (AD) ativada.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Atribuir permissões Azure RBAC a utilizadores de desktop virtual do Windows

Todos os utilizadores que necessitem de ter perfis FSLogix armazenados na conta de armazenamento devem ser atribuídos à função de Contribuinte de Partilha SMB de Ficheiros de Armazenamento.

Os utilizadores que se inscrevam nos anfitriões de sessão do Windows Virtual Desktop precisam de permissões de acesso para aceder à partilha de ficheiros. A concessão de acesso a uma partilha de Ficheiros do Azure envolve configurar permissões tanto ao nível das partilhas como ao nível do NTFS, semelhante a uma partilha tradicional do Windows.

Para configurar permissões de nível de partilha, atribua a cada utilizador uma função com as permissões de acesso adequadas. As permissões podem ser atribuídas a utilizadores individuais ou a um grupo AZure AD. Para saber mais, consulte [Atribuir permissões de acesso a uma identidade.](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)

>[!NOTE]
>As contas ou grupos a que atribui permissões deverão ter sido criados no domínio e sincronizados com o Azure Active Directory. As contas criadas no Azure Active Directory não funcionarão.

Para atribuir permissões de controlo de acesso baseado em funções Azure (Azure RBAC):

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que criou na [Configuração de uma conta de armazenamento.](#set-up-a-storage-account)

3. Selecione **as ações do Ficheiro** e, em seguida, selecione o nome da partilha de ficheiros que pretende utilizar.

4. Selecione **Controlo de Acesso (IAM)**.

5. **Selecione Adicionar uma atribuição de função**.

6. No separador **de atribuição de funções Adicionar,** selecione **Storage File Data SMB Share Elevated Contributor** para a conta de administrador.

     Para atribuir permissões aos utilizadores para os perfis FSLogix, siga estas mesmas instruções. No entanto, quando chegar ao passo 5, selecione **Storage File Data SMB Share Contributor.**

7. Selecione **Guardar**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Atribuir permissões aos utilizadores na partilha de ficheiros Azure

Uma vez que tenha atribuído permissões Azure RBAC aos seus utilizadores, em seguida terá de configurar as permissões NTFS.

Precisa de saber duas coisas do portal Azure para começar:

- O caminho da UNC.
- A chave da conta de armazenamento.

### <a name="get-the-unc-path"></a>Pegue o caminho da UNC

Eis como obter o caminho da UNC:

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que criou na [Configuração de uma conta de armazenamento.](#set-up-a-storage-account)

3. Selecione **Definições** e, em seguida, **selecione Propriedades**.

4. Copie o Endpoint URI **do Serviço de Ficheiros Primários** para o editor de texto à sua escolha.

5. Depois de copiar o URI, faça as seguintes coisas para mudá-lo para o UNC:

    - Remover `https://` e substituir por `\\`
    - Substitua o corte dianteiro `/` por um corte traseiro `\` .
    - Adicione o nome da partilha de ficheiros que criou na [Criar uma partilha de ficheiros Azure](#create-an-azure-file-share) até ao final do UNC.

        Por exemplo: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

Para obter a chave da conta de armazenamento:

1. Abra o portal do Azure.

2. Abra a conta de armazenamento que criou na [Configuração de uma conta de armazenamento.](#set-up-a-storage-account)

3. No **separador conta 'Armazenamento',** selecione **as teclas de acesso**.

4. Copie **a chave1** ou **a chave2** para um ficheiro na sua máquina local.

### <a name="configure-ntfs-permissions"></a>Configure permissões NTFS

Para configurar as suas permissões NTFS:

1. Abra um pedido de comando num VM ligado ao domínio.

2. Executar o seguinte comando para montar a partilha de ficheiros Azure e atribuir-lhe uma letra de unidade:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Executar o seguinte comando para rever as permissões de acesso à partilha de ficheiros Azure:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    `<mounted-drive-letter>`Substitua-a pela letra da unidade a que mapeou.

    Tanto a *Autoridade NT\Utilizadores autenticados* como *OS Utilizadores BUILTIN\Os utilizadores* têm determinadas permissões por padrão. Estas permissões por defeito permitem que estes utilizadores leiam os recipientes de perfil de outros utilizadores. No entanto, as permissões descritas nas [permissões de armazenamento Configure para utilização com contentores de perfil e contentores de escritório](/fslogix/fslogix-storage-config-ht) não permitem que os utilizadores leiam os recipientes de perfil uns dos outros.

4. Executar os seguintes comandos para permitir que os utilizadores do Windows Virtual Desktop criem o seu próprio recipiente de perfil enquanto bloqueiam o acesso aos seus contentores de perfil de outros utilizadores.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Substitua <> de letra montada com a letra da unidade que usou para mapear a unidade.
     - Substitua <> de e-mail de utilizador pela UPN do grupo de utilizador ou Ative Directory que contenha os utilizadores que exigirão o acesso à partilha.

     Por exemplo:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Configure fSLogix em VMs anfitrião de sessão

Esta secção vai mostrar-lhe como configurar uma VM com o FSLogix. Terá de seguir estas instruções sempre que configurar um anfitrião da sessão. Antes de começar a configurar, siga as instruções no [Download e instale o FSLogix](/fslogix/install-ht). Existem várias opções disponíveis que garantem que as chaves do registo estão definidas em todos os anfitriões da sessão. Pode definir estas opções numa imagem ou configurar uma política de grupo.

Para configurar o FSLogix na VM do anfitrião da sessão:

1. RDP para o anfitrião da sessão VM da piscina de anfitriões virtual do Windows Desktop.

2. [Descarregue e instale o FSLogix](/fslogix/install-ht).

5. Siga as instruções em [Configurar as definições do registo do contentor de perfil](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Navegue para **o Software**  >    >    >  **FSLogix de** HKEY_LOCAL_MACHINE de Computador .

    - Crie uma chave **de perfis.**

    - Criar **Ativado, DWORD** com um valor de 1.

    - Criar **VHDLocations, MULTI_SZ**.

    - Desloque o valor das **VHDLocations** para o caminho unc gerado no [caminho do UNC](#get-the-unc-path).

6. Reinicie a VM.

## <a name="testing"></a>Testar

Uma vez instalado e configurado FSLogix, pode testar a sua implementação insacientando-se com uma conta de utilizador que foi atribuída a um grupo de aplicações ou um ambiente de trabalho na piscina de anfitrião. Certifique-se de que a conta de utilizador com a qual insinou tem permissão na partilha de ficheiros.

Se o utilizador já se ter inscrito antes, terá um perfil local existente que será utilizado durante esta sessão. Para evitar a criação de um perfil local, crie uma nova conta de utilizador para utilizar para testes ou utilize os métodos de configuração descritos no [Tutorial: Contentor de perfil de configuração para redirecionar os perfis do utilizador](/fslogix/configure-profile-container-tutorial/).

Para verificar as suas permissões na sua sessão:

1. Inicie uma sessão no Windows Virtual Desktop.

2. Abra o portal do Azure.

3. Abra a conta de armazenamento que criou na [Configuração de uma conta de armazenamento.](#set-up-a-storage-account)

4. **Selecione Criar uma partilha** na página de partilha de ficheiros Create azure.

5. Certifique-se de que existe agora uma pasta que contenha o perfil do utilizador nos seus ficheiros.

Para testes adicionais, siga as instruções em [Certificar-se de que o seu perfil funciona](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas, consulte [este guia de resolução de problemas](/fslogix/fslogix-trouble-shooting-ht).
