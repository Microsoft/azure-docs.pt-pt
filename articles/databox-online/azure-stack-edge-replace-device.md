---
title: Substitua o seu dispositivo Azure Stack Edge Pro / Microsoft Docs
description: Descreve como obter um dispositivo Azure Stack Edge Pro de substituição.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 501136c6f5015dc8c68a1aba004432900675b11c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017974"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Substitua o seu dispositivo Azure Stack Edge Pro

Este artigo descreve como obter um dispositivo Azure Stack Edge Pro de substituição. Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. 


Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Abra um bilhete de suporte para problemas de hardware
> * Criar um novo recurso para dispositivo de substituição no portal Azure
> * Instale, ative o dispositivo de substituição
> * Devolva o dispositivo original

## <a name="open-a-support-ticket"></a>Abrir um bilhete de apoio

Se o seu dispositivo existente tiver uma falha de hardware, abra um bilhete de Suporte. O Suporte da Microsoft determinará se uma FRU (Unidade Substituível em Campo) não está disponível para esta instância ou se o dispositivo precisa de uma atualização de hardware. Em qualquer dos casos, o Suporte encomendará um dispositivo de substituição.

1. Abra um bilhete de suporte com o Microsoft Support indicando que deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware Azure Stack Edge Pro**.

    ![Abrir Pedido de suporte](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Um engenheiro de suporte da Microsoft entrará em contacto consigo. Forneça os detalhes do envio.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Criar um recurso para dispositivo de substituição

> [!NOTE]
> Crie um novo recurso para a ativação do seu dispositivo de substituição. A ativação do dispositivo de substituição contra um recurso existente não é suportada.

Siga estes passos para criar um recurso.

1. Siga os passos na [Criar um novo recurso](azure-stack-edge-deploy-prep.md#create-a-new-resource) para criar um recurso para o dispositivo de substituição. 

2. Certifique-se de selecionar a caixa de verificação contra **eu tenho um dispositivo Azure Stack Edge Pro**. 

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


## <a name="next-steps"></a>Próximos passos

- Saiba como [devolver um dispositivo Azure Stack Edge Pro](azure-stack-edge-return-device.md).
