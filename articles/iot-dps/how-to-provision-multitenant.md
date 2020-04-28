---
title: Como fornecer dispositivos para multitenancy no Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como fornecer dispositivos para multitenancy com a sua instância de Serviço de Provisionamento de Dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75434745"
---
# <a name="how-to-provision-for-multitenancy"></a>Como prever a multicapacidade 

As políticas de atribuição definidas pelo serviço de provisionamento apoiam uma variedade de cenários de atribuição. Dois cenários comuns são:

* **Geolocalização / GeoLatency**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada com o dispositivo aprovisionado no hub IoT mais próximo de cada local. Neste cenário, um grupo de centros IoT, que se estendem por regiões, são selecionados para inscrições. A política de atribuição de **latência mais baixa** é selecionada para estas matrículas. Esta política faz com que o Serviço de Provisionamento de Dispositivos avalie a latência do dispositivo e determine o hub IoT do grupo de hubs IoT. 

* **Multi-arrendamento**: Os dispositivos utilizados numa solução IoT podem ter de ser atribuídos a um hub específico de IoT ou a um grupo de hubs IoT. A solução pode exigir que todos os dispositivos para que um inquilino em particular se comunique com um grupo específico de hubs IoT. Em alguns casos, um inquilino pode possuir centros IoT e exigir que os dispositivos sejam atribuídos aos seus centros ioT.

É comum combinar estes dois cenários. Por exemplo, uma solução IoT multiarrendatária irá geralmente atribuir dispositivos de inquilino usando um grupo de centros IoT que estão espalhados por regiões. Estes dispositivos de inquilino podem ser atribuídos ao centro IoT daquele grupo, que tem a latência mais baixa com base na localização geográfica.

Este artigo utiliza uma amostra de dispositivo simulada do [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) para demonstrar como fornecer dispositivos num cenário multiarrendatário em todas as regiões. Realizará os seguintes passos neste artigo:

* Use o Azure CLI para criar dois centros iot regionais **(Oeste dos EUA** e **Eua Oriental)**
* Criar uma inscrição multiarrendatária
* Utilize o Azure CLI para criar dois VMs linux regionais para funcionar como dispositivos nas mesmas regiões **(Oeste dos EUA** e **Leste dos EUA)**
* Criar o ambiente de desenvolvimento para o Azure IoT C SDK em ambos os VMs Linux
* Simular os dispositivos para ver se estão previstos para o mesmo inquilino na região mais próxima.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md) arranque rápido.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Criar dois centros ioT regionais

Nesta secção, você usará a Casca de Nuvem Azure para criar dois novos centros regionais ioT nas regiões **oeste dos EUA** e Leste dos **EUA** para um inquilino.


1. Use a Casca de Nuvem Azure para criar um grupo de recursos com o [grupo Az criar](/cli/azure/group#az-group-create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

    O exemplo seguinte cria um grupo de recursos chamado *contoso-us-resource-group* na região *oriental.* Recomenda-se que utilize este grupo para todos os recursos criados neste artigo. Isto vai facilitar a limpeza depois de terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use a Casca de Nuvem Azure para criar um hub IoT na região **oriental** com o [hub az iot criar](/cli/azure/iot/hub#az-iot-hub-create) comando. O centro ioT será adicionado ao grupo de *recursos contoso-nos- nos.*

    O exemplo seguinte cria um hub IoT chamado *contoso-leste-hub* na localização *oriental.* Você deve usar seu próprio nome de hub único em vez de **contoso-leste-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Este comando pode levar alguns minutos para ser concluído.

3. Use a Casca de Nuvem Azure para criar um hub IoT na região **de Westus** com o [hub az iot criar](/cli/azure/iot/hub#az-iot-hub-create) comando. Este hub ioT também será adicionado ao grupo de *recursos contoso-nos- us.*

    O exemplo seguinte cria um hub IoT chamado *contoso-oeste-hub* na localização *westus.* Você deve usar seu próprio nome de hub único em vez de **contoso-oeste-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Este comando pode levar alguns minutos para ser concluído.



## <a name="create-the-multitenant-enrollment"></a>Criar a inscrição multiarrendatária

Nesta secção, você vai criar um novo grupo de inscrições para os dispositivos de inquilino.  

Para a simplicidade, este artigo usa a [chave simétrica](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere usar o [atestado de certificado X.509](concepts-security.md#x509-certificates) com uma cadeia de confiança.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua instância de Serviço de Provisionamento de Dispositivos.

2. Selecione o separador **'Gerir as matrículas'** e, em **seguida,** clique no botão do grupo adicionar na parte superior da página. 

3. No **Grupo adicionar inscrições,** introduza as seguintes informações e clique no botão **Guardar.**

    **Nome do grupo**: Contos **Entero-us-devices**.

    **Tipo de atestado**: Selecione **a chave simétrica**.

    **Chaves de geração automática**: Esta caixa de verificação já deve ser verificada.

    **Selecione como pretende atribuir dispositivos aos cubos**: **Selecione a latência mais baixa**.

    ![Adicione o grupo de inscrição multiarrendatária para o atestado de chave simétrica](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. No **Add Registration Group,** clique em **Link um novo hub IoT** para ligar ambos os seus centros regionais.

    **Subscrição**: Se tiver várias subscrições, escolha a subscrição onde criou os hubs ioT regionais.

    **Hub IoT**: Selecione um dos centros regionais que criou.

    **Política de Acesso**: Escolha **iothubowner**.

    ![Ligue os centros regionais de IoT com o serviço de provisionamento](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Uma vez ligados ambos os centros regionais de IoT, deve selecioná-los para o grupo de inscrições e clicar em **Save** para criar o grupo regional ioT hub para a inscrição.

    ![Criar o grupo de hub regional para a inscrição](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Depois de salvar a matrícula, reabra-a e tome nota da **Chave Primária.** Tem de poupar a inscrição primeiro para gerar as chaves. Esta chave será utilizada para gerar chaves únicas para dispositivos simulados posteriormente.


## <a name="create-regional-linux-vms"></a>Criar VMs regionais linux

Nesta secção, irá criar duas máquinas virtuais regionais do Linux (VMs). Estes VMs executarão uma amostra de simulação de dispositivos de cada região para demonstrar o fornecimento de dispositivos de dispositivos de inquilinos de ambas as regiões.

Para facilitar a limpeza, estes VMs serão adicionados ao mesmo grupo de recursos que contém os centros IoT que foram criados, *grupo de recursos contoso-nos-recursos.* No entanto, os VMs funcionarão em regiões separadas **(Eua Ocidental** e **Leste dos EUA).**

1. Na Nuvem Azure, execute o seguinte comando para criar uma VM da região **leste dos EUA** depois de efazer as seguintes alterações de parâmetros no comando:

    **-name**: Introduza um nome único para o seu dispositivo regional **dos EUA Oriental** VM. 

    **--admin-username**: Use o seu próprio nome de utilizador administrativo.

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

    Este comando vai demorar alguns minutos a ser executado. Uma vez concluído o comando, tome nota do valor **do IpAddress público** para a sua região Leste dos EUA VM.

1. Na Nuvem Azure, execute o comando para criar um VM da região **oeste dos EUA** depois de efazer as seguintes alterações de parâmetros no comando:

    **-name**: Introduza um nome único para o seu dispositivo regional VM do seu dispositivo regional **dos EUA Ocidental.** 

    **--admin-username**: Use o seu próprio nome de utilizador administrativo.

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

    Este comando vai demorar alguns minutos a ser executado. Uma vez concluído o comando, tome nota do valor **do IpAddress público** para a sua região VM da região oeste dos EUA.

1. Abra duas cápsulas de linha de comando. Ligue-se a um dos VMs regionais em cada concha utilizando SSH. 

    Passe o seu nome de utilizador administrativo e o endereço IP público que observou para o VM como parâmetros para SSH. Introduza a palavra-passe do administrador quando solicitado.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Prepare o ambiente de desenvolvimento do Azure IoT C SDK

Nesta secção, irá clonar o Azure IoT C SDK em cada VM. O SDK contém uma amostra que simulará o fornecimento de dispositivos de um inquilino de cada região.

1. Para cada VM, instale **CMake,** **g++,** **gcc**e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) utilizando os seguintes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Encontre o nome da etiqueta para o [mais recente lançamento](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

1. Clone o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) em ambos os VMs.  Utilize a etiqueta encontrada no passo anterior `-b` como valor para o parâmetro:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Para ambos os VMs, crie uma nova pasta de **cmake** dentro do repositório e mude para essa pasta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Para ambos os VMs, execute o seguinte comando, que constrói uma versão do SDK específica para a sua plataforma de cliente de desenvolvimento. 

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


## <a name="derive-unique-device-keys"></a>Obter chaves únicas do dispositivo

Ao utilizar o atestado de chave simétrica com matrículas em grupo, não utilize diretamente as chaves do grupo de matrículas. Em vez disso, cria-se uma chave derivada única para cada dispositivo e mencionada em Inscrições em [Grupo com teclas simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

Para gerar a chave do dispositivo, utilize a chave principal do grupo para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave principal do grupo no código do seu dispositivo.

Utilize o exemplo da concha Bash para criar uma chave de dispositivo derivada para cada dispositivo utilizando **o openssl**.

- Substitua o valor por **KEY** pela **Chave Primária** que observou anteriormente para a sua inscrição.

- Substitua o valor por **REG_ID** pelo seu próprio ID de registo único para cada dispositivo. Utilize caracteres alfanuméricos e traços inferiores para definir ambos os IDs.

Exemplo de geração chave de dispositivos para *contoso-simdispositivo-leste:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exemplo de geração chave de dispositivos para *contoso-simdevice-oeste:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Os dispositivos de inquilino utilizarão cada um a sua chave de dispositivo derivada e identificação de registo única para realizar o atestado de chave simétrica com o grupo de inscrição durante o fornecimento aos centros IoT do inquilino.




## <a name="simulate-the-devices-from-each-region"></a>Simular os dispositivos de cada região


Nesta secção, irá atualizar uma amostra de provisionamento no Azure IoT C SDK para ambos os VMs regionais. 

O código da amostra simula uma sequência de arranque do dispositivo que envia o pedido de provisionamento para a sua instância de Serviço de Provisionamento de Dispositivos. A sequência de arranque fará com que o dispositivo seja reconhecido e atribuído ao centro IoT que está mais próximo com base na latência.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Aberto **\_~/azure-iot-sdk-c/provisioning client/samples/prov\_\_\_\_\_\_dev client sample/prov dev client sample.c** para edição em ambos os VMs.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se `hsm_type` de que `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` a variável está definida como mostrado abaixo para corresponder ao método de atestação do grupo de matrículas. 

    Guarde as suas alterações nos ficheiros em ambos os VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Em ambos os VMs, `prov_dev_set_symmetric_key_info()` encontre a chamada para em **prov\_dev\_client\_sample.c** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descodere as chamadas de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelos IDs de registo únicos e chaves derivadas do dispositivo para cada dispositivo. As teclas abaixo são apenas para fins por exemplo. Use as chaves que gerou anteriormente.

    Leste dos EUA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Oeste dos EUA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Guarde os ficheiros.

1. Em ambos os VMs, navegue para a pasta da amostra mostrada abaixo e construa a amostra.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Uma vez que a construção tenha sucesso, execute **prov\_dev\_client\_sample.exe** em ambos os VMs para simular um dispositivo de inquilino de cada região. Note que cada dispositivo é alocado ao centro ioT inquilino mais próximo das regiões do dispositivo simulado.

    Executar a simulação:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Saída de exemplo do VM dos EUA Orientais:

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

    Saída de exemplo do West US VM:
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



## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar com os recursos criados neste artigo, pode deixá-los. Se não pretende continuar a utilizar o recurso, utilize os seguintes passos para eliminar todos os recursos criados por este artigo para evitar encargos desnecessários.

Os passos aqui assumem que criou todos os recursos neste artigo, tal como instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para eliminar o grupo de recursos pelo nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. No **Filtro pelo nome...** textbox, digite o nome do grupo de recursos que contém os seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

4. É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos novamente para confirmar e, em seguida, clique em **Eliminar**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

- Para aprender mais Reprovisionamento, consulte [conceitos de reprovisionamento de dispositivos IoT Hub](concepts-device-reprovision.md) 
- Para saber mais Deprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente auto-provisionados](how-to-unprovision-devices.md) 











