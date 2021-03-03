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
ms.openlocfilehash: f1b1a94dc1d96e625947eef5730c24f080fc155a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721417"
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
> * Utilize o [módulo IoT Edge API Proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) para encaminhar de forma segura o tráfego HTTP sobre uma única porta a partir dos seus dispositivos de camada inferior.

Neste tutorial, são definidas as seguintes camadas de rede:

* **Camada superior**: Os dispositivos IoT Edge nesta camada podem ligar-se diretamente à nuvem.

* **Camadas inferiores**: Os dispositivos IoT Edge em camadas abaixo da camada superior não podem ligar-se diretamente à nuvem. Precisam de passar por um ou mais dispositivos IoT Edge intermediários para enviar e receber dados.

Este tutorial usa uma hierarquia de dois dispositivos para a simplicidade, retratada abaixo. Um dispositivo, o **dispositivo de camada superior,** representa um dispositivo na camada superior da hierarquia, que pode ligar-se diretamente à nuvem. Este dispositivo também será referido como o **dispositivo principal**. O outro dispositivo, o **dispositivo de camada inferior,** representa um dispositivo na camada inferior da hierarquia, que não consegue ligar-se diretamente à nuvem. Pode adicionar dispositivos adicionais de camada inferior para representar o seu ambiente de produção, conforme necessário. Os dispositivos em camadas mais baixas também serão referidos como **dispositivos infantis**. A configuração de quaisquer dispositivos adicionais de camada inferior seguirá a configuração do dispositivo de **camada inferior.**

![Estrutura da hierarquia tutorial, contendo dois dispositivos: o dispositivo de camada superior e o dispositivo de camada inferior](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma hierarquia de dispositivos IoT Edge, você precisará:

* Um computador (Windows ou Linux) com conectividade à Internet.
* Uma conta Azure com uma subscrição válida. Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de nível gratuito ou padrão em Azure.
* Azure CLI v2.3.1 com a extensão Azure IoT v0.10.6 ou superior instalada. Este tutorial usa a [Azure Cloud Shell.](../cloud-shell/overview.md) Se não está familiarizado com a Azure Cloud Shell, [consulte um quickstart para obter detalhes.](./quickstart-linux.md#prerequisites)
  * Para ver as versões atuais dos módulos e extensões Azure CLI, executar [a versão az](/cli/azure/reference-index?#az_version).
* Um dispositivo Linux para configurar como um dispositivo IoT Edge para cada dispositivo na sua hierarquia. Este tutorial usa dois dispositivos. Se não tiver dispositivos disponíveis, pode criar máquinas virtuais Azure para cada dispositivo da sua hierarquia, substituindo o texto do espaço reservado no seguinte comando e executando-o:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Certifique-se de que as seguintes portas estão abertas à entrada: 8000, 443, 5671, 8883:
  * 8000: Usado para puxar imagens do contentor Docker através do representante da API.
  * 443: Utilizado entre os centros de borda do pai e da criança para chamadas DE API REST.
  * 5671, 8883: Usado para AMQP e MQTT.

  Para obter mais informações, veja [How to open ports to a virtual machine with the Azure portal](../virtual-machines/windows/nsg-quickstart-portal.md) (Como abrir portas para uma máquina virtual com o portal do Azure).

>[!TIP]
>Se você quiser um olhar automatizado para a criação de uma hierarquia de dispositivos IoT Edge, você pode seguir a amostra de [IoT IoT scripted.](https://aka.ms/iotedge-nested-sample) Este cenário scripted implementa máquinas virtuais Azure como dispositivos pré-configurados para simular um ambiente de fábrica.
>
>Se quiser prosseguir com a criação da hierarquia da amostra passo a passo, continue com os passos tutoriais abaixo.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configure a sua hierarquia de dispositivo IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Criar uma hierarquia de dispositivos IoT Edge

Os dispositivos IoT Edge compõem as camadas da sua hierarquia. Este tutorial criará uma hierarquia de dois dispositivos IoT Edge: o **dispositivo de camada superior** e o seu filho, o dispositivo de camada **inferior**. Pode criar dispositivos infantis adicionais, conforme necessário.

Para criar os seus dispositivos IoT Edge, pode utilizar o portal Azure ou o Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://ms.portal.azure.com/)navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. **Selecione + Adicione um dispositivo IoT Edge**. Este dispositivo será o dispositivo de camada superior, por isso introduza um ID exclusivo apropriado para dispositivos. Selecione **Guardar**.

1. **Selecione + Adicione novamente um dispositivo IoT Edge.** Este dispositivo será um dispositivo de camada inferior, por isso introduza um ID exclusivo apropriado para dispositivos.

1. Escolha **Definir um dispositivo-mãe,** escolha o seu dispositivo de camada superior na lista de dispositivos e selecione **Ok**. Selecione **Guardar**.

   ![Definição do pai para dispositivo de camada inferior](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell,](https://shell.azure.com/)insira o seguinte comando para criar um dispositivo IoT Edge no seu hub. Este dispositivo será o dispositivo de camada superior, por isso introduza um ID exclusivo apropriado do dispositivo:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Uma criação bem sucedida do dispositivo irá descodundo a configuração JSON do dispositivo.

   ![Saída JSON de uma criação de dispositivos de sucesso](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Introduza o seguinte comando para criar um dispositivo IoT Edge de camada inferior. Pode criar mais do que um dispositivo de camada inferior se quiser mais camada na sua hierarquia. Certifique-se de fornecer identidades únicas do dispositivo a cada um.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Introduza o seguinte comando para definir cada relação pai-filho entre o dispositivo de **camada superior** e cada dispositivo de **camada inferior**. Certifique-se de que funciona este comando para cada dispositivo de camada inferior na sua hierarquia.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Este comando não tem saída explícita.

---

Em seguida, tome nota da cadeia de ligação de cada dispositivo IoT Edge. Serão usados mais tarde.

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

Se tiver completado corretamente os passos acima, pode utilizar os seguintes passos para verificar se as suas relações pai-filho estão corretas no portal Azure ou no Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a secção **IoT Edge** do seu Hub IoT.

1. Clique no ID do dispositivo de um dispositivo de **camada inferior** na lista de dispositivos.

1. Na página de detalhes do dispositivo, deverá ver a identidade do **seu dispositivo** de camada superior listada ao lado do campo do dispositivo **Parent.**

   [Dispositivo parental reconhecido por dispositivo infantil](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Também pode fazer uma criança a sua relação com a hierarquia através do seu dispositivo de **camada superior.**

1. Clique no ID do dispositivo de um dispositivo de **camada superior** na lista de dispositivos.

1. Selecione o **separador 'Gerir dispositivos para crianças'** na parte superior.

1. Na lista de dispositivos, deverá ver o seu **dispositivo de camada inferior.**

   [Dispositivo infantil reconhecido pelo dispositivo dos pais](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/) pode verificar se qualquer um dos seus dispositivos infantis estabeleceu com sucesso a sua relação com o dispositivo principal obtendo a identidade do dispositivo parental reconhecido do dispositivo infantil. Isto irá desausa a configuração JSON do dispositivo-mãe, na imagem acima:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Também pode ter a sua relação com a hierarquia através da consulta do seu dispositivo de **camada superior**.

1. Listar os dispositivos para crianças que o dispositivo principal sabe:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Uma vez que esteja satisfeito que a sua hierarquia esteja corretamente estruturada, está pronto para prosseguir.

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
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Este comando de script cria vários certificados e ficheiros chave, mas estamos a usar o seguinte certificado e par de chaves em cada dispositivo IoT Edge e referenciado no ficheiro config:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Cada dispositivo necessita de uma cópia do certificado de CA raiz e de uma cópia do certificado ca do seu próprio dispositivo e chave privada. Pode utilizar uma unidade USB ou [uma cópia de ficheiro segura](https://www.ssh.com/ssh/scp/) para mover os certificados para cada dispositivo.

1. Após a transferência dos certificados, instale a raiz CA para cada dispositivo.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Este comando deve ser desemolado em que um certificado foi adicionado em `/etc/ssl/certs` .

   [Mensagem de instalação de certificado bem sucedida](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Se tiver completado corretamente os passos acima, pode verificar se os seus certificados estão instalados `/etc/ssl/certs` navegando para esse diretório e procurando os seus certificados instalados.

1. Navegue `/etc/ssl/certs` para:

   ```bash
   cd /etc/ssl/certs
   ```

1. Listar os certificados instalados e `grep` `azure` para:

   ```bash
   ll | grep azure
   ```

   Deverá ver um haxixe de certificado ligado ao seu certificado de CA raiz e ao seu certificado de CA raiz ligado à cópia do seu `usr/local/share` diretório.

   [Resultados da pesquisa de certificados Azure](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Uma vez satisfeitos que os seus certificados sejam instalados em cada dispositivo, está pronto para prosseguir.

### <a name="install-iot-edge-on-the-devices"></a>Instalar ioT Edge nos dispositivos

A instalação da versão IoT Edge 1.2 imagens de tempo de execução dá aos seus dispositivos acesso às funcionalidades necessárias para funcionarem como uma hierarquia de dispositivos.

Para instalar o IoT Edge, é necessário instalar a configuração adequada do repositório, instalar pré-requisitos e instalar os ativos de desbloqueio necessários.

1. Instale a configuração do repositório que corresponda ao sistema operativo do dispositivo.

   * **Servidor Ubuntu 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Estiramento de Framboesa Pi OS**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Copie a lista gerada para o diretório sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Instale a chave pública Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Atualize as listas de pacotes no seu dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale o motor Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Instale o IoT Edge e o serviço de identidade IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

Se tiver completado corretamente os passos acima, viu a mensagem de banner Azure IoT Edge solicitando que atualize o ficheiro de configuração Azure IoT Edge, `/etc/aziot/config.toml` em cada dispositivo da sua hierarquia. Em caso afirmativo, está pronto para prosseguir.

### <a name="configure-the-iot-edge-runtime"></a>Configurar o runtime do IoT Edge

Além do fornecimento dos seus dispositivos, os passos de configuração estabelecem uma comunicação fidedigna entre os dispositivos da sua hierarquia utilizando os certificados que criou anteriormente. Os passos também começam a estabelecer a estrutura de rede da sua hierarquia. O dispositivo de camada superior manterá a conectividade da internet, permitindo-lhe retirar imagens para o seu tempo de funcionaamento a partir da nuvem, enquanto dispositivos de camada inferior irão percorrer o dispositivo de camada superior para aceder a estas imagens.

Para configurar o tempo de funcionamento do IoT Edge, é necessário modificar vários componentes do ficheiro de configuração. As configurações diferem ligeiramente entre o dispositivo de **camada superior** e um dispositivo de **camada inferior**, por isso tenha em atenção qual o ficheiro de configuração do dispositivo que está a editar para cada passo. A configuração do tempo de execução IoT Edge para os seus dispositivos consiste em quatro etapas, todas realizadas através da edição do ficheiro de configuração IoT Edge:

* Disponibilizando manualmente cada dispositivo adicionando a cadeia de ligação do dispositivo ao ficheiro de configuração.

* Termine a configuração dos certificados do seu dispositivo apontando o ficheiro de configuração para o certificado ca do dispositivo, a chave privada do dispositivo CA e o certificado de CA raiz.

* Atrate as botas do sistema utilizando o agente IoT Edge.

* Atualize o parâmetro **do nome de anfitrião** para o seu dispositivo **de camada superior** e atualize o parâmetro do **nome anfitrião** e **parent_hostname** parâmetro para os seus dispositivos de **camada inferior.**

Complete estes passos e reinicie o serviço IoT Edge para configurar os seus dispositivos.

>[!TIP]
>Ao navegar no ficheiro de configuração em Nano, pode utilizar **Ctrl + W** para procurar palavras-chave no ficheiro.

1. Em cada dispositivo, crie um ficheiro de configuração baseado no modelo incluído.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Para o seu dispositivo **de camada superior,** encontre a secção **hostname.** Descomprimir a linha com o `hostname` parâmetro e atualizar o valor para ser o nome de domínio totalmente qualificado (FQDN) ou endereço IP do dispositivo IoT Edge. Utilize o valor que escolher de forma consistente em todos os dispositivos da sua hierarquia.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

1. Para qualquer dispositivo IoT Edge em **camadas inferiores,** encontre a secção **de nome de anfitrião dos pais.** Descomprimir a linha com o `parent_hostname` parâmetro e atualizar o valor para apontar para o FQDN ou IP do dispositivo-mãe. Use o valor exato que coloca no campo de **nome de anfitrião** do dispositivo principal. Para o dispositivo IoT Edge na **camada superior,** deixe este parâmetro comentado.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > Para hierarquias com mais de uma camada inferior, atualize o campo *parent_hostname* com o FQDN do dispositivo na camada imediatamente acima.

1. Encontre a secção de **cert do pacote Trust** do ficheiro. Descompromete a linha com o `trust_bundle_cert` parâmetro e atualize o valor com o caminho URI de ficheiro para o certificado de CA raiz partilhado por todos os dispositivos na hierarquia gateway.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Encontre a secção **de Provisionamento** do ficheiro. Descompromessar as linhas de **provisionamento manual com fio de ligação**. Para cada dispositivo na sua hierarquia, atualize o valor de **connection_string** com a cadeia de ligação do seu dispositivo IoT Edge.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Encontre a secção **"Agente de borda predefinido".**

   * Para o seu dispositivo **de camada superior,** atualize o valor de imagem edgeAgent para a versão pública de pré-visualização do módulo no Registo do Contentor Azure.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Para cada dispositivo IoT Edge em **camadas mais baixas,** atualize a imagem edgeAgent para apontar para o dispositivo-mãe seguido pela porta que o representante da API está a ouvir. Irá colocar o módulo de procuração API no dispositivo principal na secção seguinte.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Encontre a secção de **certificados Edge CA.** Descomprimir as três primeiras linhas desta secção. Em seguida, atualize os dois parâmetros para apontar para o certificado ca do dispositivo e dispositivo ficheiros de chaves ca privados que criou na secção anterior e mudou-se para o dispositivo IoT Edge. Forneça os caminhos URI de ficheiro, que tomam o `file:///<path>/<filename>` formato, tais como `file:///certs/iot-edge-device-ca-top-layer-device.key.pem` .

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Certifique-se de que utiliza a via de certificado ca e o nome de arquivo de **cadeia completa** para preencher o `device_ca_cert` campo.

1. Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

1. Depois de introduzir as informações de provisionamento no ficheiro de configuração, aplique as alterações:

   ```bash
   sudo iotedge config apply
   ```

Antes de continuar, certifique-se de que atualizou o ficheiro de configuração de cada dispositivo na hierarquia. Dependendo da sua estrutura hierárquica, configura um **dispositivo de camada superior** e um ou mais **dispositivos de camada inferior.**

Se tiver completado corretamente os passos acima, pode verificar se os seus dispositivos estão configurados corretamente.

1. Execute as verificações de configuração e conectividade nos seus dispositivos:

   ```bash
   sudo iotedge check
   ```

No seu **dispositivo de camada superior,** espere ver uma saída com várias avaliações de passagem e pelo menos um aviso. A verificação do `latest security daemon` will warn you that another IoT Edge version is the latest stable version, because IoT Edge version 1.2 is in public preview. Pode ver avisos adicionais sobre políticas de registos e, dependendo da sua rede, as políticas de DNS.

Num **dispositivo de camada inferior,** espere ver uma saída semelhante ao dispositivo de camada superior, mas com um aviso adicional indicando que o módulo EdgeAgent não pode ser puxado a montante. Isto é aceitável, uma vez que o módulo Proxy IoT Edge API e o módulo de registo de contentores Docker, que os dispositivos de camada inferior irão retirar imagens, ainda não estão implantados no **dispositivo de camada superior**.

Abaixo é apresentada uma amostra de `iotedge check` produção:

[Configuração da amostra e resultados de conectividade](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Uma vez que esteja satisfeito que as suas configurações estejam corretas em cada dispositivo, está pronto para prosseguir.

## <a name="deploy-modules-to-the-top-layer-device"></a>Implementar módulos para o dispositivo de camada superior

Os módulos servem para completar a implementação e o tempo de execução IoT Edge para os seus dispositivos e definir ainda mais a estrutura da sua hierarquia. O módulo IoT Edge API Proxy despende de forma segura o tráfego HTTP sobre uma única porta a partir dos seus dispositivos de camada inferior. O módulo de registo Docker permite um repositório de imagens Docker que os seus dispositivos de camada inferior podem aceder através do encaminhamento de imagens puxa para o dispositivo de camada superior.

Para implantar módulos no seu dispositivo de camada superior, pode utilizar o portal Azure ou O CLI Azure.

>[!NOTE]
>Os passos restantes para completar a configuração do tempo de execução do IoT Edge e das cargas de trabalho de implantação não são feitos nos seus dispositivos IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure:](https://ms.portal.azure.com/)

1. Navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Clique no ID do dispositivo do seu dispositivo de borda **de camada superior** na lista de dispositivos.

1. Na barra superior, selecione **Módulos de Conjunto**.

1. Selecione **Definições de tempo de execução,** ao lado do ícone da engrenagem.

1. Sob **o Edge Hub,** no campo de imagens, `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` entre.

   ![Editar a imagem do Edge Hub](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Adicione as seguintes variáveis ambientais ao seu módulo Edge Hub:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Editar as variáveis ambientais do Edge Hub](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Sob **o Edge Agent,** no campo de imagem, insira `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4` . Selecione **Guardar**.

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
                               } 
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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
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

Se tiver completado corretamente os passos acima, o seu **dispositivo de camada superior** deverá reportar os quatro módulos: o Módulo de Procuração IoT Edge API, o módulo de registo do contentor Docker e os módulos do sistema, conforme especificado na **implementação.** Pode levar alguns minutos para o dispositivo receber a sua nova implementação e iniciar os módulos. Refresque a página até ver o módulo de sensor de temperatura listado como **Reportado pelo Dispositivo**. Assim que os módulos forem comunicados pelo dispositivo, está pronto para continuar.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Implementar módulos para o dispositivo de camada inferior

Os módulos também servem como cargas de trabalho dos seus dispositivos de camada inferior. O módulo sensor de temperatura simulada cria dados de telemetria de amostra para fornecer um fluxo funcional de dados através da sua hierarquia de dispositivos.

Para implantar módulos nos seus dispositivos de camada inferior, pode utilizar o portal Azure ou o Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure:](https://ms.portal.azure.com/)

1. Navegue até ao seu Hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Clique no ID do dispositivo do seu dispositivo de camada inferior na lista de dispositivos IoT Edge.

1. Na barra superior, selecione **Módulos de Conjunto**.

1. Selecione **Definições de tempo de execução,** ao lado do ícone da engrenagem.

1. Sob **o Edge Hub,** no campo de imagens, `$upstream:8000/azureiotedge-hub:1.2.0-rc4` entre.

1. Adicione as seguintes variáveis ambientais ao seu módulo Edge Hub:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Sob **o Edge Agent,** no campo de imagem, insira `$upstream:8000/azureiotedge-agent:1.2.0-rc4` . Selecione **Guardar**.

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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
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

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

O `azureiotedge-diagnostics` valor é retirado do registo do contentor que está ligado ao módulo de registo. Este tutorial tem definido por padrão para https://mcr.microsoft.com:

| Name | Valor |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Se estiver a utilizar um registo de contentores privados, certifique-se de que todas as imagens (por exemplo, IoTEdgeAPIProxy, edgeAgent, edgeHub e diagnósticos) estão presentes no registo do contentor.

## <a name="view-generated-data"></a>Ver os dados gerados

O módulo **de sensor de temperatura simulado** que empurrou gera dados do ambiente da amostra. Envia mensagens que incluem temperatura ambiente e humidade, temperatura e pressão da máquina e um tempotando.

Pode ver as mensagens chegarem ao seu hub IoT utilizando a [extensão Azure IoT Hub para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Também pode ver estas mensagens através da [Azure Cloud Shell:](https://shell.azure.com/)

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar as configurações locais e os recursos Azure que criou neste artigo para evitar encargos.

Para eliminar os recursos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Selecione o nome do grupo de recursos que contém os recursos de teste do IoT Edge. 

3. Reveja a lista de recursos que o seu grupo de recursos contém. Se quiser eliminá-los todos, pode selecionar **Eliminar grupo de recursos**. Se quiser eliminar apenas alguns dos recursos, pode clicar em cada um para eliminá-los individualmente. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste dois dispositivos IoT Edge como portais e definiste um como o dispositivo principal do outro. Depois, demonstrou ter puxado uma imagem de contentor para o dispositivo da criança através de um portal.

Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"]
> [Implementar o modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
