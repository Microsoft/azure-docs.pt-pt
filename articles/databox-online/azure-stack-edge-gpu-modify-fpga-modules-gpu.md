---
title: Modificar os módulos IoT Edge no dispositivo FPGA para funcionar no dispositivo GPU Azure Stack Edge Pro
description: Descreve quais as modificações necessárias para que os módulos IoT Edge existentes nos dispositivos FPGA existentes sejam executados no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1276a242efb1917a0c4a24aa73c3e0d11f81e158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559167"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Executar os módulos IoT Edge existentes a partir de dispositivos Azure Stack Edge Pro FPGA no dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo detalha as alterações necessárias para um módulo IoT Edge baseado em estivador que funciona no Azure Stack Edge Pro FPGA para que possa funcionar numa plataforma IoT Edge baseada em Kubernetes no dispositivo GPU Azure Stack Edge Pro. 

## <a name="about-iot-edge-implementation"></a>Sobre a implementação do IoT Edge 

A implementação do IoT Edge é diferente nos dispositivos Azure Stack Edge Pro FPGA vs. que nos dispositivos GPU Azure Stack Edge Pro. Para os dispositivos GPU, Kubernetes é usado como uma plataforma de hospedagem para IoT Edge. O IoT Edge nos dispositivos FPGA utiliza uma plataforma baseada em estivadores. O modelo de aplicação baseado em docker ioT Edge é automaticamente traduzido para o modelo de aplicação nativa kubernetes. No entanto, ainda podem ser necessárias algumas alterações, uma vez que apenas um pequeno subconjunto do modelo de aplicação Kubernetes é suportado.

Se estiver a migrar as suas cargas de trabalho de um dispositivo FPGA para um dispositivo GPU, terá de escamar alterações nos módulos IoT Edge existentes para que os que funcionam com sucesso na plataforma Kubernetes. Poderá ter de especificar os requisitos de armazenamento, rede, utilização de recursos e procuração web de forma diferente. 

## <a name="storage"></a>Armazenamento

Considere as seguintes informações ao especificar o armazenamento dos módulos IoT Edge.

- O armazenamento para recipientes em Kubernetes é especificado com suportes de volume.
- A implantação em Kubernetes não pode ter ligações para associar o armazenamento persistente ou os caminhos de hospedeiro.
    - Para armazenamento persistente, utilize `Mounts` com o tipo `volume` .
    - Para os caminhos de anfitrião, utilize `Mounts` com o tipo `bind` .
- Para ioT Edge em Kubernetes, ligue-se através `Mounts` de obras apenas para diretório, e não para arquivo.

#### <a name="example---storage-via-volume-mounts"></a>Exemplo - Armazenamento através de suportes de volume 

Para ioT Edge no estivador, as ligações do caminho do anfitrião são usadas para mapear as ações do dispositivo para caminhos dentro do recipiente. Aqui estão as opções de criação do recipiente utilizadas em dispositivos FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Para os caminhos de anfitrião para IoT Edge em Kubernetes, é mostrado aqui um exemplo de utilização `Mounts` com `bind` tipo:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Para os dispositivos GPU que executam IoT Edge em Kubernetes, os suportes de volume são utilizados para especificar o armazenamento. Para o armazenamento de fornecimento usando ações, o valor seria `Mounts.Source` o nome da ação SMB ou NFS que foi a provisionada no seu dispositivo GPU. Este `/home/input` é o caminho em que o volume é acessível dentro do recipiente. Aqui estão as opções de criação do recipiente utilizadas nos dispositivos GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Rede

Considere as seguintes informações ao especificar a rede para os módulos IoT Edge. 

- `HostPort` especificação é necessária para expor um serviço dentro e fora do cluster.
    - Opções K8sExperimental para limitar a exposição do serviço apenas ao cluster.
- A comunicação inter módulo requer `HostPort` especificação e ligação utilizando a porta mapeada (e não utilizando a porta exposta ao recipiente).
- O acolhimento funciona em rede `dnsPolicy = ClusterFirstWithHostNet` com, com isso, todos os contentores (especialmente `edgeHub` ) não têm de estar na rede de acolhimento também. <!--Need further clarifications on this one-->
- Adicionar mapeamentos portuários para TCP, UDP no mesmo pedido não funciona.

#### <a name="example---external-access-to-modules"></a>Exemplo - Acesso externo a módulos 

Para quaisquer módulos IoT Edge que especifiquem as ligações da porta, é atribuído um endereço IP utilizando a gama IP de serviço externo Kubernetes que foi especificada na UI local do dispositivo. Não existem alterações no contentor que crie opções entre IoT Edge no docker vs IoT Edge em Kubernetes, como mostra o exemplo seguinte.  

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

No entanto, para consultar o endereço IP atribuído ao seu módulo, pode utilizar o dashboard Kubernetes como descrito no [endereço Get IP para serviços ou módulos](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Em alternativa, pode [ligar-se à interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e utilizar o comando da `iotedge` lista para listar todos os módulos em execução no seu dispositivo. A [saída do Comando](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) também indicará os IPs externos associados ao módulo.


## <a name="resource-usage"></a>Utilização de recursos 

Com as configurações IoT Edge baseadas em Kubernetes em dispositivos GPU, os recursos tais como aceleração de hardware, memória e cpu são especificados de forma diferente dos dispositivos FPGA. 

#### <a name="compute-acceleration-usage"></a>Utilização da aceleração do cálculo

Para implantar módulos na FPGA, utilize o recipiente criar opções <!--with Device Bindings--> como mostrado no config seguinte: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Para a GPU, utilize especificações de pedido de recursos em vez de Ligações ao Dispositivo, como indicado na configuração mínima seguinte. Pede recursos da Nvidia em vez de catapultar, e não precisa de especificar o `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Utilização da memória e do CPU
 
Para definir a memória e a utilização do CPU, utilize limites de processador para módulos na `k8s-experimental` secção. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
A memória e a especificação do CPU não são necessárias, mas geralmente são boas práticas. Se `requests` não for especificado, os valores definidos nos limites são utilizados como o mínimo exigido. 

A utilização da memória partilhada para módulos também requer uma forma diferente. Por exemplo, pode utilizar o modo Anfitrião IPC para acesso à memória partilhada entre soluções live video analytics e inference, conforme descrito no [Deploy Live Video Analytics no Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Proxy Web 

Considere as seguintes informações ao configurar o proxy web:

Se tiver um representante web configurado na sua rede, configure as seguintes variáveis ambientais para a `edgeHub` implementação da sua configuração IoT Edge baseada em estivadores em dispositivos FPGA:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (a menos que o representante da web permita `Amqp` o tráfego)

Para as configurações IoT Edge baseadas em Kubernetes em dispositivos GPU, terá de configurar esta variável adicional durante a implementação:

- `no_proxy`: local local

- O proxy IoT Edge na plataforma Kubernetes utiliza as portas 35000 e 35001. Certifique-se de que o seu módulo não funciona nestas portas ou que pode causar conflitos portuários. 

## <a name="other-differences"></a>Outras diferenças

- **Estratégia de implementação**: Pode ser necessário alterar o comportamento de implementação para quaisquer atualizações ao módulo. O comportamento padrão dos módulos IoT Edge está a ser atualizado. Este comportamento impede que o módulo atualizado reinicie se o módulo estiver a utilizar recursos como aceleração de hardware ou portas de rede. Este comportamento pode ter efeitos inesperados, especialmente quando se lida com volumes persistentes na plataforma Kubernetes para os dispositivos GPU. Para anular este comportamento predefinido, pode especificar uma `Recreate` na secção do seu `k8s-experimental` módulo.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Nomes dos módulos**: Os nomes dos módulos devem seguir as convenções de nomeação de Kubernetes. Poderá ter de mudar o nome dos módulos em execução no IoT Edge com o Docker quando deslocar esses módulos para IoT Edge com Kubernetes. Para obter mais informações sobre o nome, consulte [as convenções de nomeação de Kubernetes.](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)
- **Outras opções:** 
    - Certos dockers criam opções que funcionaram em dispositivos FPGA não funcionarão no ambiente Kubernetes nos seus dispositivos GPU. Por exemplo: , como – Ponto de Entrada.<!--can we confirm what exactly is required here-->
    - Variáveis ambientais, tais como `:` a necessidade de serem substituídas por `__` .
    - **O estado de criação de recipientes** para uma cápsula Kubernetes leva ao estado **de backoff** de um módulo no recurso IoT Hub. Embora existam uma série de razões para a cápsula estar neste estado, uma razão comum é quando uma grande imagem de recipiente está sendo puxada sobre uma ligação de largura de banda de baixa rede. Quando a cápsula está neste estado, o estado do módulo aparece como **backoff** no IOT Hub, embora o módulo esteja apenas a começar.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como configurar a [GPU para utilizar um módulo.](./azure-stack-edge-gpu-configure-gpu-modules.md)