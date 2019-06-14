---
title: Carregar o ficheiro VHD para o Azure DevTest Labs com o PowerShell | Documentos da Microsoft
description: Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell
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
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622784"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar máquinas virtuais. Os seguintes passos guiá-lo através da utilização do PowerShell para carregar um ficheiro VHD para a conta de armazenamento do laboratório. Depois de carregar o ficheiro VHD, o [próximos passos secção](#next-steps) apresenta uma lista de alguns artigos que mostram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações acerca dos discos e VHDs no Azure, consulte [introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo por meio de carregar um ficheiro VHD para o Azure DevTest Labs com o PowerShell. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel do laboratório, selecione **configuração**. 

1. O laboratório **Configuration** painel, selecione **imagens personalizadas (VHDs)** .

1. Sobre o **imagens personalizadas** painel, selecione **+ adicionar**. 

1. Sobre o **Custom image** painel, selecione **VHD**.

1. Sobre o **VHD** painel, selecione **carregar um VHD com o PowerShell**.

    ![Carregar o VHD com o PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Sobre o **carregue uma imagem com o PowerShell** painel, copie o script do PowerShell gerado para um editor de texto.

1. Modificar a **LocalFilePath** parâmetro do **Add-AzureVhd** cmdlet para apontar para a localização do ficheiro VHD que pretende carregar.

1. Na linha de comandos do PowerShell, execute o **Add-AzureVhd** cmdlet (com o modificado **LocalFilePath** parâmetro).

> [!WARNING] 
> 
> O processo de carregamento de um ficheiro VHD pode ser demorado, dependendo do tamanho do ficheiro VHD e sua velocidade da ligação.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
