---
title: Tutorial para ligar ao Azure Stack Edge Mini R no portal Azure
description: Saiba como se pode ligar ao seu dispositivo Azure Stack Edge Mini R utilizando a UI web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061609"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Tutorial: Conecte-se ao Azure Stack Edge Mini R

Este tutorial descreve como pode ligar-se ao seu dispositivo Azure Stack Edge Mini R utilizando a UI web local.

A conclusão do processo de ligação pode demorar cerca de 5 minutos.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar-se a um dispositivo físico



## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conecte-se à configuração local do UI web

1. Configure o adaptador Ethernet no seu computador para ligar ao dispositivo Azure Stack Edge Pro com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Ligue o computador à PORTA 1 no dispositivo. Se ligar o computador ao dispositivo diretamente (sem um comutador), utilize um cabo cruzado ou um adaptador Ethernet USB. Utilize a seguinte ilustração para identificar a PORTA 1 no dispositivo.

    ![Cablagem para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar-se a um dispositivo físico


Para aprender a configurar as definições de rede no seu dispositivo, consulte:

> [!div class="nextstepaction"]
> [Rede de configuração](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
