---
title: Run Azure IoT Edge em Máquinas Virtuais Ubuntu [ Ubuntu Virtual Machines] Microsoft Docs
description: Instruções de configuração da Borda Azure IoT para Máquinas Virtuais Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349616"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Run Azure IoT Edge em Máquinas Virtuais Ubuntu

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grandes como um servidor industrial. Uma vez configurado um dispositivo com o tempo de funcionamento do IoT Edge, pode começar a implementar a lógica do negócio a partir da nuvem.

Para saber mais sobre como funciona o tempo de funcionamento do IoT Edge e quais os componentes incluídos, consulte Compreender o tempo de funcionamento do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para implantar uma máquina virtual Ubuntu 18.04 LTS com o tempo de execução Do Limite Azure IoT instalado e configurado utilizando uma cadeia de ligação de dispositivopré-fornecida. A implementação é realizada usando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) baseado [em nuvem](../virtual-machines/linux/using-cloud-init.md
) mantido no repositório de projeto [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Na primeira bota, a máquina virtual Ubuntu 18.04 LTS instala a versão mais recente do tempo de funcionamento do [Azure IoT Edge via cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Também define uma cadeia de ligação fornecida antes do início do tempo de funcionação, permitindo-lhe configurar e ligar facilmente o dispositivo IoT Edge sem a necessidade de iniciar uma sessão de ambiente de trabalho SSH ou remota. 

## <a name="deploy-using-deploy-to-azure-button"></a>Implementar utilizando o botão Azure

O [botão Desdobrar para o Botão Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) permite a implementação simplificada dos modelos do Gestor de [Recursos Azure mantidos](../azure-resource-manager/templates/overview.md) no GitHub.  Esta secção demonstrará a utilização do botão Desativação para O Botão Azure contido no repositório do projeto [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)  


1. Vamos implantar um Azure IoT Edge habilitado linux VM usando o modelo de gestor de recursos azure-vm-deploy iotedge-vm.  Para começar, clique no botão abaixo:

    [![Desloque para o botão Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Na janela recém-lançada, preencha os campos de formuláriodisponíveis:

    > [!div class="mx-imgBorder"]
    > [![Screenshot mostrando o modelo iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subscrição**: A subscrição ativa do Azure para implantar a máquina virtual.

    **Grupo de recursos**: Um Grupo de Recursos existente ou recém-criado para conter a máquina virtual e os seus recursos associados.

    **Prefixo de etiqueta DNS**: Um valor exigido da sua escolha que é usado para pré-fixar o nome de anfitrião da máquina virtual.

    **Nome de utilizador do Administrador**: Um nome de utilizador, que será fornecido privilégios de raiz na implementação.

    **Cadeia de ligação ao dispositivo**: Uma corda de [ligação](how-to-register-device.md) de dispositivo para um dispositivo criado dentro do [seu hub IoT](../iot-hub/about-iot-hub.md)pretendido .

    **Tamanho VM**: O [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual a ser implantada

    **Versão Ubuntu OS**: A versão do Ubuntu OS a instalar na máquina virtual base.

    **Localização**: A [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) para implantar a máquina virtual, este valor não se aplica à localização do Grupo de Recursos selecionados.

    **Tipo de autenticação:** Escolha **sshPublicKey** ou **palavra-passe** dependendo da sua preferência.

    **Palavra-passe ou Chave**de Administrador : O valor da Chave Pública SSH ou o valor da palavra-passe dependendo da escolha do Tipo de Autenticação.

    Quando todos os campos tiverem sido preenchidos, selecione a caixa de verificação na parte inferior da página para aceitar os termos e selecione **Comprar** para iniciar a implementação.

1. Verifique se a implantação foi concluída com sucesso.  Um recurso virtual da máquina deveria ter sido implantado no grupo de recursos selecionados.  Tome nota do nome da máquina, `vm-0000000000000`isto deve estar no formato . Além disso, tome nota do **nome DNS** `<dnsLabelPrefix>`associado, que deve estar no formato . `<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido a partir da secção **de visão geral** da máquina virtual recém-implantada dentro do portal Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mostrando o nome dns do vm iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se pretender entrar neste VM após a configuração, utilize o **nome DNS** associado com o comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implantação a partir do Azure CLI

1. Certifique-se de que instalou a extensão iot Azure CLI com:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Em seguida, se estiver a utilizar o Azure CLI no seu ambiente de trabalho, comece por iniciar sessão:

   ```azurecli-interactive
   az login
   ```

1. Se tiver várias subscrições, selecione a subscrição que deseja utilizar:
   1. Liste as suas subscrições:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo Subscrição ID para a subscrição que deseja utilizar.

   1. Detete a sua subscrição de trabalho com o ID que copiou:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Criar um novo grupo de recursos (ou especificar um existente nos próximos passos):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Criar uma nova máquina virtual:

    Para utilizar uma **autenticaçãoTipo** de, `password`consulte o exemplo abaixo:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Para autenticar com uma chave SSH, pode fazê-lo especificando um tipo de `sshPublicKey` **autenticação** de, em seguida, fornecer o valor da chave SSH no parâmetro **adminPasswordOrKey.**  Apresentamos um exemplo abaixo.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Verifique se a implantação foi concluída com sucesso.  Um recurso virtual da máquina deveria ter sido implantado no grupo de recursos selecionados.  Tome nota do nome da máquina, `vm-0000000000000`isto deve estar no formato . Além disso, tome nota do **nome DNS** `<dnsLabelPrefix>`associado, que deve estar no formato . `<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido a partir da saída formatada JSON da etapa anterior, dentro da secção de **saídas** como parte da entrada pública de **SSH.**  O valor desta entrada pode ser utilizado para o SSH na máquina recém-implantada.

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
    > [![Screenshot mostrando o nome dns do vm iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se pretender entrar neste VM após a configuração, utilize o **nome DNS** associado com o comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas com o tempo de funcionamento do IoT Edge a instalar corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

Se quiser abrir portas para aceder ao VM através de SSH ou outras ligações de entrada, consulte a documentação das Máquinas Virtuais Azure sobre a abertura de [portas e pontos finais para um VM Linux](../virtual-machines/linux/nsg-quickstart.md)
