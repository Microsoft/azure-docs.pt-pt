---
title: Substitua o seu dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Descreve como obter um dispositivo Azure Stack Edge Pro de substituição.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726582"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Substitua o seu dispositivo Azure Stack Edge Pro

Este artigo descreve como substituir o seu dispositivo Azure Stack Edge Pro. Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. 


Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Abra um bilhete de suporte para problemas de hardware
> * Criar uma nova encomenda para um dispositivo de substituição no portal Azure
> * Instale, ative o dispositivo de substituição
> * Devolva o dispositivo original

## <a name="open-a-support-ticket"></a>Abrir um bilhete de apoio

Se o seu dispositivo existente tiver uma falha de hardware, abra um bilhete de Suporte seguindo estes passos:

1. Abra um bilhete de suporte com o Microsoft Support indicando que deseja devolver o dispositivo. Selecione o tipo de problema **de hardware Azure Stack Edge Pro** e escolha o subtipo de **problemas de hardware.**  

    ![Abrir Pedido de suporte](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Um engenheiro de Suporte da Microsoft entrará em contacto consigo para determinar se uma Unidade de Substituição de Campo (FRU) pode corrigir o problema e está disponível para este caso. Se um FRU não estiver disponível ou o dispositivo precisar de uma atualização de hardware, o Support irá guiá-lo para fazer uma nova encomenda e devolver o seu dispositivo antigo.

## <a name="create-a-new-order"></a>Criar uma nova ordem

Crie um novo recurso para a ativação do seu dispositivo de substituição seguindo os passos na [Criação de um novo recurso.](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)

> [!NOTE]
> A ativação de um dispositivo de substituição contra um recurso existente não é suportada. O novo recurso é considerado uma nova ordem. Começará a ser cobrado 14 dias após o envio do dispositivo para si.

## <a name="install-and-activate-the-replacement-device"></a>Instale e ative o dispositivo de substituição

Siga estes passos para instalar e ativar o dispositivo de substituição:

1. [Instale o seu dispositivo](azure-stack-edge-deploy-install.md).
2. [Ative o seu dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) contra o novo recurso que criou anteriormente.

## <a name="return-your-existing-device"></a>Devolva o dispositivo existente

Siga todos os passos para devolver o dispositivo original:

1. [Apoui os dados do dispositivo.](azure-stack-edge-return-device.md#erase-data-from-the-device)
2. [Iniciar a revolução](azure-stack-edge-return-device.md#initiate-device-return) do dispositivo para o dispositivo original.
3. [Agende uma recolha.](azure-stack-edge-return-device.md#schedule-a-pickup)
4. Uma vez que o dispositivo seja recebido na Microsoft, pode [eliminar o recurso](azure-stack-edge-return-device.md#delete-the-resource) associado ao dispositivo devolvido.


## <a name="next-steps"></a>Próximos passos

- Saiba como [devolver um dispositivo Azure Stack Edge Pro](azure-stack-edge-return-device.md).
