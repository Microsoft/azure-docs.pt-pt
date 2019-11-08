---
title: Criar uma imagem de VM de usuário para o Azure Marketplace
description: Lista as etapas e referências necessárias para criar uma imagem de VM de usuário.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818704"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem de VM do utilizador

Este artigo explica as duas etapas gerais necessárias para criar uma imagem não gerenciada de um VHD generalizado.  As referências são fornecidas para orientá-lo durante cada etapa: capturar a imagem e generalizar a imagem.


## <a name="capture-the-vm-image"></a>Capturar a imagem da VM

Use as instruções no seguinte artigo sobre como capturar a VM que corresponde à sua abordagem de acesso:

-  PowerShell: [como criar uma imagem de VM não gerenciada de uma VM do Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI do Azure: [como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [máquinas virtuais – captura](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalizar a imagem da VM

Como você gerou a imagem de usuário de um VHD anteriormente generalizado, ele também deve ser generalizado.  Novamente, selecione o seguinte artigo que corresponde ao seu mecanismo de acesso.  (Talvez você já tenha generalizado seu disco quando o capturou.)

-  PowerShell: [generalizar a VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI do Azure: [etapa 2: criar imagem de VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [máquinas virtuais-generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Passos seguintes

Em seguida, você [criará um certificado](cpp-create-key-vault-cert.md) e o armazenará em um novo Azure Key Vault.  Esse certificado é necessário para estabelecer uma conexão segura do WinRM com a VM.
