---
title: Configurar uma partilha do perfil de utilizador para um conjunto de anfitrião - Azure
description: Como configurar um contentor de perfil FSLogix para um conjunto de anfitrião de área de Trabalho Virtual do Windows (pré-visualização).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 9dfbda6e17cf954369fd6caa533ba9eef41fd451
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336019"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurar uma partilha do perfil de utilizador para um conjunto de anfitrião

O serviço de área de Trabalho Virtual do Windows (pré-visualização) oferece FSLogix contentores de perfil, como a solução de perfil do usuário recomendada. Não é recomendado utilizar a solução de disco de perfil de utilizador (UDP), e vão ser preterida em versões futuras do ambiente de Trabalho Virtual do Windows.

Esta secção irá dizer como configurar uma partilha de contentor do perfil de FSLogix para um conjunto de anfitrião.

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

1. Junte-se as máquinas de virtuais de anfitrião de sessão para uma [grupo de segurança do Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Este grupo de segurança será utilizado para autenticar as máquinas de virtuais de hosts de sessão para a máquina de virtual de partilha de ficheiro que acabou de criar.
2. [Ligar à máquina de virtual de partilha de ficheiros](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na máquina de virtual de partilha de ficheiros, crie uma pasta no **unidade C** que será utilizada como a partilha do perfil.
4. A nova pasta com o botão direito, selecione **propriedades**, selecione **partilha**, em seguida, selecione **de partilha avançadas...** .
5. Selecione **partilhar esta pasta**, selecione **permissões...** , em seguida, selecione **adicionar...** .
6. Procure o grupo de segurança ao qual adicionou as máquinas de virtuais de anfitrião de sessão, em seguida, certificar-se de que esse grupo tem **controlo total**.
7. Depois de adicionar o grupo de segurança, clique com botão direito na pasta, selecione **propriedades**, selecione **partilha**, em seguida, copie o **caminho de rede** a utilizar para utilizar mais tarde.

Para melhores práticas em permissões, consulte o seguinte procedimento [FSLogix documentação](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Configurar o contentor de perfil FSLogix

Para configurar as máquinas virtuais com o software de FSLogix, efetue o seguinte procedimento em cada máquina registrado para o conjunto de anfitrião:

1. [Ligar à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Inicie um browser e navegue para o seguinte [link](https://go.microsoft.com/fwlink/?linkid=2084562) para transferir o agente de FSLogix. Como parte da pré-visualização pública da área de Trabalho Virtual do Windows, obterá uma chave de licença para ativar o software de FSLogix. A chave é o arquivo de LicenseKey.txt incluído no ficheiro. zip de agente de FSLogix.
3. Instale o agente de FSLogix.
4. Navegue para **Program Files** > **FSLogix** > **aplicações** para confirmar se o agente instalado.
5. A partir do menu Iniciar, execute **RegEdit** como administrador. Navegue para **computador\\HKEY_LOCAL_MACHINE\\software\\FSLogix\\perfis**
6. Crie os seguintes valores:

| Name                | Type               | Dados/valor                        |
|---------------------|--------------------|-----------------------------------|
| Ativado             | DWORD              | 1                                 |
| VHDLocations        | Valor de múltiplas cadeias | "Caminho de rede para a partilha de ficheiros" |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | "número inteiro para o tamanho do perfil"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
