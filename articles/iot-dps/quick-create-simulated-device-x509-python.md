---
title: Provisão simulado dispositivo X.509 para Hub Azure IoT usando Python
description: Quickstart - Crie e forre um dispositivo SdK simulado utilizando o dispositivo Python SDK para o Serviço de Provisionamento de Dispositivos IoT Hub (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 44f1a2cd3336eeae87878c333fb05d2e6b1f88e8
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605384"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Criar e fornecer um dispositivo SdK simulado utilizando o dispositivo Python SDK para o Serviço de Provisionamento de Dispositivos IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Neste arranque rápido, cria-se um dispositivo X.509 simulado num computador Windows. Utiliza o código Python da amostra do dispositivo para ligar este dispositivo simulado ao seu hub IoT utilizando uma inscrição individual com o Serviço de Provisionamento de Dispositivos (DPS).

## <a name="prerequisites"></a>Pré-requisitos

- Revisão dos [conceitos de fornecimento automático.](concepts-auto-provisioning.md)
- Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) com C++desenvolvimento desktop com .
- [CMake sistema](https://cmake.org/download/)de construção .
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Este artigo aplica-se apenas ao SDK Desprecated V1 Python. Os clientes de dispositivos e serviços do Serviço de Provisionamento de Dispositivos Iot Hub ainda não estão disponíveis no V2. A equipa está neste momento a trabalhar arduamente para trazer o V2 para a paridade.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que instalou o [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 C++ou mais tarde, com o desenvolvimento do 'Desktop com ' carga de trabalho habilitada para a instalação do Seu Estúdio Visual.

2. Transfira e instale o [sistema de compilação CMake](https://cmake.org/download/).

3. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

4. Abra uma linha de comandos ou o Git Bash. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. Crie uma pasta na sua cópia local deste repositório do GitHub para o processo de compilação CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Execute o comando seguinte para criar a solução do Visual Studio para o cliente de aprovisionamento.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de inscrição individual

Nesta secção, irá utilizar um certificado X.509 autoassinado. É importante ter em consideração os seguintes pontos:

* Os certificados autoassinados são apenas para teste e não devem ser utilizados na produção.
* A data de expiração predefinida para um certificado autoassinado é de um ano.

Vai utilizar o código de exemplo do SDK C do Azure IoT para criar o certificado a ser utilizado na entrada de inscrição individual do dispositivo simulado.

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições individuais](concepts-service.md#individual-enrollment): Utilizado para inscrever um único dispositivo.

Este artigo demonstra inscrições individuais.

1. Abra a solução gerada na pasta *cmake* com o nome `azure_iot_sdks.sln` e compile-a no Visual Studio.

2. Clique com o botão direito do rato no projeto **dice\_device\_enrollment**, na pasta **Provision\_Tools** e selecione **Definir como Projeto de Arranque**. Execute a solução. 

3. Na janela de saída, introduza `i` para inscrição individual, quando lhe for pedido. A janela de saída apresenta um certificado X.509 gerado localmente para o seu dispositivo simulado. 
    
    Copie o primeiro certificado para a área de transferência. Começar pela primeira ocorrência de:
    
        -----BEGIN CERTIFICATE----- 
        
    Terminar a cópia após a primeira ocorrência de:
    
        -----END CERTIFICATE-----
        
    Certifique-se de que inclui também essas linhas. 

    ![Repartir a aplicação de inscrição de dispositivos](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Crie um ficheiro com o nome **_X509testcertificate.pem_** no seu computador Windows, abra-o num editor à sua escolha e copie os conteúdos da área de transferência para este ficheiro. Guarde o ficheiro. 

5. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu à esquerda e abra o seu serviço de provisionamento.

6. A partir do menu do Serviço de Fornecimento de Dispositivos, selecione **Gerir as matrículas.** Selecione o separador **Individual Registrations** e selecione o botão **de inscrição individual Adicionar** na parte superior. 

7. No painel **Adicionar Inscrições,** introduza as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - Sob o *certificado primário .pem ou .cer file,* escolha *Selecione um ficheiro* para selecionar o ficheiro de certificado **X509testcertificate.pem** criado nas etapas anteriores.
   - Opcionalmente, pode fornecer as seguintes informações:
     - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
     - Introduza um ID de dispositivo exclusivo. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
     - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Uma vez concluído, prima o botão **Guardar.** 

     [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Após a instalação bem-sucedida, o seu dispositivo X.509 aparece como **riot-device-cert** na coluna *ID de Registo* do separador *Inscrições Individuais*. 

## <a name="simulate-the-device"></a>Simular o dispositivo

1. A partir do menu do Serviço de Fornecimento de Dispositivos, selecione **visão geral**. Anote o seu _Âmbito de ID_ e _Ponto Final de Serviço Global_.

    ![Informações de serviço](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Transfira e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python às variáveis de ambiente específicas da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Se estiver a utilizar o Windows, instale também o [Visual C++ Redistributable para o Visual Studio 2015](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Os pacotes de pip requerem o redistributable para o carregar/executar as DLLs de C.

3. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) para criar os pacotes Python.

   > [!NOTE]
   > Se estiver a utilizar `pip`, certifique-se de que instala também o pacote `azure-iot-provisioning-device-client`.

4. Navegue para a pasta de exemplos.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. Através do IDE Python, edite o script python com o nome **provisioning\_device\_client\_sample.py**. Modifique as variáveis _GLOBAL\_PROV\_URI_ e _ID\_SCOPE_ para os valores anotados previamente.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Execute o exemplo. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. A aplicação irá ligar, inscrever o dispositivo e mostrar uma mensagem de inscrição com êxito.

    ![inscrição com êxito](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. No portal, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO***ativado**. Poderá ter de premir o botão **Refresh** na parte superior se já abriu a lâmina antes de executar a aplicação do dispositivo de amostra. 

    ![O dispositivo é registado no hub IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).
>

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Abra a lâmina **'Gerir as Matrículas'** para o seu serviço e, em seguida, selecione o separador Individual **Registration.** 
3. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra a lâmina dos **dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *DISPOSITIVO ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Apagar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo X.509 simulado na sua máquina Windows e aprovisionou-o no seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos Hub Azure IoT no portal. Para aprender a inscrever o seu dispositivo X.509 programáticamente, continue a acelerar para a inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivos X.509 para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-x509-python.md)
