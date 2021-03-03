---
title: Reinicie e reative o dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Aprenda a limpar os dados e, em seguida, reative o seu dispositivo Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746899"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Reinicie e reative o seu dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como reiniciar, reconfigurar e reativar um dispositivo Azure Stack Edge Pro se tiver problemas com o dispositivo ou precisar de começar de novo por outra razão.

Depois de reiniciar o dispositivo para remover os dados, terá de reativar o dispositivo como um novo recurso. A reposição de um dispositivo remove a configuração do dispositivo, pelo que terá de reconfigurar o dispositivo através da UI web local.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Limpe os dados dos discos de dados do dispositivo
> * Reativar o dispositivo criando uma nova ordem, reconfigurando o dispositivo e ativando-o

## <a name="reset-data-from-the-device"></a>Redefinir dados do dispositivo

Para eliminar os dados dos discos de dados do seu dispositivo, é necessário reiniciar o seu dispositivo. 

Antes de reiniciar, crie uma cópia dos dados locais do dispositivo, se necessário. Pode copiar os dados do dispositivo para um recipiente de armazenamento Azure.

>[!IMPORTANT]
> A reposição do seu dispositivo irá apagar todos os dados e cargas de trabalho locais do seu dispositivo, e isso não pode ser revertido. Reinicie o dispositivo apenas se pretender recomeçar com o dispositivo.

Pode redefinir o seu dispositivo na UI web local ou no PowerShell. Para obter instruções powerShell, consulte [reiniciar o seu dispositivo](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[! INCLUIR] [Redefinir dados do dispositivo](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Dispositivo de reativo

Depois de reiniciar o dispositivo, terá de reativar o dispositivo como um novo recurso. Depois de fazer uma nova encomenda, terá de reconfigurar e depois reativar o novo recurso.

Para reativar o dispositivo existente, siga estes passos:

1. Criar uma nova encomenda para o dispositivo existente seguindo os passos na [Criação de um novo recurso](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource). No **separador endereço de envio,** selecione **Já tenho um dispositivo.**

   ![Não especifique nenhum novo dispositivo no endereço de envio](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Obtenha a chave de ativação.](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key)

1. [Ligue-se ao dispositivo](azure-stack-edge-gpu-deploy-connect.md).

1. [Configure a rede para o dispositivo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Configurar as definições do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Certificados de configuração](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Ativar o dispositivo](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [ligar-se a um dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md).
