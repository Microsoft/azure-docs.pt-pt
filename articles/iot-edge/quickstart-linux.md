---
title: Quickstart criar um dispositivo Azure IoT Edge no Linux Microsoft Docs
description: Neste arranque rápido, aprenda a criar um dispositivo IoT Edge no Linux e, em seguida, implemente código pré-construído remotamente a partir do portal Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 720a4d14a73350d98b3f9054f748b93d296be11b
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579322"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quickstart: Implemente o seu primeiro módulo IoT Edge num dispositivo Linux virtual

Teste a Azure IoT Edge neste arranque rápido implantando código contentorizado num dispositivo virtual Linux IoT Edge. O IoT Edge permite-lhe gerir remotamente código nos seus dispositivos para que possa enviar mais cargas de trabalho para a borda. Para este arranque rápido, recomendamos a utilização de uma máquina virtual Azure para o seu dispositivo IoT Edge, que lhe permite criar rapidamente uma máquina de teste com o serviço IoT Edge instalado e, em seguida, eliminá-la quando terminar.

Neste início rápido, vai aprender a:

* Criar um Hub IoT.
* Registar um dispositivo IoT Edge no seu hub IoT.
* Instale e inicie o tempo de funcionaamento do IoT Edge no seu dispositivo virtual.
* Implementar remotamente um módulo num dispositivo IoT Edge.

![Diagrama - Arquitetura quickstart para dispositivo e nuvem](./media/quickstart-linux/install-edge-full.png)

Este quickstart acompanha-o através da criação de uma máquina virtual Linux que está configurada para ser um dispositivo IoT Edge. Em seguida, coloque um módulo do portal Azure para o seu dispositivo. O módulo utilizado neste arranque rápido é um sensor simulado que gera dados de temperatura, humidade e pressão. Os outros tutoriais do Azure IoT Edge baseiam-se no trabalho que faz aqui, implantando módulos adicionais que analisam os dados simulados para insights de negócios.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vai utilizar a CLI do Azure para concluir muitos dos passos neste início rápido e o IoT do Azure tem uma extensão para ativar funcionalidades adicionais.

Adicione a extensão Azure IoT à instância Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recursos da cloud:

* Um grupo de recursos para gerir todos os recursos que utilizar neste início rápido. Utilizamos o nome de grupo de recursos de exemplo **IoTEdgeResources** ao longo deste quickstart e dos seguintes tutoriais.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Inicie o quickstart criando um hub IoT com Azure CLI.

![Diagrama - Criar um hub IoT na nuvem](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este início rápido. Se já usou o IoT Hub no passado e já criou um hub, pode usar esse hub IoT.

O código a seguir cria um hub **de F1** gratuito no grupo de recursos **IoTEdgeResources**. `{hub_name}`Substitua-o por um nome único para o seu hub IoT. Pode levar alguns minutos para criar um hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se obtiver um erro porque já existe um hub gratuito na sua subscrição, altere o SKU para **S1**. Cada subscrição só pode ter um Hub IoT gratuito. Se tiver um erro que o nome IoT Hub não está disponível, significa que outra pessoa já tem um hub com esse nome. Tente um novo nome.

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo do IoT Edge

Registe um dispositivo do IoT Edge no seu hub IoT recentemente criado.

![Diagrama - Registar um dispositivo com identidade IoT Hub](./media/quickstart-linux/register-device.png)

Crie uma identidade do dispositivo para o seu dispositivo IoT Edge para que possa comunicar com o seu hub IoT. A identidade do dispositivo reside na cloud e verá uma cadeia de ligação do dispositivo única para associar um dispositivo físico a uma identidade do dispositivo.

Uma vez que os dispositivos IoT Edge se comportam e podem ser geridos de forma diferente dos dispositivos IoT típicos, declare esta identidade como sendo para um dispositivo IoT Edge com a `--edge-enabled` bandeira.

1. Na Azure Cloud Shell, insira o seguinte comando para criar um dispositivo chamado **MyEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se tiver um erro sobre as teclas de política do iothubowner, certifique-se de que a sua Cloud Shell está a executar a versão mais recente da extensão azure-iot.

2. Veja a cadeia de ligação do seu dispositivo, que liga o seu dispositivo físico à sua identidade no IoT Hub. Contém o nome do seu hub IoT, o nome do seu dispositivo e, em seguida, uma chave partilhada que autentica ligações entre os dois. Voltaremos a referir-nos a esta cadeia de ligação na secção seguinte quando configurar o seu dispositivo IoT Edge.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Ver cadeia de ligação a partir da saída do CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configure o seu dispositivo IoT Edge

Crie uma máquina virtual com o tempo de execução Azure IoT Edge.

![Diagrama - Iniciar o tempo de funcionaamento no dispositivo](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Tem três componentes. O *daemon de segurança IoT Edge* começa cada vez que um dispositivo IoT Edge arranca e arranca o dispositivo iniciando o agente IoT Edge. O *agente IoT Edge* facilita a implementação e monitorização de módulos no dispositivo IoT Edge, incluindo o hub IoT Edge. O *hub IoT Edge* gere comunicações entre módulos no dispositivo IoT Edge, e entre o dispositivo e o IoT Hub.

Durante a configuração do runtime, apresenta a cadeia de ligação do dispositivo. Esta é a corda que recuperaste do Azure CLI. Essa cadeia associa o dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="deploy-the-iot-edge-device"></a>Implementar o dispositivo IoT Edge

Esta secção utiliza um modelo de Gestor de Recursos Azure para criar uma nova máquina virtual e instalar o tempo de execução IoT Edge nele. Se pretender utilizar o seu próprio dispositivo Linux, pode seguir os passos de instalação no [Tempo de funcionamento do Azure IoT Edge](how-to-install-iot-edge.md)e, em seguida, voltar a este arranque rápido.

Utilize o seguinte comando CLI para criar o seu dispositivo IoT Edge com base no modelo de [implantação iotedge-vm pré-construído.](https://github.com/Azure/iotedge-vm-deploy)

* Para utilizadores de bash ou Cloud Shell, copie o seguinte comando num editor de texto, substitua o texto do espaço reservado pelas suas informações e, em seguida, copie para a sua janela bash ou Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name
   <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password'
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* Para os utilizadores do PowerShell, copie o seguinte comando na sua janela PowerShell e, em seguida, substitua o texto do espaço reservado pelas suas próprias informações:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='my-edge-vm1' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Este modelo tem os seguintes parâmetros:

| Parâmetro | Descrição |
| --------- | ----------- |
| **grupo de recursos** | O grupo de recursos no qual os recursos serão criados. Utilize os **IoTEdgeResources** padrão que temos vindo a usar ao longo deste artigo ou forneça o nome de um grupo de recursos existente na sua subscrição. |
| **modelo-uri** | Um ponteiro para o modelo do Gestor de Recursos que estamos a usar. |
| **dnsLabelPrefix** | Uma corda que será usada para criar o nome de hospedeiro da máquina virtual. Use o exemplo **my-edge-vm** ou forneça uma nova corda. |
| **adminUsername** | Um nome de utilizador para a conta de administração da máquina virtual. Utilize o exemplo **azureUser** ou forneça um novo nome de utilizador. |
| **dispositivoConnectionString** | A cadeia de ligação da identidade do dispositivo no IoT Hub, que é usada para configurar o tempo de funcionamento do IoT Edge na máquina virtual. O comando CLI dentro deste parâmetro agarra a cadeia de ligação para si. Substitua o texto do espaço reservado pelo nome do seu hub IoT. |
| **authenticationType** | O método de autenticação para a conta de administração. Este quickstart utiliza a autenticação **de palavra-passe,** mas também pode definir este parâmetro para **sshPublicKey**. |
| **adminPasswordOrKey** | A palavra-passe ou valor da chave SSH para a conta de administração. Substitua o texto do espaço reservado por uma palavra-passe segura. A sua palavra-passe deve ter pelo menos 12 caracteres e ter três de quatro dos seguintes: caracteres minúsculos, caracteres maiúsculas, dígitos e caracteres especiais. |

Uma vez concluída a implementação, deverá receber uma saída formatada por JSON no CLI que contenha as informações SSH para ligar à máquina virtual. Copiar o valor da entrada **pública de SSH** da secção **de saídas:**

   ![Recuperar o valor público da produção](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Ver o estado de runtime do IoT Edge

O resto dos comandos neste quickstart ocorrem no seu próprio dispositivo IoT Edge, para que possa ver o que está a acontecer no dispositivo. Se estiver a utilizar uma máquina virtual, ligue-se a essa máquina utilizando agora o nome de utilizador de administração que configura e o nome DNS que foi descoduado pelo comando de implementação. Também pode encontrar o nome DNS na página geral da sua máquina no portal Azure. Utilize o seguinte comando para se ligar à máquina virtual. Substitua `{admin username}` e por os seus `{DNS name}` próprios valores.

   ```console
   ssh {admin username}@{DNS name}
   ```

Uma vez ligado à sua máquina virtual, verifique se o tempo de funcionaamento foi instalado com sucesso e configurado no seu dispositivo IoT Edge.

1. Verifique se o daemon de segurança IoT Edge está funcionando como um serviço de sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o daemon IoT Edge a funcionar como um serviço de sistema](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Precisa de privilégios elevados para executar os comandos `iotedge`. Depois de terminar sessão do seu computador e iniciar sessão novamente pela primeira vez depois de instalar o runtime do IoT Edge, as suas permissões são atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

2. Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

   ```bash
   journalctl -u iotedge
   ```

3. Veja todos os módulos em execução no seu dispositivo IoT Edge. Uma vez que o serviço foi iniciado pela primeira vez, deverá ver apenas o módulo **edgeAgent** em execução. O módulo EdgeAgent funciona por predefinição e ajuda a instalar e iniciar quaisquer módulos adicionais que implemente no seu dispositivo.

   ```bash
   sudo iotedge list
   ```

   ![Ver um módulo no seu dispositivo](./media/quickstart-linux/iotedge-list-1.png)

O seu dispositivo IoT Edge está agora configurado. Está pronto para executar módulos implementados na cloud.

## <a name="deploy-a-module"></a>Implementar um módulo

Gira o seu dispositivo Azure IoT Edge a partir da cloud para implementar um módulo que irá enviar dados telemétricos para o Hub IoT.

![Diagrama - desloque o módulo de nuvem para dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Ver os dados gerados

Neste início rápido, criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge no mesmo. Em seguida, utilizou o portal Azure para implantar um módulo IoT Edge para funcionar no dispositivo sem ter de fazer alterações no próprio dispositivo.

Neste caso, o módulo que empurrou gera dados de ambiente de amostra que pode usar para testes mais tarde. O sensor simulado está a monitorizar uma máquina e o ambiente à volta da máquina. Por exemplo, este sensor poderá estar num espaço de servidor, numa fábrica ou numa turbina eólica. A mensagem inclui temperatura ambiente e humidade, temperatura e pressão do computador e um carimbo de data/hora. Os tutoriais IoT Edge utilizam os dados criados por este módulo como dados de teste para análise.

Abra novamente a linha de comandos no seu dispositivo do IoT Edge ou utilize a ligação SSH a partir da CLI do Azure. Certifique-se de que o módulo implementado a partir da cloud está em execução no seu dispositivo do IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Ver três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Veja as mensagens enviadas a partir do módulo sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Os comandos IoT Edge são sensíveis a casos quando se referem aos nomes dos módulos.

   ![Ver os dados a partir do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Também pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Azure IoT Hub para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser avançar para os tutoriais do IoT Edge, pode utilizar o dispositivo que registou e configurou neste início rápido. Caso contrário, pode eliminar os recursos Azure que criou para evitar encargos.

Se tiver criado a sua máquina virtual e o hub IoT num novo grupo de recursos, pode eliminar esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para se certificar de que não há nada que queira guardar. Se não quiser eliminar todo o grupo, pode eliminar recursos individuais.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível.

Remova o grupo de **IoTEdgeResources**. Pode levar alguns minutos para apagar um grupo de recursos.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Pode confirmar que o grupo de recursos é removido visualizando a lista de grupos de recursos.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo IoT Edge e utilizou a interface de nuvem Azure IoT Edge para implementar código no dispositivo. Agora tem um dispositivo de teste a gerar dados não processados sobre o seu ambiente.

O próximo passo é configurar o seu ambiente de desenvolvimento local para que possa começar a criar módulos IoT Edge que executam a sua lógica de negócio.

> [!div class="nextstepaction"]
> [Comece a desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md)
