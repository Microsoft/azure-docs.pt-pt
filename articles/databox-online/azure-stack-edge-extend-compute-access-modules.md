---
title: Gerir a rede de computação no Azure Stack Edge Pro para aceder a módulos Microsoft Docs
description: Descreve como estender a rede de computação no seu Azure Stack Edge Pro para aceder a módulos através de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894110"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Ativar a rede de computação no seu Azure Stack Edge Pro

Este artigo descreve como os módulos em execução no seu Azure Stack Edge Pro podem aceder à rede de computação ativada no dispositivo.

Para configurar a rede, irá tomar os seguintes passos:

- Ativar uma interface de rede no seu dispositivo Azure Stack Edge Pro para calcular
- Adicione um módulo para aceder à rede de computação no seu Azure Stack Edge Pro
- Verifique se o módulo pode aceder à interface de rede ativada

Neste tutorial, você usará um módulo de aplicações webserver para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

- Um dispositivo Azure Stack Edge Pro com configuração do dispositivo concluída.
- Completou o passo **de computação Configure** de acordo com o [Tutorial: Transforme dados com Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) no seu dispositivo. O seu dispositivo deve ter um recurso IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Ativar a interface de rede para calcular

Para aceder aos módulos em execução no seu dispositivo através de uma rede externa, terá de atribuir um endereço IP a uma interface de rede no seu dispositivo. Pode gerir estas definições de computação a partir da sua UI web local.

Dê os seguintes passos na sua UI web local para configurar as definições de computação.

1. Na UI web local, aceda às **definições de Configuração > Compute**.  

2. **Ativar** a interface de rede que pretende utilizar para ligar a um módulo de computação que irá utilizar no dispositivo.

    - Se utilizar endereços IP estáticos, introduza um endereço IP para a interface de rede.
    - Se utilizar o DHCP, os endereços IP são automaticamente atribuídos. Este exemplo utiliza o DHCP.

    ![Ativar as definições de cálculo 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **Selecione Aplicar** para aplicar as definições. Tome nota do endereço IP atribuído à interface de rede se utilizar o DHCP.

    ![Ativar as definições de cálculo](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo webserver

Tome os seguintes passos para adicionar um módulo de aplicação webserver no seu dispositivo Azure Stack Edge Pro.

1. Aceda ao recurso IoT Hub associado ao seu dispositivo Azure Stack Edge Pro e, em seguida, selecione **o dispositivo IoT Edge**.
2. Selecione o dispositivo IoT Edge associado ao seu dispositivo Azure Stack Edge Pro. Nos **detalhes**do dispositivo , selecione **definir módulos**. Nos **módulos Adicionar**, selecione **+ Adicionar** e, em seguida, selecione Módulo de **Borda IoT**.
3. Na lâmina dos **módulos personalizados IoT Edge:**

    1. Especifique um **nome** para o seu módulo de aplicação webserver que pretende implementar.
    2. Forneça um **URI de imagem** para a imagem do seu módulo. É recuperado um módulo que corresponda ao nome e etiquetas fornecidos. Neste caso, `nginx:stable` irá retirar uma imagem nginx estável (marcada como estável) do [repositório](https://hub.docker.com/_/nginx/)público do Docker .
    3. Nas **opções de criação de recipientes,** cole o seguinte código de amostra:  

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

        Esta configuração permite-lhe aceder ao módulo utilizando a rede computacional IP em *http* na porta TCP 8080 (com a porta webserver padrão a ser de 80).

        ![Especifique informações de porta na lâmina do módulo personalizado IoT Edge](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Selecione **Guardar**.

## <a name="verify-module-access"></a>Verificar acesso ao módulo

1. Verifique se o módulo está implantado com sucesso e está a funcionar. Na página **'Detalhes** do Dispositivo', no **separador Módulos,** o estado de funcionamento do módulo deve estar **em funcionamento**.  
2. Conecte-se ao módulo de aplicação do servidor web. Abra uma janela e tipo de navegador:

    `http://<compute-network-IP-address>:8080`

    Devias ver que a aplicação webserver está a funcionar.

    ![Verificar ligação ao módulo sobre a porta especificada](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](azure-stack-edge-manage-users.md).
