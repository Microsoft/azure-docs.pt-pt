---
title: Tutorial para ligar, configurar, ativar o dispositivo Azure Stack Edge Pro R no portal Azure | Microsoft Docs
description: Saiba como pode ligar-se ao seu dispositivo Azure Stack Edge Pro R utilizando a UI web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468729"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Tutorial: Conecte-se ao Azure Stack Edge Pro R

Este tutorial descreve como pode ligar-se ao seu dispositivo Azure Stack Edge Pro R utilizando a UI web local.

A conclusão do processo de ligação pode demorar cerca de 5 minutos.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar-se a um dispositivo físico


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Pro R, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conecte-se à configuração local do UI web

1. Configure o adaptador Ethernet no computador para estabelecer ligação ao dispositivo Azure Stack Edge Pro R com um endereço IP estático 192.168.100.5 e sub-rede 255.255.255.0.

2. Ligue o computador à PORTA 1 no dispositivo. Se ligar o computador ao dispositivo diretamente (sem um comutador), utilize um cabo cruzado ou um adaptador Ethernet USB. Utilize a seguinte ilustração para identificar a PORTA 1 no dispositivo.

    ![Painel posterior de um dispositivo ligado por cabo](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Abra uma janela do navegador e aceda à IU da Web local do dispositivo em `https://192.168.100.10`.  
    Esta ação pode demorar alguns minutos depois de ligar o dispositivo.

    Verá um erro ou um aviso a indicar que existe um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro do certificado de segurança do site](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. **Selecione Continue a esta página web**.  
    Estes passos podem variar consoante o browser utilizado.

5. Inicie sessão na IU da Web do dispositivo. A palavra-passe predefinida é *Palavra-passe1*. 
   
    ![Página de entrada do dispositivo Azure Stack Edge](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. A pedido, altere a palavra-passe do administrador do dispositivo.  
    A nova palavra-passe tem de ter entre 8 e 16 carateres. Tem de incluir três dos seguintes carateres: maiúsculas, minúsculas, números e carateres especiais.

Está agora na página **geral** do seu dispositivo. O próximo passo é configurar as definições de rede para o seu dispositivo.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar-se a um dispositivo físico


Para aprender a configurar as definições de rede no seu dispositivo Azure Stack Edge Pro R, consulte:

> [!div class="nextstepaction"]
> [Rede de configuração](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
