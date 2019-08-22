---
title: Criar um contêiner de perfil do FSLogix para um pool de hosts usando um compartilhamento de arquivos baseado em máquina virtual-Azure
description: Como configurar um contêiner de perfil FSLogix para um pool de hosts da visualização de área de trabalho virtual do Windows usando um compartilhamento de arquivos baseado em máquina virtual.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: cf3d682e4d0c68822267a4e63846d80b632cbdcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876798"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos

O serviço de visualização de área de trabalho virtual do Windows oferece contêineres de perfil de FSLogix como a solução de perfil de usuário recomendada. Não recomendamos o uso da solução UPD (disco de perfil do usuário), que será preterida em versões futuras da área de trabalho virtual do Windows.

Este artigo informará como configurar um compartilhamento de contêiner de perfil FSLogix para um pool de hosts usando um compartilhamento de arquivos baseado em máquina virtual. Para obter mais documentação do FSLogix, consulte o [site do FSLogix](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Criar uma nova máquina virtual que atuará como um compartilhamento de arquivos

Ao criar a máquina virtual, certifique-se de colocá-la na mesma rede virtual que as máquinas virtuais do pool de hosts ou em uma rede virtual que tenha conectividade com as máquinas virtuais do pool de hosts. Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual por meio de uma imagem da galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual com base em uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual com base em uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, ingresse-a no domínio seguindo estas etapas:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar configurações**e, em seguida, selecione **alterar...**
4. Selecione **domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparar a máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário

Veja a seguir as instruções gerais sobre como preparar uma máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário:

1. Adicione os usuários de Active Directory da área de trabalho virtual do Windows a um [grupo de segurança Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Esse grupo de segurança será usado para autenticar os usuários da área de trabalho virtual do Windows para a máquina virtual de compartilhamento de arquivos que você acabou de criar.
2. [Conecte-se à máquina virtual de compartilhamento de arquivos](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na máquina virtual de compartilhamento de arquivos, crie uma pasta na **unidade C** que será usada como o compartilhamento de perfil.
4. Clique com o botão direito do mouse na nova pasta, selecione **Propriedades**, **compartilhamento**, selecione **compartilhamento avançado...** .
5. Selecione **compartilhar esta pasta**, selecione **permissões...** e, em seguida, selecione **Adicionar...** .
6. Procure o grupo de segurança ao qual você adicionou os usuários da área de trabalho virtual do Windows e verifique se o grupo tem **controle total**.
7. Depois de adicionar o grupo de segurança, clique com o botão direito do mouse na pasta, selecione **Propriedades**, selecione **compartilhamento**e, em seguida, copie o **caminho de rede** a ser usado para mais tarde.

Para obter mais informações sobre permissões, consulte a [documentação do FSLogix](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht).

## <a name="configure-the-fslogix-profile-container"></a>Configurar o contêiner do perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada computador registrado no pool de hosts:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Inicie um navegador da Internet e navegue até [este link](https://go.microsoft.com/fwlink/?linkid=2084562) para baixar o agente do FSLogix.
3. \\Navegue até\\ versãodo\\Win32ouversãox64\\no arquivo. zip e execute FSLogixAppsSetup para instalar o agente do FSLogix. \\ \\  Para saber mais sobre como instalar o FSLogix, consulte [baixar e instalar o FSLogix](https://docs.microsoft.com/fslogix/install-ht).
4. Navegue até **arquivos** > de programas**FSLogix** > **aplicativos** para confirmar o agente instalado.
5. No menu Iniciar, execute **regedit** como administrador. Navegue até **computador\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.
6. Crie uma chave chamada **perfis**.
7. Crie os seguintes valores para a chave de perfis:

| Name                | Type               | Dados/valor                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Valor de cadeia de caracteres múltipla | "Caminho de rede para compartilhamento de arquivos"     |

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente de área de trabalho virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 em suas VMs. A área de trabalho virtual do Windows não requer uma porta de entrada aberta 3389 para que os usuários acessem as VMs do pool de hosts. Se você precisar abrir a porta 3389 para fins de solução de problemas, recomendamos o uso [do acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).