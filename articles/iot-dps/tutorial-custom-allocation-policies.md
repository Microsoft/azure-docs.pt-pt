---
title: Tutorial para a utilização de políticas de atribuição personalizadas com serviço de fornecimento de dispositivos de hub Azure IoT (DPS)
description: Tutorial para a utilização de políticas de atribuição personalizada com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 4cab1765a387bbae61c9c242a8e7a1ca881ea1f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966671"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Tutorial: Utilize políticas de atribuição personalizada com serviço de fornecimento de dispositivos (DPS)

Uma política de atribuição personalizada dá-lhe mais controlo sobre como os dispositivos são atribuídos a um hub IoT. Isto é conseguido utilizando código personalizado numa [Função Azure](../azure-functions/functions-overview.md) que funciona durante o fornecimento para atribuir dispositivos a um hub IoT. O serviço de fornecimento de dispositivos chama o seu código Azure Function fornecendo todas as informações relevantes sobre o dispositivo e a inscrição. O seu código de função é executado e devolve as informações do hub IoT utilizadas para o fornecimento do dispositivo.

Ao utilizar políticas de alocação personalizadas, define as suas próprias políticas de alocação quando as políticas fornecidas pelo Serviço de Provisionamento de Dispositivos não satisfazem os requisitos do seu cenário.

Por exemplo, talvez queira examinar o certificado que um dispositivo está a usar durante o fornecimento e atribuir o dispositivo a um hub IoT baseado numa propriedade de certificado. Ou, talvez tenha informações armazenadas numa base de dados para os seus dispositivos e precise de consultar a base de dados para determinar a que hub IoT um dispositivo deve ser atribuído.

Este artigo demonstra um grupo de inscrição com uma política de atribuição personalizada que utiliza uma Função Azure escrita em C# para a disponibilização de dispositivos de torradeira utilizando chaves simétricas. Qualquer dispositivo não reconhecido como um dispositivo de torradeira não será a provisionado num hub IoT.

Os dispositivos solicitarão o provisionamento utilizando o código de amostra de provisionamento incluído no [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).


Neste tutorial fará o seguinte:

> [!div class="checklist"]
> * Criar uma nova App de função Azure para suportar uma função de atribuição personalizada
> * Criar uma nova inscrição em grupo utilizando uma Função Azure para a política de atribuição personalizada
> * Criar chaves de dispositivo para dois dispositivos
> * Configurar o ambiente de desenvolvimento, por exemplo, código de dispositivo a partir do [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Executar os dispositivos e verificar se são a provisionados de acordo com a política de atribuição personalizada


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pressupõe que completou os passos no [Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md) para criar o seu IoT Hub e dPS.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

* Para um ambiente de desenvolvimento do Windows, é necessário o [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o desenvolvimento do 'Desktop com carga de trabalho [C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ativado. Visual Studio 2015 e Visual Studio 2017 também são suportados.

* Para Linux ou macOS, consulte a secção apropriada em Preparar o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Criar a função de atribuição personalizada

Nesta secção, cria-se uma função Azure que implementa a sua política de atribuição personalizada. Esta função decide se um dispositivo deve ser registado no seu IoT Hub com base no facto de o seu ID de registo conter a **torradeira de prefixo de** cordas .

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Na sua página inicial, selecione **+ Criar um recurso.**

2. Na caixa de pesquisa *do Marketplace,* escreva "App de função". A partir da lista de drop-down selecione **App de função** e, em seguida, selecione **Criar**.

3. Na **página de criação da App de Função,** no separador **Básicos,** introduza as seguintes definições para a sua nova aplicação de função e selecione Review **+ create**:

    **Subscrição**: Se tiver várias subscrições e a subscrição desejada não for selecionada, selecione a subscrição que pretende utilizar.

    **Grupo de Recursos**: Este campo permite-lhe criar um novo grupo de recursos, ou escolher um existente para conter a aplicação de função. Escolha o mesmo grupo de recursos que contém o hub Iot que criou para testes anteriormente, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados num grupo, pode geri-los em conjunto.

    **Função Nome da aplicação**: Introduza um nome de aplicação de função única. Este exemplo utiliza **contoso-function-app**.

    **Publicar:** Verifique se o **Código** está selecionado.

    **Pilha de tempo de** execução : Selecione **.NET Core** a partir do drop-down.

    **Região**: Selecione a mesma região que o seu grupo de recursos. Este exemplo usa **o Oeste dos EUA.**

    > [!NOTE]
    > Por predefinição, o Application Insights está ativado. A Application Insights não é necessária para este artigo, mas pode ajudá-lo a compreender e investigar quaisquer problemas que encontre com a alocação personalizada. Se preferir, pode desativar insights de aplicação selecionando o **separador Monitoring** e, em seguida, selecionando **O** para **Ativar insights de aplicação**.

    ![Criar uma App de função Azure para acolher a função de atribuição personalizada](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Na página **Resumo,** selecione **Criar** para criar a aplicação de função. A implementação poderá demorar vários minutos. Quando estiver concluído, selecione **Ir para o recurso**.

5. No painel esquerdo em **Funções** clique em **Funções** e, em seguida, **+ Adicione** para adicionar uma nova função.

6. Na página de modelos, selecione o azulejo **HTTP Trigger** e, em seguida, selecione **Criar Função**. É criada uma função chamada **HttpTrigger1** e o portal apresenta a página de visão geral para a sua função.

7. Clique **em Código + Teste** para a sua nova função. O portal apresenta o conteúdo do ficheiro de código **run.csx.** 

8. Substitua o código da função **HttpTrigger1** pelo seguinte código e selecione **Guardar**. O seu código de atribuição personalizado está pronto para ser utilizado.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. Logo abaixo da parte inferior do ficheiro de código **run.csx,** clique em **Logs** para monitorizar o registo a partir da função de atribuição personalizada. 


## <a name="create-the-enrollment"></a>Criar a inscrição

Nesta secção, você vai criar um novo grupo de inscrições que usa a política de alocação personalizada. Para simplificar, este artigo utiliza [a chave simétrica atestado](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere usar [o atestado de certificado X.509](concepts-x509-attestation.md) com uma cadeia de confiança.

1. Ainda no [portal Azure,](https://portal.azure.com)abra o seu serviço de prestação.

2. **Selecione Gerir as inscrições** no painel esquerdo e, em seguida, selecione o botão **de grupo de inscrição Add** no topo da página.

3. No **Grupo de Inscrição adicionar,** introduza as informações na tabela abaixo e clique no botão **Guardar.**

    | Campo | Descrição e/ou valor sugerido |
    | :---- | :----------------------------- |
    | **Nome do grupo** | **Insira os dispositivos contoso-personalizados atribuídos** |
    | **Tipo de Attestation** | **Selecione Chave Simétrica** |
    | **Chaves de geração automática** | Esta caixa de verificação já deve ser verificada. |
    | **Selecione como pretende atribuir dispositivos a centros** | Selecione **Personalizado (Use Azure Function)** |
    | **Selecione os hubs IoT este grupo pode ser atribuído** | Selecione o hub IoT que criou anteriormente quando terminou o arranque rápido. |
    | **Selecione Azure Function** | Selecione a subscrição que contém a aplicação de função que criou. Em seguida, selecione a **aplicação contoso-function-app** e **httpTrigger1** para a função. |

    ![Adicione o grupo de inscrição de atribuição personalizada para atestado de chave simétrica](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Depois de guardar a inscrição, reabre-a e tome nota da **Chave Primária.** Tem de guardar a inscrição primeiro para ter as chaves geradas. Esta chave simétrica primária será usada para gerar chaves de dispositivo únicas para dispositivos que tentem provisões mais tarde. 

## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo únicas

Os dispositivos não usam diretamente a chave simétrica primária. Em vez disso, utiliza a chave primária para obter uma chave de dispositivo para cada dispositivo. Nesta secção, cria-se duas chaves de dispositivo únicas. Uma chave será utilizada para um dispositivo de torradeira simulado. A outra chave será utilizada para um dispositivo simulado de bomba de calor. As teclas geradas permitirão que ambos os dispositivos tentem um registo. Apenas um ID de registo de dispositivo terá um sufixo válido para ser aceite pelo código de exemplo de política de atribuição personalizada. Como resultado, um será aceite e o outro rejeitado

Para obter a chave do dispositivo, utilize a chave simétrica que observou anteriormente para calcular o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo do dispositivo para cada dispositivo e converter o resultado em formato Base64. Para obter mais informações sobre a criação de chaves de dispositivos derivados com grupos de matrícula, consulte a secção de matrículas de grupo [do atestado de chave simétrica](concepts-symmetric-key-attestation.md).

Por exemplo, neste artigo, utilize os dois IDs de registo do dispositivo com o código abaixo para calcular uma chave de dispositivo para ambos os dispositivos:

* **contoso-torradeira-007**
* **contoso-heatpump-088**

Substitua o valor da variável **KEY** pela **Chave Primária** que notou anteriormente após a criação do seu grupo de inscrição. O valor e saída chave mostrados com o código abaixo é apenas um exemplo.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Se estiver a utilizar uma estação de trabalho baseada no Windows, pode utilizar o PowerShell para gerar a chave do dispositivo derivado, como mostra o exemplo a seguir.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Se estiver a utilizar uma estação de trabalho Linux, pode utilizar o openssl para gerar as teclas do dispositivo derivado, como mostra o exemplo bash a seguir.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Os dispositivos solicitarão o provisionamento utilizando o código de amostra de provisionamento incluído no [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

Nesta secção, você prepara o ambiente de desenvolvimento usado para construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de amostra para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

Esta secção é orientada para uma estação de trabalho baseada no Windows. Para um exemplo linux, consulte a configuração dos VMs em [Como prever a multitenência](how-to-provision-multitenant.md).

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes comandos a partir do `azure-iot-sdk-c` diretório:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Execute o seguinte comando para compilar uma versão do SDK específica da plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Se `cmake` não encontrar o seu compilador C++, poderá obter erros de construção durante a execução do comando. Se isso acontecer, tente executar o comando no [comando do Estúdio Visual.](/dotnet/framework/tools/developer-command-prompt-for-vs)

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

## <a name="simulate-the-devices"></a>Simular os dispositivos

Nesta secção, você atualizou uma amostra de **provisão chamada prov \_ dev \_ client \_ sample** localizado no Azure IoT C SDK que você configura anteriormente.

Este código de amostra simula uma sequência de arranque do dispositivo que envia o pedido de provisionamento para a sua instância de Serviço de Provisionamento de Dispositivos. A sequência de arranque fará com que o dispositivo da torradeira seja reconhecido e atribuído ao hub IoT utilizando a política de atribuição personalizada.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra **o** azure_iot_sdks.sln ficheiro de solução que foi gerado pela execução do CMake anteriormente. O ficheiro de solução deve estar na seguinte localização:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. Na `main()` função, encontre a chamada para `Prov_Device_Register_Device()` . Antes dessa chamada, adicione as seguintes linhas de código que usam [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) para passar uma carga personalizada do JSON durante o provisionamento. Isto pode ser usado para fornecer mais informações às suas funções de atribuição personalizada. Isto também poderia ser usado para passar o tipo de dispositivo em vez de examinar o ID de registo.

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**.

### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de torradeira Contoso

1. Para simular o dispositivo da torradeira, encontre a chamada na `prov_dev_set_symmetric_key_info()` **amostra do cliente prov \_ \_ dev.c \_** que é comentada.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descompromete a chamada de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelo ID de registo da torradeira e pela chave do dispositivo derivado que gerou anteriormente. O valor-chave **JC8F96eayuQwz+PkE7IzjH2lIAjCUnAaa61tDigBnSs=** mostrado abaixo é dado como exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Guarde o ficheiro.

2. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Na pronta para reconstruir o projeto, selecione **Sim,** para reconstruir o projeto antes de correr.

    O texto a seguir é o exemplo de registo da saída do código de função de atribuição personalizada em execução para o dispositivo da torradeira. Note que um hub é selecionado com sucesso para um dispositivo de torradeira. Note também o `payload` membro que contém o conteúdo JSON personalizado que adicionou ao código. Isto está disponível para o seu código para usar dentro do `deviceRuntimeContext` .

    Esta sessão está disponível clicando em **Registos** no código de função no portal:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    A saída do dispositivo por exemplo mostra que o dispositivo de torradeira simulado foi iniciado com sucesso e conectando-se à instância de serviço de fornecimento a atribuir ao hub IoT das torradeiras pela política de atribuição personalizada:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de bomba de calor Contoso

1. Para simular o dispositivo da bomba de calor, atualize a chamada para a `prov_dev_set_symmetric_key_info()` **amostra do cliente prov \_ \_ dev.c \_** novamente com o ID de registo da bomba de calor e a chave do dispositivo derivado que gerou anteriormente. O valor-chave **6uejA9PfkQgmYlj8Zerp3kcbeVrGZ172YLa7VSnJzg=** mostrado abaixo também é dado como exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Guarde o ficheiro.

2. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Na pronta para reconstruir o projeto, selecione **Sim** para reconstruir o projeto antes de executar.

    O texto a seguir é o exemplo de registo da saída do código de função de atribuição personalizada em execução para o dispositivo da bomba de calor. A política de atribuição personalizada rejeita este registo com um erro HTTP 400 Bad Request. Note o `payload` membro que contém o conteúdo JSON personalizado que adicionou ao código. Isto está disponível para o seu código para usar dentro do `deviceRuntimeContext` .

    Esta sessão está disponível clicando em **Registos** no código de função no portal:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    A saída do dispositivo de exemplo mostra o dispositivo simulado da bomba de calor a arrancar e a ligar-se à instância de serviço de fornecimento para tentar o registo de um hub IoT utilizando a política de atribuição personalizada. Isto falha com erro `Custom allocation failed with status code: 400` () uma vez que a política de atribuição personalizada foi concebida apenas para permitir dispositivos de torradeira:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar com os recursos criados neste artigo, pode deixá-los. Se não pretender continuar a utilizar os recursos, use os seguintes passos para eliminar todos os recursos criados neste artigo para evitar encargos desnecessários.

Os passos aqui pressupõem que criou todos os recursos neste artigo, tal como instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para eliminar o grupo de recursos pelo nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. No **Filtro pelo nome...** textbox, digite o nome do grupo de recursos que contém os seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos **...** na lista de resultados, selecione... e depois **elimine o grupo de recursos**.

4. Ser-lhe-á pedido que confirme a supressão do grupo de recursos. Escreva novamente o nome do seu grupo de recursos para confirmar e, em seguida, selecione **Delete**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo de política de atribuição personalizada mais aprofundado, consulte [como utilizar políticas de atribuição personalizadas](how-to-use-custom-allocation-policies.md).
* Para saber mais reprovisionamento, consulte [conceitos de reprovisionamento do IoT Hub Device](concepts-device-reprovision.md).
* Para saber mais desprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente autoprovisionados](how-to-unprovision-devices.md).