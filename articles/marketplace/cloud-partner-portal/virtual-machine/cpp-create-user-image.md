---
title: Criar uma imagem VM do utilizador para o Mercado Azure
description: Lista os passos e referências necessários para criar uma imagem VM do utilizador.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273958"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem de VM do utilizador

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da sua Máquina Virtual Azure ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create your Azure Virtual Machine ativos técnicos](https://aka.ms/AzureVMTechAsset) para gerir as suas ofertas migradas.

Este artigo explica os dois passos gerais necessários para criar uma imagem não gerida a partir de um VHD generalizado.  São fornecidas referências para guiá-lo através de cada passo: capturar a imagem e generalizar a imagem.


## <a name="capture-the-vm-image"></a>Capture a imagem VM

Utilize as instruções no seguinte artigo sobre a captura do VM que corresponde à sua abordagem de acesso:

-  PowerShell: [Como criar uma imagem VM não gerida a partir de um VM Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Máquinas Virtuais - Captura](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalize a imagem VM

Como gerou a imagem do utilizador a partir de um VHD previamente generalizado, também deve ser generalizado.  Mais uma vez, selecione o seguinte artigo que corresponda ao seu mecanismo de acesso.  (Pode já ter generalizado o seu disco quando o capturou.)

-  PowerShell: [Generalize o VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Passo 2: Criar imagem VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Máquinas Virtuais - Generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Passos seguintes

Em seguida, [você vai criar um certificado](cpp-create-key-vault-cert.md) e armazená-lo em um novo Cofre chave Azure.  Este certificado é necessário para estabelecer uma ligação WinRM segura ao VM.
