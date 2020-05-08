---
title: Gerir a rede de computação no Azure Stack Edge para aceder aos módulos. Microsoft Docs
description: Descreve como estender a rede de cálculo no seu Azure Stack Edge para aceder a módulos através de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: b352458b768c997f295596a7cbd5e8c02f843a9f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569800"
---
# <a name="enable-compute-network-on-your-azure-stack-edge"></a>Ativar a rede compute no seu Azure Stack Edge

Este artigo descreve como os módulos em execução no seu Azure Stack Edge podem aceder à rede computacional ativada no dispositivo.

Para configurar a rede, tomará os seguintes passos:

- Ative uma interface de rede no seu dispositivo Azure Stack Edge para calcular
- Adicione um módulo para aceder à rede de computação no seu Azure Stack Edge
- Verifique se o módulo pode aceder à interface de rede ativada

Neste tutorial, você usará um módulo de aplicação webserver para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar:

- Um dispositivo Azure Stack Edge com configuração do dispositivo concluída.
- Completou o passo da **computação configure** de acordo com o [Tutorial: Transforme os dados com Azure Stack Edge](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) no seu dispositivo. O seu dispositivo deve ter um recurso IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Ativar a interface de rede para computação

Para aceder aos módulos em execução no seu dispositivo através de uma rede externa, terá de atribuir um endereço IP a uma interface de rede no seu dispositivo. Pode gerir estas definições de cálculo a partir da sua UI web local.

Dê os seguintes passos na sua UI web local para configurar as definições de cálculo.

1. Na Web UI local, vá às definições de **Configuração > Compute**.  

2. **Ative** a interface de rede que pretende utilizar para se ligar a um módulo de computação que irá executar no dispositivo.

    - Se utilizar endereços IP estáticos, introduza um endereço IP para a interface da rede.
    - Se utilizar o DHCP, os endereços IP são automaticamente atribuídos. Este exemplo utiliza dHCP.

    ![Ativar as definições de computação 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecione **Aplicar** para aplicar as definições. Tome nota do endereço IP atribuído à interface de rede se utilizar o DHCP.

    ![Ativar as definições de computação](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo webserver

Tome os seguintes passos para adicionar um módulo de aplicação webserver no seu dispositivo Azure Stack Edge.

1. Vá ao recurso IoT Hub associado ao seu dispositivo Azure Stack Edge e, em seguida, selecione o **dispositivo IoT Edge**.
2. Selecione o dispositivo IoT Edge associado ao seu dispositivo Azure Stack Edge. Nos detalhes do **Dispositivo,** selecione **Módulos set**. Em **adicionar módulos,** selecione **+ Adicione** e, em seguida, selecione Módulo **IoT Edge**.
3. Na lâmina de **módulos personalizados IoT Edge:**

    1. Especifique um **Nome** para o módulo de aplicação do webserver que pretende implementar.
    2. Forneça um **Uri de Imagem** para a sua imagem de módulo. É recuperado um módulo que corresponda ao nome e etiquetas fornecidos. Neste caso, `nginx:stable` retirará uma imagem de nginx estável (marcada como estável) do [repositório](https://hub.docker.com/_/nginx/)público Docker .
    3. No **recipiente Criar Opções,** colar o seguinte código de amostra:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Esta configuração permite-lhe aceder ao módulo utilizando o IP da rede computacional em *http* na porta 8080 do TCP (sendo a porta webserver padrão 80).

        ![Especifique informações da porta na lâmina do módulo personalizado IoT Edge](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Selecione **Guardar**.

## <a name="verify-module-access"></a>Verificar o acesso ao módulo

1. Verifique se o módulo está implantado com sucesso e está em execução. Na página Detalhes do **Dispositivo,** no separador **Módulos,** o estado de funcionamento do módulo deve estar **em execução**.  
2. Ligue-se ao módulo de aplicação do servidor web. Abra uma janela do navegador e escreva:

    `http://<compute-network-IP-address>:8080`

    Deve ver que a aplicação webserver está em execução.

    ![Verificar a ligação ao módulo sobre a porta especificada](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](azure-stack-edge-manage-users.md).
