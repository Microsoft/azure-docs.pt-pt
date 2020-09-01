---
title: Tutorial para ligar, configurar, ativar o dispositivo Azure Stack Edge com GPU no portal Azure / Microsoft Docs
description: Tutorial para implementar Azure Stack Edge instrui-o a ligar, configurar e ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c0581a79bda4ae4e311390f5b2d929073e0833d9
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182025"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-with-gpu"></a>Tutorial: Configurar configurações de dispositivos para Azure Stack Edge com GPU

Este tutorial descreve como configura as definições relacionadas com o dispositivo para o seu dispositivo Azure Stack Edge com uma GPU a bordo. Pode configurar o nome do seu dispositivo, atualizar o servidor e o servidor de tempo através da UI web local.

As definições do dispositivo podem demorar cerca de 5 a 7 minutos a ser concluídas.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar definições de dispositivos
> * Atualização de configuração 
> * Tempo de configuração

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar as definições relacionadas com o dispositivo no seu dispositivo Azure Stack Edge com GPU, certifique-se de que:

* Para o seu dispositivo físico:

    - Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Configuraste a rede e ativaste e configuraste a rede de computação no teu dispositivo conforme detalhado no [Tutorial: Rede de configuração para Azure Stack Edge com GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Configurar definições de dispositivos

Siga estes passos para configurar as definições relacionadas com o dispositivo.
 
1. No **azulejo de configuração** do dispositivo, para **dispositivo,** selecione **Configure**.

    ![Página local de UI "Dispositivo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-1.png)

    Na página **dispositivo,** tome os seguintes passos:

    1. Insira um nome amigável para o seu dispositivo. O nome amigável deve conter de 1 a 13 caracteres e pode ter letra, números e hífens.

    2. Forneça um **domínio DNS** para o seu dispositivo. Este domínio é utilizado para configurar o dispositivo como um servidor de ficheiros.

    3. Para validar e aplicar as definições do dispositivo configuradas, selecione **Aplicar**.

        ![Página local de UI "Dispositivo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Se tiver alterado o nome do dispositivo e o domínio DNS, os certificados auto-assinados gerados automaticamente no dispositivo não funcionarão. Tem de escolher uma das seguintes opções quando configurar certificados.: 
        
        - Gere e descarregue os certificados do dispositivo. 
        - Traga os seus próprios certificados para o dispositivo, incluindo a cadeia de assinaturas.
    

        ![Página local de UI "Dispositivo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Quando o nome do dispositivo e o domínio DNS são alterados, os pontos finais SMB e NFS são criados.  

    5. Depois de aplicadas as definições, volte para **começar**.

## <a name="configure-update"></a>Atualização de configuração

1. No **azulejo de configuração** do dispositivo, para **atualização,** selecione **Configurar**. Agora pode configurar a localização de onde deve descarregar as atualizações do seu dispositivo.  

    ![Página local do UI "Update Server"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-1.png)

    - Pode obter as atualizações diretamente do **servidor Microsoft Update**.

        ![Página local do UI "Update Server"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        Também pode optar por implementar atualizações a partir dos serviços de **Atualização** do Servidor do Windows (WSUS). Fornecer o caminho para o servidor WSUS.
        
        ![Página local do UI "Update Server"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Se um servidor separado do Windows Update estiver configurado e optar por ligar mais *de https* (em vez de *http),* são necessários certificados de cadeia de assinatura necessários para se ligar ao servidor de atualização. Para obter informações sobre como criar e carregar certificados, vá a [Gerir certificados.](azure-stack-edge-j-series-manage-certificates.md) 

2. Selecione **Aplicar**.
3. Depois de configurar o servidor de atualização, volte para **Começar**.
    

## <a name="configure-time"></a>Tempo de configuração

Siga estes passos para configurar as definições de tempo no seu dispositivo. 

1. No azulejo de configuração do **dispositivo,** selecione **Tempo**. Pode selecionar o fuso horário e os servidores NTP primários e secundários para o seu dispositivo.  

    > [!IMPORTANT]
    > Embora as definições de tempo sejam opcionais, recomendamos vivamente que configufique um NTP primário e um servidor NTP secundário na rede local para o seu dispositivo. Se o servidor local não estiver disponível, os servidores NTP públicos podem ser configurados.
    
    Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.

    ![Página local de UI "Tempo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-1.png)

2. Na página **'Tempo',** faça o seguinte:
    
    1. Na lista de down-down do **fuso** horário, selecione o fuso horário que corresponde à localização geográfica em que o dispositivo está a ser implantado.
        O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Na caixa **do servidor NTP primário,** introduza o servidor primário para o seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Certifique-se de que a sua rede permite que o tráfego NTP passe do seu datacenter para a internet.

    3. Opcionalmente, na caixa **de servidor NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, **selecione Aplicar**.

        ![Página local de UI "Tempo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

3. Depois de aplicadas as definições, volte para **começar**.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar definições de dispositivos
> * Atualização de configuração 
> * Tempo de configuração

Para aprender a configurar certificados para o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Configurar os certificados](./azure-stack-edge-gpu-deploy-configure-certificates.md)
