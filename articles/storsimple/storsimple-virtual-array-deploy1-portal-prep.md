---
title: Preparação do portal para StorSimple Virtual Array
description: Primeiro tutorial para implantar matriz virtual StorSimple envolve a preparação do portal Azure
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254537"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Implementar Matriz Virtual StorSimple - Prepare o portal Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este é o primeiro artigo na série de tutoriais de implementação necessários para implementar completamente a sua matriz virtual como servidor de ficheiros ou servidor iSCSI utilizando o modelo De Gestor de Recursos. Este artigo descreve a preparação necessária para criar e configurar o seu serviço StorSimple Device Manager antes de fornecer uma matriz virtual. Este artigo também se liga a uma lista de verificação de configuração de implementação e pré-requisitos de configuração.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que reveja a lista de verificação de configuração de implementação antes de começar. A preparação do portal demora menos de 10 minutos.

As informações publicadas neste artigo aplicam-se à implementação de Matrizes Virtuais StorSimple no portal Azure e Microsoft Azure Government Cloud.

### <a name="get-started"></a>Introdução
O fluxo de trabalho de implantação consiste em preparar o portal, fornecer uma matriz virtual no seu ambiente virtualizado e completar a configuração. Para começar com a implementação do StorSimple Virtual Array como um servidor de ficheiros ou um servidor iSCSI, precisa de se referir aos seguintes recursos tabulados.

#### <a name="deployment-articles"></a>Artigos de implantação

Para implementar o seu StorSimple Virtual Array, consulte os seguintes artigos na sequência prescrita.

| **#** | **Neste passo** | **Faça isto...** | **E use estes documentos.** |
| --- | --- | --- | --- |
| 1. |**Criar o portal Azure** |Crie e configure o seu serviço StorSimple Device Manager antes de fornecer um StorSimple Virtual Array. |[Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Provision a Matriz Virtual** |Para hyper-V, provisão e ligação a um StorSimple Virtual Array num sistema de anfitriões que executa Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para VMware, foretae e ligue-se a um StorSimple Virtual Array num sistema de anfitriões que executa VMware ESXi 5.0, 5.5, 6.0 ou 6.5.<br></br> |[Fornecer uma matriz virtual em Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Fornecer uma matriz virtual em VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurar o Matriz Virtual** |Para o seu servidor de ficheiros, execute a configuração inicial, registe o servidor de ficheiros StorSimple e complete a configuração do dispositivo. Em seguida, pode aprovisionar partilhas SMB. <br></br> <br></br> Para o seu servidor iSCSI, execute a configuração inicial, registe o seu servidor StorSimple iSCSI e complete a configuração do dispositivo. Em seguida, pode fornecer volumes iSCSI. |[Configurar a matriz virtual como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Agora, pode começar a configurar o portal do Azure.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que precisa de recolher antes de configurar o software no seu StorSimple Virtual Array. Preparar esta informação com antecedência ajuda a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Dependendo se o seu StorSimple Virtual Array é implementado como um servidor de ficheiros ou um servidor iSCSI, precisa de uma das seguintes listas de verificação.

* Descarregue a lista de verificação de configuração do servidor de [ficheiros de ficheiros de ficheiros virtual StorSimple](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Descarregue a lista de verificação de configuração do [servidor StorSimple Virtual Array iSCSI](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui encontra os pré-requisitos de configuração para o seu serviço StorSimple Device Manager, o seu StorSimple Virtual Array e a rede datacenter.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple

Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* A subscrição do Microsoft Azure deve ser ativada para o serviço StorSimple Device Manager.

### <a name="for-the-storsimple-virtual-array"></a>Para o Matriz Virtual StorSimple

Antes de implementar uma matriz virtual, certifique-se de que:

* Tem acesso a um sistema de anfitriões que executa hiper-V no Windows Server 2008 R2 ou posteriormente ou VMware (ESXi 5.0, 5.5, 6.0 ou 6.5) que pode ser usado para fornecer um dispositivo.
* O sistema de acolhimento é capaz de dedicar os seguintes recursos para fornecer a sua matriz virtual:
  
  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar a matriz virtual como servidor de ficheiros, 8 GB suporta 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

* A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo StorSimple. Para mais informações, consulte os requisitos do sistema de [matriz virtual StorSimple](storsimple-ova-system-requirements.md).
* O Seu StorSimple Virtual Array tem sempre uma largura de banda de 5 Mbps de internet (ou mais) disponível. Esta largura de banda não deve ser partilhada com outras aplicações.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Utilize as seguintes instruções passo a passo para preparar o seu portal para o serviço StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Uma única instância do serviço StorSimple Device Manager pode gerir várias Matrizes Virtuais StorSimple. Execute os seguintes passos para criar uma instância do serviço Gestor de Dispositivos do StorSimple. Se tiver um serviço storSimple Device Manager existente para gerir as suas matrizes virtuais, ignore este passo e vá para [o Passo 2: Obtenha a chave](#step-2-get-the-service-registration-key)de registo de serviço .

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
> A chave de registo de serviço é utilizada para registar todos os dispositivos StorSimple Device Manager que precisam de se registar com o seu serviço StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Passo 3: Descarregue a imagem de matriz virtual

Depois de ter a chave de registo de serviço, terá de descarregar a imagem de matriz virtual apropriada para fornecer uma matriz virtual no seu sistema de hospedagem. As imagens de matriz virtual são específicas do sistema operativo e podem ser descarregadas a partir da página Quick Start no portal Azure.

> [!IMPORTANT]
> O software em execução no StorSimple Virtual Array só pode ser utilizado com o serviço StorSimple Device Manager.
> 
> 

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Para obter a imagem de matriz virtual

1. Assine no [portal Azure.](https://portal.azure.com/) 
2. No portal Azure, clique em Navegar > Gestores de **Dispositivos StorSimple**.
3. Selecione um serviço storSimple Device Manager existente. Na lâmina **StorSimple Device Manager,** clique em **Quick Start**. 
4. Clique no link correspondente à imagem que pretende descarregar a partir do Microsoft Download Center. Os ficheiros de imagem têm, aproximadamente, 4,8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e mais tarde
   * VHD para Hyper-V no Windows Server 2008 R2 e mais tarde
   * VMDK para VMWare ESXi 5.0, 5.5, 6.0 ou 6.5
5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passo opcional: Configure uma nova conta de armazenamento para o serviço

Este passo é opcional e só deve ser realizado se não permitir a criação automática de uma conta de armazenamento com o seu serviço.

Se precisar de criar uma conta de armazenamento Azure numa região diferente, consulte [como criar uma conta](../storage/common/storage-account-create.md) de armazenamento para instruções passo a passo.

Execute os seguintes passos no [portal Azure](https://ms.portal.azure.com/) na página de serviço StorSimple Device Manager para adicionar uma conta de armazenamento Microsoft Azure existente.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tem a mesma subscrição Azure que o serviço Gestor de Dispositivos

1. Navegue para o seu serviço De Gestor de Dispositivos, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.**
3. Clique em **Adicionar**.
4. Na lâmina adicionar uma conta de **armazenamento,** faça o seguinte:
   
   1. Para **subscrição,** selecione **Current**.
   
   2. Forneça o nome da sua conta de armazenamento Azure.
   
   3. Selecione **Ativar** para criar um canal seguro para a comunicação de rede entre o seu dispositivo StorSimple e a nuvem. Selecione **Desativar** apenas se estiver a operar dentro de uma nuvem privada.
   
   4. Clique em **Adicionar**. É notificado após a criação da conta de armazenamento com sucesso.<br></br>
   
      ![Adicione uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Passo seguinte

O próximo passo é fornecer uma máquina virtual para o seu StorSimple Virtual Array. Consoante o sistema operativo anfitrião, veja as instruções detalhadas em:

* [Provisão de um Matriz Virtual StorSimple em Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Provision a StorSimple Virtual Array em VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

