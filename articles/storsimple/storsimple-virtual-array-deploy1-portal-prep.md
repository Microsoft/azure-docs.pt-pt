---
title: Preparação do portal do StorSimple Virtual Array | Documentos da Microsoft
description: Primeiro tutorial para implementar a matriz virtual StorSimple envolve a preparação do portal do Azure
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
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6f0a6371b38f0271237db0f7d80b831ecc145c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088711"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Implantar o StorSimple Virtual Array - preparar o portal do Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Descrição geral

Este é o primeiro artigo da série de tutoriais de implementação necessários para implementar completamente sua matriz virtual como um servidor de ficheiros ou um servidor de iSCSI com o modelo do Resource Manager. Este artigo descreve a preparação necessária para criar e configurar o seu serviço StorSimple Device Manager antes de aprovisionar uma matriz virtual. Este artigo também contém ligações horizontalmente a uma lista de verificação de configuração de implementação e configuração de pré-requisitos.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que reveja a lista de verificação de configuração de implementação antes de começar. A preparação do portal demora menos de 10 minutos.

As informações publicadas neste artigo aplica-se para a implementação de matrizes virtuais do StorSimple no portal do Azure e Cloud do Microsoft Azure Government.

### <a name="get-started"></a>Introdução
O fluxo de trabalho de implantação consiste em preparar o portal, uma matriz virtual no seu ambiente virtualizado de aprovisionamento e concluir a configuração. Para começar a utilizar com a implementação de matriz Virtual StorSimple como um servidor de ficheiros ou um servidor de iSCSI, terá de consultar os seguintes recursos tabulated.

#### <a name="deployment-articles"></a>Artigos sobre implantação

Para implementar a sua matriz Virtual StorSimple, consulte os artigos seguintes na seqüência prescrita.

| **#** | **Neste passo** | **Fazê-lo...** | **E usar esses documentos.** |
| --- | --- | --- | --- |
| 1. |**Configurar o portal do Azure** |Criar e configurar o seu serviço StorSimple Device Manager antes de aprovisionar uma matriz Virtual StorSimple. |[Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Aprovisionar a matriz Virtual** |Para o Hyper-V, aprovisionar e ligar a uma matriz Virtual do StorSimple num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para o VMware, aprovisionar e ligar a uma matriz Virtual do StorSimple num sistema anfitrião com o VMware ESXi 5.0, 5.5, 6.0 ou 6.5.<br></br> |[Aprovisionar uma matriz virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprovisionar uma matriz virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurar a matriz Virtual** |Para o servidor de ficheiros, execute a configuração inicial, registar o servidor de ficheiros do StorSimple e concluir a configuração do dispositivo. Em seguida, pode aprovisionar partilhas SMB. <br></br> <br></br> Para o seu servidor de iSCSI, execute a configuração inicial, registar o servidor de iSCSI do StorSimple e concluir a configuração do dispositivo. Em seguida, pode aprovisionar volumes de iSCSI. |[Configurar a matriz virtual como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Agora, pode começar a configurar o portal do Azure.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que tem de recolher antes de configurar o software na sua matriz Virtual StorSimple. A preparar essas informações antecipadamente ajuda a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Dependendo se a sua matriz Virtual StorSimple é implementado como um servidor de ficheiros ou um servidor de iSCSI, precisa de um dos seguintes listas de configuração.

* Transfira o [lista de verificação do StorSimple Virtual Array ficheiro Server Configuration](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Transfira o [lista de verificação do servidor de configuração do StorSimple Virtual Array iSCSI](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui, encontrará os pré-requisitos de configuração para o serviço StorSimple Device Manager, a sua matriz Virtual StorSimple e a rede de centro de dados.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple

Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* Subscrição do Microsoft Azure deve estar ativada para o serviço StorSimple Device Manager.

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz Virtual StorSimple

Antes de implementar uma matriz virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2008 R2 ou posterior ou o VMware (ESXi 5.0, 5.5, 6.0 ou 6.5), que pode ser utilizado para um aprovisionar um dispositivo.
* O sistema anfitrião é capaz de dedicar os seguintes recursos para aprovisionar a sua matriz virtual:
  
  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se quiser configurar a matriz virtual como servidor de ficheiros, 8 GB suporta 2 milhões de ficheiros. Terá de 16 GB de RAM para oferecer suporte a 2-4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual 500 GB de dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

* A rede no seu datacenter está configurada de acordo com os requisitos de rede para o dispositivo StorSimple. Para obter mais informações, consulte a [requisitos de sistema da matriz Virtual StorSimple](storsimple-ova-system-requirements.md).
* A sua matriz Virtual StorSimple tem uma largura de banda Mbps Internet dedicado 5 (ou mais) disponível, o tempo todo. Esta largura de banda não deve ser partilhada com outras aplicações.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Utilize as seguintes instruções passo a passo para preparar o seu portal para o serviço StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Uma única instância do serviço StorSimple Device Manager pode gerir várias matrizes virtuais do StorSimple. Execute os seguintes passos para criar uma instância do serviço Gestor de Dispositivos do StorSimple. Se tiver um serviço StorSimple Device Manager existente para gerir as matrizes virtuais, ignore este passo e vá para [passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se tiver ativado a criação automática de uma conta de armazenamento, aceda a [passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço

Assim que o serviço Gestor de Dispositivos do StorSimple estiver ativo e em execução, será necessário obter a chave de registo do serviço. Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço.

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A chave de registo do serviço é utilizada para registar todos os dispositivos do StorSimple Device Manager tem de registar com o seu serviço StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Passo 3: Descarregar imagem da matriz virtual

Depois de ter a chave de registo do serviço, terá de transferir a imagem de matriz virtual adequados para Aprovisionar uma matriz virtual no seu sistema anfitrião. As imagens de matriz virtual são específicas do sistema operativo e podem ser transferidas a partir da página de início rápido no portal do Azure.

> [!IMPORTANT]
> O software em execução no StorSimple Virtual Array só pode ser utilizado com o serviço StorSimple Device Manager.
> 
> 

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Para obter a imagem da matriz virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/). 
2. No portal do Azure, clique em **Procurar > StorSimple Device Managers**.
3. Selecione um serviço StorSimple Device Manager existente. Na **StorSimple Device Manager** painel, clique em **início rápido**. 
4. Clique no link correspondente para a imagem que pretende transferir a partir do Microsoft Download Center. Os ficheiros de imagem têm, aproximadamente, 4,8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e versões posteriores
   * VHD para Hyper-V no Windows Server 2008 R2 e posterior
   * VMDK para VMWare ESXi 5.0, 5.5, 6.0 ou 6.5
5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passo opcional: Configurar uma nova conta do Storage para o serviço

Este passo é opcional e deve ser efetuado apenas se não tiver ativado a criação automática de uma conta de armazenamento com o seu serviço.

Se precisar de criar uma conta de armazenamento do Azure numa região diferente, veja [como criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter instruções passo a passo.

Execute os seguintes passos no [portal do Azure](https://ms.portal.azure.com/) na página do serviço StorSimple Device Manager para adicionar uma conta de armazenamento existente do Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço de Gestor de dispositivos

1. Navegue para o serviço de Gestor de dispositivos, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção.
3. Clique em **Adicionar**.
4. Na **adicionar uma conta de armazenamento** painel, faça o seguinte:
   
   1. Para **subscrição**, selecione **atual**.
   
   2. Forneça o nome da sua conta de armazenamento do Azure.
   
   3. Selecione **ativar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a cloud. Selecione **desativar** apenas se estiver a utilizar uma nuvem privada.
   
   4. Clique em **Adicionar**. Será notificado depois da conta de armazenamento for criada com êxito.<br></br>
   
      ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Passo seguinte

A próxima etapa é aprovisionar uma máquina virtual do StorSimple Virtual Array. Consoante o sistema operativo anfitrião, veja as instruções detalhadas em:

* [Aprovisionar uma matriz Virtual do StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Aprovisionar uma matriz Virtual do StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

