---
title: Partilha de contentores de perfil FSLogix do Windows Virtual Desktop - Azure
description: Como configurar um recipiente de perfil FSLogix para um conjunto de anfitriões virtual do Windows Desktop utilizando uma partilha de ficheiros virtual baseada em máquinas.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 69582f26544d954088fd5a57bfe5730750b7b5a8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447918"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Criar um contentor de perfis para um conjunto de anfitriões através de uma partilha de ficheiros

O serviço De Ambiente de Trabalho Virtual Windows oferece recipientes de perfil FSLogix como a solução recomendada para o perfil do utilizador. Não recomendamos a utilização da solução De Disco de Perfil do Utilizador (UPD), que será depreciada em futuras versões do Windows Virtual Desktop.

Este artigo dir-lhe-á como configurar uma partilha de contentores de perfil FSLogix para uma piscina hospedeira utilizando uma partilha de ficheiros virtual baseada em máquinas. Recomendamos vivamente a utilização de Ficheiros Azure em vez de partilhas de ficheiros. Para obter mais documentação FSLogix, consulte o site da [FSLogix.](https://docs.fslogix.com/)

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte [as opções de armazenamento para recipientes de perfil FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Crie uma nova máquina virtual que atue como uma partilha de ficheiros

Ao criar a máquina virtual, certifique-se de colocá-la na mesma rede virtual que as máquinas virtuais do pool anfitrião ou numa rede virtual que tenha conectividade com as máquinas virtuais do pool anfitrião. Pode criar uma máquina virtual de várias formas:

- [Crie uma máquina virtual a partir de uma imagem da Galeria Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem nãogerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, junte-a ao domínio fazendo as seguintes coisas:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, lance o **Painel de Controlo** e selecione **Sistema.**
3. Selecione **nome de computador**, selecione Alterar as **definições** e, em seguida, selecione **Alterar...**
4. Selecione **Domínio** e, em seguida, introduza o domínio Ative Directory na rede virtual.
5. Autenticar com uma conta de domínio que tem privilégios para máquinas de união de domínios.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Prepare a máquina virtual para funcionar como uma partilha de ficheiros para perfis de utilizador

Seguem-se instruções gerais sobre como preparar uma máquina virtual para funcionar como uma partilha de ficheiros para perfis de utilizador:

1. Adicione os utilizadores do Windows Virtual Desktop Ative Directory a um [grupo de segurança ative directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Este grupo de segurança será utilizado para autenticar os utilizadores do Windows Virtual Desktop na máquina virtual de partilha de ficheiros que acabou de criar.
2. [Ligue-se à máquina virtual de partilha de ficheiros](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na máquina virtual de partilha de ficheiros, crie uma pasta na **unidade C** que será usada como partilha de perfil.
4. Clique com o botão direito na nova pasta, selecione **Propriedades,** selecione **Partilhar,** em seguida, selecione **Partilha Avançada...**.
5. **Selecione Partilhe esta pasta,** selecione **Permissões...** e, em seguida, selecione **Adicionar...**.
6. Procure o grupo de segurança ao qual adicionou os utilizadores do Windows Virtual Desktop e, em seguida, certifique-se de que o grupo tem **Controlo Total**.
7. Depois de adicionar o grupo de segurança, clique com o botão direito na pasta, selecione **Propriedades**, selecione **Partilhar** e, em seguida, copie o Caminho da **Rede** para usar para mais tarde.

Para obter mais informações sobre permissões, consulte a [documentação FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Configure o recipiente de perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada máquina registada na piscina hospedeira:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Lance um navegador de internet e navegue [para este link](https://go.microsoft.com/fwlink/?linkid=2084562) para descarregar o agente FSLogix.
3. Navegue para \\ \\ o Lançamento win32 \\ ou \\ \\ X64 no ficheiro .zip e executar \\ **fSLogixAppsSetup** para instalar o agente FSLogix.  Para saber mais sobre como instalar o FSLogix, consulte [Download e instale o FSLogix](/fslogix/install-ht/).
4. Navegue para **ficheiros de programa**  >  **FSLogix**  >  **Apps** para confirmar o agente instalado.
5. A partir do menu inicial, executar **RegEdit** como administrador. Navegue para **o software de HKEY_LOCAL_MACHINE de computador \\ \\ \\ FSLogix**.
6. Crie uma chave chamada **Perfis.**
7. Criar os seguintes valores para a tecla Perfis:

| Nome                | Tipo               | Dados/Valor                        |
|---------------------|--------------------|-----------------------------------|
| Ativado             | DWORD              | 1                                 |
| VHDLocations        | Valor multi-cordas | "Caminho da rede para partilha de ficheiros"     |

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs da piscina anfitriã. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [acesso vm just-in-time](../security-center/security-center-just-in-time.md).
