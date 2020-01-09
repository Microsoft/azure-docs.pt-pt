---
title: Provisionar dispositivos herdados usando chaves simétricas – serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como usar chaves simétricas para provisionar dispositivos herdados com sua instância do serviço de provisionamento de dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434718"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Como provisionar dispositivos herdados usando chaves simétricas

Um problema comum com muitos dispositivos herdados é que eles geralmente têm uma identidade composta por uma única informação. Normalmente, essas informações de identidade são um endereço MAC ou um número de série. Os dispositivos herdados podem não ter um certificado, TPM ou qualquer outro recurso de segurança que possa ser usado para identificar o dispositivo com segurança. O serviço de provisionamento de dispositivos para o Hub IoT inclui atestado de chave simétrica. O atestado de chave simétrica pode ser usado para identificar um dispositivo com base em informações como o endereço MAC ou um número de série.

Se você puder instalar facilmente um [HSM (módulo de segurança de hardware)](concepts-security.md#hardware-security-module) e um certificado, isso poderá ser uma abordagem melhor para identificar e provisionar seus dispositivos. Como essa abordagem pode permitir que você ignore a atualização do código implantado em todos os seus dispositivos, e você não teria uma chave secreta inserida na imagem do dispositivo.

Este artigo pressupõe que nem um HSM ou um certificado é uma opção viável. No entanto, supõe-se que você tenha algum método de atualizar o código do dispositivo para usar o serviço de provisionamento de dispositivos para provisionar esses dispositivos. 

Este artigo também pressupõe que a atualização do dispositivo ocorra em um ambiente seguro para impedir o acesso não autorizado à chave do grupo mestre ou à chave do dispositivo derivado.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).

> [!NOTE]
> O exemplo usado neste artigo é escrito em C. Também há um [ C# exemplo de chave simétrica de provisionamento de dispositivos](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) disponível. Para usar este exemplo, baixe ou clone o repositório [Azure-IOT-Samples-Csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) e siga as instruções embutidas no código de exemplo. Você pode seguir as instruções neste artigo para criar um grupo de registro de chave simétrica usando o portal e para localizar o escopo da ID e as chaves primárias e secundárias necessárias para executar o exemplo. Você também pode criar registros individuais usando o exemplo.

## <a name="overview"></a>Visão geral

Uma ID de registro exclusiva será definida para cada dispositivo com base nas informações que identificam esse dispositivo. Por exemplo, o endereço MAC ou um número de série.

Um grupo de registro que usa o [atestado de chave simétrica](concepts-symmetric-key-attestation.md) será criado com o serviço de provisionamento de dispositivos. O grupo de registro incluirá uma chave mestra de grupo. Essa chave mestra será usada para aplicar o hash a cada ID de registro exclusiva para produzir uma chave de dispositivo exclusiva para cada dispositivo. O dispositivo usará essa chave de dispositivo derivada com sua ID de registro exclusiva para atestar o serviço de provisionamento de dispositivos e ser atribuído a um hub IoT.

O código do dispositivo demonstrado neste artigo seguirá o mesmo padrão que o [início rápido: provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md). O código simulará um dispositivo usando um exemplo do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c). O dispositivo simulado atestará com um grupo de registro em vez de um registro individual, conforme demonstrado no guia de início rápido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do serviço de provisionamento de dispositivos no Hub IOT com o guia de início rápido do portal do Azure](./quick-setup-auto-provision.md) .

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a seção apropriada em [preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK do.

* O [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com a carga de [trabalho C++' desenvolvimento de desktop com '](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

O SDK inclui o código de exemplo para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

1. Baixe o [sistema de Build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Localize o nome da marca para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Execute os comandos a seguir para clonar a versão mais recente do repositório GitHub do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) . Use a marca que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Execute os seguintes comandos no diretório `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Execute o seguinte comando para compilar uma versão do SDK específica da plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Se `cmake` não encontrar o compilador de C++, poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Criar um grupo de registro de chave simétrica

1. Entre no [portal do Azure](https://portal.azure.com)e abra a instância do serviço de provisionamento de dispositivos.

2. Selecione a guia **gerenciar registros** e, em seguida, clique no botão **Adicionar grupo de registros** na parte superior da página. 

3. Em **Adicionar grupo de registro**, insira as informações a seguir e clique no botão **salvar** .

   - **Nome do grupo**: insira **mylegacydevices**.

   - **Tipo de atestado**: selecione **chave simétrica**.

   - **Chaves de Geração Automática**: selecione esta caixa.

   - **Selecione como você deseja atribuir dispositivos a hubs**: selecione **configuração estática** para que você possa atribuir a um hub específico.

   - **Selecione os hubs IOT aos quais este grupo pode ser atribuído**: selecione um dos hubs.

     ![Adicionar grupo de registro para atestado de chave simétrica](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Depois de guardar a inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à entrada de inscrição. O grupo de registro de chave simétrica aparece como **mylegacydevices** na coluna *nome do grupo* na guia *grupos de registro* . 

    Abra a inscrição e copie o valor da **Chave Primária** gerada. Essa é a chave do grupo mestre.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha uma ID de registro exclusiva para o dispositivo

Uma ID de registro exclusiva deve ser definida para identificar cada dispositivo. Você pode usar o endereço MAC, o número de série ou qualquer informação exclusiva do dispositivo. 

Neste exemplo, usamos uma combinação de um endereço MAC e um número de série que formam a cadeia de caracteres a seguir para uma ID de registro.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Crie uma ID de registro exclusiva para seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e Dash ('-').


## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo 

Para gerar a chave do dispositivo, use a chave mestra de grupo para computar um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro exclusiva do dispositivo e converter o resultado no formato base64.

Não inclua sua chave mestra de grupo em seu código de dispositivo.


#### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se você estiver usando uma estação de trabalho do Linux, poderá usar o OpenSSL para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela sua ID de registro.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Estações de trabalho baseadas no Windows

Se você estiver usando uma estação de trabalho baseada no Windows, poderá usar o PowerShell para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela sua ID de registro.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Seu dispositivo usará a chave de dispositivo derivada com sua ID de registro exclusiva para executar o atestado de chave simétrica com o grupo de registro durante o provisionamento.



## <a name="create-a-device-image-to-provision"></a>Criar uma imagem de dispositivo para provisionamento

Nesta seção, você atualizará um exemplo de provisionamento chamado **prov\_dev\_cliente\_exemplo** localizado no SDK do Azure IOT C que você configurou anteriormente. 

Este código de exemplo simula uma sequência de inicialização de dispositivo que envia a solicitação de provisionamento para a instância do serviço de provisionamento de dispositivos. A sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído ao Hub IoT configurado no grupo de registro.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o arquivo de solução **azure_iot_sdks. sln** que foi gerado pela execução do cmake anteriormente. O ficheiro de solução deve estar na seguinte localização:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Na janela *Solution Explorer* (Explorador de Soluções) do Visual Studio, navegue para a pasta **Provision\_Samples**. Expanda o projeto de exemplo com o nome **prov\_dev\_client\_sample**. Expanda **Source Files** (Ficheiros de Origem) e abra **prov\_dev\_client\_sample.c**.

4. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, conforme mostrado abaixo:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_cliente\_exemplo. c** , que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova a marca de comentário da chamada de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) pela ID de registro exclusiva para seu dispositivo e a chave de dispositivo derivada gerada.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Guarde o ficheiro.

7. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 

8. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. Na linha de comandos para recriar o projeto, clique em **Yes** (Sim) para recriar o projeto antes da execução.

    O resultado seguinte é um exemplo do dispositivo simulado a arrancar com êxito e a ligar à instância do serviço de aprovisionamento para atribuição a um hub IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. No portal, navegue até o Hub IoT ao qual o dispositivo simulado foi atribuído e clique na guia **dispositivos IOT** . No provisionamento bem-sucedido da Simulated para o Hub, sua ID de dispositivo aparece na folha **dispositivos IOT** , com *status* como **habilitado**. Poderá ter de clicar no botão **Atualizar** na parte superior. 

    ![O dispositivo é registado no hub IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Questões de segurança

Lembre-se de que isso deixa a chave de dispositivo derivada incluída como parte da imagem, que não é uma prática recomendada de segurança. Essa é uma razão pela qual a segurança e a facilidade de uso são compensações. 





## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md) 
* [Início rápido: provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* Para saber mais sobre desprovisionamento, confira [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md) 











