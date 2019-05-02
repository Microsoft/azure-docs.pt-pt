---
title: Criar uma imagem de VM do utilizador para o Azure Marketplace
description: Lista os passos e as referências necessárias para criar uma imagem VM do utilizador.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938319"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem de VM do utilizador

Este artigo explica os dois passos gerais necessários para criar uma imagem não gerida a partir de um VHD generalizado.  Referências são fornecidas para orientá-lo em cada etapa: capturar a imagem e generalizar a imagem.


## <a name="capture-the-vm-image"></a>Capturar a imagem VM

Utilize as instruções no artigo sobre como capturar a VM que corresponde à sua abordagem de acesso:

-  PowerShell: [Como criar uma imagem VM não gerida a partir de uma VM do Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI do Azure: [Como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Máquinas virtuais - captura](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalize a imagem VM

Uma vez que tê gerou a imagem do utilizador a partir de um VHD generalizado anteriormente, também deve ser generalizado.  Mais uma vez, selecione o seguinte artigo que corresponde ao seu mecanismo de acesso.  (Pode ter já generalizada seu disco quando capturou isso.)

-  PowerShell: [Generalizar a VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI do Azure: [Passo 2: Criar imagem de VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Máquinas virtuais - generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Passos Seguintes

Em seguida, irá [criar um certificado](cpp-create-key-vault-cert.md) e o armazenamos num novo cofre de chaves do Azure.  Este certificado é necessário para estabelecer uma ligação segura de WinRM para a VM.
