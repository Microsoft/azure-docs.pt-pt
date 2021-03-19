---
title: Faça upload do ficheiro VHD para Azure DevTest Labs usando | PowerShell Microsoft Docs
description: Este artigo fornece uma passagem que mostra como carregar um ficheiro VHD para Azure DevTest Labs usando PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88650762"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Faça upload do ficheiro VHD para a conta de armazenamento do laboratório usando o PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para alojamento de máquinas virtuais. Os passos seguintes acompanham-no usando o PowerShell para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Uma vez carregado o seu ficheiro VHD, a [secção passos seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução a discos geridos](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os passos seguintes acompanham-no através do upload de um ficheiro VHD para a Azure DevTest Labs usando o PowerShell. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. Na lâmina do laboratório, selecione **Configuração**. 

1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.

1. Na lâmina de **imagens personalizadas,** Selecione **+Add**. 

1. Na lâmina **de imagem personalizada,** selecione **VHD**.

1. Na lâmina **VHD,** **selecione Carreque um VHD utilizando o PowerShell**.

    ![Upload VHD usando PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. No Upload de uma imagem utilizando a lâmina **PowerShell,** copie o script PowerShell gerado para um editor de texto.

1. Modifique o parâmetro **LocalFilePath** do **cmdlet Add-AzureVhd** para indicar a localização do ficheiro VHD que pretende carregar.

1. Num pedido powerShell, executar o **cmdlet Add-AzureVhd** (com o parâmetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> O processo de upload de um ficheiro VHD pode ser longo, dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação.

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
