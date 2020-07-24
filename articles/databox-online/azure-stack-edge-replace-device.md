---
title: Substitua o seu dispositivo Azure Stack Edge / Microsoft Docs
description: Descreve como obter um dispositivo Azure Stack Edge de substituição.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098749"
---
# <a name="replace-your-azure-stack-edge-device"></a>Substitua o seu dispositivo Azure Stack Edge

Este artigo descreve como obter um dispositivo Azure Stack Edge de substituição. Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. 


Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Abra um bilhete de suporte para problemas de hardware
> * Criar um novo recurso para dispositivo de substituição no portal Azure
> * Instale, ative o dispositivo de substituição
> * Devolva o dispositivo original

## <a name="open-a-support-ticket"></a>Abrir um bilhete de apoio

Se o seu dispositivo existente tiver uma falha de hardware, abra um bilhete de Suporte. O Microsoft Support determinará que uma Unidade de Substituição de Campo (FRU) não está disponível para este caso ou que o dispositivo necessita de uma atualização de hardware. Em qualquer dos casos, o Suporte encomendará um dispositivo de substituição.

1. Abra um bilhete de suporte com o Microsoft Support indicando que deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware Azure Stack Edge**.

    ![Abrir Pedido de suporte](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Um engenheiro de suporte da Microsoft entrará em contacto consigo. Forneça os detalhes do envio.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Criar um recurso para dispositivo de substituição

Siga estes passos para criar um recurso.

1. Siga os passos na [Criar um novo recurso](azure-stack-edge-deploy-prep.md#create-a-new-resource) para criar um recurso para o dispositivo de substituição. 

2. Certifique-se de selecionar a caixa de verificação contra **eu tenho um dispositivo Azure Stack Edge**. 

    ![Recurso para dispositivo de substituição](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Instale e ative o dispositivo de substituição

Siga estes passos para instalar e ativar o dispositivo de substituição:

1. [Instale o seu dispositivo](azure-stack-edge-deploy-install.md).

2. [Ative o seu dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) contra o novo recurso que criou anteriormente.

## <a name="return-your-existing-device"></a>Devolva o dispositivo existente

Siga todos os passos para devolver o dispositivo original:

1. [Apoui os dados do dispositivo.](azure-stack-edge-return-device.md#erase-data-from-the-device)
2. [Iniciar a revolução](azure-stack-edge-return-device.md#initiate-device-return) do dispositivo para o dispositivo original.
3. [Agende uma recolha.](azure-stack-edge-return-device.md#schedule-a-pickup)
4. [Elimine o recurso](azure-stack-edge-return-device.md#delete-the-resource) associado ao dispositivo devolvido.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [devolver um dispositivo Azure Stack Edge](azure-stack-edge-return-device.md).
