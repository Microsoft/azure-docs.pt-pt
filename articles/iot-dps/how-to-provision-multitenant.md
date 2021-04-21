---
title: Como providenciar dispositivos para multitenancy no Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Como providenciar dispositivos para multitenancy com a sua instância do Serviço de Provisionamento de Dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777388"
---
# <a name="how-to-provision-for-multitenancy"></a>Como aprovisionar para arquitetura "multitenancy" 

Este artigo demonstra como providenciar de forma segura múltiplos dispositivos chave simétricas a um grupo de Hubs IoT utilizando uma política de [atribuição](concepts-service.md#allocation-policy). As políticas de atribuição definidas pelo serviço de prestação de serviços suportam uma variedade de cenários de atribuição. Dois cenários comuns são:

* **Geolocalização / GeoLatency**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada com o dispositivo a provisionado ao hub IoT mais próximo de cada local. Neste cenário, um grupo de hubs IoT, que se estendem por regiões, são selecionados para inscrições. A política de atribuição **de latência mais baixa** é selecionada para estas matrículas. Esta política faz com que o Serviço de Provisionamento de Dispositivos avalie a latência do dispositivo e determine o hub IoT do armário fora do grupo de hubs IoT. 

* **Multi-arrendamento**: Os dispositivos utilizados numa solução IoT podem ter de ser atribuídos a um hub específico de IoT ou a um grupo de hubs IoT. A solução pode exigir que todos os dispositivos para um inquilino particular se comunique com um grupo específico de centros IoT. Em alguns casos, um inquilino pode possuir centros IoT e exigir que os dispositivos sejam atribuídos aos seus hubs IoT.

É comum combinar estes dois cenários. Por exemplo, uma solução IoT multitenant irá comumente atribuir dispositivos de inquilino usando um grupo de hubs IoT que estão espalhados por regiões. Estes dispositivos de inquilino podem ser atribuídos ao hub IoT desse grupo, que tem a latência mais baixa com base na localização geográfica.

Este artigo utiliza uma amostra de dispositivo simulada do [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) para demonstrar como providenciar dispositivos num cenário multitennte entre regiões. Executará os seguintes passos neste artigo:

> [!div class="checklist"]
> * Use o CLI Azure para criar dois hubs regionais de IoT **(Eua Ocidental** e **Leste)**
> * Criar uma inscrição multitenant
> * Utilize o CLI Azure para criar dois LM regionais de Linux para funcionar como dispositivos nas mesmas regiões **(Eua Ocidental** e **Leste)**
> * Criar o ambiente de desenvolvimento para o Azure IoT C SDK em ambos os LM Linux
> * Simular os dispositivos para ver se estão previstos para o mesmo inquilino da região mais próxima.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

- Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o](./quick-setup-auto-provision.md) quickstart do portal Azure.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Criar dois centros regionais de IoT

Nesta secção, você usará a Azure Cloud Shell para criar dois novos centros regionais de IoT nas regiões **oeste dos EUA** e leste dos **EUA** para um inquilino.


1. Utilize o Azure Cloud Shell para criar um grupo de recursos com o [grupo az criar](/cli/azure/group#az_group_create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

    O exemplo a seguir cria um grupo de recursos chamado *contoso-us-resource-group* na região *leste.* Recomenda-se que utilize este grupo para todos os recursos criados neste artigo. Isto facilitará a limpeza depois de terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use a Azure Cloud Shell para criar um hub IoT na região **leste** com o [hub az iot criar](/cli/azure/iot/hub#az_iot_hub_create) comando. O hub IoT será adicionado ao *grupo de recursos contoso-us.*

    O exemplo a seguir cria um hub IoT chamado *contoso-east-hub* na localização *leste.* Você deve usar seu próprio nome de hub único em vez de **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Este comando pode demorar alguns minutos a ser concluído.

3. Use a Azure Cloud Shell para criar um hub IoT na região **oeste** com o [hub az iot criar](/cli/azure/iot/hub#az_iot_hub_create) comando. Este hub IoT também será adicionado ao *grupo contoso-us-resource- group*.

    O exemplo a seguir cria um hub IoT chamado *contoso-west-hub* na localização *westus.* Você deve usar seu próprio nome de hub único em vez de **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Este comando pode demorar alguns minutos a ser concluído.



## <a name="create-the-multitenant-enrollment"></a>Criar a inscrição multitenante

Nesta secção, você vai criar um novo grupo de inscrições para os dispositivos de inquilino.  

Para simplificar, este artigo utiliza [a chave simétrica atestado](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere usar [o atestado de certificado X.509](concepts-x509-attestation.md) com uma cadeia de confiança.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua instância do Serviço de Provisionamento de Dispositivos.

2. Selecione o **separador 'Gerir' e,** em seguida, clique no botão **de grupo de inscrição Add** no topo da página. 

3. No **Grupo de Inscrição adicionar,** introduza as seguintes informações e clique no botão **Guardar.**

    **Nome do grupo**: **Insira os dispositivos contoso-us**.

    **Tipo de Atestado**: Selecione **Chave Simétrica**.

    **Chaves de geração automática**: Esta caixa de verificação já deve ser verificada.

    **Selecione como pretende atribuir dispositivos aos hubs**: Selecione **a latência mais baixa**.

    ![Adicione o grupo de inscrição multitenante para atestado de chave simétrica](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. No **Add Registration Group,** clique em **Link um novo hub IoT** para ligar ambos os seus centros regionais.

    **Subscrição**: Se tiver várias subscrições, escolha a subscrição onde criou os hubs regionais de IoT.

    **Hub IoT**: Selecione um dos centros regionais que criou.

    **Política de Acesso**: Escolha **iothubowner**.

    ![Ligue os centros regionais de IoT com o serviço de fornecimento](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Uma vez ligados os dois hubs regionais de IoT, deve selecioná-los para o grupo de inscrição e clicar em **Guardar** para criar o grupo de hub ioT regional para a inscrição.

    ![Criar o grupo central regional para a inscrição](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Depois de guardar a inscrição, reabre-a e tome nota da **Chave Primária.** Tem de guardar a inscrição primeiro para ter as chaves geradas. Esta chave será utilizada para gerar chaves de dispositivo únicas para ambos os dispositivos simulados mais tarde.


## <a name="create-regional-linux-vms"></a>Criar VMs linux regionais

Nesta secção, irá criar duas máquinas virtuais regionais linux (VMs). Estes VMs irão executar uma amostra de simulação de dispositivos de cada região para demonstrar o fornecimento de dispositivos para dispositivos de arrendamento de ambas as regiões.

Para facilitar a limpeza, estes VMs serão adicionados ao mesmo grupo de recursos que contém os hubs IoT que foram criados, *contoso-us-resource-group*. No entanto, os VMs serão executados em regiões separadas **(Eua Ocidental** e **Leste dos EUA).**

1. Na Azure Cloud Shell, execute o seguinte comando para criar uma VM região **dos EUA oriental** depois de escrublando as seguintes alterações de parâmetros no comando:

    **--nome**: Insira um nome único para o seu dispositivo regional **norte-americano** VM. 

    **--nome de utilizador de administração:** Use o seu próprio nome de utilizador administrativo.

    **--admin-password**: Use a sua própria senha de administração.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Este comando vai demorar alguns minutos a ser executado. Uma vez concluído o comando, tome nota do valor **publicitárioIpAddress** para a sua região norte-americana VM.

1. Na Azure Cloud Shell, execute o comando para criar uma VM região **dos EUA ocidental** depois de fazer as seguintes alterações de parâmetros no comando:

    **--nome**: Insira um nome único para o seu dispositivo regional **dos EUA** VM. 

    **--nome de utilizador de administração:** Use o seu próprio nome de utilizador administrativo.

    **--admin-password**: Use a sua própria senha de administração.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Este comando vai demorar alguns minutos a ser executado. Uma vez concluído o comando, tome nota do valor **publicitárioIpAddress** para a sua região oeste dos EUA VM.

1. Abra duas cápsulas de linha de comando. Ligue-se a um dos VMs regionais em cada concha utilizando SSH. 

    Passe o seu nome de utilizador de administração e o endereço IP público que observou para o VM como parâmetros para SSH. Introduza a palavra-passe de administração quando solicitado.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento Azure IoT C SDK

Nesta secção, você clonará o Azure IoT C SDK em cada VM. O SDK contém uma amostra que simulará o fornecimento de dispositivos de um inquilino de cada região.

1. Para cada VM, instale **CMake,** **g++**, **gcc,** e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) utilizando os seguintes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

1. Clone o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) em ambos os VMs.  Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Para ambos os VMs, crie uma nova pasta **de cmake** dentro do repositório e mude para essa pasta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Para ambos os VMs, executar o seguinte comando, que constrói uma versão do SDK específica para a sua plataforma de clientes de desenvolvimento. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo únicas

Ao utilizar o atestado de chave simétrica com as matrículas de grupo, não utilize as chaves do grupo de inscrição diretamente. Em vez disso, cria uma chave derivada única para cada dispositivo e mencionada nas [Matrículas do Grupo com teclas simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

Para gerar a chave do dispositivo, utilize a chave principal do grupo para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave principal de grupo no código do dispositivo.

Utilize o exemplo da casca bash para criar uma chave de dispositivo derivada para cada dispositivo utilizando **o openssl**.

- Substitua o valor de **CHAVE** pela **Chave Primária** que notou anteriormente para a sua inscrição.

- Substitua o valor **por REG_ID** pelo seu próprio ID de registo único para cada dispositivo. Utilize caracteres alfanuméricos e traços minúsculos ('-')para definir ambos os IDs.

Exemplo de geração chave do dispositivo para *contoso-simdevice-leste*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exemplo de geração chave do dispositivo para *contoso-simdevice-oeste*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Os dispositivos de arrendamento utilizarão cada um a sua chave de dispositivo derivado e iD de registo único para realizar o atestado chave simétrico com o grupo de inscrição durante o fornecimento aos centros IoT do inquilino.




## <a name="simulate-the-devices-from-each-region"></a>Simular os dispositivos de cada região


Nesta secção, irá atualizar uma amostra de provisionamento no Azure IoT C SDK para ambos os VM regionais. 

O código de amostra simula uma sequência de arranque do dispositivo que envia o pedido de provisionamento para a sua instância do Serviço de Provisionamento de Dispositivos. A sequência de arranque fará com que o dispositivo seja reconhecido e atribuído ao hub IoT mais próximo da latência.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Abra **a amostra de \_ cliente/amostra de cliente/prov \_ dev \_ \_ /prov \_ dev \_ \_ dev.c** para edição em ambos os VMs.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que a `hsm_type` variável está definida `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` de acordo com o método de atestado do grupo de inscrição. 

    Guarde as alterações nos ficheiros em ambos os VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Em ambos os VMs, encontre a chamada para a `prov_dev_set_symmetric_key_info()` **amostra do cliente prov \_ \_ dev.c \_** que é comentada.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descompromete as chamadas de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelos IDs de registo únicos e teclas de dispositivo derivadas para cada dispositivo. As teclas apresentadas abaixo são apenas para fins. Use as chaves que gerou anteriormente.

    Leste dos EUA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Eua Ocidentais:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Guarde os ficheiros.

1. Em ambos os VMs, navegue para a pasta da amostra apresentada abaixo e construa a amostra.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Assim que a construção tiver sucesso, executar **o cliente prov \_ dev \_ \_sample.exe** em ambos os VMs para simular um dispositivo de inquilino de cada região. Note que cada dispositivo é atribuído ao centro IoT do inquilino mais próximo das regiões do dispositivo simulado.

    Executar a simulação:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exemplo de saída do East US VM:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Exemplo de saída do West US VM:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende continuar a trabalhar com os recursos criados neste artigo, pode deixá-los. Se não pretender continuar a utilizar o recurso, utilize as seguintes medidas para eliminar todos os recursos criados por este artigo para evitar encargos desnecessários.

Os passos aqui pressupõem que criou todos os recursos neste artigo, tal como instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para eliminar o grupo de recursos pelo nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. No **Filtro pelo nome...** textbox, digite o nome do grupo de recursos que contém os seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

4. É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos novamente para confirmar e, em seguida, clique em **Eliminar**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a reprovisionamento, ver

> [!div class="nextstepaction"]
> [Conceitos de reprovisionamento de dispositivos IoT Hub](concepts-device-reprovision.md)

* Para saber mais sobre desprovisionamento, ver
> [!div class="nextstepaction"]
> [Como desprovisionar dispositivos que foram previamente auto-aprovisionados](how-to-unprovision-devices.md)