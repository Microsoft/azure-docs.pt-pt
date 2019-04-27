---
title: Carregar o ficheiro VHD para o Azure DevTest Labs com o AzCopy | Documentos da Microsoft
description: Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o AzCopy
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634989"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar máquinas virtuais. Os seguintes passos guiá-lo através da utilização do utilitário de linha de comandos do AzCopy para carregar um ficheiro VHD para a conta de armazenamento do laboratório. Depois de carregar o ficheiro VHD, o [próximos passos secção](#next-steps) apresenta uma lista de alguns artigos que mostram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações acerca dos discos e VHDs no Azure, consulte [introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> O AzCopy é um utilitário de linha de comandos do Windows só.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo por meio de carregar um ficheiro VHD para utilizar o Azure DevTest Labs [AzCopy](https://aka.ms/downloadazcopy). 

1. Obter o nome da conta de armazenamento do laboratório com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel do laboratório, selecione **configuração**. 

1. O laboratório **Configuration** painel, selecione **imagens personalizadas (VHDs)**.

1. Sobre o **imagens personalizadas** painel, selecione **+ adicionar**. 

1. Sobre o **Custom image** painel, selecione **VHD**.

1. Sobre o **VHD** painel, selecione **carregar um VHD com o PowerShell**.

    ![Carregar o VHD com o PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. O **carregue uma imagem com o PowerShell** painel apresenta uma chamada para o **Add-AzureVhd** cmdlet. O primeiro parâmetro (*destino*) contém o URI para um contentor de BLOBs (*carrega*) no seguinte formato:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anote o URI completo que é utilizada em passos posteriores.

1. Carregar o ficheiro VHD com o AzCopy:
 
1. [Transfira e instale a versão mais recente do AzCopy](https://aka.ms/downloadazcopy).

1. Abra uma janela de comando e navegue para o diretório de instalação do AzCopy. Opcionalmente, pode adicionar a localização de instalação do AzCopy ao caminho do sistema. Por predefinição, o AzCopy estiver instalado no diretório seguinte:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Utilizar a conta BLOBs e a chave de contentor de armazenamento de URI, execute o seguinte comando no prompt de comando. O *vhdFileName* valor tem de ser aspas. O processo de carregamento de um ficheiro VHD pode ser demorado, dependendo do tamanho do ficheiro VHD e sua velocidade da ligação.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)