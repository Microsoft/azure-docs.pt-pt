---
title: Como instalar e executar o recipiente de Análise Espacial - Visão computacional
titleSuffix: Azure Cognitive Services
description: O recipiente de Análise Espacial permite detetar pessoas e distâncias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: db21f1170dacbfa1e4367e7f22143ec3d0b0f6e4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737341"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Instale e execute o recipiente de análise espacial (Pré-visualização)

O recipiente de análise espacial permite-lhe analisar vídeos de streaming em tempo real para entender as relações espaciais entre as pessoas, o seu movimento e interações com objetos em ambientes físicos. Os contentores são ótimos para requisitos específicos de governação de dados e segurança.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão De Computador crie um recurso de "  target="_blank"> Visão De Computador para o <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nível S1 padrão no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para executar o recipiente de análise espacial. Usará a sua chave e o ponto final mais tarde.


### <a name="spatial-analysis-container-requirements"></a>Requisitos do contentor de análise espacial

Para executar o recipiente de análise espacial, precisa de um dispositivo computacional com um [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Recomendamos que utilize [o Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) com aceleração gpu, no entanto o recipiente funciona em qualquer outra máquina de ambiente de trabalho que cumpra os requisitos mínimos. Vamos referir-nos a este dispositivo como o computador anfitrião.

#### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)

Azure Stack Edge é uma solução hardware-as-a-Service e um dispositivo de computação de borda ativado por IA com capacidades de transferência de dados de rede. Para obter instruções detalhadas de preparação e configuração, consulte a [documentação Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Máquina de desktop](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware

* RAM sistema de 4 GB
* 4 GB de GPU RAM
* CPU de 8 núcleos
* 1 NVIDIA Tesla T4 GPU
* 20 GB de espaço HDD

#### <a name="recommended-hardware"></a>Hardware recomendado

* RAM sistema de 32 GB
* 16 GB de GPU RAM
* CPU de 8 núcleos
* 2 GPUs NVIDIA Tesla T4
* 50 GB de espaço SSD

Neste artigo, você irá baixar e instalar os seguintes pacotes de software. O computador anfitrião deve poder executar o seguinte (ver abaixo para instruções):

* [Condutores gráficos da NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) e [kit de ferramentas NVIDIA CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configurações para [MPS NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Serviço Multi-Processo).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) e [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Tempo de execução de Azure IoT Edge.](../../iot-edge/how-to-install-iot-edge.md)

#### <a name="azure-vm-with-gpu"></a>[Azure VM com GPU](#tab/virtual-machine)
No nosso exemplo, vamos utilizar uma [série NC VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que tem um K80 GPU.

---

| Requisito | Description |
|--|--|
| Câmara | O recipiente de análise espacial não está ligado a uma marca de câmara específica. O dispositivo da câmara precisa de suportar Real-Time Protocolo de Streaming (RTSP) e codificação H.264, estar acessível ao computador anfitrião e ser capaz de ser transmitido a 15FPS e resolução de 1080p. |
| Linux OS | [O Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) deve ser instalado no computador anfitrião.  |


## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contentor

Preencha e submeta o [formulário de pedido](https://aka.ms/csgate) para solicitar aprovação para executar o contentor.

O formulário solicita informações sobre si, sobre a sua empresa e sobre o cenário de utilizador para o qual utilizará o recipiente. Depois de submeter o formulário, a equipa dos Serviços Cognitivos da Azure irá analisá-lo e enviar-lhe um e-mail com uma decisão.

> [!IMPORTANT]
> * No formulário, deve utilizar um endereço de e-mail associado a um ID de assinatura Azure.
> * O recurso De Visão De Computador que utiliza para executar o contentor deve ter sido criado com o ID de assinatura Azure aprovado.

Depois de aprovado, poderá executar o contentor depois de o descarregar do Registo de Contentores da Microsoft (MCR), descrito mais tarde no artigo.

Não poderá executar o contentor se a sua assinatura Azure não tiver sido aprovada.

## <a name="set-up-the-host-computer"></a>Configurar o computador anfitrião

Recomenda-se que utilize um dispositivo Azure Stack Edge para o seu computador anfitrião. Clique **em Desktop Machine** se estiver a configurar um dispositivo diferente ou Máquina **Virtual** se estiver a utilizar um VM.

#### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Configuure computação no portal Azure Stack Edge 
 
A análise espacial utiliza as características computacionais do Azure Stack Edge para executar uma solução de IA. Para ativar as funcionalidades de cálculo, certifique-se de que: 

* [Ligou e ativou](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) o seu dispositivo Azure Stack Edge. 
* Tem um sistema de clientes Windows a executar o PowerShell 5.0 ou mais tarde, para aceder ao dispositivo.  
* Para implementar um cluster Kubernetes, é necessário configurar o seu dispositivo Azure Stack Edge através da **UI local** no [portal Azure](https://portal.azure.com/): 
  1. Ativar a função de cálculo no seu dispositivo Azure Stack Edge. Para ativar o cálculo, aceda à página **compute** na interface web para o seu dispositivo. 
  2. Selecione uma interface de rede que deseja ativar para calcular e, em seguida, clique em **Enable**. Isto irá criar um interruptor virtual no seu dispositivo, nessa interface de rede.
  3. Deixe os endereços IP do nó de teste kubernetes e os endereços IP dos serviços externos Kubernetes em branco.
  4. Clique em **Aplicar**. Esta operação pode demorar cerca de dois minutos. 

![Configure computação](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Crie um papel de computação Edge e crie um recurso IoT Hub

No [portal Azure,](https://portal.azure.com/)navegue para o seu recurso Azure Stack Edge. Na página **de visão geral** ou na lista de navegação, clique no botão Edge compute **Get start.** No azulejo  **computacional Configure Edge,**   clique em **Configurar**. 

![Ligação](media/spatial-analysis/configure-edge-compute-tile.png)

Na página de **computação Configure Edge,**   escolha um Hub IoT existente ou opte por criar um novo. Por padrão, um nível de preços Standard (S1) é usado para criar um recurso IoT Hub. Para utilizar um recurso IoT Hub de nível livre, crie um e, em seguida, selecione-o. O recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é usado pelo recurso Azure Stack Edge 

Clique em **Criar**. A criação de recursos IoT Hub pode demorar alguns minutos. Após a criação do recurso IoT Hub, o azulejo **computacional Configure Edge** atualizar-se-á para mostrar a nova configuração. Para confirmar que a função de computação Edge foi configurada, selecione **Ver configurar** no azulejo **computacional Configure.**  

Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. O tempo de execução Azure IoT Edge já estará em funcionamento no dispositivo IoT Edge.

> [!NOTE]
> * Atualmente apenas a plataforma Linux é suportada para dispositivos IoT Edge. Para ajudar a resolver problemas no dispositivo Azure Stack Edge, consulte o artigo [de registo e resolução de problemas.](spatial-analysis-logging.md)
> * Para saber mais sobre como configurar um dispositivo IoT Edge para comunicar através de um servidor proxy, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor de procuração](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Ativar MPS no Azure Stack Edge 

1. Executar uma sessão Windows PowerShell como administrador. 

2. Certifique-se de que o serviço de Gestão Remota do Windows está a funcionar no seu cliente. No terminal PowerShell, utilize o seguinte comando 
    
    ```powershell
    winrm quickconfig
    ```
    
    Se vir avisos sobre uma exceção à firewall, verifique o tipo de ligação de rede e consulte a documentação de [Gestão Remota](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) do Windows.

3. Atribua uma variável ao endereço IP do dispositivo. 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. Para adicionar o endereço IP do seu dispositivo à lista de anfitriões fidedignos do cliente, utilize o seguinte comando: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Inicie uma sessão Do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Forneça a palavra-passe quando solicitado. Use a mesma palavra-passe que é usada para iniciar súm na UI web local. A senha de UI local padrão é `Password1` .

Digite `Start-HcsGpuMPS` para iniciar o serviço MPS no dispositivo. 

Para ajudar a resolver problemas no dispositivo Azure Stack Edge, consulte [Troubleshooting do dispositivo Azure Stack Edge](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Máquina de desktop](#tab/desktop-machine)

Siga estas instruções se o seu computador anfitrião não for um dispositivo Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Instale os controladores gráficos NVIDIA CUDA e Nvidia no computador anfitrião

Utilize o seguinte script de bash para instalar os controladores gráficos Nvidia necessários e o KIT de Ferramentas CUDA.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Reinicie a máquina e executar o seguinte comando.

```bash
nvidia-smi
```

Deverá ver o seguinte resultado.

![Saída do condutor da NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Instale Docker CE e nvidia-docker2 no computador anfitrião

Instale o Docker CE no computador anfitrião.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Instale o pacote de software *nvidia-docker-2.*

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Ativar mps NVIDIA no computador anfitrião

> [!TIP]
> * Não instale MPS se a sua capacidade de computação GPU for inferior a 7.x (pré-Volta). Consulte [a cuda Compatability](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) para referência. 
> * Executar as instruções de MPS a partir de uma janela de terminal no computador anfitrião. Não dentro do seu contentor Docker.

Para melhor desempenho e utilização, configuure as GPU(s) do computador anfitrião para [o Serviço Multiprocessamento NVIDIA (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Executar as instruções de MPS a partir de uma janela de terminal no computador anfitrião.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Configure Azure IoT Edge no computador anfitrião

Para implantar o recipiente de análise espacial no computador anfitrião, crie uma instância de um serviço [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) utilizando o nível de preços Standard (S1) ou Free (F0). Se o seu computador anfitrião for um Azure Stack Edge, utilize o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.

Utilize o CLI Azure para criar um exemplo de Azure IoT Hub. Substitua os parâmetros, se for caso disso. Em alternativa, pode criar o Azure IoT Hub no [portal Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
sudo az login
sudo az account set --subscription <name or ID of Azure Subscription>
sudo az group create --name "test-resource-group" --location "WestUS"

sudo az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

sudo az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

Se o computador anfitrião não for um dispositivo Azure Stack Edge, terá de instalar a versão 1.0.9 do [Azure IoT Edge.](../../iot-edge/how-to-install-iot-edge.md) Siga estes passos para descarregar a versão correta:

Servidor Ubuntu 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Copie a lista gerada.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Instale a chave pública Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Atualize as listas de pacotes no seu dispositivo.

```bash
sudo apt-get update
```

Instale o desbloqueio 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Em seguida, registe o computador anfitrião como um dispositivo IoT Edge na sua instância IoT Hub, utilizando uma [cadeia de ligação](../../iot-edge/how-to-manual-provision-symmetric-key.md?view=iotedge-2018-06).

Tem de ligar o dispositivo IoT Edge ao seu Azure IoT Hub. Tem de copiar a cadeia de ligação do dispositivo IoT Edge que criou anteriormente. Em alternativa, pode executar o comando abaixo no CLI Azure.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

No computador anfitrião aberto  `/etc/iotedge/config.yaml` para edição. `ADD DEVICE CONNECTION STRING HERE`Substitua-a pela cadeia de ligação. Guarde e feche o ficheiro. Executar este comando para reiniciar o serviço IoT Edge no computador anfitrião.

```bash
sudo systemctl restart iotedge
```

Implementar o recipiente de análise espacial como módulo IoT no computador anfitrião, quer a partir do [portal Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou [do Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows). Se estiver a utilizar o portal, deite a imagem URI para a localização do seu Registo de Contentores Azure. 

Utilize os degraus abaixo para colocar o recipiente utilizando o Azure CLI.

#### <a name="azure-vm-with-gpu"></a>[Azure VM com GPU](#tab/virtual-machine)

Uma Máquina Virtual Azure com uma GPU também pode ser usada para executar análises espaciais. O exemplo abaixo utilizará uma [série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM que tem um K80 GPU.

#### <a name="create-the-vm"></a>Criar a VM

Abra o assistente [criar uma máquina virtual](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) no portal Azure.

Dê um nome ao seu VM e selecione a região para ser (EUA) West US 2. Certifique-se de definir `Availability Options` "Não é necessário despedimento de infraestruturas". Consulte a figura abaixo para a configuração completa e o passo seguinte para ajudar a localizar o tamanho correto do VM. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.png" alt-text="Detalhes da configuração da máquina virtual." lightbox="media/spatial-analysis/virtual-machine-instance-details.png":::

Para localizar o tamanho VM, selecione "Ver todos os tamanhos" e, em seguida, consulte a lista para "Tamanhos VM de armazenamento não premium", mostrados abaixo.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Tamanhos de máquina virtual." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Em seguida, selecione **NC6** ou **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="seleção promocional" lightbox="media/spatial-analysis/promotional-selection.png":::

Em seguida, Crie o VM. Uma vez criado, navegue para o recurso VM no portal Azure e selecione `Extensions` a partir do painel esquerdo. A janela de extensões aparecerá com todas as extensões disponíveis. Selecione, `NVIDIA GPU Driver Extension` clique em criar e complete o assistente.

Assim que a extensão for aplicada com sucesso, navegue na página principal do VM no portal Azure e clique `Connect` em . O VM pode ser acedido através de SSH ou RDP. O PDR será útil, pois permitirá a visualização da janela do visualizador (explicado mais tarde). Configure o acesso RDP seguindo [estes passos](../../virtual-machines/linux/use-remote-desktop.md) e abrindo uma ligação de ambiente de trabalho remoto ao VM.

### <a name="verify-graphics-drivers-are-installed"></a>Verifique se os controladores gráficos estão instalados

Executar o seguinte comando para verificar se os controladores gráficos foram instalados com sucesso. 

```bash
nvidia-smi
```

Deverá ver o seguinte resultado.

![Saída do condutor da NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Instalar Docker CE e nvidia-docker2 no VM

Executar os seguintes comandos um de cada vez para instalar Docker CE e nvidia-docker2 no VM.

Instale o Docker CE no computador anfitrião.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Instale o pacote de software *nvidia-docker-2.*

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Agora que montou e configuraram o seu VM, siga os passos abaixo para implantar o recipiente de análise espacial. 

---

### <a name="iot-deployment-manifest"></a>Manifesto de implantação do IoT

Para simplificar a implantação de contentores em vários computadores hospedeiros, pode criar um ficheiro manifesto de implantação para especificar as opções de criação de contentores e variáveis ambientais. Pode encontrar um exemplo de um manifesto de implantação [para Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [outras máquinas de desktop](https://go.microsoft.com/fwlink/?linkid=2152270), e [Azure VM com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) no GitHub.

A tabela seguinte mostra as várias variáveis ambientais utilizadas pelo Módulo de Borda IoT. Também pode defini-los no manifesto de implantação acima ligado, utilizando o `env` atributo em `spatialanalysis` :

| Nome da Definição | Valor | Descrição|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Informação; Verbose | Nível de registo, selecione um dos dois valores|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Não modificar|
| ARCHON_PERF_MARKER| false| Desa parte para a exploração de desempenho, caso contrário, isto deve ser falso.| 
| ARCHON_NODES_LOG_LEVEL | Informação; Verbose | Nível de registo, selecione um dos dois valores|
| OMP_WAIT_POLICY | PASSIVO | Não modificar|
| QT_X11_NO_MITSHM | 1 | Não modificar|
| APIKEY | sua chave API| Recolha este valor a partir do portal Azure a partir do seu recurso Visão de Computador. Pode encontrá-lo na secção **Chave e ponto final** para o seu recurso. |
| FATURAÇÃO | o seu Endpoint URI| Recolha este valor a partir do portal Azure a partir do seu recurso Visão de Computador. Pode encontrá-lo na secção **Chave e ponto final** para o seu recurso.|
| EULA | aceitar | Este valor precisa de ser definido para *aceitar* que o contentor corra |
| EXIBIÇÃO | :1 | Este valor tem de ser igual à saída do `echo $DISPLAY` computador anfitrião. Os dispositivos Azure Stack Edge não têm ecrã. Esta definição não é aplicável|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Adicione esta variável de ambiente se o seu GPU **não** for T4 ou NVIDIA 2080 Ti|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Adicione esta variável de ambiente se o seu GPU **não** for T4 ou NVIDIA 2080 Ti|
| KEY_ENV | Chave de encriptação ASE | Adicione esta variável de ambiente se Video_URL é uma corda obfuscada |
| IV_ENV | Vetor de inicialização | Adicione esta variável de ambiente se Video_URL é uma corda obfuscada|

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)

Uma vez atualizado o manifesto de implementação para [dispositivos Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [uma máquina de ambiente de trabalho](https://go.microsoft.com/fwlink/?linkid=2152270) ou [VM Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) com as suas próprias definições e seleção de operações, pode utilizar o comando [CLI abaixo do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows) para implantar o recipiente no computador anfitrião, como módulo IoT Edge.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json --subscription "<subscriptionId>"
```

|Parâmetro  |Descrição  |
|---------|---------|
| `--hub-name` | O seu nome Azure IoT Hub. |
| `--content` | O nome do ficheiro de implantação. |
| `--target-condition` | O nome do seu dispositivo IoT Edge para o computador anfitrião. |
| `-–subscription` | ID de assinatura ou nome. |

Este comando iniciará a implantação. Navegue para a página do seu exemplo Azure IoT Hub no portal Azure para ver o estado de implantação. O estado pode mostrar como *417 – A configuração de implementação do dispositivo não é definida* até que o dispositivo termine de descarregar as imagens do contentor e comece a funcionar.

## <a name="validate-that-the-deployment-is-successful"></a>Validar que a implementação é bem sucedida

Há várias formas de validar que o contentor está a funcionar. Localizar o Estado de *execução* nas Definições do **Módulo de Borda IoT** para o módulo de análise espacial no seu exemplo Azure IoT Hub no portal Azure. Validar que o **valor desejado** e o **valor reportado** para o estado de *execução* *estão em execução.*

![Verificação de implementação de exemplo](./media/spatial-analysis/deployment-verification.png)

Uma vez concluída a implantação e o contentor em funcionamento, o **computador anfitrião** começará a enviar eventos para o Azure IoT Hub. Se utilizar a `.debug` versão das operações, verá uma janela de visualizador para cada câmara que configurar no manifesto de implantação. Pode agora definir as linhas e zonas que pretende monitorizar no manifesto de implantação e seguir as instruções para ser novamente implantado. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Configure as operações realizadas por análise espacial

Terá de utilizar [operações de análise espacial](spatial-analysis-operations.md) para configurar o recipiente para utilizar câmaras conectadas, configurar as operações e muito mais. Para cada dispositivo de câmara que configurar, as operações de análise espacial gerarão um fluxo de saída de mensagens JSON, enviadas para o seu exemplo de Azure IoT Hub.

## <a name="redeploy-or-delete-the-deployment"></a>Reimplantar ou eliminar a implantação

Se precisar de atualizar a implementação, tem de se certificar de que as suas implementações anteriores estão implantadas com sucesso ou que tem de eliminar as implementações do dispositivo IoT Edge que não foram concluídas. Caso contrário, essas implementações continuarão, deixando o sistema em mau estado. Pode utilizar o portal Azure ou o [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows).

## <a name="use-the-output-generated-by-the-container"></a>Utilize a saída gerada pelo recipiente

Se pretender começar a consumir a saída gerada pelo recipiente, consulte os seguintes artigos:

*   Utilize o Azure Event Hub SDK para a sua linguagem de programação escolhida para ligar ao ponto final do Azure IoT Hub e receber os eventos. Consulte [as mensagens dispositivo-nuvem do ponto final incorporado](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
*   Confise o Encaminhamento de Mensagens no seu Azure IoT Hub para enviar os eventos para outros pontos finais ou guardar os eventos para o Azure Blob Storage, etc. Consulte [o Encaminhamento de Mensagens IoT Hub](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Execução da análise espacial com um ficheiro de vídeo gravado

Pode utilizar a análise espacial com vídeo gravado ou ao vivo. Para utilizar a análise espacial para vídeo gravado, tente gravar um ficheiro de vídeo e guarde-o como um ficheiro mp4. Crie uma conta de armazenamento de bolhas em Azure, ou use uma existente. Em seguida, atualize as seguintes definições de armazenamento de bolhas no portal Azure:
    1. Alterar **Transferência Segura necessária** para **Deficientes**
    2. Alterar **Permitir o acesso público do Blob** a **Enabled**

Navegue na secção **do Recipiente** e crie um novo recipiente ou utilize um existente. Em seguida, faça o upload do ficheiro de vídeo para o recipiente. Expandir as definições de ficheiro para o ficheiro carregado e selecionar **Gerar SAS**. Certifique-se de que define a **Data de Validade tempo** suficiente para cobrir o período de testes. Definir **Protocolos Permitidos** a *HTTP* *(HTTPS* não é suportado).

Clique em **Generate SAS Token e URL** e copie o URL Blob SAS. Substitua o início `https` e teste o URL num browser que suporte a `http` reprodução de vídeo.

Substitua `VIDEO_URL` no manifesto de implantação do seu dispositivo [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179) [máquina de ambiente de trabalho,](https://go.microsoft.com/fwlink/?linkid=2152270)ou [Azure VM por GPU](https://go.microsoft.com/fwlink/?linkid=2152189) com o URL que criou, para todos os gráficos. Definir `VIDEO_IS_LIVE` para , e `false` recolocar o recipiente de análise espacial com o manifesto atualizado. Veja o exemplo abaixo.

O módulo de análise espacial começará a consumir ficheiro de vídeo e também irá reproduzir-se continuamente automaticamente.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar problemas ao iniciar ou executar o recipiente, consulte [a telemetria e a resolução de problemas](spatial-analysis-logging.md) para obter etapas para questões comuns. Este artigo também contém informações sobre a geração e recolha de registos e a recolha da saúde do sistema.

## <a name="billing"></a>Faturação

O recipiente de análise espacial envia informações de faturação para o Azure, utilizando um recurso de Visão De Computador na sua conta Azure. A utilização da análise espacial na visualização pública é atualmente gratuita. 

Os recipientes Azure Cognitive Services não estão licenciados para funcionar sem estarem ligados ao ponto final de medição/faturação. Deve permitir que os recipientes comuniquem sempre informações de faturação com o ponto final de faturação. Os contentores dos Serviços Cognitivos não enviam dados dos clientes, como o vídeo ou a imagem que está a ser analisado, para a Microsoft.


## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar o recipiente de análise espacial. Em resumo:

* A análise espacial é um contentor Linux para o Docker.
* As imagens do contentor são descarregadas a partir do Registo de Contentores da Microsoft.
* As imagens do contentor funcionam como Módulos IoT em Azure IoT Edge.
* Como configurar o recipiente e implantá-lo numa máquina hospedeira.

## <a name="next-steps"></a>Próximos passos

* [Implementar uma aplicação web People Counting](spatial-analysis-web-app.md)
* [Configure operações de análise espacial](spatial-analysis-operations.md)
* [Registo e resolução de problemas](spatial-analysis-logging.md)
* [Guia de colocação de câmera](spatial-analysis-camera-placement.md)
* [Guia de colocação de zona e linha](spatial-analysis-zone-line-placement.md)