---
title: Dispositivos legados de fornecimento usando chaves simétricas - Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como utilizar chaves simétricas para fornecer dispositivos legados com a sua instância de Serviço de Provisionamento de Dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434718"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Como fornecer dispositivos legados usando chaves simétricas

Um problema comum com muitos dispositivos legados é que muitas vezes têm uma identidade que é composta por uma única informação. Esta informação de identidade é geralmente um endereço MAC ou um número de série. Os dispositivos legados podem não ter um certificado, TPM ou qualquer outra funcionalidade de segurança que possa ser usada para identificar o dispositivo de forma segura. O Serviço de Provisionamento de Dispositivos para o hub IoT inclui o atestado de chave simétrica. O atestado da chave simétrica pode ser usado para identificar um dispositivo baseado em informações como o endereço MAC ou um número de série.

Se conseguir instalar facilmente um módulo de segurança de [hardware (HSM)](concepts-security.md#hardware-security-module) e um certificado, então essa pode ser uma melhor abordagem para identificar e fornecer os seus dispositivos. Uma vez que essa abordagem pode permitir contornar a atualização do código implementado para todos os seus dispositivos, e não teria uma chave secreta incorporada na imagem do seu dispositivo.

Este artigo pressupõe que nem um HSM ou um certificado é uma opção viável. No entanto, presume-se que tem algum método de atualização do código do dispositivo para utilizar o Serviço de Fornecimento de Dispositivos para fornecer estes dispositivos. 

Este artigo também assume que a atualização do dispositivo ocorre num ambiente seguro para impedir o acesso não autorizado à chave do grupo principal ou à chave do dispositivo derivado.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).

> [!NOTE]
> A amostra utilizada neste artigo está escrita em C. Existe também uma amostra de [chave simétrica de fornecimento de dispositivoC#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) disponível. Para utilizar esta amostra, descarregue ou clone o repositório [azure-iot-amostras-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) e siga as instruções em linha no código da amostra. Pode seguir as instruções deste artigo para criar um grupo de inscrição simétrico usando o portal e encontrar o Âmbito de Identificação e as teclas primárias e secundárias do grupo de matrícula necessárias para executar a amostra. Também pode criar inscrições individuais utilizando a amostra.

## <a name="overview"></a>Descrição geral

Será definido um ID de registo único para cada dispositivo com base em informações que identifiquem esse dispositivo. Por exemplo, o endereço MAC ou um número de série.

Será criado um grupo de inscrições que utilize atestado de [chave simétrica](concepts-symmetric-key-attestation.md) com o Serviço de Provisionamento de Dispositivos. O grupo de inscrições incluirá uma chave-mestre de grupo. Esta chave principal será usada para hash cada IDENTIFICAção de registo única para produzir uma chave única de dispositivo para cada dispositivo. O dispositivo utilizará essa chave de dispositivo derivada com o seu ID de registo único para atestar com o Serviço de Provisionamento de Dispositivos e será atribuído a um hub IoT.

O código do dispositivo demonstrado neste artigo seguirá o mesmo padrão que o [Quickstart: Fornecer um dispositivo simulado com teclas simétricas](quick-create-simulated-device-symm-key.md). O código simulará um dispositivo utilizando uma amostra do [SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). O dispositivo simulado atesta com um grupo de matrículas em vez de uma inscrição individual, como demonstrado no arranque rápido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md) arranque rápido.

Os seguintes pré-requisitos são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a secção adequada em Preparar o [seu ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) de desenvolvimento na documentação SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o 'Desenvolvimento desktop [com C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) habilitado. O Visual Studio 2015 e o Visual Studio 2017 também são apoiados.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

O SDK inclui o código de amostra para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para o [mais recente lançamento](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar o mais recente lançamento do [repositório Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior `-b` como valor para o parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes `azure-iot-sdk-c` comandos do diretório:

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Criar um grupo de inscrição simétrica

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua instância de Serviço de Provisionamento de Dispositivos.

2. Selecione o separador **'Gerir as matrículas'** e, em **seguida,** clique no botão do grupo adicionar na parte superior da página. 

3. No **Grupo adicionar inscrições,** introduza as seguintes informações e clique no botão **Guardar.**

   - **Nome do grupo**: Introduza **os mylegacydevices**.

   - **Tipo de atestado**: Selecione **a chave simétrica**.

   - **Chaves de Geração Automática**: selecione esta caixa.

   - **Selecione como pretende atribuir dispositivos a centros**: Selecione **configuração estática** para que possa atribuir a um hub específico.

   - **Selecione os hubs IoT**a que este grupo pode ser atribuído : Selecione um dos seus hubs.

     ![Adicionar grupo de inscrição para atestado de chave simétrica](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Depois de guardar a inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à entrada de inscrição. O seu grupo de inscrição de chaves simétricas aparece como **mylegacydevices** sob a coluna *Nome do Grupo* no separador *Grupos de Inscrição.* 

    Abra a inscrição e copie o valor da **Chave Primária** gerada. Esta chave é a sua chave de grupo principal.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha um ID de registo único para o dispositivo

Deve ser definido um ID de registo único para identificar cada dispositivo. Pode utilizar o endereço MAC, número de série ou qualquer informação única do dispositivo. 

Neste exemplo, utilizamos uma combinação de um endereço MAC e um número de série formando a seguinte corda para um ID de registo.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Crie um ID de registo único para o seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e traços ('-').


## <a name="derive-a-device-key"></a>Derivar uma chave do dispositivo 

Para gerar a chave do dispositivo, utilize a chave principal do grupo para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave principal do grupo no código do seu dispositivo.


#### <a name="linux-workstations"></a>Estações de trabalho linux

Se estiver a utilizar uma estação de trabalho Linux, pode utilizar o openssl para gerar a sua chave de dispositivo derivada, como mostra o seguinte exemplo.

Substitua o valor da **KEY** pela **Chave Primária** que observou anteriormente.

Substitua o valor do **REG_ID** pelo seu ID de registo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em janelas

Se estiver a utilizar uma estação de trabalho baseada no Windows, pode utilizar o PowerShell para gerar a sua chave de dispositivo derivada, como mostra o seguinte exemplo.

Substitua o valor da **KEY** pela **Chave Primária** que observou anteriormente.

Substitua o valor do **REG_ID** pelo seu ID de registo.

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


O seu dispositivo utilizará a chave do dispositivo derivado com o seu ID de registo único para realizar o atestado de chave simétrica com o grupo de matrículadurante o fornecimento.



## <a name="create-a-device-image-to-provision"></a>Criar uma imagem de dispositivo para fornecer

Nesta secção, irá atualizar uma amostra de provisionamento chamada amostra de **cliente\_\_\_prov dev** localizada no Azure IoT C SDK que configuramais cedo. 

Este código de amostra simula uma sequência de arranque do dispositivo que envia o pedido de provisionamento para a sua instância de Serviço de Provisionamento de Dispositivos. A sequência de boot fará com que o dispositivo seja reconhecido e atribuído ao centro IoT configurado no grupo de matrículas.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o ficheiro de solução **azure_iot_sdks.sln** que foi gerado pela execução de CMake anteriormente. O ficheiro de solução deve estar na seguinte localização:

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

6. Encontre a `prov_dev_set_symmetric_key_info()` chamada em **prov\_dev\_client\_sample.c** que é comentada.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descodere a chamada de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelo ID de registo único para o seu dispositivo e a chave do dispositivo derivado que gerou.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Guarde o ficheiro.

7. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 

8. No menu estúdio visual, selecione **Debug** > **Start sem depurar** para executar a solução. Na linha de comandos para recriar o projeto, clique em **Yes** (Sim) para recriar o projeto antes da execução.

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

9. No portal, navegue para o hub IoT a que o seu dispositivo simulado foi atribuído e clique no separador **IoT Devices.** Ao obter o fornecimento bem sucedido da simulação ao hub, o seu ID do dispositivo aparece na lâmina **ioT Devices,** com *status* conforme **ativado**. Poderá ter de clicar no botão **Atualizar** na parte superior. 

    ![O dispositivo é registado no hub IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Questões de segurança

Esteja ciente de que isto deixa a chave do dispositivo derivado incluída como parte da imagem, o que não é uma boa prática de segurança recomendada. Esta é uma das razões pelas quais a segurança e a facilidade de utilização são as compensações. 





## <a name="next-steps"></a>Passos seguintes

* Para aprender mais Reprovisionamento, consulte [conceitos de reprovisionamento de dispositivos IoT Hub](concepts-device-reprovision.md) 
* [Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* Para saber mais Deprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente auto-provisionados](how-to-unprovision-devices.md) 











