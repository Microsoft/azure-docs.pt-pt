---
title: Faça upload do ficheiro VHD para Azure DevTest Labs usando AzCopy Microsoft Docs
description: Este artigo fornece uma passagem para usar o utilitário da linha de comando AzCopy para carregar um ficheiro VHD para a conta de armazenamento de um laboratório em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650779"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Faça upload do ficheiro VHD para a conta de armazenamento do laboratório usando a AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para alojamento de máquinas virtuais. Os passos seguintes acompanham-no utilizando o utilitário da linha de comando AzCopy para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Uma vez carregado o seu ficheiro VHD, a [secção passos seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução a discos geridos](../virtual-machines/managed-disks-overview.md)

> [!NOTE] 
>  
> A azCopy é um utilitário de linha de comando apenas para Windows.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os passos seguintes acompanham-no através do upload de um ficheiro VHD para a Azure DevTest Labs usando [a AzCopy](https://aka.ms/downloadazcopy). 

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal Azure:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. Na lâmina do laboratório, selecione **Configuração**. 

1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.

1. Na lâmina de **imagens personalizadas,** Selecione **+Add**. 

1. Na lâmina **de imagem personalizada,** selecione **VHD**.

1. Na lâmina **VHD,** **selecione Carreque um VHD utilizando o PowerShell**.

    ![Upload VHD usando PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. O Upload de uma imagem utilizando a lâmina **PowerShell** exibe uma chamada para o **cmdlet Add-AzureVhd.** O primeiro parâmetro *(Destino)* contém o URI para um recipiente blob *(uploads)* no seguinte formato:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Tome nota do URI completo, uma vez que é utilizado em etapas posteriores.

1. Faça o upload do ficheiro VHD utilizando o AzCopy:
 
1. [Descarregue e instale a versão mais recente do AzCopy.](https://aka.ms/downloadazcopy)

1. Abra uma janela de comando e navegue para o diretório de instalação AzCopy. Opcionalmente, pode adicionar a localização de instalação AzCopy ao seu caminho do sistema. Por predefinição, a AzCopy é instalada no seguinte diretório:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Utilizando a chave da conta de armazenamento e o recipiente blob URI, executar o seguinte comando na indicação de comando. O valor *vhdFileName* tem de estar em aspas. O processo de upload de um ficheiro VHD pode ser longo, dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)