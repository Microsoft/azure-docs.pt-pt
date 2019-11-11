---
title: Como usar políticas de alocação personalizadas com o serviço de provisionamento de dispositivos no Hub IoT do Azure | Microsoft Docs
description: Como usar políticas de alocação personalizadas com o serviço de provisionamento de dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 11872f8efcebf39edef2f97cd30c225edbe74bb4
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903573"
---
# <a name="how-to-use-custom-allocation-policies"></a>Como usar políticas de alocação personalizadas

Uma política de alocação personalizada oferece mais controle sobre como os dispositivos são atribuídos a um hub IoT. Isso é feito usando código personalizado em uma [função do Azure](../azure-functions/functions-overview.md) para atribuir dispositivos a um hub IOT. O serviço de provisionamento de dispositivos chama o código de função do Azure, fornecendo todas as informações relevantes sobre o dispositivo e o registro. O código de função é executado e retorna as informações do Hub IoT usadas para provisionar o dispositivo.

Usando políticas de alocação personalizadas, você define suas próprias políticas de alocação quando as políticas fornecidas pelo serviço de provisionamento de dispositivos não atendem aos requisitos do seu cenário.

Por exemplo, talvez você queira examinar o certificado que um dispositivo está usando durante o provisionamento e atribuir o dispositivo a um hub IoT com base em uma propriedade de certificado. Ou, talvez você tenha informações armazenadas em um banco de dados para seus dispositivos e precise consultar o banco de dados para determinar a qual Hub IoT um dispositivo deve ser atribuído.

Este artigo demonstra uma política de alocação personalizada usando uma função do Azure C#gravada no. Dois novos hubs IoT são criados representando uma *divisão de torradeiras da Contoso* e uma *divisão de bombas de calor da Contoso*. Os dispositivos que solicitam provisionamento devem ter uma ID de registro com um dos seguintes sufixos a serem aceitos para provisionamento:

* **-contoso-tstrsd-007**: divisão dos torradeiras da contoso
* **-contoso-hpsd-088**: Divisão de bombas de calor da contoso

Os dispositivos serão provisionados com base em um desses sufixos necessários na ID de registro. Esses dispositivos serão simulados usando um exemplo de provisionamento incluído no [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c).

Você executa as seguintes etapas neste artigo:

* Usar o CLI do Azure para criar dois hubs IoT de divisão da Contoso (**divisão de torradeiras da Contoso** e **divisão de bombas de calor da Contoso**)
* Criar um novo registro de grupo usando uma função do Azure para a política de alocação personalizada
* Crie chaves de dispositivo para duas simulações de dispositivo.
* Configurar o ambiente de desenvolvimento para o SDK do Azure IoT C
* Simular os dispositivos e verificar se eles são provisionados de acordo com o código de exemplo na política de alocação personalizada

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do serviço de provisionamento de dispositivos no Hub IOT com o guia de início rápido do portal do Azure](./quick-setup-auto-provision.md) .
* O [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior com a carga de [trabalho C++' desenvolvimento de desktop com '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Criar dois hubs IoT de divisões

Nesta seção, você usa o Azure Cloud Shell para criar dois novos hubs IoT que representam a **divisão de torradeiras da Contoso** e a **divisão de bombas de aquecimento da Contoso**.

1. Use o Azure Cloud Shell para criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group#az-group-create) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

    O exemplo a seguir cria um grupo de recursos chamado *contoso-US-Resource-Group* na região *eastus* . É recomendável que você use esse grupo para todos os recursos criados neste artigo. Essa abordagem facilitará a limpeza depois que você terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use o Azure Cloud Shell para criar o Hub IOT da **divisão de torradeiras da Contoso** com o comando [AZ IOT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . O Hub IoT será adicionado ao *contoso-US-Resource-Group*.

    O exemplo a seguir cria um hub IoT chamado *contoso-torradeirars-Hub-1098* no local *eastus* . Você deve usar um nome de Hub exclusivo. Crie seu próprio sufixo no nome do Hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-toasters-` no nome do Hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Esse comando pode levar alguns minutos para ser concluído.

3. Use o Azure Cloud Shell para criar o Hub IOT da **divisão de bombas de calor da Contoso** com o comando [AZ IOT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Esse Hub IoT também será adicionado ao *contoso-US-Resource-Group*.

    O exemplo a seguir cria um hub IoT chamado *contoso-heatpumps-Hub-1098* na localização *lesteus* . Você deve usar um nome de Hub exclusivo. Crie seu próprio sufixo no nome do Hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-heatpumps-` no nome do Hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Esse comando também pode levar alguns minutos para ser concluído.

## <a name="create-the-enrollment"></a>Criar o registro

Nesta seção, você criará um novo grupo de registro que usa a política de alocação personalizada. Para simplificar, este artigo usa o [atestado de chave simétrica](concepts-symmetric-key-attestation.md) com o registro. Para obter uma solução mais segura, considere o uso do [atestado de certificado X. 509](concepts-security.md#x509-certificates) com uma cadeia de confiança.

1. Entre no [portal do Azure](https://portal.azure.com)e abra a instância do serviço de provisionamento de dispositivos.

2. Selecione a guia **gerenciar registros** e, em seguida, clique no botão **Adicionar grupo de registros** na parte superior da página. 

3. Em **Adicionar grupo de registro**, insira as informações a seguir e clique no botão **salvar** .

    **Nome do grupo**: insira **contoso-Custom-allocated-Devices**.

    **Tipo de atestado**: selecione **chave simétrica**.

    **Gerar chaves automaticamente**: essa caixa de seleção já deve estar marcada.

    **Selecione como você deseja atribuir dispositivos a hubs**: selecione **personalizado (usar função do Azure)** .

    ![Adicionar grupo de registro de alocação personalizada para atestado de chave simétrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Em **Adicionar grupo de registro**, clique em **vincular um novo hub IOT** para vincular os dois hubs IOT de sua nova configuração. 

    Execute esta etapa para os dois hubs IoT de sua divisão.

    **Assinatura**: se você tiver várias assinaturas, escolha a assinatura em que você criou os hubs IOT de divisões.

    **Hub IOT**: selecione um dos hubs de divisões que você criou.

    **Política de acesso**: escolha **iothubowner**.

    ![Vincular os hubs IoT de divisões ao serviço de provisionamento](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Em **Adicionar grupo de registro**, depois que ambos os hubs IOT de divisão tiverem sido vinculados, você deverá selecioná-los como o grupo de Hub IOT para o grupo de registro, conforme mostrado abaixo:

    ![Criar o grupo de hubs de divisões para o registro](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. Em **Adicionar grupo de registro**, role para baixo até a seção **selecionar função do Azure** e clique em **criar um novo aplicativo de funções**.

7. Na **aplicativo de funções** criar página que é aberta, insira as seguintes configurações para sua nova função e clique em **criar**:

    **Nome do aplicativo**: Insira um nome de aplicativo de função exclusivo. **contoso-function-app-1098** é mostrado como um exemplo.

    **Grupo de recursos**: selecione **usar existente** e o **grupo de recursos contoso-US-Resource** para manter todos os recursos criados neste artigo juntos.

    **Application insights**: para este exercício, você pode desativar essa opção.

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/function-app-create.png)

8. De volta à página **Adicionar grupo de registros** , verifique se o novo aplicativo de funções está selecionado. Talvez seja necessário selecionar novamente a assinatura para atualizar a lista de aplicativos de funções.

    Depois que o novo aplicativo de funções for selecionado, clique em **criar uma nova função**.

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    seu novo aplicativo de funções será aberto.

9. Em seu aplicativo de funções, clique em **+** para criar uma nova função

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/new-function.png)

    Para a nova função, use as configurações padrão para criar uma nova **API webhook +** usando o idioma **Csharp** . Clique em **criar esta função**.

    Isso cria uma nova C# função chamada **HttpTriggerCSharp1**.

10. Substitua o código da nova C# função pelo código a seguir e clique em **salvar**:

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```

11. Volte para a página **Adicionar grupo de registros** e verifique se a nova função está selecionada. Talvez seja necessário selecionar novamente o aplicativo de função para atualizar a lista de funções.

    Quando a nova função for selecionada, clique em **salvar** para salvar o grupo de registros.

    ![Por fim, salve o grupo de registros](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

12. Depois de salvar o registro, reabra-o e anote a **chave primária**. Você deve salvar o registro primeiro para que as chaves sejam geradas. Essa chave será usada para gerar chaves de dispositivo exclusivas para dispositivos simulados posteriormente.

## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo exclusivas

Nesta seção, você criará duas chaves de dispositivo exclusivas. Uma chave será usada para um dispositivo de torradeira simulado. A outra chave será usada para um dispositivo de bomba de calor simulado.

Para gerar a chave do dispositivo, use a **chave primária** que você anotou anteriormente para calcular o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro do dispositivo para cada dispositivo e converta o resultado no formato base64. Para obter mais informações sobre como criar chaves de dispositivo derivadas com grupos de registro, consulte a seção registros de grupo de [atestado de chave simétrica](concepts-symmetric-key-attestation.md).

Para o exemplo neste artigo, use as duas IDs de registro de dispositivo a seguir e Compute uma chave de dispositivo para ambos os dispositivos. Ambas as IDs de registro têm um sufixo válido para trabalhar com o código de exemplo para a política de alocação personalizada:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se você estiver usando uma estação de trabalho do Linux, poderá usar o OpenSSL para gerar as chaves de dispositivo derivadas, conforme mostrado no exemplo a seguir.

1. Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas no Windows

Se você estiver usando uma estação de trabalho baseada no Windows, poderá usar o PowerShell para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

1. Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

Os dispositivos simulados usarão as chaves de dispositivo derivadas com cada ID de registro para executar o atestado de chave simétrica.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta seção, você prepara o ambiente de desenvolvimento usado para criar o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de exemplo para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

Esta seção é orientada para uma estação de trabalho baseada no Windows. Para um exemplo do Linux, consulte a configuração das VMs em [como provisionar para multilocação](how-to-provision-multitenant.md).

1. Baixe o [sistema de Build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (o Visual Studio e a carga de trabalho ' C++desenvolvimento de desktops com ') estejam instalados em seu computador **antes** de iniciar a instalação do `CMake`. Depois que os pré-requisitos estiverem em vigor e o download for verificado, instale o sistema de compilação CMake.

2. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o SDK C do Azure IoT no repositório do GitHub:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Esta operação deve demorar vários minutos a ser concluída.

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

    Se `cmake` não encontrar seu C++ compilador, você poderá obter erros de compilação ao executar o comando. Se isso acontecer, tente executar o comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

Nesta seção, você atualiza um exemplo de provisionamento chamado **prov\_dev\_cliente\_exemplo** localizado no SDK do Azure IOT C que você configurou anteriormente.

Este código de exemplo simula uma sequência de inicialização de dispositivo que envia a solicitação de provisionamento para a instância do serviço de provisionamento de dispositivos. A sequência de inicialização fará com que o dispositivo de torradeira seja reconhecido e atribuído ao Hub IoT usando a política de alocação personalizada.

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

6. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 

### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de torradeira da contoso

1. Para simular o dispositivo do torradeira, localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_cliente\_exemplo. c** , que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova a marca de comentário da chamada de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) com a ID de registro do torradeira e a chave de dispositivo derivada gerada anteriormente. O valor de chave **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** mostrado abaixo só é fornecido como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Guarde o ficheiro.

2. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. Na linha de comandos para recriar o projeto, clique em **Yes** (Sim) para recriar o projeto antes da execução.

    A saída a seguir é um exemplo do dispositivo de torradeira simulado inicializado com êxito e conectando-se à instância do serviço de provisionamento a ser atribuída ao Hub IoT dos torradeiras pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de bomba de calor da contoso

1. Para simular o dispositivo de bomba de calor, atualize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_cliente\_Sample. c** novamente com a ID de registro da bomba de calor e a chave de dispositivo derivada que você gerou anteriormente. O valor de chave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** mostrado abaixo também é fornecido apenas como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Guarde o ficheiro.

2. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. No prompt para recompilar o projeto, clique em **Sim** para recompilar o projeto antes de executá-lo.

    A saída a seguir é um exemplo do dispositivo de bomba de calor simulado Inicializando com êxito e conectando-se à instância do serviço de provisionamento a ser atribuída ao Hub IoT de bombas de aquecimento da Contoso pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Solucionando problemas de políticas de alocação personalizadas

A tabela a seguir mostra os cenários esperados e os códigos de erro de resultados que você pode receber. Use esta tabela para ajudar a solucionar problemas de falhas de política de alocação personalizadas com seu Azure Functions.

| Cenário | Resultado do registro do serviço de provisionamento | Provisionando resultados do SDK |
| -------- | --------------------------------------------- | ------------------------ |
| O webhook retorna 200 OK com ' iotHubHostName ' definido como um nome de host do Hub IoT válido | Status do resultado: atribuído  | O SDK retorna PROV_DEVICE_RESULT_OK juntamente com informações de Hub |
| O webhook retorna 200 OK com ' iotHubHostName ' presente na resposta, mas definido como uma cadeia de caracteres vazia ou nula | Status do resultado: falha<br><br> Código de erro: CustomAllocationIotHubNotSpecified (400208) | O SDK retorna PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| O webhook retorna 401 não autorizado | Status do resultado: falha<br><br>Código de erro: CustomAllocationUnauthorizedAccess (400209) | O SDK retorna PROV_DEVICE_RESULT_UNAUTHORIZED |
| Um registro individual foi criado para desabilitar o dispositivo | Status do resultado: desabilitado | O SDK retorna PROV_DEVICE_RESULT_DISABLED |
| O webhook retorna o código de erro > = 429 | A orquestração da DPS será repetida várias vezes. A política de repetição é atualmente:<br><br>&nbsp;&nbsp;-contagem de repetições: 10<br>&nbsp;&nbsp;-intervalo inicial: 1s<br>&nbsp;&nbsp;-incremento: noves | O SDK irá ignorar o erro e enviar outra mensagem de status de obtenção no tempo especificado |
| O webhook retorna qualquer outro código de status | Status do resultado: falha<br><br>Código de erro: CustomAllocationFailed (400207) | O SDK retorna PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com os recursos criados neste artigo, você pode deixá-los. Se você não planeja continuar usando os recursos, use as etapas a seguir para excluir todos os recursos criados neste artigo para evitar encargos desnecessários.

As etapas aqui pressupõem que você criou todos os recursos neste artigo, conforme instruído no mesmo grupo de recursos chamado **contoso-US-Resource-Group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para excluir o grupo de recursos por nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...** , digite o nome do grupo de recursos que contém seus recursos, **contoso-US-Resource-Group**. 

3. À direita do seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

4. Você será solicitado a confirmar a exclusão do grupo de recursos. Escreva o nome do grupo de recursos novamente para confirmar e, em seguida, clique em **Eliminar**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md) 
* Para saber mais sobre desprovisionamento, confira [como desprovisionar dispositivos que foram anteriormente autoprovisionados](how-to-unprovision-devices.md) 
