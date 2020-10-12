---
title: Implementar vídeo ao vivo analytics no Azure Stack Edge
description: Este artigo lista os passos que o ajudarão a implementar o Live Video Analytics no seu Azure Stack Edge.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937717"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Implementar vídeo ao vivo analytics no Azure Stack Edge

Este artigo lista os passos que o ajudarão a implementar o Live Video Analytics no seu Azure Stack Edge. Depois de o dispositivo ter sido configurado e ativado, está pronto para a implementação do Live Video Analytics. 

Para o Live Video Analytics, vamos implementar via IoT Hub, mas os recursos do Azure Stack Edge expõem uma API Kubernetes, que permite ao cliente implementar soluções adicionais não-IoT Hub que possam interagir com o Live Video Analytics. 

> [!TIP]
> A utilização da API de Kubernetes (K8s) para implantação personalizada é um caso avançado. Recomenda-se que o cliente crie módulos de borda e implemente através do IoT Hub para cada recurso Azure Stack Edge em vez de utilizar a API de Kubernetes. Neste artigo, vamos mostrar-lhe os passos de implantação do módulo Live Video Analytics usando o IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure para a qual tem [privilégios de proprietário.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)
* Um recurso [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)
   
* [Um hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* Um [diretor de serviço](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) para o módulo Live Video Analytics.

   Utilize uma dessas regiões onde o IoT Hub está disponível: East US 2, Central EUA, North Central US, Japan East, West US 2, West Central US, Canadá East, UK South, France Central, France South, Switzerland North, Switzerland West e Japan West.
* Conta de armazenamento

    Recomenda-se que utilize contas de armazenamento v2 (GPv2) para fins gerais.  
    Saiba mais sobre uma [conta de armazenamento v2 para fins gerais.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal)
* [Código de estúdio visual](https://code.visualstudio.com/) na sua máquina de desenvolvimento. Certifique-se de que tem a [extensão Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Certifique-se de que a rede de que a sua máquina de desenvolvimento está ligada permite o Protocolo avançado de fila de mensagens sobre a porta 5671. Esta configuração permite que as ferramentas Azure IoT se comuniquem com o Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Configurar Azure Stack Edge para usar vídeo ao vivo Analytics

Azure Stack Edge é uma solução hardware-as-a-Service e um dispositivo de computação de borda ativado por IA com capacidades de transferência de dados de rede. Leia mais sobre [a Azure Stack Edge e instruções detalhadas de configuração](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep). Para começar, siga as instruções nos links abaixo:

* [Azure Stack Edge / Data Box Gateway Criação de recursos](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [Instalação e Configuração](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [Ligação e Ativação](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Anexar um hub IoT ao Azure Stack Edge

1. No [portal Azure,](https://ms.portal.azure.com)aceda ao seu recurso Azure Stack Edge e clique em Overview. No painel direito, no azulejo computo, selecione Começar.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. No azulejo computacional Configure Edge, selecione Configure compute.
1. Na lâmina de computação Configure Edge, insira o seguinte:
    
    | Campo|Valor|
    |---|---|
    |IoT Hub|Escolha entre Novo ou Já.<br/>Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente.<br/>Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado o recurso Azure Stack Edge.|
    |Nome|Insira um nome para o seu recurso IoT Hub.|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge":::
1. Selecione **Criar**. A criação de recursos IoT Hub demora alguns minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos **de computação Configure** para mostrar a configuração do cálculo. Para confirmar que a função de computação Edge foi configurada, selecione **Ver Computação** no azulejo **do cálculo Configure.**

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Azure Stack Edge":::

    > [!NOTE]
    > Se o diálogo Configure Compute for fechado antes de o IoT Hub estar associado ao recurso Azure Stack Edge, o IoT Hub é criado mas não é mostrado na configuração do cálculo. Recarregue a página após alguns minutos e veja-a aparecer.
    
    Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um tempo de execução IoT Edge também está a ser executado no dispositivo IoT Edge. Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.
    
    Uma vez preenchida toda a informação, verá o cartão de computação Configure Edge algo assim:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Azure Stack Edge":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Ativar os pré-requisitos computo no Azure Stack Edge Local UI

Antes de continuar, certifique-se de que:

* Ativou o seu recurso Azure Stack Edge.
* Tem acesso a um sistema de clientes Windows que executa o PowerShell 5.0 ou mais tarde para aceder ao recurso Azure Stack Edge.
* Para implementar um cluster Kubernetes, é necessário configurar o seu recurso Azure Stack Edge através da sua [UI web local.](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup) 
    
    * Para ativar o cálculo, na UI web local do seu dispositivo, aceda à página compute.
    
        * Selecione uma interface de rede que deseja ativar para calcular. Selecione Ativar. Permitir a computação resulta na criação de um interruptor virtual no seu dispositivo nessa interface de rede.
        * Deixe os IPs de teste de Kubernetes e os IPs dos serviços externos Kubernetes em branco.
        * Selecione Aplicar - Esta operação deve demorar cerca de 2 minutos.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge":::

        * Se o DNS não estiver configurado para o recurso API e Azure Stack Edge da Kubernetes, pode atualizar o ficheiro de anfitrião da janela.
        
            * Abrir um editor de texto como administrador
            * Abrir o ficheiro 'a C:\Windows\System32\drivers\etc\anfitriões
            * Adicione o nome do dispositivo API de Kubernetes IPv4 e nome de anfitrião ao ficheiro. (Esta informação pode ser encontrada no Portal Azure Stack Edge sob a secção dispositivos.)
            * Guardar e fechar

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Implementar módulo Live Video Analytics Edge utilizando o portal Azure

Para isso, só vamos tomar medidas específicas a partir da implementação do [Live Video Analytics via IoT Hub](deploy-iot-edge-device.md).

1. Ignore os passos de criação do utilizador e do grupo e vá para implementar o módulo [Deploy Live Video Analytics Edge.](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) Siga os passos aí mencionados.
1. Nas Opções de Criação de Contentores não é necessário definir variáveis ambientais. Então, salta este passo.
1. Abra o separador Opções de Criação de Recipientes.

   * Copiar e colar o seguinte JSON na caixa, para limitar o tamanho dos ficheiros de registo produzidos pelo módulo.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Se utilizar o protocolo gRPC com transferência de memória partilhada, utilize o modo Anfitrião IPC para acesso à memória partilhada entre soluções live video analytics e inferência.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > A secção "Ligações" no JSON tem 2 entradas. Sinta-se à vontade para atualizar o dispositivo de borda, mas certifique-se de que esses diretórios existem.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": Isto é usado para ligar os dados persistentes de configuração da aplicação do recipiente e armazená-lo no dispositivo de borda.
    * "/var/media:/var/media": Isto liga as pastas de mídia entre o dispositivo de borda e o recipiente. Isto é usado para armazenar as gravações de vídeo quando você executar uma topologia de gráficos de mídia que suporta o armazenamento de clips de vídeo no dispositivo de borda.
        
1. Continue os passos no doc e preencha as Definições Twin do Módulo.
1. Selecione **Seguinte**: Rotas para continuar até à secção de rotas. Especifique rotas.

    Mantenha as rotas predefinidos e selecione Seguinte: Revisão + criar para continuar na secção de revisão.
1. [Reveja e verifique a sua implementação](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>(Opcional) Montagem de volume de docker de configuração

Se pretender visualizar os dados nos diretórios de trabalho, siga estes passos para configurar os Conjuntos de Volume Docker antes de ser implantado. 

Estes passos abrangem a criação de um utilizador Gateway e a criação de partilhas de ficheiros para visualizar o conteúdo do diretório de trabalho live video analytics e da pasta de mídia Live Video Analytics.

> [!NOTE]
> Os suportes de ligação são suportados, mas os suportes de volume permitem que os dados sejam visualizados e se desejarem copiados remotamente. É possível utilizar tanto os suportes bind como os volumes, mas não podem apontar para o mesmo caminho do contentor.

1. Abra o portal Azure e vá ao recurso Azure Stack Edge.
1. Crie um **Utilizador Gateway** que possa aceder a partilhas.
    
    1. No painel de navegação esquerdo, clique em **Utilizadores gateway->**.
    1. Clique em **+ Adicionar Utilizador** ao conjunto o nome de utilizador e a palavra-passe. (Recomendado: `lvauser` ).
    1. Clique em **Adicionar**.
    
1. Crie uma **partilha local** para persistência de vídeo sonoro ao vivo.

    1. Clique em **Gateway->Shares**.
    1. Clique em **+ Adicionar Ações**.
    1. Desafete um nome de ações. (Recomendado: `lva` ).
    1. Mantenha o tipo de partilha como SMB.
    1. Certifique-se de que **a partilha com o cálculo Edge** é verificada.
    1. Certifique-se de **que a Configure à medida que a partilha local edge** é verificada.
    1. Em Detalhes do Utilizador, dê acesso à partilha ao utilizador recentemente criado.
    1. Clique em **Criar**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Azure Stack Edge":::
    
1. Crie uma Partilha Remota para armazenamento de sincronização de ficheiros.

    1. Primeiro criar uma conta de armazenamento de bolhas na mesma região.
    1. Clique em **Gateway->Shares**.
    1. Clique em **+ Adicionar Ações**.
    1. Desafete um nome de ações. (Recomendado: media).
    1. Mantenha o tipo de partilha como SMB.
    1. Certifique-se de que **a partilha com o cálculo Edge** é verificada.
    1. Certifique-se de **que a Configure, uma vez que a participação local edge** não é verificada.
    1. Selecione a conta de armazenamento recentemente criada.
    1. Definir um nome de recipiente.
    1. Descreva o tipo de armazenamento para Block Blob.
    1. Em Detalhes do Utilizador, dê acesso à partilha ao utilizador recentemente criado.
    1. Clique em **Criar**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Azure Stack Edge"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>Verifique se o módulo está em funcionamento

O passo final é garantir que o módulo está conectado e funcionando como esperado. O estado de tempo de funcionamento do módulo deve estar a funcionar para o seu dispositivo IoT Edge no recurso IoT Hub.

Para verificar se o módulo está em funcionamento, faça o seguinte:

1. No portal Azure, volte ao recurso Azure Stack Edge
1. Selecione o azulejo dos Módulos. Isto leva-te à lâmina dos Módulos. Na lista de módulos, identifique o módulo que implementou. O estado de funcionamento do módulo que adicionou deve estar a funcionar.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Azure Stack Edge":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configure a extensão Azure IoT Tools

Siga estas instruções para ligar ao seu hub IoT utilizando a extensão Azure IoT Tools.

1. No Código do Estúdio Visual, selecione Ver > Explorer. Ou selecione Ctrl+Shift+E.
1. No canto inferior esquerdo do separador Explorer, selecione Azure IoT Hub.
1. Selecione o ícone Mais Opções para ver o menu de contexto. Em seguida, selecione set IoT Hub Connection String.
1. Quando aparecer uma caixa de entrada, introduza a sua cadeia de ligação IoT Hub. 

   * Para obter a cadeia de ligação, vá ao seu IoT Hub no portal Azure e clique nas políticas de acesso compartilhados no painel de navegação à esquerda.
   * Clique em iothubowner obter as teclas de acesso partilhadas.
   * Copie a Cadeia de Ligação – tecla primária e cole-a na caixa de entrada do VSCode.

   A cadeia de ligação será como:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Se a ligação for bem sucedida, aparece a lista de dispositivos de borda. Devias ver o teu Azure Stack Edge. Agora pode gerir os seus dispositivos IoT Edge e interagir com o Azure IoT Hub através do menu de contexto. Para visualizar os módulos implantados no dispositivo de borda, sob o dispositivo Azure Stack, expanda o nó de Módulos.
    
## <a name="troubleshooting"></a>Resolução de problemas

* Kubernetes API Access (kubectl).

    * Siga a documentação para configurar a sua máquina para [acesso ao cluster Kubernetes.](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues)
    * Todos os módulos IoT Edge implantados utilizam o espaço de `iotedge` nome. Certifique-se de que inclui isso quando utilizar kubectl.
* Registos de módulos

    A `iotedge` ferramenta não está acessível para obter registos. Tem de utilizar [registos de kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  para visualizar os troncos ou o tubo num ficheiro. Exemplo: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Métricas de pod e nó

    Use [a parte superior de kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  para ver as métricas do vagem e do nó. (Esta funcionalidade estará disponível no próximo lançamento do Azure Stack Edge. >v2007)<br/>`kubectl top pods -n iotedge`
* A descoberta do módulo Networking For Module no Azure Stack Edge é necessário que o módulo tenha a ligação da porta do anfitrião em createOptions. O módulo será então endereçada. `moduleName:hostport`
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Montagem de volume

    Um módulo não arrancará se o recipiente estiver a tentar montar um volume num diretório existente e não vazio.
* Memória Partilhada

    A memória partilhada nos recursos do Azure Stack Edge é suportada através de cápsulas em qualquer espaço de nome, utilizando o Anfitrião IPC.
    Configurar a memória partilhada num módulo de borda para implantação via IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* (Avançado) Co-localização do pod

    Ao utilizar k8s para implementar soluções de inferência personalizadas que comunicam com o Live Video Analytics via gRPC, é necessário garantir que as cápsulas são implantadas nos mesmos nós que os módulos Live Video Analytics.

    * Opção 1 - Utilize a Affinity nó e incorporada em etiquetas nó para co-localização.

    Atualmente, a configuração personalizada nodeSelector não parece ser uma opção, uma vez que os utilizadores não têm acesso a etiquetas definidas nos Nós. No entanto, dependendo das convenções de topologia e nomeação do cliente, poderão utilizar [etiquetas de nó incorporados.](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels) Uma secção de nodeAffinity que refere os recursos do Azure Stack Edge com Live Video Analytics pode ser adicionada ao manifesto do casulo de inferência para alcançar a co-localização.
    * Opção 2 - Utilize a Affinity do Pod para co-localização (recomendado).
Kubernetes tem suporte para [Pod Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  que pode agendar pods no mesmo nó. Uma secção de podAffinity referente ao módulo Live Video Analytics pode ser adicionada ao manifesto do casulo de inferência para obter a co-localização.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Passos seguintes

Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando métodos diretos. [Invoque os métodos diretos](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) no módulo.
