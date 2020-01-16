---
title: Preparação do portal para a matriz virtual StorSimple | Microsoft Docs
description: O primeiro tutorial para implantar o StorSimple virtual array envolve preparar a portal do Azure
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b938de0fec099b3f964eae60dc94d059e05a1c6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973493"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Implantar o StorSimple virtual array-preparar o portal do Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Visão geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este é o primeiro artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI usando o modelo do Resource Manager. Este artigo descreve a preparação necessária para criar e configurar o serviço de Device Manager do StorSimple antes de provisionar uma matriz virtual. Este artigo também se vincula a uma lista de verificação de configuração de implantação e aos pré-requisitos de configuração.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que você examine a lista de verificação da configuração de implantação antes de começar. A preparação do portal demora menos de 10 minutos.

As informações publicadas neste artigo se aplicam à implantação de matrizes virtuais do StorSimple no portal do Azure e Microsoft Azure Governamental nuvem.

### <a name="get-started"></a>Começar
O fluxo de trabalho de implantação consiste em preparar o portal, provisionar uma matriz virtual em seu ambiente virtualizado e concluir a instalação. Para começar a usar a implantação do StorSimple virtual array como um servidor de arquivos ou um servidor iSCSI, você precisa consultar os seguintes recursos tabulados.

#### <a name="deployment-articles"></a>Artigos de implantação

Para implantar o StorSimple virtual array, consulte os artigos a seguir na sequência prescrita.

| **#** | **Neste passo** | **Você faz isso...** | **E use esses documentos.** |
| --- | --- | --- | --- |
| 1. |**Configurar o portal do Azure** |Crie e configure seu serviço de Device Manager do StorSimple antes de provisionar uma matriz virtual StorSimple. |[Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Provisionar a matriz virtual** |Para o Hyper-V, provisione e conecte-se a uma matriz virtual StorSimple em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para VMware, provisione e conecte-se a uma matriz virtual StorSimple em um sistema de host executando o VMware ESXi 5,0, 5,5, 6,0 ou 6,5.<br></br> |[Provisionar uma matriz virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Provisionar uma matriz virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurar a matriz virtual** |Para o servidor de arquivos, execute a instalação inicial, registre o servidor de arquivos do StorSimple e conclua a instalação do dispositivo. Em seguida, pode aprovisionar partilhas SMB. <br></br> <br></br> Para o servidor iSCSI, execute a configuração inicial, registre o servidor iSCSI do StorSimple e conclua a instalação do dispositivo. Você pode provisionar volumes iSCSI. |[Configurar matriz virtual como servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurar matriz virtual como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Agora, pode começar a configurar o portal do Azure.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que você precisa coletar antes de configurar o software em sua matriz virtual StorSimple. Preparar essas informações antecipadamente ajuda a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente. Dependendo se a matriz virtual StorSimple é implantada como um servidor de arquivos ou um servidor iSCSI, você precisa de uma das listas de verificação a seguir.

* Baixe a [lista de verificação de configuração do servidor de arquivos do StorSimple virtual array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Baixe a [lista de verificação de configuração do servidor iSCSI do StorSimple virtual array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui você encontra os pré-requisitos de configuração para o serviço de Device Manager do StorSimple, sua matriz virtual StorSimple e a rede do datacenter.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple

Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* Sua assinatura do Microsoft Azure deve ser habilitada para o serviço de Device Manager do StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz virtual StorSimple

Antes de implantar uma matriz virtual, verifique se:

* Você tem acesso a um sistema de host executando o Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5,0, 5,5, 6,0 ou 6,5) que pode ser usado para provisionar um dispositivo.
* O sistema host é capaz de dedicar os seguintes recursos para provisionar sua matriz virtual:
  
  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a 2 milhões arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

* A rede em seu datacenter está configurada de acordo com os requisitos de rede para seu dispositivo StorSimple. Para obter mais informações, consulte os [requisitos de sistema do StorSimple virtual array](storsimple-ova-system-requirements.md).
* Sua matriz virtual StorSimple tem uma largura de banda de Internet dedicada de 5 Mbps (ou mais) sempre disponível. Esta largura de banda não deve ser partilhada com outras aplicações.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Use as instruções passo a passo a seguir para preparar seu portal para o serviço StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Uma única instância do serviço StorSimple Device Manager pode gerenciar várias matrizes virtuais StorSimple. Execute os seguintes passos para criar uma instância do serviço Gestor de Dispositivos do StorSimple. Se você tiver um serviço StorSimple Device Manager existente para gerenciar suas matrizes virtuais, pule esta etapa e vá para a [etapa 2: obter a chave de registro do serviço](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se tiver ativado a criação automática de uma conta do Storage, vá para [Passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço

Assim que o serviço Gestor de Dispositivos do StorSimple estiver ativo e em execução, será necessário obter a chave de registo do serviço. Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço.

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A chave de registro do serviço é usada para registrar todos os dispositivos StorSimple Device Manager que precisam ser registrados com o serviço StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Etapa 3: baixar a imagem da matriz virtual

Depois de ter a chave de registro do serviço, você precisará baixar a imagem apropriada da matriz virtual para provisionar uma matriz virtual em seu sistema host. As imagens da matriz virtual são específicas do sistema operacional e podem ser baixadas na página Início Rápido no portal do Azure.

> [!IMPORTANT]
> O software em execução na matriz virtual StorSimple só pode ser usado com o serviço StorSimple Device Manager.
> 
> 

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Para obter a imagem da matriz virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/). 
2. Na portal do Azure, clique em **procurar > gerenciadores de dispositivos do StorSimple**.
3. Selecione um serviço de Device Manager StorSimple existente. Na folha **Device Manager do StorSimple** , clique em **início rápido**. 
4. Clique no link correspondente à imagem que você deseja baixar no centro de download da Microsoft. Os ficheiros de imagem têm, aproximadamente, 4,8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e posterior
   * VHD para Hyper-V no Windows Server 2008 R2 e posterior
   * VMDK para VMWare ESXi 5,0, 5,5, 6,0 ou 6,5
5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Etapa opcional: configurar uma nova conta de armazenamento para o serviço

Esta etapa é opcional e deve ser executada somente se você não habilitou a criação automática de uma conta de armazenamento com seu serviço.

Se você precisar criar uma conta de armazenamento do Azure em uma região diferente, consulte [como criar uma conta de armazenamento](../storage/common/storage-account-create.md) para obter instruções passo a passo.

Execute as etapas a seguir no [portal do Azure](https://ms.portal.azure.com/) na página serviço StorSimple Device Manager para adicionar uma conta de armazenamento de Microsoft Azure existente.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma assinatura do Azure que o serviço de Device Manager

1. Navegue até o serviço Device Manager, selecione e clique duas vezes nele. Isso abre a folha **visão geral** .
2. Selecione **as credenciais da conta de armazenamento** na seção **configuração** .
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento** , faça o seguinte:
   
   1. Para **assinatura**, selecione **atual**.
   
   2. Forneça o nome da sua conta de armazenamento do Azure.
   
   3. Selecione **habilitar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **desabilitar** somente se você estiver operando em uma nuvem privada.
   
   4. Clique em **Adicionar**. Você será notificado depois que a conta de armazenamento for criada com êxito.<br></br>
   
      ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Passo seguinte

A próxima etapa é provisionar uma máquina virtual para sua matriz virtual StorSimple. Consoante o sistema operativo anfitrião, veja as instruções detalhadas em:

* [Provisionar uma matriz virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Provisionar uma matriz virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

