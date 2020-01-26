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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759760"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Upload vHD ficheiro para a conta de armazenamento do laboratório usando PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. Os seguintes passos passam por usar o PowerShell para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de carregar o arquivo VHD, a [seção próximas etapas](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do arquivo VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [introdução aos Managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por enviar um ficheiro VHD para o Azure DevTest Labs utilizando o PowerShell. 

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. Na folha do laboratório, selecione **configuração**. 

1. Na folha **configuração** do laboratório, selecione **imagens personalizadas (VHDs)** .

1. Na folha **imagens personalizadas** , selecione **+ Adicionar**. 

1. Na folha **imagem personalizada** , selecione **VHD**.

1. Na folha **VHD** , selecione **carregar um VHD usando o PowerShell**.

    ![Carregar VHD usando o PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. No Upload de uma imagem utilizando a lâmina **PowerShell,** copie o script PowerShell gerado para um editor de texto.

1. Modifique o parâmetro **LocalFilePath** do cmdlet **Add-AzureVhd** para apontar para a localização do ficheiro VHD que pretende carregar.

1. Num pedido powerShell, execute o cmdlet **Add-AzureVhd** (com o parâmetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> O processo de carregar um arquivo VHD pode ser demorado dependendo do tamanho do arquivo VHD e da velocidade de conexão.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
