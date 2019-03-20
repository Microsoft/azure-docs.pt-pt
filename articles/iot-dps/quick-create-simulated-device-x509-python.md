---
title: Aprovisionar um dispositivo X.509 simulado no Hub IoT do Azure através do Python | Microsoft Docs
description: Início Rápido do Azure - criar e aprovisionar um dispositivo X.509 simulado através do SDK de dispositivo Python com o Serviço Aprovisionamento de Dispositivos no Hub IoT. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 6a62e75fadad14148f5f82da3a213d4b13cbe343
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122678"
---
# <a name="create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e aprovisionar um dispositivo X.509 simulado através do SDK de dispositivo Python com o Serviço Aprovisionamento de Dispositivos no Hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estes passos mostram como simular um dispositivo X.509 no seu computador de desenvolvimento que executa o SO Windows e utilizar um código de exemplo Python para ligar esse dispositivo ao Serviço Aprovisionamento de Dispositivos e ao seu hub IoT. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): Utilizado para inscrever vários dispositivos relacionados.
- [Inscrições individuais](concepts-service.md#individual-enrollment): Utilizado para inscrever um dispositivo individual.

Este artigo vai demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) ou o [Visual Studio 2017](https://www.visualstudio.com/vs/) instalado no seu computador. Tem de ter a carga de trabalho do "Ambiente de trabalho de desenvolvimento com C++" ativada para a sua instalação do Visual Studio.

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

5. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o seu serviço de aprovisionamento.

6. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique no botão **Adicionar inscrição individual** na parte superior. 

7. No painel **Adicionar Inscrição**, introduza as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - Em *Ficheiro de certificado primário .pem ou .cer*, clique em *Selecionar um ficheiro* para selecionar o ficheiro de certificado **X509testcertificate.pem** criado nos passos anteriores.
   - Opcionalmente, pode fornecer as seguintes informações:
     - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
     - Introduza um ID de dispositivo exclusivo. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
     - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Quando tiver terminado, clique no botão **Guardar**. 

     [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Após a instalação bem-sucedida, o seu dispositivo X.509 aparece como **riot-device-cert** na coluna *ID de Registo* do separador *Inscrições Individuais*. 

## <a name="simulate-the-device"></a>Simular o dispositivo

1. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Descrição geral**. Anote o seu _Âmbito de ID_ e _Ponto Final de Serviço Global_.

    ![Informações de serviço](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Transfira e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python às variáveis de ambiente específicas da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Se estiver a utilizar o Windows, instale também o [Visual C++ Redistributable para o Visual Studio 2015](https://www.microsoft.com/download/confirmation.aspx?id=48145). Os pacotes de pip requerem o redistributable para o carregar/executar as DLLs de C.

3. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) para criar os pacotes Python.

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

8. No portal, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO* **ativado**. Poderá ter de clicar no botão **Atualizar**, na parte superior, se já tiver aberto o painel antes de executar a aplicação de dispositivo de exemplo. 

    ![O dispositivo é registado no hub IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).
>

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar e a explorar o exemplo de cliente do dispositivo, não limpe os recursos criados neste Guia Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Abra o painel **Gerir Inscrições** para o seu serviço e, em seguida, clique no separador **Inscrições Individuais**. Selecione o *ID DE REGISTO* do dispositivo que inscreveu neste Início Rápido e clique no botão **Eliminar** na parte superior. 
3. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Abra o painel **Dispositivos IoT** do seu hub, selecione o *ID DE DISPOSITIVO* do dispositivo que registou neste Início Rápido e, em seguida, clique no botão **Eliminar** Na parte superior.

## <a name="next-steps"></a>Passos Seguintes

e para o hub IoT ligado ao seu serviçNeste Início Rápido, criou um dispositivo X.509 simulado no seu computador Windows e aprovisionou-o no seu hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure no portal. Para saber como inscrever o seu dispositivo X.509 programaticamente, continue para o Manual de Início Rápido para inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Manual de Início Rápido do Azure - Inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-x509-python.md)
