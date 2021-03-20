---
title: Quickstart - Inscreva o dispositivo TPM no Serviço de Provisionamento de Dispositivos Azure utilizando Python
description: Quickstart - Inscreva o dispositivo TPM para o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) utilizando o serviço de fornecimento python SDK. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 96bd1e85de45ac36515580025dfc392e931643f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323768"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Quickstart: Inscreva o dispositivo TPM no Serviço de Provisionamento de Dispositivos IoT Hub utilizando o serviço de fornecimento python SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Neste arranque rápido, você cria programáticamente uma inscrição individual para um dispositivo TPM no Serviço de Provisionamento de Dispositivos Azure IoT Hub, utilizando o Serviço de Provisionamento Python SDK com a ajuda de uma aplicação Python de amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão da [Configuração do Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 2.x ou 3.x](https://www.python.org/downloads/). Este quickstart instala o [Serviço de Provisionamento Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) abaixo.
- [Pip,](https://pip.pypa.io/en/stable/installing/)se não estiver incluído com a sua distribuição de Python.
- Chave de apoio. Utilize os passos em [Criar e fornecam um dispositivo simulado](quick-create-simulated-device.md) ou utilize a chave de averbamento fornecida com o SDK, descrito abaixo.

> [!IMPORTANT]
> Este artigo aplica-se apenas ao V1 Python SDK precedido. Os clientes de dispositivos e serviços para o Serviço de Provisionamento de Dispositivos IoT Hub ainda não estão disponíveis em V2. A equipa está neste momento a trabalhar arduamente para trazer a V2 para apresentar paridade.

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Transfira e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python às variáveis de ambiente específicas da sua plataforma. 

1. Para o [Serviço de Provisionamento Python SDK,](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)escolha uma das seguintes opções:

    - Crie e compile o **SDK do Azure IoT Python**. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) para criar os pacotes Python. Se estiver a utilizar o SO Windows, instale também o [pacote redistribuível Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads), para permitir a utilização de DLLs nativas de Python.

    - [Instale ou atualize o *pip*, o sistema de gestão de pacotes Python](https://pip.pypa.io/en/stable/installing/) e instale o pacote através do seguinte comando:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Precisa da chave de endossamento para o seu dispositivo. Se seguiu o manual de início rápido [Criar e aprovisionar um dispositivo simulado](quick-create-simulated-device.md) para criar um dispositivo TPM simulado, utilize a chave criada para esse dispositivo. Caso contrário, pode utilizar a seguinte chave de endossamento fornecida com o SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Modificar o código de exemplo do Python

Esta secção mostra como adicionar os detalhes de aprovisionamento do seu dispositivo TPM ao código de exemplo. 

1. Num editor de texto, crie um novo ficheiro **TpmEnrollment.py**.

1. Adicione ase seguintes afirmações e variáveis `import` no início do ficheiro **TpmEnrollment.py**. Em seguida, `dpsConnectionString` substitua a sua cadeia de ligação encontrada nas **políticas de acesso partilhado** no seu Serviço de **Provisionamento de Dispositivos** no **portal Azure**. Substitua `endorsementKey` pelo valor anteriormente anotado em [Preparar o ambiente](quick-enroll-device-tpm-python.md#prepareenvironment). Por fim, crie um `registrationid` exclusivo e certifique-se de que este consiste apenas em carateres alfanuméricos em minúsculas e hífenes.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Adicione a seguinte função e chamada de função para implementar a criação de inscrição em grupos:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Guarde e feche o ficheiro **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Executar a inscrição TPM simples

1. Abra uma linha de comandos e execute o script.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Observe a saída para ver se a inscrição foi executada com êxito.

1. Navegue até ao serviço de aprovisionamento no portal do Azure. Selecione **Gerir inscrições**. Repare que o seu dispositivo TPM aparece no separador **Inscrições Individuais**, com o nome `registrationid` criado anteriormente. 

    ![Verificar a inscrição do TPM com êxito no portal](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Limpar os recursos
Se planeia explorar a amostra de serviço java, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de Python no seu computador.
1. Se criou um dispositivo TPM simulado, feche a janela do simulador TPM.
1. Navegue para o seu serviço de Provisionamento de Dispositivos no portal Azure, **selecione Gerir as inscrições** e, em seguida, selecione o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* para a entrada de inscrição que criou usando este arranque rápido e prima o botão **Eliminar** na parte superior do painel.


## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, criou programáticamente uma entrada individual de inscrição para um dispositivo TPM e, opcionalmente, criou um dispositivo simulado TPM na sua máquina e forte-o para o seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos Azure IoT Hub. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
