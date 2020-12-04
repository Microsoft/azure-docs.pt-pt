---
title: Tutorial - Criar uma hierarquia de dispositivos IoT Edge - Azure IoT Edge
description: Este tutorial mostra-lhe como criar uma estrutura hierárquica de dispositivos IoT Edge usando gateways.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 2f59feb83149042781b624aeb0df73fe3ab32587
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574229"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Tutorial: Criar uma hierarquia de dispositivos IoT Edge (Pré-visualização)

Implementar nós Azure IoT Edge através de redes organizadas em camadas hierárquicas. Cada camada de uma hierarquia é um dispositivo de gateway que lida com mensagens e pedidos de dispositivos na camada abaixo dela.

>[!NOTE]
>Esta funcionalidade requer a versão 1.2 do IoT Edge, que se encontra em pré-visualização pública, a executar contentores Linux.

Você pode estruturar uma hierarquia de dispositivos para que apenas a camada superior tenha conectividade com a nuvem, e as camadas inferiores só podem comunicar com camadas adjacentes norte e sul. Esta camada de rede é a base da maioria das redes industriais, que seguem a [norma ISA-95.](https://en.wikipedia.org/wiki/ANSI/ISA-95)

O objetivo deste tutorial é criar uma hierarquia de dispositivos IoT Edge que simula um ambiente de produção. No final, irá implantar o [módulo Sensor de Temperatura Simulado](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) num dispositivo de camada inferior sem acesso à Internet, descarregando imagens de contentores através da hierarquia.

Para atingir este objetivo, este tutorial acompanha-o através da criação de uma hierarquia de dispositivos IoT Edge, implantando recipientes de tempo de execução IoT Edge para os seus dispositivos e configurando os seus dispositivos localmente. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie e defina as relações numa hierarquia de dispositivos IoT Edge.
> * Configure o tempo de funcionamento do IoT Edge nos dispositivos da sua hierarquia.
> * Instale certificados consistentes em toda a hierarquia do seu dispositivo.
> * Adicione cargas de trabalho aos dispositivos da sua hierarquia.
> * Utilize um módulo de procuração API para encaminhar de forma segura o tráfego HTTP sobre uma única porta a partir dos seus dispositivos de camada inferior.

Neste tutorial, são definidas as seguintes camadas de rede:

* **Camada superior**: Os dispositivos IoT Edge nesta camada podem ligar-se diretamente à nuvem.

* **Camada inferior**: Os dispositivos IoT Edge nesta camada não podem ligar-se diretamente à nuvem. Precisam de passar por um ou mais dispositivos IoT Edge intermediários para enviar e receber dados.

Este tutorial usa uma hierarquia de dois dispositivos para a simplicidade. Um dispositivo, **topLayerDevice,** representa um dispositivo na camada superior da hierarquia, que pode ligar-se diretamente à nuvem. Este dispositivo também será referido como o **dispositivo principal**. O outro dispositivo, **o LowerLayerDevice,** representa um dispositivo na camada inferior da hierarquia, que não consegue ligar-se diretamente à nuvem. Este aparelho também será referido como o **dispositivo infantil**. Pode adicionar dispositivos adicionais de camada inferior para representar o seu ambiente de produção. A configuração de quaisquer dispositivos adicionais de camada inferior seguirá a configuração do **LowerLayerDevice.**

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma hierarquia de dispositivos IoT Edge, você precisará:

* Um computador (Windows ou Linux) com conectividade à Internet.
* Dois dispositivos Linux para configurar como dispositivos IoT Edge. Se não tiver dispositivos disponíveis, pode utilizar [máquinas virtuais Azure.](../virtual-machines/linux/index.yml)
* Uma conta Azure com uma subscrição válida. Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de nível gratuito ou padrão em Azure.
* Azure CLI v2.3.1 com a extensão Azure IoT v0.10.6 ou superior instalada. Este tutorial usa a [Azure Cloud Shell.](../cloud-shell/overview.md) Se não está familiarizado com a Azure Cloud Shell, [consulte um quickstart para obter detalhes.](./quickstart-linux.md#prerequisites)

Você também pode experimentar este cenário seguindo a amostra de [IoT Azure IoT](https://aka.ms/iotedge-nested-sample)scripted, que implementa máquinas virtuais Azure como dispositivos pré-configurados para simular um ambiente de fábrica.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configure a sua hierarquia de dispositivo IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Criar uma hierarquia de dispositivos IoT Edge

O primeiro passo, criando os seus dispositivos IoT Edge, pode ser feito através do portal Azure ou do Azure CLI. Este tutorial criará uma hierarquia de dois dispositivos IoT Edge: **topLayerDevice** e seu **filho inferiorLayerDevice**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://ms.portal.azure.com/)navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. **Selecione + Adicione um dispositivo IoT Edge**. Este dispositivo será o dispositivo de camada superior, por isso introduza um ID exclusivo apropriado para dispositivos. Selecione **Guardar**.

1. **Selecione + Adicione novamente um dispositivo IoT Edge.** Este dispositivo será o dispositivo de camada inferior de borda, por isso introduza um ID de dispositivo único apropriado.

1. Escolha **Definir um dispositivo-mãe,** escolha o seu dispositivo de camada superior na lista de dispositivos e selecione **Ok**. Selecione **Guardar**.

   ![Definição do pai para dispositivo de camada inferior](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell,](https://shell.azure.com/)insira o seguinte comando para criar um dispositivo IoT Edge no seu hub. Este dispositivo será o dispositivo de camada superior, por isso introduza um ID exclusivo apropriado do dispositivo:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Insira o seguinte comando para criar o dispositivo IoT Edge da sua criança e criar a relação pai-filho entre dispositivos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Tome nota da cadeia de ligação de cada dispositivo IoT Edge. Serão usados mais tarde.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a secção **IoT Edge** do seu Hub IoT.

1. Clique no ID do dispositivo de um dos dispositivos na lista de dispositivos.

1. Selecione **Copy** no campo **de cordas de ligação primária** e grave-o num local à sua escolha.

1. Repita para todos os outros dispositivos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), para cada dispositivo, introduza o seguinte comando para recuperar a cadeia de ligação do seu dispositivo e regisá-la num local à sua escolha:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Criar certificados

Todos os dispositivos num cenário de [gateway](iot-edge-as-gateway.md) precisam de um certificado partilhado para estabelecer ligações seguras entre eles. Utilize os seguintes passos para criar certificados de demonstração para ambos os dispositivos neste cenário.

Para criar certificados de demonstração num dispositivo Linux, é necessário clonar os scripts de geração e confiá-los para serem executados localmente em bash.

1. Clone o repo ioT Edge git, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navegue para o diretório em que quer trabalhar. Todos os certificados e ficheiros-chave serão criados neste diretório.

1. Copie os ficheiros config e scripts do repo IoT Edge clonado para o seu diretório de trabalho.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Crie o certificado de CA raiz e um certificado intermédio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Este comando de script cria vários certificados e ficheiros chave, mas estamos a usar o seguinte ficheiro como **certificado de CA raiz** para a hierarquia gateway:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Crie dois conjuntos de certificados CA do dispositivo IoT Edge e teclas privadas com o seguinte comando: um conjunto para o dispositivo de camada superior e um conjunto para o dispositivo de camada inferior. Forneça nomes memoráveis para os certificados de CA para distingui-los uns dos outros.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Este comando de script cria vários certificados e ficheiros chave, mas estamos a usar o seguinte certificado e par de chaves em cada dispositivo IoT Edge e referenciado no ficheiro config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Cada dispositivo necessita de uma cópia do certificado de CA raiz e de uma cópia do certificado ca do seu próprio dispositivo e chave privada. Pode utilizar uma unidade USB ou [uma cópia de ficheiro segura](https://www.ssh.com/ssh/scp/) para mover os certificados para cada dispositivo.

1. Após a transferência dos certificados, instale a raiz CA para cada dispositivo.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Este comando deve ser descoduado que um certificado foi adicionado em /etc/ssl/certs.

### <a name="install-iot-edge-on-the-devices"></a>Instalar ioT Edge nos dispositivos

Instale o IoT Edge seguindo estes passos em ambos os dispositivos.

1. Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale o motor Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Instale o daemon hsmlib e IoT Edge <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/libiothsm-std_1.2.0.rc2-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/iotedge_1.2.0_rc2-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Configurar o runtime do IoT Edge

Configure o tempo de execução IoT Edge seguindo estes passos em ambos os dispositivos. A configuração do tempo de execução IoT Edge para os seus dispositivos consiste em quatro etapas, todas realizadas através da edição do ficheiro de configuração IoT Edge:

1. Disponibilizando manualmente cada dispositivo adicionando a cadeia de ligação do dispositivo ao ficheiro de configuração.

1. Termine a configuração dos certificados do seu dispositivo apontando o ficheiro de configuração para o certificado ca do dispositivo, a chave privada do dispositivo CA e o certificado de CA raiz.

1. Atrate as botas do sistema utilizando o agente IoT Edge.

1. Atualize o parâmetro **do nome de anfitrião** para o seu dispositivo **de camada superior** e atualize o parâmetro do **nome anfitrião** e **parent_hostname** parâmetro para os seus dispositivos de **camada inferior.**

Complete estes passos e reinicie o serviço IoT Edge para configurar os seus dispositivos.

1. Em cada dispositivo, abra o ficheiro de configuração IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Encontre as configurações de provisionamento do ficheiro e descompromete **a configuração manual de provisionamento utilizando uma secção de cadeia de ligação.**

1. Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de provisionamento são comentadas. Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

1. Encontre a secção **de certificados.** Descomprimir e atualizar os três campos de certificados para apontar para os certificados que criou na secção anterior e mudou-se para o dispositivo IoT Edge. Forneça os caminhos URI de ficheiro, que tomam o formato `file:///<path>/<filename>` .

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Para o seu dispositivo **de camada superior,** encontre o parâmetro **do nome hospedeiro.** Atualize o valor para ser o nome de domínio totalmente qualificado (FQDN) ou endereço IP do dispositivo IoT Edge. Utilize o valor que escolher de forma consistente em todos os dispositivos da sua hierarquia.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Para dispositivos IoT Edge em **camadas mais baixas,** atualize o ficheiro config para apontar para o FQDN ou IP do dispositivo principal, correspondendo ao que estiver no campo de nome de hospedeiro do dispositivo **principal.** Para dispositivos IoT Edge na **camada superior,** deixe este parâmetro comentado.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Para hierarquias com mais de uma camada inferior, atualize o campo *parent_hostname* com o FQDN do dispositivo na camada imediatamente acima.

1. Para o seu dispositivo **de camada superior,** encontre a secção yaml do **agente** e atualize o valor de imagem para a versão correta do agente IoT Edge. Neste caso, vamos apontar o agente IoT Edge da camada superior no Registo de Contentores Azure com a versão pública de pré-visualização da imagem do agente IoT Edge disponível.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Para dispositivos IoT Edge em **camadas mais baixas,** atualize o nome de domínio do valor da imagem para apontar para o FQDN ou IP do dispositivo-mãe seguido pela porta de procuração API, 8000. Irá adicionar o módulo de procuração API na secção seguinte.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

1. Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Implementar módulos para o dispositivo de camada superior

Os passos restantes para completar a configuração do tempo de execução do IoT Edge e das cargas de trabalho de implantação são feitos a partir da Cloud, quer através do portal Azure ou do Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure:](https://ms.portal.azure.com/)

1. Navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Clique no ID do dispositivo do seu dispositivo de borda **de camada superior** na lista de dispositivos.

1. Na barra superior, selecione **Módulos de Conjunto**.

1. Selecione **Definições de tempo de execução,** ao lado do ícone da engrenagem.

1. Sob **o Edge Hub,** no campo de imagens, `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` entre.

   ![Editar a imagem do Edge Hub](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Adicione as seguintes variáveis ambientais ao seu módulo Edge Hub:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Editar as variáveis ambientais do Edge Hub](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Sob **o Edge Agent,** no campo de imagem, insira `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` . Selecione **Guardar**.

1. Adicione o módulo de registo Docker ao seu dispositivo de camada superior. **Selecione + Adicione** e escolha o Módulo de Borda **IoT** a partir do dropdown. Forneça o nome `registry` do seu módulo de registo Docker e introduza para a imagem `registry:latest` URI. Em seguida, adicione variáveis ambientais e crie opções para apontar o seu módulo de registo local no registo do contentor da Microsoft para descarregar imagens de contentores de e para servir estas imagens no registo:5000.

1. No separador variáveis ambientais, insira o seguinte par de valor-nome variável do ambiente:

    | Name | Valor |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. No separador de opções do recipiente, introduza o seguinte JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Em seguida, adicione o módulo de procuração API ao seu dispositivo de camada superior. **Selecione + Adicione** e escolha o Módulo **Marketplace** a partir do dropdown. Procure `IoT Edge API Proxy` e selecione o módulo. O representante da IoT Edge API utiliza a porta 8000 e está configurado para utilizar o módulo de registo denominado `registry` na porta 5000 por predefinição.

1. Selecione **Rever + criar** e, em seguida, **criar** para completar a implementação. O tempo de funcionaamento IoT Edge do seu dispositivo de camada superior, que tem acesso à internet, irá puxar e executar os configs **de pré-visualização do público** para o hub IoT Edge e o agente IoT Edge.

   ![Implementação completa contendo Edge Hub, Edge Agent, Módulo de Registo e Módulo de Procuração API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Na [Azure Cloud Shell](https://shell.azure.com/), insira o seguinte comando para criar uma deployment.jsem arquivo:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Copie o conteúdo do JSON abaixo no seu deployment.js, guarde-o e feche-o.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Introduza o seguinte comando para criar uma implementação para o seu dispositivo de borda de camada superior:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Implementar módulos para o dispositivo de camada inferior

Pode utilizar tanto o portal Azure como o Azure CLI para implementar cargas de trabalho da Cloud para os seus dispositivos **de camada inferior.**

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure:](https://ms.portal.azure.com/)

1. Navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Clique no ID do dispositivo do seu dispositivo de camada inferior na lista de dispositivos IoT Edge.

1. Na barra superior, selecione **Módulos de Conjunto**.

1. Selecione **Definições de tempo de execução,** ao lado do ícone da engrenagem.

1. Sob **o Edge Hub,** no campo de imagens, `$upstream:8000/azureiotedge-hub:1.2.0-rc2` entre.

1. Adicione as seguintes variáveis ambientais ao seu módulo Edge Hub:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Sob **o Edge Agent,** no campo de imagem, insira `$upstream:8000/azureiotedge-agent:1.2.0-rc2` . Selecione **Guardar**.

1. Adicione o módulo de sensor de temperatura. **Selecione + Adicione** e escolha o Módulo **Marketplace** a partir do dropdown. Procure `Simulated Temperature Sensor` e selecione o módulo.

1. Nos **Módulos IoT Edge,** selecione o `Simulated Temperature Sensor` módulo que acaba de adicionar e atualize a sua imagem URI para apontar para `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` .

1. **Selecione Guardar,** **Rever + criar** e **criar** para completar a implementação.

   ![Implementação completa contendo Edge Hub, Edge Agent e Sensor de Temperatura Simulado](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Na [Azure Cloud Shell](https://shell.azure.com/), insira o seguinte comando para criar uma deployment.jsem arquivo:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Copie o conteúdo do JSON abaixo no seu deployment.js, guarde-o e feche-o.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Introduza o seguinte comando para criar uma implementação de módulos definidos para o seu dispositivo de borda de camada inferior:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar as configurações locais e os recursos Azure que criou neste artigo para evitar encargos.

Para eliminar os recursos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Selecione o nome do grupo de recursos que contém os recursos de teste do IoT Edge. 

3. Reveja a lista de recursos que o seu grupo de recursos contém. Se quiser eliminá-los todos, pode selecionar **Eliminar grupo de recursos**. Se quiser eliminar apenas alguns dos recursos, pode clicar em cada um para eliminá-los individualmente. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste dois dispositivos IoT Edge como portais e definiste um como o dispositivo principal do outro. Depois, demonstrou ter puxado uma imagem de contentor para o dispositivo da criança através de um portal. Você também pode experimentar este cenário seguindo a amostra de [IoT Azure IoT](https://aka.ms/iotedge-nested-sample)scripted, que implementa máquinas virtuais Azure como dispositivos pré-configurados para simular um ambiente de fábrica.

Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"]
> [Implementar o modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)