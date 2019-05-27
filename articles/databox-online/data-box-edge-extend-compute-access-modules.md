---
title: Gerir a rede de computação na borda de caixa de dados do Azure para módulos de acesso | Documentos da Microsoft
description: Descreve como expandir a rede de computação no seu limite de caixa de dados para aceder aos módulos através de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917239"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Ativar a rede de computação no seu limite de caixa de dados do Azure

Este artigo descreve como os módulos em execução no seu limite de caixa de dados do Azure podem acessar a rede de computação ativado no dispositivo.

Para configurar a rede, que toma as seguintes etapas:

- Ativar uma interface de rede no seu dispositivo do Edge de caixa de dados para computação
- Adicionar um módulo para a rede de computação de acesso na extremidade da caixa de dados
- Certifique-se de que o módulo pode aceder à interface de rede ativada

Neste tutorial, vai utilizar um módulo de aplicação de servidor Web para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, terá de:

- Um dispositivo de limite de caixa de dados com a configuração de dispositivo foi concluída.
- Concluiu **configurar computação** passo, de acordo a [Tutorial: Transformar dados com o Edge de caixa de dados do Azure](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) no seu dispositivo. O dispositivo deve ter um recurso do IoT Hub associado, um dispositivo de IoT e um dispositivo IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Ativar a interface de rede para computação

Para acessar os módulos em execução no seu dispositivo através de uma rede externa, terá de atribuir um endereço IP a uma interface de rede no seu dispositivo. Pode gerir estas definições da web local da interface do Usuário de computação.

Siga os passos seguintes na sua web local da interface do Usuário para configurar as definições de computação.

1. Na IU da web local, aceda a **configuração > definições de computação**.  

2. **Ativar** a interface de rede que pretende utilizar para ligar a um módulo de computação que vai ser executado no dispositivo.

    - Se utilizar endereços IP estáticos, introduza um endereço IP para a interface de rede.
    - Se utilizar DHCP, os endereços IP são atribuídos automaticamente. Este exemplo utiliza o DHCP.

    ![Ativar as definições de computação 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecione **aplicar** para aplicar as definições. Tome nota do endereço IP atribuído à interface de rede, se utilizar o DHCP.

    ![Ativar as definições de computação](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Adicionar o módulo de aplicativo de servidor Web

Siga os passos seguintes para adicionar um módulo de aplicação de servidor Web no seu dispositivo do Edge de caixa de dados.

1. Vá para o recurso do IoT Hub associado com o seu dispositivo Edge de caixa de dados e, em seguida, selecione **dispositivo IoT Edge**.
2. Selecione o dispositivo do IoT Edge associado com o seu dispositivo Edge de caixa de dados. Sobre o **detalhes do dispositivo**, selecione **definir módulos**. No **adicionar módulos**, selecione **+ adicionar** e, em seguida, selecione **módulo do IoT Edge**.
3. Na **módulos personalizados do IoT Edge** painel:

    1. Especifique um **nome** para seu módulo de aplicação de servidor Web que pretende implementar.
    2. Fornecer uma **URI da imagem** para que a imagem do módulo. Um módulo o nome fornecido e as etiquetas correspondentes é recuperado. Neste caso, `nginx:stable` obterá uma imagem da nginx estável (marcada como estável) do público [repositório de Docker](https://hub.docker.com/_/nginx/).
    3. Na **opções de criar contentor**, cole o seguinte código de exemplo:  

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

        Esta configuração permite-lhe aceder o módulo com o IP de rede de computação através de *http* na porta TCP 8080 (com a porta predefinida do servidor Web que está a ser 80).

        ![Especifique as informações da porta no painel de módulo personalizado do IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selecione **Guardar**.

## <a name="verify-module-access"></a>Verificar o acesso de módulo

1. Certifique-se de que o módulo for implementado com êxito e está em execução. Na **detalhes do dispositivo** na página a **módulos** separador, o estado de tempo de execução do módulo deve ser **em execução**.  
2. Ligue-se para o módulo de aplicação de servidor web. Abra uma janela do browser e escreva:

    `http://<compute-network-IP-address>:8080`

    Deverá ver que a aplicação de servidor Web está em execução.

    ![Verificar a ligação ao módulo através da porta especificada](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](data-box-edge-manage-users.md).
