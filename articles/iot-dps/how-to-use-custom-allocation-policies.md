---
title: Políticas de atribuição personalizada com serviço de fornecimento de dispositivos de hub Azure IoT
description: Como utilizar políticas de atribuição personalizada com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 48b8737fc37a183405f42b958e38c328a2ce7cb8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739595"
---
# <a name="how-to-use-custom-allocation-policies"></a>Como utilizar políticas de alocação personalizadas

Uma política de atribuição personalizada dá-lhe mais controlo sobre como os dispositivos são atribuídos a um hub IoT. Isto é conseguido utilizando código personalizado numa [Função Azure](../azure-functions/functions-overview.md) para atribuir dispositivos a um hub IoT. O serviço de fornecimento de dispositivos chama o seu código Azure Function fornecendo todas as informações relevantes sobre o dispositivo e a inscrição. O seu código de função é executado e devolve as informações do hub IoT utilizadas para o fornecimento do dispositivo.

Ao utilizar políticas de alocação personalizadas, define as suas próprias políticas de alocação quando as políticas fornecidas pelo Serviço de Provisionamento de Dispositivos não satisfazem os requisitos do seu cenário.

Por exemplo, talvez queira examinar o certificado que um dispositivo está a usar durante o fornecimento e atribuir o dispositivo a um hub IoT baseado numa propriedade de certificado. Ou, talvez tenha informações armazenadas numa base de dados para os seus dispositivos e precise de consultar a base de dados para determinar a que hub IoT um dispositivo deve ser atribuído.

Este artigo demonstra uma política de atribuição personalizada utilizando uma Função Azure escrita em C#. Dois novos hubs IoT são criados representando uma Divisão de *Torradeiras Contoso* e uma *Divisão de Bombas de Calor Contoso.* Os dispositivos que solicitem o provisionamento devem ter uma identificação de registo com um dos seguintes sufixos a aceitar para o provisionamento:

* **-contoso-tstrsd-007** : Divisão de Torradeiras Contoso
* **-contoso-hpsd-088** : Divisão de Bombas de Calor Contoso

Os dispositivos serão abastados com base num destes sufixos necessários no ID de registo. Estes dispositivos serão simulados utilizando uma amostra de provisionamento incluída no [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

Executa os seguintes passos neste artigo:

* Use o CLI Azure para criar dois hubs IoT da divisão Contoso **(Divisão de Torradeiras Contoso** e **Divisão de Bombas de Calor Contoso)**
* Criar uma nova inscrição em grupo utilizando uma Função Azure para a política de atribuição personalizada
* Crie chaves do dispositivo para duas simulações de dispositivo.
* Criar o ambiente de desenvolvimento para o Azure IoT C SDK
* Simular os dispositivos e verificar se são provisionados de acordo com o código de exemplo na política de atribuição personalizada

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a secção apropriada no preparar o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o ['desenvolvimento do ambiente de trabalho com C++'](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitado. Visual Studio 2015 e Visual Studio 2017 também são suportados.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Criar o serviço de fornecimento e dois hubs IoT divisionários

Nesta secção, você usa a Azure Cloud Shell para criar um serviço de fornecimento e dois hubs IoT representando a **Divisão de Torradeiras Contoso** e a **divisão de Bombas de Calor Contoso.**

> [!TIP]
> Os comandos utilizados neste artigo criam o serviço de fornecimento e outros recursos na localização dos EUA Ocidentais. Recomendamos que crie os seus recursos na região mais próxima que suporte o Serviço de Provisionamento de Dispositivos. Pode ver uma lista das localizações disponíveis ao executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou ao aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procurar "Serviço Aprovisionamento de Dispositivos". Nos comandos, as localizações podem ser especificadas num formato de uma palavra ou multi-palavras; por exemplo: westus, West US, WEST US, etc. O valor não é sensível a casos. Se especificar a localização com o formato de várias palavras, ponha o valor entre aspas, como, por exemplo, `-- location "West US"`.
>

1. Utilize o Azure Cloud Shell para criar um grupo de recursos com o [grupo az criar](/cli/azure/group#az-group-create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

    O exemplo a seguir cria um grupo de recursos chamado *contoso-us-resource-group* na região *oeste.* Recomenda-se que utilize este grupo para todos os recursos criados neste artigo. Esta abordagem vai facilitar a limpeza depois de terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Utilize o Azure Cloud Shell para criar um serviço de fornecimento de dispositivos com os [dps az iot criar](/cli/azure/iot/dps#az-iot-dps-create) comando. O serviço de prestação será adicionado ao *grupo contoso-us-resource- group* .

    O exemplo a seguir cria um serviço de prestação denominado *contoso-provisioning-service-1098* no local *westus.* Deve usar um nome de serviço único. Faça o seu próprio sufixo no nome de serviço em **1098** .

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Este comando pode demorar alguns minutos a ser concluído.

3. Use a Azure Cloud Shell para criar o hub IoT **da Divisão de Torradeiras Contoso** com o [hub az iot criar](/cli/azure/iot/hub#az-iot-hub-create) comando. O hub IoT será adicionado ao *grupo de recursos contoso-us.*

    O exemplo a seguir cria um hub IoT chamado *contoso-torradeiras-hub-1098* na localização *westus.* Deve usar um nome único. Invente o seu próprio sufixo no nome do hub no lugar de **1098.** O código de exemplo para a política de atribuição personalizada requer `-toasters-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Este comando pode demorar alguns minutos a ser concluído.

4. Utilize a Concha de Nuvem Azure para criar o hub IoT **da Divisão de Bombas de Calor Contoso** com o hub [az iot criar](/cli/azure/iot/hub#az-iot-hub-create) comando. Este hub IoT também será adicionado ao *grupo de recursos contoso-us.*

    O exemplo a seguir cria um hub IoT chamado *contoso-heatpumps-hub-1098* na localização *westus.* Deve usar um nome único. Invente o seu próprio sufixo no nome do hub no lugar de **1098.** O código de exemplo para a política de atribuição personalizada requer `-heatpumps-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Este comando pode demorar alguns minutos a ser concluído.

## <a name="create-the-custom-allocation-function"></a>Criar a função de atribuição personalizada

Nesta secção, cria-se uma função Azure que implementa a sua política de atribuição personalizada. Esta função decide em que hub IoT divisional um dispositivo deve ser registado com base no facto de o seu ID de registo conter a cadeia **-contoso-tstrsd-007** ou **-contoso-hpsd-088** . Também define o estado inicial do dispositivo gémeo com base no facto de o dispositivo ser uma torradeira ou uma bomba de calor.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Na sua página inicial, selecione **+ Criar um recurso.**

2. Na caixa de pesquisa *do Marketplace,* escreva "App de função". A partir da lista de drop-down selecione **App de função** e, em seguida, selecione **Criar** .

3. Na **página de criação da App de Função,** no separador **Básicos,** introduza as seguintes definições para a sua nova aplicação de função e selecione Review **+ create** :

    **Grupo de Recursos** : Selecione o **grupo de recursos contoso-us-resource** para manter todos os recursos criados neste artigo juntos.

    **Função Nome da aplicação** : Introduza um nome de aplicação de função única. Este exemplo utiliza **contoso-function-app-1098** .

    **Publicar:** Verifique se o **Código** está selecionado.

    **Pilha de tempo de** execução : Selecione **.NET Core** a partir do drop-down.

    **Região** : Selecione a mesma região que o seu grupo de recursos. Este exemplo usa **o Oeste dos EUA.**

    > [!NOTE]
    > Por predefinição, o Application Insights está ativado. A Application Insights não é necessária para este artigo, mas pode ajudá-lo a compreender e investigar quaisquer problemas que encontre com a alocação personalizada. Se preferir, pode desativar insights de aplicação selecionando o **separador Monitoring** e, em seguida, selecionando **O** para **Ativar insights de aplicação** .

    ![Criar uma App de função Azure para acolher a função de atribuição personalizada](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na página **Resumo,** selecione **Criar** para criar a aplicação de função. A implementação poderá demorar vários minutos. Quando estiver concluído, selecione **Ir para o recurso** .

5. No painel esquerdo da página **de visão geral** da aplicação de funções, selecione ao lado de **+** **Funções** para adicionar uma nova função.

    ![Adicionar uma função à App de Função](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Nas **Funções Azure para .NET - começar** página, para o passo **ESCOLHA UM AMBIENTE DE IMPLEMENTAÇÃO,** selecione o azulejo **in-portal** e, em seguida, selecione **Continue** .

    ![Selecione o ambiente de desenvolvimento do portal](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Na página seguinte, para o passo **CREATE A FUNCTION,** selecione o **teia Webhook + API** e, em seguida, selecione **Criar** . É criada uma função chamada **HttpTrigger1** e o portal exibe o conteúdo do ficheiro de código **run.csx.**

8. Referência necessária pacotes Nuget. Para criar o twin do dispositivo inicial, a função de atribuição personalizada utiliza classes que são definidas em dois pacotes Nuget que devem ser carregados no ambiente de hospedagem. Com as Funções Azure, os pacotes Nuget são referenciados através de um ficheiro *fun.host.* Neste passo, guarde e carreize um ficheiro *fun.host.*

    1. Copie as seguintes linhas para o seu editor favorito e guarde o ficheiro no seu computador como *fun.host* .

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. Na função **HttpTrigger1,** expanda o separador **'Ver Ficheiros'** no lado direito da janela.

        ![Ficheiros de visualização aberta](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Selecione **Upload,** navegue no ficheiro **function.proj** e selecione **Open** para carregar o ficheiro.

        ![Selecione o ficheiro de upload](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Substitua o código da função **HttpTrigger1** pelo seguinte código e **selecione Guardar:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
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
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Criar a inscrição

Nesta secção, você vai criar um novo grupo de inscrições que usa a política de alocação personalizada. Para simplificar, este artigo utiliza [a chave simétrica atestado](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere usar [o atestado de certificado X.509](concepts-x509-attestation.md) com uma cadeia de confiança.

1. Ainda no [portal Azure,](https://portal.azure.com)abra o seu serviço de prestação.

2. **Selecione Gerir as inscrições** no painel esquerdo e, em seguida, selecione o botão **de grupo de inscrição Add** no topo da página.

3. No **Grupo de Inscrição adicionar,** introduza as seguintes informações e selecione o botão **Guardar.**

    **Nome do grupo** : **Insira os dispositivos contoso-personalizados** .

    **Tipo de Atestado** : Selecione **Chave Simétrica** .

    **Chaves de geração automática** : Esta caixa de verificação já deve ser verificada.

    **Selecione como pretende atribuir dispositivos aos hubs** : Selecione **Custom (Use Azure Function)** .

    ![Adicione o grupo de inscrição de atribuição personalizada para atestado de chave simétrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. No **Add Registration Group** , selecione Link um novo hub **IoT** para ligar ambos os seus novos hubs de IoT divisionários.

    Execute este passo para ambos os seus centros IoT divisionários.

    **Subscrição** : Se tiver várias subscrições, escolha a subscrição onde criou os hubs IoT divisionários.

    **Hub IoT** : Selecione um dos centros de divisão que criou.

    **Política de Acesso** : Escolha **iothubowner** .

    ![Ligue os hubs ioT divisionários com o serviço de fornecimento](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. No **Add Registration Group** , uma vez ligados os dois hubs IoT divisionais, deve selecioná-los como o grupo IoT Hub para o grupo de inscrição, como mostrado abaixo:

    ![Crie o grupo de centros divisionais para a inscrição](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. No **Grupo de Inscrição adicionar** , desloque-se até à secção **'Função Select Azure',** selecione a aplicação 'Função' que criou na secção anterior. Em seguida, selecione a função criada e selecione Guardar para salvar o grupo de inscrição.

    ![Selecione a função e salve o grupo de inscrição](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Depois de guardar a inscrição, reabre-a e tome nota da **Chave Primária.** Tem de guardar a inscrição primeiro para ter as chaves geradas. Esta chave será utilizada para gerar chaves de dispositivo únicas para dispositivos simulados mais tarde.

## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo únicas

Nesta secção, cria-se duas chaves de dispositivo únicas. Uma chave será utilizada para um dispositivo de torradeira simulado. A outra chave será utilizada para um dispositivo simulado de bomba de calor.

Para gerar a chave do dispositivo, utilize a **Chave Primária** que observou anteriormente para calcular o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo do dispositivo para cada dispositivo e converter o resultado em formato Base64. Para obter mais informações sobre a criação de chaves de dispositivos derivados com grupos de matrícula, consulte a secção de matrículas de grupo [do atestado de chave simétrica](concepts-symmetric-key-attestation.md).

Por exemplo, neste artigo, utilize os dois IDs de registo do dispositivo e calcule uma chave do dispositivo para ambos os dispositivos. Ambos os IDs de registo têm um sufixo válido para trabalhar com o código de exemplo para a política de atribuição personalizada:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Estações de trabalho linux

Se estiver a utilizar uma estação de trabalho Linux, pode utilizar o openssl para gerar as teclas do dispositivo derivado, como mostra o exemplo seguinte.

1. Substitua o valor da **CHAVE** pela **Chave Primária** que observou anteriormente.

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

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em janelas

Se estiver a utilizar uma estação de trabalho baseada no Windows, pode utilizar o PowerShell para gerar a chave do dispositivo derivado, como mostra o exemplo a seguir.

1. Substitua o valor da **CHAVE** pela **Chave Primária** que observou anteriormente.

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

Os dispositivos simulados utilizarão as teclas do dispositivo derivado com cada ID de registo para executar o atestado de chave simétrica.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

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

    Se `cmake` não encontrar o seu compilador C++, poderá obter erros de construção durante a execução do comando. Se isso acontecer, tente executar o comando no [comando do Estúdio Visual.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

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

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o ficheiro de solução **azure_iot_sdks.sln** que foi gerado pela execução do CMake anteriormente. O ficheiro de solução deve estar na seguinte localização:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Na janela *Solution Explorer* (Explorador de Soluções) do Visual Studio, navegue para a pasta **Provision\_Samples** . Expanda o projeto de exemplo com o nome **prov\_dev\_client\_sample** . Expanda **Source Files** (Ficheiros de Origem) e abra **prov\_dev\_client\_sample.c** .

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

6. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque** .

### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de torradeira Contoso

1. Para simular o dispositivo de torradeira, encontre a chamada para `prov_dev_set_symmetric_key_info()` **a amostra do cliente prov \_ dev \_ \_** que é comentada.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descompromete a chamada de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelo ID de registo da torradeira e pela chave do dispositivo derivado que gerou anteriormente. O valor-chave **JC8F96eayuQwz+PkE7IzjH2lIAjCUnAaa61tDigBnSs=** mostrado abaixo é dado como exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Guarde o ficheiro.

2. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Na pronta para reconstruir o projeto, selecione **Sim,** para reconstruir o projeto antes de correr.

    A seguinte saída é um exemplo do dispositivo de torradeira simulado que inicia e liga com sucesso à instância de serviço de fornecimento a atribuir ao hub IoT das torradeiras pela política de atribuição personalizada:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de bomba de calor Contoso

1. Para simular o dispositivo da bomba de calor, atualize a chamada para `prov_dev_set_symmetric_key_info()` a amostra do cliente **prov \_ dev novamente \_ \_ com** o ID de registo da bomba de calor e a chave do dispositivo derivado que gerou anteriormente. O valor-chave **6uejA9PfkQgmYlj8Zerp3kcbeVrGZ172YLa7VSnJzg=** mostrado abaixo também é dado como exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Guarde o ficheiro.

2. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Na pronta para reconstruir o projeto, selecione **Sim** para reconstruir o projeto antes de executar.

    A seguinte saída é um exemplo do dispositivo simulado da bomba de calor que reinicia e liga com sucesso à instância de serviço de fornecimento a atribuir ao centro de bombas de calor Contoso através da política de atribuição de medidas de alocação personalizadas:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Resolução de problemas de políticas de atribuição de medidas

A tabela seguinte mostra cenários esperados e os códigos de erro de resultados que poderá receber. Utilize esta tabela para ajudar a resolver problemas de falhas na política de atribuição personalizada com as suas Funções Azure.

| Cenário | Resultado do registo do Serviço de Provisionamento | A provisionar resultados da SDK |
| -------- | --------------------------------------------- | ------------------------ |
| O webhook devolve 200 OK com 'iotHubHostName' definido para um nome de anfitrião de hub IoT válido | Estado dos resultados: Atribuído  | SDK devolve PROV_DEVICE_RESULT_OK juntamente com informações do hub |
| O webhook devolve 200 OK com 'iotHubHostName' presente na resposta, mas definido para uma corda vazia ou nulo | Estado do resultado: Falhado<br><br> Código de erro: CustomAllocationIotHubNotSpecified (400208) | SDK regressa PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| O webhook devolve 401 não autorizado | Estado do resultado: Falhado<br><br>Código de erro: CustomAllocationUnautizedAccesss (400209) | SDK regressa PROV_DEVICE_RESULT_UNAUTHORIZED |
| Foi criada uma inscrição individual para desativar o dispositivo | Estado do resultado: Desativado | SDK regressa PROV_DEVICE_RESULT_DISABLED |
| O webhook devolve >de código de erro = 429 | A orquestração do DPS vai voltar a tentar várias vezes. A política de retenção é atualmente:<br><br>&nbsp;&nbsp;- Contagem de repetições: 10<br>&nbsp;&nbsp;- Intervalo inicial: 1s<br>&nbsp;&nbsp;- Incremento: 9s | A SDK ignorará o erro e submeterá outra mensagem de estado de obter no tempo especificado |
| O webhook devolve qualquer outro código de estado | Estado do resultado: Falhado<br><br>Código de erro: CustomAllocationFailed (400207) | SDK regressa PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar com os recursos criados neste artigo, pode deixá-los. Se não pretender continuar a utilizar os recursos, use os seguintes passos para eliminar todos os recursos criados neste artigo para evitar encargos desnecessários.

Os passos aqui pressupõem que criou todos os recursos neste artigo, tal como instruído no mesmo grupo de recursos chamado **contoso-us-resource-group** .

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para eliminar o grupo de recursos pelo nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos** .

2. No **Filtro pelo nome...** textbox, digite o nome do grupo de recursos que contém os seus recursos, **contoso-us-resource-group** . 

3. À direita do seu grupo de recursos **...** na lista de resultados, selecione... e depois **elimine o grupo de recursos** .

4. Ser-lhe-á pedido que confirme a supressão do grupo de recursos. Escreva novamente o nome do seu grupo de recursos para confirmar e, em seguida, selecione **Delete** . Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais Reprovisioning, consulte [conceitos de reprovisionamento do IoT Hub Device](concepts-device-reprovision.md) 
* Para saber mais Deprovisionamento, veja [Como desprovisionar dispositivos que foram previamente autoprovisionados](how-to-unprovision-devices.md) 
