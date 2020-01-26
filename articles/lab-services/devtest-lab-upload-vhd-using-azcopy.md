---
title: Upload VHD ficheiro para Azure DevTest Labs usando AzCopy Microsoft Docs
description: Este artigo fornece uma passagem para usar o utilitário da linha de comando AzCopy para carregar um ficheiro VHD para uma conta de armazenamento de um laboratório em Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757427"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Upload vHD ficheiro para a conta de armazenamento do laboratório usando AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. Os seguintes passos passam por você usando o utilitário da linha de comando AzCopy para carregar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de carregar o arquivo VHD, a [seção próximas etapas](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do arquivo VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [introdução aos Managed disks](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> O AzCopy é um utilitário de linha de comando apenas para windows.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por você através do upload de um ficheiro VHD para o Azure DevTest Labs usando [o AzCopy](https://aka.ms/downloadazcopy). 

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal do Azure:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. Na folha do laboratório, selecione **configuração**. 

1. Na folha **configuração** do laboratório, selecione **imagens personalizadas (VHDs)** .

1. Na folha **imagens personalizadas** , selecione **+ Adicionar**. 

1. Na folha **imagem personalizada** , selecione **VHD**.

1. Na folha **VHD** , selecione **carregar um VHD usando o PowerShell**.

    ![Carregar VHD usando o PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A folha **carregar uma imagem usando o PowerShell** exibe uma chamada para o cmdlet **Add-AzureVhd** . O primeiro parâmetro *(Destino)* contém o URI para um recipiente de bolhas *(uploads)* no seguinte formato:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Tome nota do URI completo como é utilizado em etapas posteriores.

1. Faça upload do ficheiro VHD utilizando o AzCopy:
 
1. [Descarregue e instale a versão mais recente do AzCopy.](https://aka.ms/downloadazcopy)

1. Abra uma janela de comando e navegue para o diretório de instalação DazCopy. Opcionalmente, pode adicionar a localização de instalação AzCopy ao seu caminho do sistema. Por predefinição, a AzCopy está instalada no seguinte diretório:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Utilizando a chave da conta de armazenamento e o recipiente de blob URI, execute o seguinte comando no pedido de comando. O valor *vhdFileName* tem de estar em cotações. O processo de carregar um arquivo VHD pode ser demorado dependendo do tamanho do arquivo VHD e da velocidade de conexão.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)