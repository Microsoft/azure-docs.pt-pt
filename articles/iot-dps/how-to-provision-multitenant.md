---
title: Como provisionar dispositivos para multilocação no serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como provisionar dispositivos para multilocação com sua instância do serviço de provisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 6d9755c076763a72d54abb66cfdf01b0ac7ffb9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228795"
---
# <a name="how-to-provision-for-multitenancy"></a>Como provisionar para multilocação 

As políticas de alocação definidas pelo serviço de provisionamento oferecem suporte a uma variedade de cenários de alocação. Dois cenários comuns são:

* **Geolocalização/geolatência**: como um dispositivo se move entre locais, a latência de rede é aprimorada com o dispositivo provisionado para o Hub IOT mais próximo de cada local. Nesse cenário, um grupo de hubs IoT, que abrangem várias regiões, é selecionado para registros. A política de alocação de **latência mais baixa** é selecionada para esses registros. Essa política faz com que o serviço de provisionamento de dispositivos avalie a latência do dispositivo e determine o Hub IoT do armário fora do grupo de hubs IoT. 

* **Multilocação**: os dispositivos usados em uma solução de IOT podem precisar ser atribuídos a um hub IOT específico ou a um grupo de hubs IOT. A solução pode exigir que todos os dispositivos de um locatário específico se comuniquem com um grupo específico de hubs IoT. Em alguns casos, um locatário pode possuir hubs IoT e exigir que os dispositivos sejam atribuídos aos seus hubs IoT.

É comum combinar esses dois cenários. Por exemplo, uma solução IoT multilocatário normalmente atribuirá dispositivos de locatário usando um grupo de hubs IoT espalhados entre regiões. Esses dispositivos de locatário podem ser atribuídos ao Hub IoT nesse grupo, que tem a menor latência com base na localização geográfica.

Este artigo usa um exemplo de dispositivo simulado do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) para demonstrar como provisionar dispositivos em um cenário multilocatário entre regiões. Você executará as seguintes etapas neste artigo:

* Use o CLI do Azure para criar dois hubs de IoT regionais (**oeste dos EUA** e **leste dos EUA**)
* Criar um registro multilocatário
* Use o CLI do Azure para criar duas VMs Linux regionais para atuar como dispositivos nas mesmas regiões (**oeste dos EUA** e **leste dos EUA**)
* Configurar o ambiente de desenvolvimento para o SDK do Azure IoT C em ambas as VMs do Linux
* Simule os dispositivos para ver que eles são provisionados para o mesmo locatário na região mais próxima.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do serviço de provisionamento de dispositivos no Hub IOT com o guia de início rápido do portal do Azure](./quick-setup-auto-provision.md) .


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Criar dois hubs IoT regionais

Nesta seção, você usará o Azure Cloud Shell para criar dois novos hubs de IoT regionais nas regiões **oeste dos EUA** e **leste dos EUA** para um locatário.


1. Use o Azure Cloud Shell para criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group#az-group-create) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

    O exemplo a seguir cria um grupo de recursos chamado *contoso-US-Resource-Group* na região *eastus* . É recomendável que você use esse grupo para todos os recursos criados neste artigo. Isso facilitará a limpeza depois que você terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Use o Azure Cloud Shell para criar um hub IoT na região **lesteus** com o comando [AZ IOT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . O Hub IoT será adicionado ao grupo de *recursos contoso-US-Resource*.

    O exemplo a seguir cria um hub IoT chamado *contoso-East-Hub* no local *eastus* . Você deve usar seu próprio nome de Hub exclusivo em vez do **contoso-East-Hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Esse comando pode levar alguns minutos para ser concluído.

3. Use o Azure Cloud Shell para criar um hub IoT na região **westus** com o comando [AZ IOT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Esse Hub IoT também será adicionado ao grupo de *recursos contoso-US-Resource*.

    O exemplo a seguir cria um hub IoT chamado *contoso-West-Hub* no local *westus* . Você deve usar seu próprio nome de Hub exclusivo em vez do **contoso-West-Hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Esse comando pode levar alguns minutos para ser concluído.



## <a name="create-the-multitenant-enrollment"></a>Criar o registro multilocatário

Nesta seção, você criará um novo grupo de registro para os dispositivos de locatário.  

Para simplificar, este artigo usa o [atestado de chave simétrica](concepts-symmetric-key-attestation.md) com o registro. Para obter uma solução mais segura, considere o uso do [atestado de certificado X. 509](concepts-security.md#x509-certificates) com uma cadeia de confiança.

1. Entre no [portal do Azure](https://portal.azure.com)e abra a instância do serviço de provisionamento de dispositivos.

2. Selecione a guia **gerenciar registros** e, em seguida, clique no botão **Adicionar grupo de registros** na parte superior da página. 

3. Em **Adicionar grupo de registro**, insira as informações a seguir e clique no botão **salvar** .

    **Nome do grupo**: insira **contoso-US-Devices**.

    **Tipo de atestado**: selecione **chave simétrica**.

    **Gerar chaves automaticamente**: essa caixa de seleção já deve estar marcada.

    **Selecione como você deseja atribuir dispositivos a hubs**: selecione **menor latência**.

    ![Adicionar grupo de registro multilocatário para atestado de chave simétrica](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Em **Adicionar grupo de registro**, clique em **vincular um novo hub IOT** para vincular os dois hubs regionais.

    **Assinatura**: se você tiver várias assinaturas, escolha a assinatura em que você criou os hubs de IOT regionais.

    **Hub IOT**: selecione um dos hubs regionais que você criou.

    **Política de acesso**: escolha **iothubowner**.

    ![Vincular os hubs de IoT regionais ao serviço de provisionamento](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Depois que os hubs IoT regionais tiverem sido vinculados, você deverá selecioná-los para o grupo de registro e clicar em **salvar** para criar o grupo de Hub IOT regional para o registro.

    ![Criar o grupo de Hub regional para o registro](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Depois de salvar o registro, reabra-o e anote a **chave primária**. Você deve salvar o registro primeiro para que as chaves sejam geradas. Essa chave será usada para gerar chaves de dispositivo exclusivas para os dispositivos simulados posteriormente.


## <a name="create-regional-linux-vms"></a>Criar VMs Linux regionais

Nesta seção, você criará duas VMs (máquinas virtuais) Linux regionais. Essas VMs executarão um exemplo de simulação de dispositivo de cada região para demonstrar o provisionamento do dispositivo para dispositivos de locatário de ambas as regiões.

Para facilitar a limpeza, essas VMs serão adicionadas ao mesmo grupo de recursos que contém os hubs IoT que foram criados, *contoso-US-Resource-Group*. No entanto, as VMs serão executadas em regiões separadas (**oeste dos EUA** e **leste dos EUA**).

1. No Azure Cloud Shell, execute o seguinte comando para criar uma VM de região **leste dos EUA** depois de fazer as seguintes alterações de parâmetro no comando:

    **--Name**: Insira um nome exclusivo para a VM do dispositivo regional **leste dos EUA** . 

    **--admin-username**: Use seu próprio nome de usuário de administrador.

    **--admin-password**: Use sua própria senha de administrador.

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

    Este comando levará alguns minutos para ser concluído. Depois que o comando for concluído, anote o valor **publicIpAddress** para a VM da região leste dos EUA.

1. No Azure Cloud Shell, execute o comando para criar uma VM da região **oeste dos EUA** depois de fazer as seguintes alterações de parâmetro no comando:

    **--Name**: Insira um nome exclusivo para a VM do dispositivo regional do **oeste dos EUA** . 

    **--admin-username**: Use seu próprio nome de usuário de administrador.

    **--admin-password**: Use sua própria senha de administrador.

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

    Este comando levará alguns minutos para ser concluído. Após a conclusão do comando, anote o valor **publicIpAddress** da VM da região oeste dos EUA.

1. Abra dois shells de linha de comando. Conecte-se a uma das VMs regionais em cada shell usando SSH. 

    Passe o nome de usuário do administrador e o endereço IP público que você anotou para a VM como parâmetros para SSH. Insira a senha de administrador quando solicitado.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento do SDK do Azure IoT C

Nesta seção, você clonará o SDK do Azure IoT C em cada VM. O SDK contém um exemplo que simulará o provisionamento de dispositivos de um locatário de cada região.


1. Para cada VM, instale **CMake**, **g + +** , **gcc**e [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) usando os seguintes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Clone o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) em ambas as VMs.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Para ambas as VMs, crie uma nova pasta **CMake** dentro do repositório e altere para essa pasta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Para ambas as VMs, execute o comando a seguir, que cria uma versão do SDK específica para sua plataforma de cliente de desenvolvimento. 

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


## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo exclusivas

Ao usar o atestado de chave simétrica com registros de grupo, você não usa as chaves do grupo de registro diretamente. Em vez disso, você cria uma chave derivada exclusiva para cada dispositivo e é mencionada em [registros de grupo com chaves simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

Para gerar a chave do dispositivo, use a chave mestra de grupo para computar um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro exclusiva do dispositivo e converter o resultado no formato base64.

Não inclua sua chave mestra de grupo em seu código de dispositivo.

Use o exemplo de shell bash para criar uma chave de dispositivo derivada para cada dispositivo usando **OpenSSL**.

- Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente para seu registro.

- Substitua o valor de **REG_ID** pela sua própria ID de registro exclusiva para cada dispositivo. Use caracteres alfanuméricos minúsculos e traços ('-') para definir ambas as IDs.

Exemplo de geração de chave de dispositivo para *contoso-simdevice-leste*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exemplo de geração de chave de dispositivo para *contoso-simdevice-West*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Os dispositivos de locatário usarão cada chave de dispositivo derivada e ID de registro exclusivo para executar o atestado de chave simétrica com o grupo de registro durante o provisionamento para os hubs IoT de locatário.




## <a name="simulate-the-devices-from-each-region"></a>Simular os dispositivos de cada região


Nesta seção, você atualizará um exemplo de provisionamento no SDK do Azure IoT C para ambas as VMs regionais. 

O código de exemplo simula uma sequência de inicialização de dispositivo que envia a solicitação de provisionamento para a instância do serviço de provisionamento de dispositivos. A sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído ao Hub IoT mais próximo com base na latência.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Abra **~/azure-iot-sdk-c/provisioning\_cliente/amostras/prov\_dev\_cliente\_exemplo/prov\_dev\_cliente\_Sample. c** para edição em ambas as VMs.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Localize a definição da função `main()` no mesmo ficheiro. Verifique se a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` conforme mostrado abaixo para corresponder ao método de atestado do grupo de registro. 

    Salve as alterações nos arquivos em ambas as VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Em ambas as VMs, localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_cliente\_exemplo. c** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova a marca de comentário das chamadas de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) por IDs de registro exclusivas e chaves de dispositivo derivadas para cada dispositivo. As chaves mostradas abaixo são apenas para fins de exemplo. Use as chaves que você gerou anteriormente.

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

    Salve os arquivos.

1. Em ambas as VMs, navegue até a pasta de exemplo mostrada abaixo e compile o exemplo.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Depois que a compilação for realizada com sucesso, execute o **prov\_dev\_cliente\_Sample. exe** em ambas as VMs para simular um dispositivo de locatário de cada região. Observe que cada dispositivo é alocado para o Hub IoT do locatário mais próximo às regiões do dispositivo simulado.

    Execute a simulação:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exemplo de saída da VM leste dos EUA:

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

    Exemplo de saída da VM oeste dos EUA:
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

Se você planeja continuar trabalhando com os recursos criados neste artigo, você pode deixá-los. Se você não planeja continuar usando o recurso, use as etapas a seguir para excluir todos os recursos criados por este artigo para evitar encargos desnecessários.

As etapas aqui pressupõem que você criou todos os recursos neste artigo, conforme instruído no mesmo grupo de recursos chamado **contoso-US-Resource-Group**.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado o Hub IoT no interior de um grupo de recursos existente que contenha recursos que pretende manter, elimine apenas o recurso do Hub IoT, em vez de eliminar o grupo de recursos.
>

Para excluir o grupo de recursos por nome:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...** , digite o nome do grupo de recursos que contém seus recursos, **contoso-US-Resource-Group**. 

3. À direita do seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

4. É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos novamente para confirmar e, em seguida, clique em **Eliminar**. Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md) 
- Para saber mais sobre desprovisionamento, confira [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md) 











