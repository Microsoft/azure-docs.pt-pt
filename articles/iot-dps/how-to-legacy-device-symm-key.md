---
title: Como utilizar as chaves simétricas para aprovisionar dispositivos legados com o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como utilizar as chaves simétricas para aprovisionar dispositivos legados com a sua instância do serviço de aprovisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: cf4ee8b1b9d96ad4d32a0ac8b1682968b90653f9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541495"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Como aprovisionar dispositivos legados com chaves simétricas


Um problema comum em muitos dispositivos legados é que eles têm, muitas vezes, uma identidade que é composta por uma parte das informações. Estas informações de identidade são normalmente um endereço MAC ou um número de série. Dispositivos legados não podem ter um certificado, TPM ou qualquer outra funcionalidade de segurança que pode ser utilizada para identificar com segurança o dispositivo. O serviço de aprovisionamento de dispositivos do IoT hub inclui um atestado de chave simétrico. Atestado de chave simétrico pode ser utilizado para identificar um dispositivo com base no informações como o endereço MAC ou um número de série.

Se possa instalar um [módulo de segurança de hardware (HSM)](concepts-security.md#hardware-security-module) e um certificado, em seguida, que pode ser uma melhor abordagem para identificar e aprovisionar os seus dispositivos. Uma vez que essa abordagem permite-lhe ignorar a atualizar o código implantado para todos os seus dispositivos, e não teria uma chave secreta incorporada em sua imagem do dispositivo.

Este artigo pressupõe que nem um HSM ou um certificado é uma opção viável. No entanto, é assumido que tem algum método de atualização do código de dispositivo para utilizar o serviço de aprovisionamento de dispositivos para aprovisionar estes dispositivos. 

Este artigo também presume que a atualização do dispositivo é executado num ambiente seguro para impedir acessos não autorizados para a chave mestra de grupo ou a chave de dispositivo derivada.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).


## <a name="overview"></a>Descrição geral

Um ID de registo único será definido para cada dispositivo com base nas informações que identifica que o dispositivo. Por exemplo, o endereço MAC ou um número de série.

Um grupo de inscrição que utiliza [atestado de chave simétrico](concepts-symmetric-key-attestation.md) será criado com o serviço de aprovisionamento de dispositivos. O grupo de inscrição incluirá uma chave mestra de grupo. Essa chave mestra será utilizado para cada ID de registo único para produzir uma chave de dispositivo exclusivo para cada dispositivo de hash. O dispositivo irá utilizar essa chave derivada de dispositivo com o seu ID de registo único para atestar com o serviço de aprovisionamento de dispositivos e ser atribuídos a um hub IoT.

O código de dispositivo demonstrado neste artigo seguirá o mesmo padrão a [início rápido: Aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md). O código irá simular um dispositivo com uma amostra a partir da [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). O dispositivo simulado será atestar com um grupo de inscrição em vez de uma inscrição individual, como demonstrado no guia de introdução.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [configurar o serviço aprovisionamento de dispositivos Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) início rápido.
* Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento do ambiente de trabalho em C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ativada.
* Versão mais recente do [Git](https://git-scm.com/download/) instalada.


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

O SDK inclui o código de exemplo para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

1. Transfira a versão 3.11.4 do [sistema de compilação CMake](https://cmake.org/download/). Verifique o binário transferido com o valor de hash criptográfico correspondente. O exemplo seguinte utilizou o Windows PowerShell para verificar o hash criptográfico para a versão 3.11.4 da distribuição de MSI x64:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Os seguintes valores hash para a versão 3.11.4 foram listados no site do CMake no momento da redação deste artigo:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o SDK C do Azure IoT no repositório do GitHub:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Atualmente, o tamanho deste repositório é de cerca de 220 MB. Esta operação deve demorar vários minutos a ser concluída.


3. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute o seguinte comando para compilar uma versão do SDK específica da plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`. 

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Criar um grupo de inscrição de chave simétrica

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a sua instância do serviço aprovisionamento de dispositivos.

2. Selecione o **gerir inscrições** separador e, em seguida, clique nas **adicionar grupo de inscrição** botão na parte superior da página. 

3. No **adicionar grupo de inscrição**, introduza as seguintes informações e clique nas **guardar** botão.

    - **Nome do grupo**: Introduza **mylegacydevices**.

    - **Tipo de atestado**: Selecione **chave simétrica**.

    - **Chaves de geração automática**: Esta caixa de verificação.

    - **Selecione como pretende atribuir dispositivos a hubs**: Selecione **configuração estática** para poder atribuir a um concentrador específico.

    - **Selecione os hubs IoT deste grupo pode ser atribuído a**: Selecione um dos seus hubs.

    ![Adicionar grupo de inscrição para o atestado de chave simétrico](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Depois de guardar a inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à entrada de inscrição. Seu grupo de inscrição de chave simétrica é apresentado como **mylegacydevices** sob a *nome do grupo* coluna no *grupos de inscrição* separador. 

    Abra a inscrição e copie o valor da **Chave Primária** gerada. Esta chave é a chave de grupo principal.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha um ID de registo único para o dispositivo

Um ID de registo único tem de ser definido para identificar cada dispositivo. Pode usar qualquer informação exclusiva do dispositivo, número de série ou o endereço MAC. 

Neste exemplo, usamos uma combinação de um endereço MAC e o número de série que formam a seguinte cadeia de caracteres para um ID de registo.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Crie um ID de registo único para o seu dispositivo. Carateres válidos são minúsculas, carateres alfanuméricos e de traço ('-').


## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo 

Para gerar a chave de dispositivo, utilize a chave mestra de grupo para computar um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave mestra de grupo no seu código de dispositivo.


#### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se estiver a utilizar uma estação de trabalho do Linux, pode utilizar openssl para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **chave** com o **chave primária** que anotou anteriormente.

Substitua o valor de **REG_ID** com o ID de registo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Estações de trabalho baseados em Windows

Se estiver a utilizar uma estação de trabalho baseados em Windows, pode utilizar o PowerShell para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **chave** com o **chave primária** que anotou anteriormente.

Substitua o valor de **REG_ID** com o ID de registo.

```PowerShell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```PowerShell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


O dispositivo irá utilizar a chave de dispositivo derivadas com o seu ID de registo único para executar o atestado de chave simétrico com o grupo de inscrição durante o aprovisionamento.



## <a name="create-a-device-image-to-provision"></a>Criar uma imagem do dispositivo para aprovisionamento

Nesta secção, irá atualizar um exemplo de aprovisionamento com o nome **prov\_dev\_cliente\_exemplo** localizado no SDK C do Azure IoT configurou anteriormente. 

Este código de exemplo simula uma sequência de arranque de dispositivo que envia a solicitação de provisionamento à sua instância do serviço aprovisionamento de dispositivos. A sequência de arranque fará com que o dispositivo a serem reconhecidas e atribuído ao hub IoT configurado no grupo de inscrição.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra a **azure_iot_sdks.sln** arquivo da solução que foi gerado por executar o CMake anterior. O ficheiro de solução deve estar na seguinte localização:

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

6. Encontre a chamada para `prov_dev_set_symmetric_key_info()` no **prov\_dev\_cliente\_Sample** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Anule os comentários a chamada de função e substitua os valores de marcador de posição (incluindo os parênteses angulares) com o ID de registo único para o seu dispositivo e a chave de derivada de dispositivo que gerou.

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

9. No portal, navegue até ao hub IoT ao qual o dispositivo simulado foi atribuído e clique no separador **Dispositivos IoT**. Após o aprovisionamento bem-sucedido do dispositivo simulado no hub, o ID de dispositivo aparece no painel **Dispositivos IoT**, com o *ESTADO* **ativado**. Poderá ter de clicar no botão **Atualizar** na parte superior. 

    ![O dispositivo é registado no hub IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Questões de segurança

Lembre-se de que isso deixa a chave de dispositivo derivada incluída como parte da imagem, que não é uma prática recomendada de segurança recomendadas. Este é um motivo por que segurança e facilidade de utilização são vantagens e desvantagens. 





## <a name="next-steps"></a>Passos Seguintes

* Para saber mais Reprovisioning, veja [conceitos de reprovisoning de dispositivos no Hub IoT](concepts-device-reprovision.md) 
* [Quickstart: Aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* Para saber mais desaprovisionamento, veja [como desaprovisionar os dispositivos que foram anteriormente aprovisionados](how-to-unprovision-devices.md) 











