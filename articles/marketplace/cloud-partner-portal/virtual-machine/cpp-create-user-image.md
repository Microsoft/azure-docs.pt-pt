---
title: Criar uma imagem de VM do utilizador para o Azure Marketplace | Documentos da Microsoft
description: Lista os passos e as referências necessárias para criar uma imagem VM do utilizador.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744113"
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
