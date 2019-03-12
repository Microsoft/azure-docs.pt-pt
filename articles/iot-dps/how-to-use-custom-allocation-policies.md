---
title: Como utilizar as políticas de alocação personalizado com o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como utilizar as políticas de alocação personalizado com o serviço de aprovisionamento de dispositivos do Azure IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 739780bedc701cc63747db6100242d0d6cad6085
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542680"
---
# <a name="how-to-use-custom-allocation-policies"></a>Como utilizar políticas de alocação personalizado


Uma política de alocação personalizado dá-lhe mais controlo sobre a forma como os dispositivos são atribuídos a um hub IoT. Isto é conseguido utilizando código personalizado num [função do Azure](../azure-functions/functions-overview.md) atribuir dispositivos a um hub IoT. O serviço aprovisionamento de dispositivos chama seu código de função do Azure, fornecendo todas as informações relevantes sobre o dispositivo e a inscrição. O código de função é executado e devolve as informações do hub IoT utilizadas para aprovisionar o dispositivo.

Ao utilizar políticas de alocação personalizados definem suas próprias políticas de alocação quando as políticas fornecidas pelo serviço de aprovisionamento de dispositivos não cumprem os requisitos do seu cenário.

Por exemplo, talvez deseja examinar o certificado de que um dispositivo está a utilizar durante o aprovisionamento e atribuir o dispositivo para um hub IoT com base numa propriedade de certificado. Talvez talvez tenham informações armazenadas numa base de dados para os seus dispositivos e tem de consultar a base de dados para determinar qual deve ser atribuído um dispositivo para o hub IoT.


Este artigo demonstra uma política de alocação personalizado usando uma função do Azure, escrito em c#. Os hubs IoT do novo dois são criados que representa um *Contoso Torradeiras divisão* e uma *divisão de calor bombas Contoso*. Solicitando o aprovisionamento de dispositivos têm de ter um ID de registo com um dos seguintes sufixos sejam aceites para aprovisionamento:

- **-contoso-tstrsd-007**: Divisão de Torradeiras de contoso
- **-contoso-hpsd-088**: Divisão de calor bombas de contoso

Os dispositivos serão aprovisionados com base em um desses sufixos necessários no ID do registo. Estes dispositivos irão ser simulados com um exemplo de aprovisionamento incluído nos [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

Realizará os seguintes passos neste artigo:

* Utilizar a CLI do Azure para criar dois hubs de IoT de divisão Contoso (**Contoso Torradeiras divisão** e **divisão de calor bombas Contoso**)
* Criar uma nova inscrição de grupo com uma função do Azure para a política de alocação personalizado
* Crie chaves de dispositivo para dois simulações de dispositivo.
* Configurar o ambiente de desenvolvimento do SDK de C do IoT do Azure
* Simular os dispositivos que estão aprovisionada, de acordo com o código de exemplo da política de alocação personalizado


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [configurar o serviço aprovisionamento de dispositivos Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) início rápido.
* Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento do ambiente de trabalho em C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ativada.
* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Criar dois os IoT hubs divisão

Nesta secção, irá utilizar o Azure Cloud Shell para criar dois hubs IoT novo que representa a **Contoso Torradeiras divisão** e o **divisão Contoso térmico bombas**.

1. Utilizar o Azure Cloud Shell para criar um grupo de recursos com o [criar grupo az](/cli/azure/group#az-group-create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

    O exemplo seguinte cria um grupo de recursos chamado *contoso-nos-resource-group* no *eastus* região. Recomenda-se que utilize este grupo para todos os recursos criados neste artigo. Essa abordagem facilitará de limpeza do wsu depois de terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Utilizar o Azure Cloud Shell para criar o **Contoso Torradeiras divisão** hub IoT com o [criar o hub iot az](/cli/azure/iot/hub#az-iot-hub-create) comando. O hub IoT será adicionado ao *contoso-nos-resource-group*.

    O exemplo seguinte cria um hub IoT com o nome *contoso-Torradeiras-hub-1098* no *eastus* localização. Tem de utilizar o seu próprio nome de hub exclusivo. Compõem a sua própria sufixo no nome do hub, em vez de **1098**. O código de exemplo para a política de alocação personalizado requer `-toasters-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Este comando pode demorar alguns minutos a concluir.

3. Utilizar o Azure Cloud Shell para criar o **Contoso bombas de calor divisão** hub IoT com o [criar o hub iot az](/cli/azure/iot/hub#az-iot-hub-create) comando. Este hub IoT, também será adicionado ao *contoso-nos-resource-group*.

    O exemplo seguinte cria um hub IoT com o nome *contoso-heatpumps-hub-1098* no *eastus* localização. Tem de utilizar o seu próprio nome de hub exclusivo. Compõem a sua própria sufixo no nome do hub, em vez de **1098**. O código de exemplo para a política de alocação personalizado requer `-heatpumps-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Este comando também pode demorar alguns minutos a concluir.




## <a name="create-the-enrollment"></a>Criar a inscrição

Nesta secção, irá criar um novo grupo de inscrição que utiliza a política de alocação personalizado. Para simplificar, este artigo usa [atestado de chave simétrico](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere a utilização [atestado de certificado X.509](concepts-security.md#x509-certificates) com uma cadeia de fidedignidade.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a sua instância do serviço aprovisionamento de dispositivos.

2. Selecione o **gerir inscrições** separador e, em seguida, clique nas **adicionar grupo de inscrição** botão na parte superior da página. 

3. No **adicionar grupo de inscrição**, introduza as seguintes informações e clique nas **guardar** botão.

    **Nome do grupo**: Introduza **contoso-personalizada-alocado-dispositivos**.

    **Tipo de atestado**: Selecione **chave simétrica**.

    **Chaves de geração automática**: Esta caixa de verificação já deve ser verificada.

    **Selecione como pretende atribuir dispositivos a hubs**: Selecione **personalizado (utilizar a função do Azure)**.

    ![Adicionar grupo de inscrição de alocação personalizado para o atestado de chave simétrico](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. No **adicionar grupo de inscrição**, clique em **ligar um hub IoT novo** para associar ambos os hubs de IoT de divisão nova. 

    Tem de executar este passo para ambos os hubs IoT divisão.

    **Subscrição**: Se tiver várias subscrições, escolha a subscrição onde criou os hubs IoT divisão.

    **IoT hub**: Selecione um dos hubs de divisão que criou.

    **Política de acesso**: Escolher **iothubowner**.

    ![Ligar os divisão hubs IoT com o serviço de aprovisionamento](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. No **adicionar grupo de inscrição**, uma vez que ambos os hubs IoT divisão foram associados, tem de selecioná-los como o grupo do IoT Hub para o grupo de inscrição conforme mostrado abaixo:

    ![Criar o grupo de divisão de hub para a inscrição](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. No **adicionar grupo de inscrição**, desloque para baixo até o **selecione a função do Azure** secção e clique em **criar uma nova aplicação de função**.

7. No **aplicação de funções** criar página que abre-se, introduza as seguintes definições para a nova função e clique em **criar**:

    **Nome da aplicação**: Introduza um nome de aplicação de funções exclusivo. **Contoso-função-aplicação-1098** é mostrado como um exemplo.

    **Grupo de recursos**: Selecione **utilizar existente** e o **contoso-nos-resource-group** para manter todos os recursos criados neste artigo em conjunto.

    **O Application Insights**: Para este exercício podem desativar esta definição.

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Novamente no seu **adicionar grupo de inscrição** página, certifique-se de que a sua nova aplicação de função está selecionada. Poderá ter da selecionar a subscrição para atualizar a lista de aplicações de função.

    Assim que a sua nova aplicação de função for selecionada, clique em **criar uma nova função**.

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    a nova aplicação de função será aberta.

9. Na sua aplicação function app, clique em criar uma nova função

    ![Criar a aplicação de funções](./media/how-to-use-custom-allocation-policies/new-function.png)

    Para a nova função, utilize as predefinições para criar um novo **Webhook + API** utilizando o **CSharp** idioma. Clique em **criar esta função**.

    Esta ação cria uma nova função c# com o nome **HttpTriggerCSharp1**.

10. Substitua o código para a nova função c# com o código a seguir e clique em **guardar**:    

    ```C#
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


11. Regresse ao seu **adicionar grupo de inscrição** de páginas e certifique-se a nova função é selecionada. Poderá ter da selecionar a aplicação de funções para atualizar a lista de funções.

    Depois de sua nova função é selecionada, clique em **guardar** para guardar o grupo de inscrição.

    ![Por fim, guardar o grupo de inscrição](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Depois de guardar a inscrição, abri-lo novamente e anote o **chave primária**. Tem de guardar primeiro para ter as chaves geradas o Registro. Esta chave será utilizada para gerar chaves de dispositivo exclusivo para os dispositivos simulados mais tarde.


## <a name="derive-unique-device-keys"></a>Derivar as chaves de dispositivo exclusivo

Nesta secção, irá criar duas chaves de dispositivo exclusivo. Uma chave será utilizada para um dispositivo simulado torradeira. A outra chave será utilizada para um dispositivo simulado bomba de calor.

Para gerar a chave de dispositivo, que irá utilizar o **chave primária** que anotou anteriormente para computar a [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo do dispositivo para cada dispositivo e converter o resultado em formato Base64. Para obter mais informações sobre como criar as chaves de dispositivo derivada com grupos de inscrição, consulte a secção de inscrições de grupo de [atestado de chave simétrico](concepts-symmetric-key-attestation.md).

Para o exemplo neste artigo, utilize o seguinte registo de dispositivos de dois IDs e uma chave de dispositivo para ambos os dispositivos de computação. Ambos os IDs de registo têm um sufixo válido para trabalhar com o código de exemplo para a política de alocação personalizado:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se estiver a utilizar uma estação de trabalho do Linux, pode utilizar openssl para gerar as chaves de dispositivo derivada, conforme mostrado no exemplo a seguir.

1. Substitua o valor de **chave** com o **chave primária** que anotou anteriormente.

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


#### <a name="windows-based-workstations"></a>Estações de trabalho baseados em Windows

Se estiver a utilizar uma estação de trabalho baseados em Windows, pode utilizar o PowerShell para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

1. Substitua o valor de **chave** com o **chave primária** que anotou anteriormente.

    ```PowerShell
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

    ```PowerShell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


Os dispositivos simulados irão utilizar as chaves de dispositivo derivada com cada ID de registo para executar o atestado de chave simétrico.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de exemplo para o dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

Esta secção é orientada em relação uma estação de trabalho baseados em Windows. Para obter um exemplo de Linux, consulte a configuração das VMs no [como aprovisionar para arquitetura "multitenancy"](how-to-provision-multitenant.md).



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




## <a name="simulate-the-devices"></a>Simular os dispositivos

Nesta secção, irá atualizar um exemplo de aprovisionamento com o nome **prov\_dev\_cliente\_exemplo** localizado no SDK C do Azure IoT configurou anteriormente. 

Este código de exemplo simula uma sequência de arranque de dispositivo que envia a solicitação de provisionamento à sua instância do serviço aprovisionamento de dispositivos. A sequência de arranque fará com que o dispositivo de torradeira a serem reconhecidas e atribuído para o hub de IoT com a política de alocação personalizado.

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

6. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 


#### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de torradeira Contoso

1. Para simular o dispositivo de torradeira, encontre a chamada para `prov_dev_set_symmetric_key_info()` no **prov\_dev\_cliente\_Sample** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Anule os comentários a chamada de função e substitua os valores de marcador de posição (incluindo os parênteses angulares) com o ID de registo de torradeira e a chave de dispositivo derivada que gerou anteriormente. O valor da chave **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** mostrado abaixo só é fornecido como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Guarde o ficheiro.

2. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. Na linha de comandos para recriar o projeto, clique em **Yes** (Sim) para recriar o projeto antes da execução.

    O resultado seguinte é um exemplo de dispositivo simulado torradeira com êxito a arrancar e ligar-se para a instância de serviço de aprovisionamento a ser atribuída ao hub IoT Torradeiras pela política de alocação personalizado:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de calor bomba de Contoso

1. Para simular o dispositivo de bombeamento de calor, atualize a chamada para `prov_dev_set_symmetric_key_info()` no **prov\_dev\_cliente\_Sample** novamente com o ID de registo de bombeamento de calor e a chave de dispositivo derivada gerou anteriormente . O valor da chave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** mostrado abaixo também só é fornecido como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Guarde o ficheiro.

2. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. Na linha de comandos para recriar o projeto, clique em **Yes** (Sim) para recriar o projeto antes da execução.

    O resultado seguinte é um exemplo de dispositivo simulado bomba de calor com êxito a arrancar e ligar-se para a instância de serviço de aprovisionamento a ser atribuído para o hub de IoT de calor bombas Contoso pela política de alocação personalizado:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Resolução de problemas de políticas de alocação personalizado

A tabela seguinte mostra cenários esperados e os códigos de erro de resultados que poderá encontrar. Utilize esta tabela para o ajudar a resolver problemas de falhas de política de alocação personalizado com as suas funções do Azure.


| Cenário | Resultado de registo do serviço de aprovisionamento | Resultados do SDK de aprovisionamento |
| -------- | --------------------------------------------- | ------------------------ |
| O webhook devolve 200 OK com "iotHubHostName' definido como um nome de anfitrião válido do hub IoT | Estado do resultado: Atribuído  | SDK devolve PROV_DEVICE_RESULT_OK juntamente com informações de hub |
| O webhook devolve 200 OK com 'iotHubHostName' presente na resposta, mas definida como uma cadeia vazia nem nula | Estado do resultado: Com Falhas<br><br> Código do erro: CustomAllocationIotHubNotSpecified (400208) | SDK devolve PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| O webhook devolve 401 não autorizado | Estado do resultado: Com Falhas<br><br>Código do erro: CustomAllocationUnauthorizedAccess (400209) | SDK devolve PROV_DEVICE_RESULT_UNAUTHORIZED |
| Uma inscrição Individual foi criada para desativar o dispositivo | Estado do resultado: Desactivado | SDK devolve PROV_DEVICE_RESULT_DISABLED |
| O webhook retorna o código de erro > = 429 | Orquestração dos pontos de distribuição tentará novamente várias vezes. A política de repetição é atualmente:<br><br>&nbsp;&nbsp;-Número de tentativas: 10<br>&nbsp;&nbsp;-Intervalo de inicial: 1s<br>&nbsp;&nbsp;-Incremento: 9s | SDK irá ignorar o erro e submeta outra mensagem de estado de get no tempo especificado |
| O webhook devolve qualquer outro código de estado | Estado do resultado: Com Falhas<br><br>Código do erro: CustomAllocationFailed (400207) | SDK returns PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Limpar recursos

Se planeia continuar a trabalhar com recursos criados neste artigo, pode deixá-los. Se não planear continuar a utilizar o recurso, utilize os seguintes passos para eliminar todos os recursos criados neste artigo para evitar cobranças desnecessárias.

Os passos aqui descritos partem do princípio de todos os recursos que criou neste artigo com as instruções no mesmo grupo de recursos com o nome **contoso-nos-resource-group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para eliminar o grupo de recursos por nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na **filtrar por nome...**  caixa de texto, o nome do recurso de grupo que contém os recursos do tipo **contoso-nos-resource-group**. 

3. À direita do seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

4. É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos novamente para confirmar e, em seguida, clique em **Eliminar**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais Reprovisioning, veja [conceitos de reprovisoning de dispositivos no Hub IoT](concepts-device-reprovision.md) 
- Para saber mais desaprovisionamento, veja [como desaprovisionar os dispositivos que foram anteriormente aprovisionados](how-to-unprovision-devices.md) 











