---
title: Tutorial - Criar uma hierarquia de dispositivos IoT Edge - Azure IoT Edge
description: Este tutorial mostra-lhe como criar uma estrutura hierárquica de dispositivos IoT Edge usando gateways.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309196"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Tutorial: Criar uma hierarquia de dispositivos IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Implementar nós Azure IoT Edge através de redes organizadas em camadas hierárquicas. Cada camada de uma hierarquia é um dispositivo de gateway que lida com mensagens e pedidos de dispositivos na camada abaixo dela.

Você pode estruturar uma hierarquia de dispositivos para que apenas a camada superior tenha conectividade com a nuvem, e as camadas inferiores só podem comunicar com camadas adjacentes norte e sul. Esta camada de rede é a base da maioria das redes industriais, que seguem a [norma ISA-95.](https://en.wikipedia.org/wiki/ANSI/ISA-95)

O objetivo deste tutorial é criar uma hierarquia de dispositivos IoT Edge que simula um ambiente de produção simplificado. No final, irá implantar o [módulo Sensor de Temperatura Simulado](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) num dispositivo de camada inferior sem acesso à Internet, descarregando imagens de contentores através da hierarquia.

Para atingir este objetivo, este tutorial acompanha-o através da criação de uma hierarquia de dispositivos IoT Edge, implantando recipientes de tempo de execução IoT Edge para os seus dispositivos e configurando os seus dispositivos localmente. Neste tutorial, utiliza uma ferramenta de configuração automatizada para:

> [!div class="checklist"]
>
> * Crie e defina as relações numa hierarquia de dispositivos IoT Edge.
> * Configure o tempo de funcionamento do IoT Edge nos dispositivos da sua hierarquia.
> * Instale certificados consistentes em toda a hierarquia do seu dispositivo.
> * Adicione cargas de trabalho aos dispositivos da sua hierarquia.
> * Utilize o [módulo IoT Edge API Proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) para encaminhar de forma segura o tráfego HTTP sobre uma única porta a partir dos seus dispositivos de camada inferior.

>[!TIP]
>Este tutorial inclui uma mistura de passos manuais e automatizados para fornecer uma montra de características aninhadas de IoT Edge.
>
>Se você quiser um olhar totalmente automatizado para a criação de uma hierarquia de dispositivos IoT Edge, você pode seguir a amostra de [IoT IoT scripted.](https://aka.ms/iotedge-nested-sample) Este cenário scripted implementa máquinas virtuais Azure como dispositivos pré-configurados para simular um ambiente de fábrica.
>
>Se quiser analisar aprofundadamente os passos manuais para criar e gerir uma hierarquia de dispositivos IoT Edge, consulte o guia sobre as hierarquias de [gateway do dispositivo IoT Edge](how-to-connect-downstream-iot-edge-device.md).

Neste tutorial, são definidas as seguintes camadas de rede:

* **Camada superior**: Os dispositivos IoT Edge nesta camada podem ligar-se diretamente à nuvem.

* **Camadas inferiores**: Os dispositivos IoT Edge em camadas abaixo da camada superior não podem ligar-se diretamente à nuvem. Precisam de passar por um ou mais dispositivos IoT Edge intermediários para enviar e receber dados.

Este tutorial usa uma hierarquia de dois dispositivos para a simplicidade, retratada abaixo. Um dispositivo, o **dispositivo de camada superior,** representa um dispositivo na camada superior da hierarquia, que pode ligar-se diretamente à nuvem. Este dispositivo também será referido como o **dispositivo principal**. O outro dispositivo, o **dispositivo de camada inferior,** representa um dispositivo na camada inferior da hierarquia, que não consegue ligar-se diretamente à nuvem. Pode adicionar mais dispositivos de camada inferior para representar o seu ambiente de produção, conforme necessário. Os dispositivos em camadas mais baixas também serão referidos como **dispositivos infantis**.

![Estrutura da hierarquia tutorial, contendo dois dispositivos: o dispositivo de camada superior e o dispositivo de camada inferior](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma hierarquia de dispositivos IoT Edge, você precisará:

* Um computador (Windows ou Linux) com conectividade à Internet.
* Uma conta Azure com uma subscrição válida. Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de nível gratuito ou padrão em Azure.
* Uma concha bash em Azure Cloud Shell utilizando Azure CLI v2.3.1 com a extensão Azure IoT v0.10.6 ou superior instalada. Este tutorial usa a [Azure Cloud Shell.](../cloud-shell/overview.md) Se não está familiarizado com a Azure Cloud Shell, [consulte um quickstart para obter detalhes.](./quickstart-linux.md#prerequisites)
  * Para ver as versões atuais dos módulos e extensões Azure CLI, executar [a versão az](/cli/azure/reference-index?#az_version).
* Um dispositivo Linux para configurar como um dispositivo IoT Edge para cada dispositivo na sua hierarquia. Este tutorial usa dois dispositivos. Se não tiver dispositivos disponíveis, pode criar máquinas virtuais Azure para cada dispositivo da sua hierarquia utilizando o comando abaixo.

   Substitua o texto do espaço reservado no seguinte comando e execute-o duas vezes, uma para cada máquina virtual. Cada máquina virtual precisa de um prefixo DNS único, que também servirá como seu nome. O prefixo DNS deve estar em conformidade com a seguinte expressão regular: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   A máquina virtual utiliza chaves SSH para autenticar utilizadores. Se não estiver familiarizado com a criação e utilização de teclas SSH, pode seguir [as instruções para os pares de chaves ssh público-privado para Os VMs Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

   A versão 1.2 do IoT Edge está pré-instalada com este modelo ARM, poupando a necessidade de instalar manualmente os ativos nas máquinas virtuais. Se estiver a instalar o IoT Edge nos seus próprios dispositivos, consulte [instalar o Azure IoT Edge para Linux (versão 1.2)](how-to-install-iot-edge.md) ou [atualizar o IoT Edge para a versão 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Uma criação bem sucedida de uma máquina virtual utilizando este modelo ARM irá descodur o punho da sua máquina virtual `SSH` e o nome de domínio totalmente qualificado `FQDN` (). Utilizará o manípulo SSH e o endereço FQDN ou IP de cada máquina virtual para configuração em etapas posteriores, por isso, acompanhe estas informações. Uma saída de amostra é retratada abaixo.

   >[!TIP]
   >Também pode encontrar o endereço IP e FQDN no portal Azure. Para obter o endereço IP, navegue na sua lista de máquinas virtuais e observe o **campo de endereços IP público.** Para o FQDN, vá à página geral de cada máquina virtual e procure o campo **de nome DNS.**

   ![A máquina virtual irá fazer um JSON após a criação, que contém o seu cabo SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Certifique-se de que as seguintes portas estão abertas para todos os dispositivos, exceto o dispositivo de camada mais baixa: 8000, 443, 5671, 8883:
  * 8000: Usado para puxar imagens do contentor Docker através do representante da API.
  * 443: Utilizado entre os centros de borda do pai e da criança para chamadas DE API REST.
  * 5671, 8883: Usado para AMQP e MQTT.

  Para mais informações, consulte [como abrir portas a uma máquina virtual com o portal Azure.](../virtual-machines/windows/nsg-quickstart-portal.md)

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configure a sua hierarquia de dispositivo IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Criar uma hierarquia de dispositivos IoT Edge

Os dispositivos IoT Edge compõem as camadas da sua hierarquia. Este tutorial criará uma hierarquia de dois dispositivos IoT Edge: o **dispositivo de camada superior** e o seu filho, o dispositivo de camada **inferior**. Pode criar dispositivos infantis adicionais, se necessário.

Para criar e configurar a sua hierarquia de dispositivos IoT Edge, utilizará a `iotedge-config` ferramenta. Esta ferramenta simplifica a configuração da hierarquia automatizando e condensando vários passos em dois:

1. Configurar a configuração da nuvem e preparar cada configuração do dispositivo, que inclui:

   * Criar dispositivos no seu Hub IoT
   * Definir as relações pai-filho para autorizar a comunicação entre dispositivos
   * Gerando uma cadeia de certificados para cada dispositivo para estabelecer uma comunicação segura entre eles
   * Gerando ficheiros de configuração para cada dispositivo

1. Instalação de cada configuração do dispositivo, que inclui:

   * Instalação de certificados em cada dispositivo
   * Aplicação dos ficheiros de configuração para cada dispositivo

A `iotedge-config` ferramenta também fará automaticamente as implementações do módulo para o seu dispositivo IoT Edge.

Para utilizar a `iotedge-config` ferramenta para criar e configurar a sua hierarquia, siga os passos abaixo no CLI Azure:

1. No [Azure Cloud Shell,](https://shell.azure.com/)faça um diretório para os recursos do seu tutorial:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Descarregue o desbloqueio da ferramenta de configuração e dos modelos de configuração:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Isto irá criar a `iotedge_config_cli_release` pasta no seu diretório tutorial.

   O ficheiro de modelo utilizado para criar a hierarquia do seu dispositivo é o `iotedge_config.yaml` ficheiro encontrado em `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . No mesmo diretório, `deploymentLowerLayer.json` encontra-se um ficheiro de implantação JSON contendo instruções para quais os módulos a utilizar no seu **dispositivo de camada inferior**. O `deploymentTopLayer.json` ficheiro é o mesmo, mas para o seu dispositivo de camada **superior**, como os módulos implantados em cada dispositivo não são os mesmos. O `device_config.toml` ficheiro é um modelo para configurações de dispositivos IoT Edge e será usado para gerar automaticamente os pacotes de configuração para os dispositivos da sua hierarquia.

   Se quiser dar uma olhada no código fonte e scripts da `iotedge-config` ferramenta, consulte [o repositório Azure-Samples no GitHub.](https://github.com/Azure-Samples/iotedge_config_cli)

1. Abra o modelo de configuração tutorial e edite-o com as suas informações:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Na secção **iothub,** povoe os `iothub_hostname` campos e campos com a sua `iothub_name` informação. Estas informações podem ser encontradas na página geral do seu Hub IoT no portal Azure.

   Na secção **de certificados** opcionais, pode povoar os campos com os caminhos absolutos para o seu certificado e chave. Se deixar estes campos em branco, o script gerará automaticamente certificados de teste auto-assinados para a sua utilização. Se não está familiarizado com a forma como os certificados são usados num cenário de gateway, consulte [a secção de certificados do guia](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   Na secção de **configuração,** `template_config_path` o caminho para o modelo utilizado para criar as `device_config.toml` configurações do seu dispositivo. O `default_edge_agent` campo determina o que os dispositivos de camada inferior de imagem do Edge Agent irão puxar e de onde.

   Na secção **edgedevices,** para um cenário de produção, pode editar a árvore da hierarquia para refletir a sua estrutura desejada. Para efeitos deste tutorial, aceite a árvore padrão. Para cada dispositivo, existe um `device_id` campo onde pode nomear os seus dispositivos. Há também o `deployment` campo, que especifica o caminho para a implementação JSON para aquele dispositivo.

   Também pode registar manualmente dispositivos IoT Edge no seu Hub IoT através do portal Azure ou da Azure Cloud Shell. Para saber como, consulte [o guia sobre como registar um dispositivo IoT Edge](how-to-register-device.md).

   Também pode definir as relações entre pais e filhos manualmente. Consulte a [secção de hierarquia de gateway](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) do guia de como saber para saber mais.

   ![A secção edgedevices do ficheiro de configuração permite-lhe definir a sua hierarquia](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Guarde e feche o ficheiro:

   `CTRL + S`, `CTRL + Q`

1. Crie um diretório de saídas para os pacotes de configuração no seu diretório de recursos tutoriais:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Navegue para o seu `iotedge_config_cli_release` diretório e execute a ferramenta para criar a sua hierarquia de dispositivos IoT Edge:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Com a `--output` bandeira, a ferramenta cria os certificados do dispositivo, os pacotes de certificados e um ficheiro de registo num diretório à sua escolha. Com o conjunto de `-f` bandeiras, a ferramenta procurará automaticamente os dispositivos IoT Edge existentes no seu Hub IoT e removê-los-á, para evitar erros e manter o seu hub limpo.

   A ferramenta de configuração cria os seus dispositivos IoT Edge e configura as relações pai-filho entre eles. Opcionalmente, cria certificados para os seus dispositivos utilizarem. Se forem fornecidos caminhos para a implantação de JSONs, a ferramenta criará automaticamente estas implementações para os seus dispositivos, mas tal não é necessário. Finalmente, a ferramenta gerará os pacotes de configuração para os seus dispositivos e coloca-os no diretório de saída. Para uma análise aprofundada dos passos dados pela ferramenta de configuração, consulte o ficheiro de registo no diretório de saída.

   ![O guião mostrará uma topologia da sua hierarquia após a execução](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Verifique duas vezes se a saída de topologia do guião parece correta. Uma vez que esteja satisfeito que a sua hierarquia esteja corretamente estruturada, está pronto para prosseguir.

### <a name="configure-the-iot-edge-runtime"></a>Configurar o runtime do IoT Edge

Além do fornecimento dos seus dispositivos, os passos de configuração estabelecem uma comunicação fidedigna entre os dispositivos da sua hierarquia utilizando os certificados que criou anteriormente. Os passos também começam a estabelecer a estrutura de rede da sua hierarquia. O dispositivo de camada superior manterá a conectividade da internet, permitindo-lhe retirar imagens para o seu tempo de funcionaamento a partir da nuvem, enquanto dispositivos de camada inferior irão percorrer o dispositivo de camada superior para aceder a estas imagens.

Para configurar o tempo de execução do IoT Edge, é necessário aplicar os pacotes de configuração criados pelo script de configuração para os seus dispositivos. As configurações diferem ligeiramente entre o dispositivo de **camada superior** e um dispositivo de **camada inferior**, por isso tenha em atenção qual o ficheiro de configuração do dispositivo que está a aplicar a cada dispositivo.

1. Cada dispositivo precisa do seu pacote de configuração correspondente. Pode utilizar uma unidade USB ou [uma cópia de ficheiro segura](https://www.ssh.com/ssh/scp/) para mover os pacotes de configuração para cada dispositivo.

   Certifique-se de que envia o pacote de configuração correto para cada dispositivo.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Significa `:~` que a pasta de configuração será colocada no diretório doméstico na máquina virtual.

1. Inicie sessão na sua máquina virtual para aplicar o pacote de configuração no dispositivo:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Em cada dispositivo, desaperte o pacote de configuração. Primeiro, terá de instalar o zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Em cada dispositivo, aplique o pacote de configuração no dispositivo:

   ```bash
   sudo ./install.sh
   ```

   No dispositivo de **camada superior,** receberá um pedido para introduzir o nome de anfitrião. No dispositivo de **camada inferior,** pedirá o nome de hospedeiro e o nome de hospedeiro dos pais. Forneça o IP ou FQDN apropriado para cada ímpa. Pode usar qualquer um, mas seja consistente na sua escolha através de dispositivos. A saída do script de instalação é retratada abaixo.

   Se quiser ver mais de perto as modificações que estão a ser feitas no ficheiro de configuração do seu dispositivo, consulte [a borda IoT configurada na secção de dispositivos do guia de como fazer.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

  ![A instalação dos pacotes de configuração atualizará os ficheiros config.toml no seu dispositivo e reiniciará automaticamente todos os serviços IoT Edge](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Se tiver completado corretamente os passos acima, pode verificar se os seus dispositivos estão configurados corretamente.

Verifique a configuração e a conectividade dos seus dispositivos. Para o **dispositivo de camada superior:**

   ```bash
   sudo iotedge check
   ```

Para o **dispositivo de camada inferior,** a imagem de diagnóstico tem de ser transmitida manualmente no comando:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

No seu **dispositivo de camada superior,** espere ver uma saída com várias avaliações de passagem. Pode ver alguns avisos sobre as políticas de registos e, dependendo da sua rede, as políticas de DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Uma vez que esteja satisfeito que as suas configurações estejam corretas em cada dispositivo, está pronto para prosseguir.

## <a name="deploy-modules-to-your-devices"></a>Implementar módulos para os seus dispositivos

As implementações do módulo para os seus dispositivos foram geradas automaticamente quando os dispositivos foram criados. A `iotedge-config-cli` ferramenta alimentou a implementação JSONs para os **dispositivos de camada superior e inferior** após a sua criação. A implementação do módulo estava pendente enquanto configurava o tempo de funcionamento do IoT Edge em cada dispositivo. Uma vez configurado o tempo de execução, as implementações para o **dispositivo de camada superior** começaram. Após a conclusão destas implementações, o **dispositivo de camada inferior** poderia utilizar o módulo **IoT Edge API Proxy** para retirar as imagens necessárias.

No [Azure Cloud Shell,](https://shell.azure.com/)pode dar uma olhada na implementação **do dispositivo de camada superior** JSON para entender que módulos foram implantados no seu dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Além dos módulos de tempo de execução **IoT Edge Agent** e **IoT Edge Hub,** o **dispositivo de camada superior** recebe o módulo de registo **Docker** e o módulo **IoT Edge API Proxy.**

O módulo **de registo Docker** aponta para um registo de contentores Azure existente. Neste caso, `REGISTRY_PROXY_REMOTEURL` aponta para o Registo de Contentores da Microsoft. No `createOptions` , pode ver-se que comunica no porto 5000.

O módulo **IoT Edge API Proxy** rotas HTTP solicita a outros módulos, permitindo que dispositivos de camada inferior puxem imagens de contentores ou empurrem bolhas para o armazenamento. Neste tutorial, comunica na porta 8000 e está configurado para enviar a rota de pedidos de imagem do estivador para o seu módulo **de registo Docker** na porta 5000. Além disso, qualquer carregamento de armazenamento de bolhas solicita rota para módulo AzureBlobStorageonIoTEdge na porta 11002. Para obter mais informações sobre o módulo **Proxy IoT Edge API** e como configurá-lo, consulte o [guia](how-to-configure-api-proxy-module.md)do módulo.

Se quiser ver como criar uma implementação como esta através do portal Azure ou da Azure Cloud Shell, consulte [a secção de dispositivos de camada superior do guia de como fazer.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)

No [Azure Cloud Shell,](https://shell.azure.com/)pode dar uma olhada na implementação **do dispositivo de camada inferior** JSON para entender que módulos foram implantados no seu dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Pode ver por `systemModules` baixo que os módulos de tempo de funcionaamento **do dispositivo de camada inferior** estão definidos para puxar de , em vez de , como fez o dispositivo de `$upstream:8000` camada `mcr.microsoft.com` **superior.** O **dispositivo de camada inferior** envia a imagem de Docker solicita o módulo **Proxy IoT Edge API** na porta 8000, uma vez que não consegue retirar diretamente as imagens da nuvem. O outro módulo implantado no dispositivo de **camada inferior**, o módulo sensor de **temperatura simulada,** também faz o seu pedido de imagem para `$upstream:8000` .

Se quiser ver como criar uma implementação como esta através do portal Azure ou da Azure Cloud Shell, consulte a [secção de dispositivos de camada inferior do guia de como fazer.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

Pode ver o estado dos seus módulos utilizando o comando:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Este comando irá desausar todas as propriedades reportadas pela EdgeAgent. Aqui estão algumas úteis para monitorizar o estado do dispositivo: estado de *funcionamento,* *tempo de funcionamento,* *tempo de funcionamento da última saída,* *contagem de reinício do tempo de execução*.

Também pode ver o estado dos seus módulos no [portal Azure](https://ms.portal.azure.com/). Navegue na secção **IoT Edge** do seu IoT Hub para ver os seus dispositivos e módulos.

Uma vez satisfeito com as implementações do módulo, está pronto para prosseguir.

## <a name="view-generated-data"></a>Ver os dados gerados

O módulo **de sensor de temperatura simulado** que empurrou gera dados do ambiente da amostra. Envia mensagens que incluem temperatura ambiente e humidade, temperatura e pressão da máquina e um tempotando.

Também pode ver estas mensagens através da [Azure Cloud Shell:](https://shell.azure.com/)

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Resolução de problemas

Executar o `iotedge check` comando para verificar a configuração e para verificar erros de resolução de problemas.

Você pode correr `iotedge check` numa hierarquia aninhada, mesmo que as máquinas infantis não tenham acesso direto à internet.

Quando se corre `iotedge check` da camada inferior, o programa tenta retirar a imagem do progenitor através da porta 443.

Neste tutorial, usamos a porta 8000, por isso precisamos especirá-la:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

O `azureiotedge-diagnostics` valor é retirado do registo do contentor que está ligado ao módulo de registo. Este tutorial tem definido por padrão para https://mcr.microsoft.com:

| Name | Valor |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Se estiver a utilizar um registo de contentores privados, certifique-se de que todas as imagens (IoTEdgeAPIProxy, edgeAgent, edgeHub, Sensor de Temperatura Simulada e diagnósticos) estão presentes no registo do contentor.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar as configurações locais e os recursos Azure que criou neste artigo para evitar encargos.

Para eliminar os recursos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Selecione o nome do grupo de recursos que contém os recursos de teste do IoT Edge. 

3. Reveja a lista de recursos que o seu grupo de recursos contém. Se quiser eliminá-los todos, pode selecionar **Eliminar grupo de recursos**. Se quiser eliminar apenas alguns dos recursos, pode clicar em cada um para eliminá-los individualmente. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste dois dispositivos IoT Edge como portais e definiste um como o dispositivo principal do outro. Em seguida, demonstrou ter puxado uma imagem de recipiente para o dispositivo da criança através de um gateway utilizando o módulo IoT Edge API Proxy. Consulte [o guia de como fazer sobre a utilização do módulo proxy](how-to-configure-api-proxy-module.md) se quiser saber mais.

Para saber mais sobre a utilização de gateways para criar camadas hierárquicas de dispositivos IoT Edge, consulte [o guia de como ligar dispositivos IoT Edge a jusante](how-to-connect-downstream-iot-edge-device.md).

Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"]
> [Implementar o modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
