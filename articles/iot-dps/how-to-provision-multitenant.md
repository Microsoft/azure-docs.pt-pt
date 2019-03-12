---
title: Como aprovisionar dispositivos para arquitetura "multitenancy", em que o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como aprovisionar dispositivos para arquitetura "multitenancy" com a sua instância do serviço de aprovisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ea071b704c58383ce52615bb1dfd88df8017ef7f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544448"
---
# <a name="how-to-provision-for-multitenancy"></a>Como aprovisionar para arquitetura "multitenancy" 

As políticas de alocação definidas pelo serviço de aprovisionamento suportam uma variedade de cenários de alocação. Dois cenários comuns são:

* **Geolocalização / GeoLatency**: Como um dispositivo se move entre localizações, a latência de rede foi aprimorada fazendo com que o dispositivo aprovisionado no hub IoT mais próximos de cada localização. Neste cenário, um grupo de hubs IoT, que abrangem em várias regiões, estão selecionados para inscrições. O **latência mais baixa** política de alocação está selecionada para essas inscrições. Esta política faz com que o serviço de aprovisionamento de dispositivos avaliar a latência de dispositivo e determinar o armário hub IoT para fora do grupo de IoT hubs. 

* **Vários inquilinos**: Dispositivos utilizados dentro de uma solução de IoT poderão ter de ser atribuídos a um hub de IoT específico ou grupo de IoT hubs. A solução pode exigir que todos os dispositivos para um inquilino específico comunicar com um grupo específico de IoT hubs. Em alguns casos, um inquilino pode possuir os hubs IoT e exigir que os dispositivos a serem atribuídas aos seus hubs IoT.

É comum para combinar esses dois cenários. Por exemplo, uma solução de IoT multi-inquilino normalmente irá atribuir dispositivos de inquilino através de um grupo de hubs IoT que estão espalhados entre regiões. Estes dispositivos de inquilino podem ser atribuídos ao hub IoT nesse grupo, que tem a menor latência com base na localização geográfica.

Este artigo utiliza uma amostra de um dispositivo simulado a [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) para demonstrar como aprovisionar dispositivos num cenário de multi-inquilino em várias regiões. Realizará os seguintes passos neste artigo:

* Utilizar a CLI do Azure para criar dois os IoT hubs regionais (**E.U.A. oeste** e **E.U.A. Leste**)
* Criar uma inscrição de multi-inquilino
* Utilizar a CLI do Azure para criar duas VMs de Linux regional para atuar como dispositivos nas mesmas regiões (**E.U.A. oeste** e **E.U.A. Leste**)
* Configurar o ambiente de desenvolvimento para o SDK de C do Azure IoT em ambas as VMs do Linux
* Simule os dispositivos que estão aprovisionada para o mesmo inquilino na região mais próxima.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [configurar o serviço aprovisionamento de dispositivos Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) início rápido.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Criar dois os IoT hubs regionais

Nesta secção, irá utilizar o Azure Cloud Shell para criar dois novos IoT hubs regionais no **E.U.A. oeste** e **E.U.A. Leste** regiões para um inquilino.


1. Utilizar o Azure Cloud Shell para criar um grupo de recursos com o [criar grupo az](/cli/azure/group#az-group-create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

    O exemplo seguinte cria um grupo de recursos chamado *contoso-nos-resource-group* no *eastus* região. Recomenda-se que utilize este grupo para todos os recursos criados neste artigo. Isso facilitará de limpeza do wsu depois de terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Utilizar o Azure Cloud Shell para criar um hub IoT a **eastus** região com o [criar hub de iot de az](/cli/azure/iot/hub#az-iot-hub-create) comando. O hub IoT será adicionado para o *contoso-nos-resource-group*.

    O exemplo seguinte cria um hub IoT com o nome *contoso-Leste-hub* no *eastus* localização. Tem de utilizar o seu próprio nome de hub exclusivo em vez de **contoso-Leste-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Este comando pode demorar alguns minutos a concluir.

3. Utilizar o Azure Cloud Shell para criar um hub IoT a **westus** região com o [criar hub de iot de az](/cli/azure/iot/hub#az-iot-hub-create) comando. Este hub IoT, também será adicionado para o *contoso-nos-resource-group*.

    O exemplo seguinte cria um hub IoT com o nome *contoso-oeste-hub* no *westus* localização. Tem de utilizar o seu próprio nome de hub exclusivo em vez de **contoso-oeste-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Este comando pode demorar alguns minutos a concluir.



## <a name="create-the-multitenant-enrollment"></a>Criar a inscrição de multi-inquilino

Nesta secção, irá criar um novo grupo de inscrição para os dispositivos de inquilino.  

Para simplificar, este artigo usa [atestado de chave simétrico](concepts-symmetric-key-attestation.md) com a inscrição. Para uma solução mais segura, considere a utilização [atestado de certificado X.509](concepts-security.md#x509-certificates) com uma cadeia de fidedignidade.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a sua instância do serviço aprovisionamento de dispositivos.

2. Selecione o **gerir inscrições** separador e, em seguida, clique nas **adicionar grupo de inscrição** botão na parte superior da página. 

3. No **adicionar grupo de inscrição**, introduza as seguintes informações e clique nas **guardar** botão.

    **Nome do grupo**: Introduza **contoso-nos-dispositivos**.

    **Tipo de atestado**: Selecione **chave simétrica**.

    **Chaves de geração automática**: Esta caixa de verificação já deve ser verificada.

    **Selecione como pretende atribuir dispositivos a hubs**: Selecione **latência mais baixa**.

    ![Adicionar grupo de inscrição de multi-inquilino para o atestado de chave simétrico](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. No **adicionar grupo de inscrição**, clique em **ligar um hub IoT novo** para associar ambos os hubs regionais.

    **Subscrição**: Se tiver várias subscrições, escolha a subscrição onde criou os hubs IoT regionais.

    **IoT hub**: Selecione um dos hubs regionais, que criou.

    **Política de acesso**: Escolher **iothubowner**.

    ![Ligar os hubs IoT regionais com o serviço de aprovisionamento](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Depois de ambos os hubs IoT regionais foram associados, tem de selecioná-los para o grupo de inscrição e clique em **guardar** para criar o grupo de hub IoT regional para a inscrição.

    ![Criar o grupo de regional hub para a inscrição](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Depois de guardar a inscrição, abri-lo novamente e anote o **chave primária**. Tem de guardar primeiro para ter as chaves geradas o Registro. Esta chave será utilizada para gerar chaves de dispositivo exclusivo para ambos os dispositivos simulados mais tarde.


## <a name="create-regional-linux-vms"></a>Criar VMs do Linux regional

Nesta secção, irá criar dois regionais máquinas virtuais do Linux (VMs). Estas VMs serão executado um exemplo de simulação de dispositivo de cada região para demonstrar o aprovisionamento de dispositivos para os dispositivos de inquilino em ambas as regiões.

Para fazer a limpeza, estas VMs será adicionado ao mesmo grupo de recursos que contém os hubs IoT que foram criados *contoso-nos-resource-group*. No entanto, as VMs serão executados em diferentes regiões (**E.U.A. oeste** e **E.U.A. Leste**).

1. No Azure Cloud Shell, execute o seguinte comando para criar uma **E.U.A. Leste** região VM depois de efetuar as seguintes alterações de parâmetro no comando:

    **--name**: Introduza um nome exclusivo para o seu **E.U.A. Leste** dispositivo regional VM. 

    **nome de utilizador – administrador**: Utilize o seu próprio nome de utilizador de administrador.

    **palavra-passe de administrador –**: Utilize a sua própria palavra-passe de administrador.

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

    Este comando irá demorar alguns minutos a concluir. Assim que o comando for concluído, anote o **publicIpAddress** valor para a sua VM de região E.U.A. Leste.

1. No Azure Cloud Shell, execute o comando para criar uma **E.U.A. oeste** região VM depois de efetuar as seguintes alterações de parâmetro no comando:

    **--name**: Introduza um nome exclusivo para o seu **E.U.A. oeste** dispositivo regional VM. 

    **nome de utilizador – administrador**: Utilize o seu próprio nome de utilizador de administrador.

    **palavra-passe de administrador –**: Utilize a sua própria palavra-passe de administrador.

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

    Este comando irá demorar alguns minutos a concluir. Assim que o comando for concluído, anote o **publicIpAddress** valor para a sua VM de região E.U.A. oeste.

1. Abra duas shells de linha de comandos. Ligar a uma das VMs regionais em cada shell através de SSH. 

    Transmita o seu nome de utilizador de administrador e o endereço IP público que anotou para a VM como parâmetros para encaminhar o SSH. Introduza a palavra-passe de administrador quando lhe for pedido.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento do SDK de C do Azure IoT

Nesta secção, irá clone o SDK de C do Azure IoT em cada VM. O SDK contém um exemplo que simulará a partir de cada região de aprovisionamento de dispositivos de um inquilino.


1. Para cada VM, instale **Cmake**, **g + +**, **gcc**, e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) através dos seguintes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Clone o [SDK de C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) em ambas as VMs.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Atualmente, o tamanho deste repositório é de cerca de 220 MB. Esta operação deve demorar vários minutos a ser concluída.

1. Para ambas as VMs, crie um novo **cmake** pasta dentro do repositório e a alteração para essa pasta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Para ambas as VMs, execute o seguinte comando, que cria uma versão do SDK específica da sua plataforma de cliente de desenvolvimento. 

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


## <a name="derive-unique-device-keys"></a>Derivar as chaves de dispositivo exclusivo

Ao utilizar o atestado de chave simétrico com inscrições em grupo, não utilize as chaves de grupo de inscrição diretamente. Em vez disso, criar exclusivo derivado chave para cada dispositivo e mencionado na [inscrições de grupo com chaves simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

Para gerar a chave de dispositivo, utilize a chave mestra de grupo para computar um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave mestra de grupo no seu código de dispositivo.

Utilize o exemplo de shell do Bash para criar uma chave de dispositivo derivada para cada dispositivo a utilizar **openssl**.

- Substitua o valor para **chave** com o **chave primária** que anotou anteriormente para a sua inscrição.

- Substitua o valor para **REG_ID** com o seu próprio ID de registo único para cada dispositivo. Utilizar minúsculas, carateres alfanuméricos e de traço ('-') caracteres para definir os dois IDs.

Geração de chaves de dispositivo de exemplo para *contoso-leste da Ásia-simdevice*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Geração de chaves de dispositivo de exemplo para *contoso-simdevice-oeste*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Os dispositivos de inquilino cada usará seus chave derivada de dispositivo e o ID de registo único para efetuar o atestado de chave simétrico com o grupo de inscrição durante o aprovisionamento para o inquilino IoT hubs.




## <a name="simulate-the-devices-from-each-region"></a>Simular os dispositivos móveis de cada região


Nesta secção, irá atualizar um exemplo de aprovisionamento no SDK de C do IoT do Azure para ambas as VMs regionais. 

O código de exemplo simula uma sequência de arranque de dispositivo que envia a solicitação de provisionamento à sua instância do serviço aprovisionamento de dispositivos. A sequência de arranque fará com que o dispositivo a serem reconhecidas e atribuído ao hub IoT que é o mais próximo com base na latência.

1. No portal do Azure, selecione o separador **Descrição Geral** do Serviço de Aprovisionamento de Dispositivos e anote o valor de **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Open **~/azure-iot-sdk-c/provisioning\_amostras/cliente/prov\_dev\_cliente\_exemplo/prov\_dev\_cliente\_Sample**para a edição em ambas as VMs.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que o `hsm_type` variável é definida como `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` conforme mostrado abaixo, de acordo com o método de atestado do grupo de inscrição. 

    Guarde as alterações aos ficheiros em ambas as VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Em ambas as VMs, encontre a chamada para `prov_dev_set_symmetric_key_info()` no **prov\_dev\_cliente\_Sample** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Anule os comentários as chamadas de função e substitua os valores de marcador de posição (incluindo os parênteses angulares) com os IDs de registo único e as chaves de dispositivo derivada para cada dispositivo. As chaves mostradas a seguir são, por exemplo, apenas para fins. Utilize as teclas que gerou anteriormente.

    E.U. a leste:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Oeste dos E.U.A.:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Guarde os ficheiros.

1. Em ambas as VMs, navegue para a pasta de exemplo mostrada abaixo e criar o exemplo.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Depois da compilação for concluída com êxito, execute **prov\_dev\_cliente\_sample.exe** em ambas as VMs para simular um dispositivo de inquilino em cada região. Tenha em atenção que cada dispositivo é atribuído ao inquilino do hub IoT mais próximo de regiões do dispositivo simulado.

    Execute a simulação:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exemplo de saída do leste e.u.a. centro-VM:

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

    Exemplo de saída da oeste-na VM:
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











