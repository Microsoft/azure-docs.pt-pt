---
title: Partilha de recipientede perfil FSLogix do Windows Virtual Desktop - Azure
description: Como configurar um recipiente de perfil FSLogix para um conjunto de anfitriões do Windows Virtual Desktop utilizando uma partilha virtual de ficheiros baseado em máquinas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127363"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Criar um contentor de perfis para um conjunto de anfitriões através de uma partilha de ficheiros

O serviço de ambiente de trabalho virtual windows oferece os recipientes de perfil FSLogix como a solução de perfil recomendado para o utilizador. Não recomendamos a utilização da solução user Profile Disk (UPD), que será depreciada em futuras versões do Windows Virtual Desktop.

Este artigo dir-lhe-á como configurar uma partilha de contentor de perfil FSLogix para uma piscina hospedeira utilizando uma partilha virtual de ficheiros baseado em máquinas. Para obter mais documentação do FSLogix, consulte o [site do FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte opções de armazenamento para recipientes de [perfil FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Criar uma nova máquina virtual que funcionará como uma partilha de ficheiros

Ao criar a máquina virtual, certifique-se de colocá-la na mesma rede virtual que as máquinas virtuais do pool anfitrião ou numa rede virtual que tenha conectividade com as máquinas virtuais do pool anfitrião. Pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual a partir de uma imagem da Galeria Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual a partir de uma imagem não gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, junte-a ao domínio fazendo as seguintes coisas:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Na máquina virtual, lance **o Painel de Controlo** e selecione **System**.
3. Selecione **o nome do computador,** selecione **definições de alteração**e, em seguida, selecione **Mudar...**
4. Selecione **Domínio** e, em seguida, introduza o domínio de Diretório Ativo na rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios de unir máquinas de união de domínios.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Prepare a máquina virtual para funcionar como uma partilha de ficheiros para os perfis dos utilizadores

As seguintes são instruções gerais sobre como preparar uma máquina virtual para funcionar como uma partilha de ficheiros para os perfis do utilizador:

1. Adicione os utilizadores ativos do Windows Ao Diretório Ativo do Windows ao [Diretório Ativo](/windows/security/identity-protection/access-control/active-directory-security-groups/). Este grupo de segurança será utilizado para autenticar os utilizadores do Windows Virtual Desktop na máquina virtual de partilha de ficheiros que acaba de criar.
2. [Ligue-se à máquina virtual de partilha de ficheiros](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na máquina virtual de partilha de ficheiros, crie uma pasta na **unidade C** que será utilizada como partilha de perfil.
4. Clique na nova pasta, selecione **Propriedades,** selecione **Partilhar,** em seguida, selecione **Partilha Avançada...** .
5. Selecione **Partilhar esta pasta,** selecione **Permissões...** e, em seguida, selecione **Adicionar...** .
6. Procure o grupo de segurança ao qual adicionou os utilizadores do Windows Virtual Desktop e, em seguida, certifique-se de que o grupo tem **controlo completo**.
7. Depois de adicionar o grupo de segurança, clique na pasta, selecione **Propriedades,** **selecione Partilhar,** e depois copie o Caminho da **Rede** para usar mais tarde.

Para obter mais informações sobre permissões, consulte a [documentação do FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Configure o recipiente de perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada máquina registada na piscina anfitriã:

1. [Ligue-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais que forneceu ao criar a máquina virtual.
2. Lance um navegador de internet e navegue para [este link](https://go.microsoft.com/fwlink/?linkid=2084562) para descarregar o agente FSLogix.
3. Navegue para \\\\Win32\\Release ou \\\\X64\\Lançamento no ficheiro .zip e execute **fSLogixAppsSetup** para instalar o agente FSLogix.  Para saber mais sobre como instalar o FSLogix, consulte [O Download e instala o FSLogix](/fslogix/install-ht/).
4. Navegue para **Ficheiros** de Programa > **FSLogix** > **Apps** para confirmar o agente instalado.
5. A partir do menu inicial, executar **RegEdit** como administrador. Navegue para **o software\\de\\HKEY_LOCAL_MACHINE de\\de computador  FSLogix**.
6. Criar uma chave chamada **Perfis.**
7. Criar os seguintes valores para a chave Perfis:

| Nome                | Tipo               | Dados/Valor                        |
|---------------------|--------------------|-----------------------------------|
| Ativado             | DWORD              | 1                                 |
| VHDLocations        | Valor multi-string | "Caminho de rede para partilha de ficheiros"     |

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs do grupo anfitrião. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [o acesso VM just-in-time](../security-center/security-center-just-in-time.md).
