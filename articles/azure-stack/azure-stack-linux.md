---
title: Adicionar imagens do Linux no Azure Stack
description: Saiba como adicionar imagens do Linux no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: af95c07b43682322693aed18b6bcf58ca70291d1
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242790"
---
# <a name="add-linux-images-to-azure-stack"></a>Adicionar imagens do Linux no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode implementar máquinas virtuais do Linux (VMs) no Azure Stack ao adicionar uma imagem baseada em Linux no Azure Marketplace, pilha. A maneira mais fácil para adicionar uma imagem do Linux no Azure Stack é através da gestão do Marketplace. Estas imagens tem sido preparadas e testadas quanto à compatibilidade com o Azure Stack.

## <a name="marketplace-management"></a>Gestão do Marketplace

Para transferir imagens do Linux no Azure Marketplace, utilize os procedimentos a [transferir itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md) artigo. Selecione as imagens do Linux que deseja oferecer aos utilizadores no seu Azure Stack. 

Tenha em atenção que existem atualizações freqüentes para estas imagens, por isso, verifique a gestão de Marketplace, muitas vezes, para manter atualizados.

## <a name="prepare-your-own-image"></a>Preparar a sua própria imagem

Sempre que possível, transfira as imagens disponíveis por meio do gerenciamento de mercado que tenham sido preparado e testado para o Azure Stack.

O agente Linux do Azure (normalmente denominados `WALinuxAgent` ou `walinuxagent`) é necessário, e nem todas as versões do agente irão funcionar no Azure Stack. Deve usar a versão mais recente do WALA ou versão 2.2.20 se criar sua própria imagem. Tenha em atenção que as versões entre 2.2.20 e 2.2.36 não funcionam no Azure Stack. Tenha em atenção que [cloud-init](https://cloud-init.io/) , neste momento, não é suportado no Azure Stack.

Pode se preparar a sua própria imagem de Linux utilizar as instruções seguintes:

* [Distribuições baseada em centOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Adicionar a sua imagem no Marketplace

Siga [adicionar a imagem no Marketplace](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro estiver definido como `Linux`.

Depois de adicionar a imagem no Marketplace, é criado um item do Marketplace e os utilizadores podem implementar uma máquina virtual Linux.

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes para obter mais informações:

- [Transferir itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Descrição geral do Azure Marketplace de pilha](azure-stack-marketplace.md)
