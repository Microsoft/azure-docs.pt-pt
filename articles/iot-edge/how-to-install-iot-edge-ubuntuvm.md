---
title: Executar Azure IoT Edge em Máquinas Virtuais Ubuntu Microsoft Docs
description: Instruções de configuração Azure IoT Edge para máquinas virtuais Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 95fd10ab7de4885d3630b5defe4080fe0203b62f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296982"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar borda Azure IoT em máquinas virtuais Ubuntu

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud.

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais os componentes incluídos, consulte [o tempo de execução do Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para implantar uma máquina virtual Ubuntu 18.04 LTS com o tempo de execução Azure IoT Edge instalado e configurado utilizando uma cadeia de ligação do dispositivo pré-fornecida. A implementação é realizada usando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) baseado [em nuvem](../virtual-machines/linux/using-cloud-init.md
) mantido no repositório de projeto [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Na primeira bota, a máquina virtual Ubuntu 18.04 LTS [instala a versão mais recente do tempo de funcionamento do Azure IoT Edge através da cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Também define uma cadeia de ligação fornecida antes do início do tempo de funcionamento, permitindo-lhe configurar e ligar facilmente o dispositivo IoT Edge sem a necessidade de iniciar uma sessão de ambiente de trabalho SSH ou remote. 

## <a name="deploy-using-deploy-to-azure-button"></a>Implementar utilizando o botão Azure

O [Botão Implementar para Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) permite a implementação simplificada dos modelos do Gestor de [Recursos Azure mantidos](../azure-resource-manager/templates/overview.md) no GitHub.  Esta secção demonstrará a utilização do Botão Deploy to Azure contido no repositório de projeto [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)  


1. Implementaremos um Azure IoT Edge habilitado para o Linux VM utilizando o modelo de Gestor de Recursos Azure iotedge-vm-deploy.  Para começar, clique no botão abaixo:

    [![Botão Implementar no Azure iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Na janela recém-lançada, preencha os campos de formulários disponíveis:

    > [!div class="mx-imgBorder"]
    > [![Captura de ecrã a mostrar o modelo iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subscrição**: A subscrição ativa do Azure para implantar a máquina virtual.

    **Grupo de recursos**: Um Grupo de Recursos existente ou recém-criado para conter a máquina virtual e os seus recursos associados.

    **Prefixo da etiqueta DO DNS**: Um valor necessário à sua escolha que é usado para pré-fixar o nome de anfitrião da máquina virtual.

    **Nome de utilizador Admin**: Um nome de utilizador, que será fornecido privilégios de raiz na implementação.

    **Cadeia de ligação do dispositivo**: uma [cadeia de ligação](how-to-register-device.md) do dispositivo para um dispositivo que foi criado dentro do seu [hub IoT](../iot-hub/about-iot-hub.md)pretendido .

    **Tamanho VM**: O [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual a ser implantada

    **Versão Ubuntu OS**: A versão do Ubuntu OS a ser instalada na máquina virtual base.

    **Localização**: A [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) para implantar a máquina virtual, este valor desresídeo à localização do Grupo de Recursos selecionado.

    **Tipo de autenticação**: Escolha **sshPublicKey** ou **palavra-passe** dependendo da sua preferência.

    **Palavra-passe ou chave de administração**: O valor da Chave Pública SSH ou o valor da palavra-passe dependendo da escolha do Tipo de Autenticação.

    Quando todos os campos tiverem sido preenchidos, selecione a caixa de verificação na parte inferior da página para aceitar os termos e selecione **Comprar** para iniciar a implementação.

1. Verifique se a implementação foi concluída com êxito.  Um recurso da máquina virtual devia ter sido implementado no grupo de recursos selecionados.  Tome nota do nome da máquina, este deve estar no formato `vm-0000000000000` . Além disso, tome nota do **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **Nome DNS ** pode ser obtido na secção **Descrição geral** da máquina virtual recentemente implementada no portal do Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mostrando o nome dns do iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se pretender entrar neste VM após a configuração, utilize o **Nome DNS** associado com o comando:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implantação a partir de Azure CLI

1. Certifique-se de que instalou a extensão Azure CLI iot com:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Em seguida, se estiver a utilizar o Azure CLI no seu ambiente de trabalho, comece por iniciar sessão:

   ```azurecli-interactive
   az login
   ```

1. Se tiver várias subscrições, selecione a subscrição que pretende utilizar:
   1. Liste as suas subscrições:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriçãoID para a subscrição que pretende utilizar.

   1. Desa cotado a sua subscrição de trabalho com o ID que copiou:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Criar um novo grupo de recursos (ou especificar um existente nos próximos passos):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Criar uma nova máquina virtual:

    Para utilizar um Tipo de **autenticação,** `password` consulte o exemplo abaixo:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Para autenticar com uma chave SSH, poderá fazê-lo especificando um Tipo de **autenticação,** `sshPublicKey` em seguida, fornecer o valor da chave SSH no parâmetro **adminPasswordOrKey.**  Apresentamos um exemplo abaixo.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Verifique se a implementação foi concluída com êxito.  Um recurso da máquina virtual devia ter sido implementado no grupo de recursos selecionados.  Tome nota do nome da máquina, este deve estar no formato `vm-0000000000000` . Além disso, tome nota do **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido a partir da saída formatada por JSON do passo anterior, dentro da secção **de saídas** como parte da entrada **pública do SSH.**  O valor desta entrada pode ser utilizado para o SSH na máquina recém-implantada.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    O **Nome DNS** também pode ser obtido a partir da secção **de visão geral** da máquina virtual recém-implantada dentro do portal Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mostrando o nome dns do iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se pretender entrar neste VM após a configuração, utilize o **Nome DNS** associado com o comando:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge alojotado com o tempo de funcionaamento instalado, pode [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se tiver problemas com a instalação correta do tempo de funcionamento do IoT Edge, consulte a página [de resolução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Se quiser abrir portas para aceder ao VM através de SSH ou outras ligações de entrada, consulte a documentação das Máquinas Virtuais Azure sobre a [abertura de portas e pontos finais para um Linux VM](../virtual-machines/linux/nsg-quickstart.md)
