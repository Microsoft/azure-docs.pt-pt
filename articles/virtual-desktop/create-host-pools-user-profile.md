---
title: Configurar uma partilha do perfil de utilizador para um conjunto de anfitrião do Windows Virtual Desktop Preview - Azure
description: Como configurar um contentor de perfil FSLogix para um conjunto de anfitrião do Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f6516e37107a16d80c4d9eb9514782bdbcc44184
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925215"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurar uma partilha do perfil de utilizador para um conjunto de anfitriões

O serviço de pré-visualização de ambiente de Trabalho Virtual do Windows oferece FSLogix contentores de perfil, como a solução de perfil do usuário recomendada. Não é recomendada a utilizar a solução de disco de perfil de utilizador (UDP), que será preterido em futuras versões de área de Trabalho Virtual do Windows.

Esta secção irá dizer como configurar uma partilha de contentor do perfil de FSLogix para um conjunto de anfitrião. Para obter documentação geral sobre FSLogix, consulte a [FSLogix site](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Criar uma nova máquina virtual que funcionará como uma partilha de ficheiros

Ao criar a máquina virtual, certifique-se de que colocá-lo em qualquer um da mesma rede virtual que as máquinas de virtuais do conjunto de anfitrião ou numa rede virtual que tenha conetividade com as máquinas de virtuais do conjunto de anfitrião. Pode criar uma máquina virtual de várias formas:

- [Criar uma máquina virtual a partir de uma imagem da galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual a partir de uma imagem não gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, associe-o ao domínio, efetuando os seguintes procedimentos:

1. [Ligar à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Na máquina virtual, inicie **painel de controlo** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar as definições**e, em seguida, selecione **alteração...**
4. Selecione **domínio** e, em seguida, introduza o domínio do Active Directory na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para máquinas de associação a um domínio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparar a máquina virtual para agir como uma partilha de ficheiros para perfis de utilizador

Seguem-se instruções gerais sobre como preparar uma máquina virtual para agir como uma partilha de ficheiros para perfis de utilizador:

1. Adicionar utilizadores do Windows Virtual Desktop Active Directory para um [grupo de segurança do Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Este grupo de segurança será utilizado para autenticar os utilizadores de área de Trabalho Virtual do Windows para a máquina de virtual de partilha de ficheiro que acabou de criar.
2. [Ligar à máquina de virtual de partilha de ficheiros](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na máquina de virtual de partilha de ficheiros, crie uma pasta no **unidade C** que será utilizada como a partilha do perfil.
4. A nova pasta com o botão direito, selecione **propriedades**, selecione **partilha**, em seguida, selecione **de partilha avançadas...** .
5. Selecione **partilhar esta pasta**, selecione **permissões...** , em seguida, selecione **adicionar...** .
6. Procure o grupo de segurança ao qual adicionou os usuários de área de Trabalho Virtual do Windows, em seguida, certificar-se de que esse grupo tem **controlo total**.
7. Depois de adicionar o grupo de segurança, clique com botão direito na pasta, selecione **propriedades**, selecione **partilha**, em seguida, copie o **caminho de rede** a utilizar para utilizar mais tarde.

Para obter mais informações sobre as permissões, consulte a [FSLogix documentação](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Configurar o contentor de perfil FSLogix

Para configurar as máquinas virtuais com o software de FSLogix, efetue o seguinte procedimento em cada máquina registrado para o conjunto de anfitrião:

1. [Ligar à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Inicie um browser e navegue para [esta ligação](https://go.microsoft.com/fwlink/?linkid=2084562) para transferir o agente de FSLogix. Como parte da pré-visualização pública da área de Trabalho Virtual do Windows, obterá uma chave de licença para ativar o software de FSLogix. A chave é o arquivo de LicenseKey.txt incluído no ficheiro. zip de agente de FSLogix.
3. Navegue para o \\ \\Win32\\versão ou \\ \\X64\\lançamento de ficheiro. zip e execute **FSLogixAppsSetup** para instalar o agente de FSLogix.
4. Navegue para **Program Files** > **FSLogix** > **aplicações** para confirmar se o agente instalado.
5. A partir do menu Iniciar, execute **RegEdit** como administrador. Navegue para **computador\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.
6. Crie uma chave denominada **perfis**.
7. Crie os seguintes valores para a chave de perfis:

| Name                | Type               | Dados/valor                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Valor de múltiplas cadeias | "Caminho de rede para a partilha de ficheiros"     |

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de área de Trabalho Virtual do Windows no Azure, recomendamos que não abrir a porta 3389 de entrada nas suas VMs. Área de Trabalho Virtual do Windows não precisa de uma porta de entrada aberta 3389 para os utilizadores acedam a VMs do conjunto de anfitrião. Se é necessário abrir a porta 3389 para fins de resolução de problemas, recomendamos que utilize [o acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).