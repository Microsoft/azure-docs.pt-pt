---
title: Tutorial para configurar dispositivo, atualização, tempo para o dispositivo Azure Stack Edge Mini R no portal Azure
description: Tutorial para implementar O Azure Stack Edge Mini R instrui-o a configurar as definições de dispositivo, atualização e tempo para o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: e9f4d4f4ad27081bb105cdb1698438837fc2fe02
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546674"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>Tutorial: Configurar as definições do dispositivo para Azure Stack Edge Mini R

Este tutorial descreve como configura as definições relacionadas com o dispositivo para o seu dispositivo Azure Stack Edge Mini R com uma GPU a bordo. Pode configurar o nome do seu dispositivo, atualizar o servidor e o servidor de tempo através da UI web local.

As definições do dispositivo podem demorar cerca de 5 a 7 minutos a ser concluídas.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar definições de dispositivos
> * Atualização de configuração 
> * Tempo de configuração

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar as definições relacionadas com o dispositivo no seu dispositivo Azure Stack Edge Mini R com GPU, certifique-se de que:

* Para o seu dispositivo físico:

    - Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Mini R ](azure-stack-edge-mini-r-deploy-install.md).
    - Configuraste a rede e ativaste e configuraste a rede de computação no teu dispositivo conforme detalhado no [Tutorial: Rede de configuração para Azure Stack Edge Mini R com GPU](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Configurar definições de dispositivos

Siga estes passos para configurar as definições relacionadas com o dispositivo:

1. Na página **dispositivo,** tome os seguintes passos:

    1. Insira um nome amigável para o seu dispositivo. O nome amigável deve conter de 1 a 13 caracteres e pode ter letra, números e hífens.

    2. Forneça um **domínio DNS** para o seu dispositivo. Este domínio é utilizado para configurar o dispositivo como um servidor de ficheiros.

    3. Para validar e aplicar as definições do dispositivo configuradas, selecione **Aplicar**.

        ![Web local UI "Dispositivo" página 1](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        Se tiver alterado o nome do dispositivo e o domínio DNS, os certificados auto-assinados que existiam no dispositivo não funcionarão. 

        ![Web local UI "Dispositivo" página 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        Tem de escolher uma das seguintes opções quando configurar certificados: 
        
        - Gere e descarregue os certificados do dispositivo. 
        - Traga os seus próprios certificados para o dispositivo, incluindo a cadeia de assinaturas.
    

    4. Quando o nome do dispositivo e o domínio DNS são alterados, o ponto final SMB é criado.  

        ![Web local UI "Dispositivo" página 3](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. Depois de aplicadas as definições, selecione **Seguinte: Atualizar servidor**.


## <a name="configure-update"></a>Atualização de configuração

1. Na página **'Atualização',** já pode configurar a localização de onde deve descarregar as atualizações do seu dispositivo.  

    - Pode obter as atualizações diretamente do **servidor Microsoft Update**.

        ![Página local do UI "Update Server"](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        Também pode optar por implementar atualizações a partir dos serviços de **Atualização** do Servidor do Windows (WSUS). Fornecer o caminho para o servidor WSUS.
        
        ![Página 2 do UI "Update Server" local](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > Se um servidor separado do Windows Update estiver configurado e optar por ligar mais *de https* (em vez de *http),* são necessários certificados de cadeia de assinatura necessários para se ligar ao servidor de atualização. Para obter informações sobre como criar e carregar certificados, vá a [Gerir certificados.](azure-stack-edge-gpu-manage-certificates.md) Para trabalhar num modo desligado, como o nível do dispositivo Azure Stack Edge para o Modular Data Center, ative a opção WSUS. Durante a ativação, o dispositivo procura atualizações e se o servidor não estiver configurado, a ativação falhará. 

2. Selecione **Aplicar**.
3. Depois de configurar o servidor de atualização, selecione **Seguinte: Hora**.
    

## <a name="configure-time"></a>Tempo de configuração

Siga estes passos para configurar as definições de tempo no seu dispositivo. 

> [!IMPORTANT]
> Embora as definições de tempo sejam opcionais, recomendamos vivamente que configufique um NTP primário e um servidor NTP secundário na rede local para o seu dispositivo. Se o servidor local não estiver disponível, os servidores NTP públicos podem ser configurados.

Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.

1. Na página **'Tempo',** pode selecionar o fuso horário e os servidores NTP primários e secundários para o seu dispositivo.  
    
    1. Na lista de down-down do **fuso** horário, selecione o fuso horário que corresponde à localização geográfica em que o dispositivo está a ser implantado.
        O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Na caixa **do servidor NTP primário,** introduza o servidor primário para o seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Certifique-se de que a sua rede permite que o tráfego NTP passe do seu datacenter para a internet.

    3. Opcionalmente, na caixa **de servidor NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, **selecione Aplicar**.

        ![Página local de UI "Tempo"](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. Depois de aplicadas as definições, selecione **Seguinte: Certificados**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar definições de dispositivos
> * Atualização de configuração 
> * Tempo de configuração

Para aprender a configurar certificados para o seu dispositivo Azure Stack Edge Mini R, consulte:

> [!div class="nextstepaction"]
> [Configurar os certificados](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
