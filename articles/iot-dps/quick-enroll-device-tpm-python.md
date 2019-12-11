---
title: Registrar o dispositivo TPM no serviço de provisionamento de dispositivos do Azure usando o Python
description: Início rápido-registrar dispositivo TPM no serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS) usando o SDK do serviço de provisionamento do Python. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 6266ef3479e74103d0989b8eb0286626da5eb28f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976797"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Início rápido: registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT usando o SDK do serviço de provisionamento do Python

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Estas etapas mostram como criar programaticamente um registro individual para um dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT do Azure, usando o [SDK do serviço de provisionamento de Python do v1](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) com a ajuda de um aplicativo Python de exemplo. Embora o SDK do serviço Python funcione em computadores Windows e Linux, este artigo utiliza um computador de desenvolvimento Windows para percorrer o processo de inscrição.

> [!IMPORTANT]
> Este artigo se aplica somente ao SDK do Python v1 preterido. Os clientes de dispositivo e serviço para o serviço de provisionamento de dispositivos no Hub IOT ainda não estão disponíveis na v2. No momento, a equipe está trabalhando atualmente para trazer a paridade de v2 para recursos.

Antes de avançar, certifique-se de que [configura o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Transfira e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python às variáveis de ambiente específicas da sua plataforma. 

1. Selecione uma das seguintes opções:

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

1. Adicione ase seguintes afirmações e variáveis `import` no início do ficheiro **TpmEnrollment.py**. Em seguida, substitua `dpsConnectionString` pela sua cadeia de ligação, que se encontra nas **Políticas de acesso partilhadas** no seu **Serviço Aprovisionamento de Dispositivos** no **portal do Azure**. Substitua `endorsementKey` pelo valor anteriormente anotado em [Preparar o ambiente](quick-enroll-device-tpm-python.md#prepareenvironment). Por fim, crie um `registrationid` exclusivo e certifique-se de que este consiste apenas em carateres alfanuméricos em minúsculas e hífenes.  
   
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


## <a name="clean-up-resources"></a>Limpar recursos
Se você planeja explorar o exemplo de serviço Java, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de Python no seu computador.
1. Se criou um dispositivo TPM simulado, feche a janela do simulador TPM.
1. Navegue até o serviço de provisionamento de dispositivos no portal do Azure, selecione **gerenciar registros**e, em seguida, selecione a guia **registros individuais** . Marque a caixa de seleção ao lado da ID de *registro* da entrada de registro criada usando este guia de início rápido e pressione o botão **excluir** na parte superior do painel.


## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você criou programaticamente uma entrada de registro individual para um dispositivo TPM e, opcionalmente, criou um dispositivo simulado do TPM em seu computador e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos do Hub IoT do Azure. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
