---
title: Tutorial para ligar, configurar, ativar o dispositivo Azure Stack Edge com GPU no portal Azure / Microsoft Docs
description: Tutorial para implementar Azure Stack Edge GPU instrui-o a ligar, configurar e ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b500a7d93d19bcc0cd9c23d64ad079f0abfd353
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421361"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Tutorial: Conecte-se ao Azure Stack Edge com GPU

Este tutorial descreve como pode ligar-se ao seu dispositivo Azure Stack Edge com uma GPU a bordo, utilizando a UI web local.

O processo de ligação pode demorar cerca de 5 minutos a ser concluído.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar-se a um dispositivo físico


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge com GPU, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conecte-se à configuração local do UI web

1. Configure o adaptador Ethernet no seu computador para ligar ao dispositivo Azure Stack Edge com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Ligue o computador ao PORTO 1 no seu dispositivo. Se ligar o computador diretamente ao dispositivo (sem um interruptor), utilize um cabo de cruzamento ou um adaptador USB Ethernet. Utilize a seguinte ilustração para identificar a PORTA 1 no seu dispositivo.

    ![Painel posterior de um dispositivo ligado por cabo](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    O backplane do dispositivo pode parecer ligeiramente diferente dependendo do modelo exato que recebeu. Para obter mais informações, consulte [o cabo do seu dispositivo.](azure-stack-edge-gpu-deploy-install.md#cable-the-device)


3. Abra uma janela do navegador e aceda à UI web local do dispositivo em `https://192.168.100.10` .  
    Esta ação pode demorar alguns minutos depois de ter ligado o dispositivo.

    Você vê um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro do certificado de segurança do site](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **Selecione Continue a esta página web**.  
    Estes passos podem variar dependendo do navegador que você está usando.

5. Inscreva-se na UI web do seu dispositivo. A palavra-passe predefinida é *Palavra-passe1*. 
   
    ![Página de entrada do dispositivo Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. A pedido, altere a palavra-passe do administrador do dispositivo.  
    A nova palavra-passe deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.

Está agora na página **geral** do seu dispositivo. O próximo passo é configurar as definições de rede para o seu dispositivo.


## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar-se a um dispositivo físico


Para aprender a configurar as definições de rede no seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Rede de configuração](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
