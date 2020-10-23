---
title: Quickstart - Use a chave simétrica para providenciar um dispositivo ao Azure IoT Hub usando Python
description: Neste arranque rápido, você usará o Azure IoT Python SDK para providenciar um dispositivo chave simétrico a um hub IoT usando o Serviço de Provisionamento de Dispositivos IoT IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 8b87d9d487257504a438cf13a5b94e3ca4f9233d
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426381"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>Quickstart: Provision a um dispositivo Python com chaves simétricas

Neste arranque rápido, você aprenderá a providenciar uma máquina de desenvolvimento Windows como um dispositivo para um hub IoT usando Python. Este dispositivo utilizará uma chave simétrica e uma inscrição individual para autenticar com uma instância do Serviço de Provisionamento de Dispositivos (DPS) para ser atribuído a um hub IoT. O código de amostra do [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) será utilizado para o fornecimento do dispositivo. 

Embora este artigo demonstre o provisionamento com uma inscrição individual, você também pode usar grupos de inscrição. Há algumas diferenças na utilização de grupos de inscrição. Por exemplo, deve utilizar uma chave de dispositivo derivada com um ID de registo único para o dispositivo. Embora os grupos de inscrições de chave simétrica não estejam limitados a dispositivos legados, o artigo [Como aprovisionar dispositivos legados com o atestado de chave simétrica](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de inscrições. Para obter mais informações, veja [Inscrições em grupo para o Atestado de Chave Simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento.](about-iot-dps.md#provisioning-process) 

Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. Este guia de início rápido exige que já tenha criado a instância do Serviço de Aprovisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que tem [Python 3.7](https://www.python.org/downloads/) ou mais tarde instalado na sua máquina baseada no Windows. Pode verificar a sua versão de Python correndo `python --version` .

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>Prepare o ambiente Python SDK 

1. Certifique-se de que o Git está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

2. Abra uma linha de comandos. Clone o repo GitHub para o Azure IoT Python SDK:
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. Navegue para o `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` diretório onde está localizado o ficheiro da amostra, _provision_symmetric_key.py._
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. Instale a biblioteca _de dispositivos azure-iot_ executando o seguinte comando.

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu esquerdo e abra a sua instância de Serviço de Provisionamento de Dispositivos (DPS).

2. Selecione o **separador Descodusagens** de Gestão e, em seguida, selecione o botão **de inscrição individual Adicionar** no topo. 

3. No painel **'Adicionar Inscrição',** introduza as seguintes informações e prima o botão **Guardar.**

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Teclas de geração automática**: Verifique esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **symm-key-python-device-008**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **python-device-008**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. Uma vez guardada a sua inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à inscrição. A inscrição do seu dispositivo chave simétrico aparece como **symm-key-python-device-008** sob a coluna *de Identificação* de Registo no separador *Matrículas Individuais.* 

5. Abra a inscrição e copie o valor da **Chave Primária** gerada. Utilizará este valor chave e o **ID de registo** mais tarde quando adicionar variáveis ambientais para utilização com o código de amostra de provisionamento do dispositivo.



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Preparar o código de provisionamento do dispositivo

Nesta secção, irá adicionar as seguintes quatro variáveis ambientais que serão utilizadas como parâmetros para o dispositivo que fornece o código de amostra para o seu dispositivo chave simétrica. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

O código de provisionamento entrará em contacto com a instância DPS com base nestas variáveis para autenticar o seu dispositivo. O dispositivo será então atribuído a um hub IoT já ligado à instância DPS com base na configuração de inscrição individual. Uma vez previsto, o código de amostra enviará alguma telemetria de teste para o hub IoT.

1. No [portal Azure](https://portal.azure.com), no menu serviço de fornecimento de dispositivos, selecione **Overview** e copie o seu _Ponto final_ de serviço e _ID Scope_. Utilizará estes valores para as `PROVISIONING_HOST` variáveis e `PROVISIONING_IDSCOPE` ambientais.

    ![Informações de serviço](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. Na sua solicitação de comando Python, adicione as variáveis ambientais utilizando os valores que copiou. 

    Os seguintes comandos são exemplos para mostrar sintaxe de comando. Certifique-se de que utiliza os valores corretos.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. Na sua solicitação de comando Python, adicione as variáveis ambientais para o ID de registo e a chave simétrica que copiou da inscrição individual na secção anterior. 

    Os seguintes comandos são exemplos para mostrar sintaxe de comando. Certifique-se de que utiliza os valores corretos.

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. Executar o código de amostra python em _provision_symmetric_key.py_.

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. A saída esperada deve ser semelhante à seguinte, que mostra o hub IoT ligado ao qual o dispositivo foi atribuído com base nas definições individuais de inscrição. Alguns exemplos de telemetria de velocidade do vento também são enviados para o centro como um teste:

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. No portal Azure, navegue até ao hub IoT ligado ao seu serviço de fornecimento e abra a lâmina dos **dispositivos IoT.** Depois de ter previsto com sucesso o dispositivo chave simétrico no hub, o ID do dispositivo é mostrado com *status* conforme **ativado**. Pode ser necessário premir o botão **Refresh** na parte superior se já tiver aberto a lâmina antes de executar o código de amostra do dispositivo. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)
>


## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar e explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de Provisionamento de Dispositivos. Abrir **As Inscrições** para o seu serviço e, em seguida, selecionar o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* do dispositivo que inscreveu neste arranque rápido e prima o botão **Eliminar** na parte superior do painel. 
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adquirou um dispositivo de chave simétrica baseado no Windows para o seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Para aprender a providenciar dispositivos de certificado X.509 usando Python, continue com o quickstart abaixo para dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Provisão dispositivos X.509 usando DPS e Python](quick-create-simulated-device-x509-python.md)
