---
title: Upload VHD ficheiro para Azure DevTest Labs usando PowerShell / Microsoft Docs
description: Este artigo fornece um walkthrough que mostra como enviar um ficheiro VHD para o Azure DevTest Labs usando o PowerShell.
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
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759760"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Upload vHD ficheiro para a conta de armazenamento do laboratório usando PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para fornecer máquinas virtuais. Os seguintes passos passam por usar o PowerShell para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de ter carregado o ficheiro VHD, a [secção Depassos Seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por enviar um ficheiro VHD para o Azure DevTest Labs utilizando o PowerShell. 

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. Na lâmina do laboratório, **selecione Configuração**. 

1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.

1. Na lâmina de **imagens Personalizadas,** Selecione **+Adicionar**. 

1. Na lâmina de **imagem Personalizada,** selecione **VHD**.

1. Na lâmina **VHD,** **selecione Carregar um VHD utilizando powerShell**.

    ![Upload VHD usando PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. No Upload de uma imagem utilizando a lâmina **PowerShell,** copie o script PowerShell gerado para um editor de texto.

1. Modifique o parâmetro **LocalFilePath** do cmdlet **Add-AzureVhd** para apontar para a localização do ficheiro VHD que pretende carregar.

1. Num pedido powerShell, execute o cmdlet **Add-AzureVhd** (com o parâmetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> O processo de upload de um ficheiro VHD pode ser longo dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação.

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando powerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
