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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757427"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Upload vHD ficheiro para a conta de armazenamento do laboratório usando AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para fornecer máquinas virtuais. Os seguintes passos passam por você usando o utilitário da linha de comando AzCopy para carregar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de ter carregado o ficheiro VHD, a [secção Depassos Seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> O AzCopy é um utilitário de linha de comando apenas para windows.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por você através do upload de um ficheiro VHD para o Azure DevTest Labs usando [o AzCopy](https://aka.ms/downloadazcopy). 

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal Azure:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. Na lâmina do laboratório, **selecione Configuração**. 

1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.

1. Na lâmina de **imagens Personalizadas,** Selecione **+Adicionar**. 

1. Na lâmina de **imagem Personalizada,** selecione **VHD**.

1. Na lâmina **VHD,** **selecione Carregar um VHD utilizando powerShell**.

    ![Upload VHD usando PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. O Upload de uma imagem utilizando a lâmina **PowerShell** exibe uma chamada para o cmdlet **Add-AzureVhd.** O primeiro parâmetro *(Destino)* contém o URI para um recipiente de bolhas *(uploads)* no seguinte formato:

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

1. Utilizando a chave da conta de armazenamento e o recipiente de blob URI, execute o seguinte comando no pedido de comando. O valor *vhdFileName* tem de estar em cotações. O processo de upload de um ficheiro VHD pode ser longo dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando powerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)