---
title: Criar uma imagem VM do utilizador para o Mercado Azure
description: Lista os passos e referências necessários para criar uma imagem VM do utilizador.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278012"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem de VM do utilizador

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
