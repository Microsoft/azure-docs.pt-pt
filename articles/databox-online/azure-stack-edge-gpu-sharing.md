---
title: GPU partilhando no dispositivo GPU Azure Stack Edge Pro
description: Descreve as abordagens para a partilha de GPUs no dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: ff1c7b79a49b0b659056c89af3c61f28b72ebc50
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645241"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>GPU partilhando no seu dispositivo GPU Azure Stack Edge Pro

A unidade de processamento de gráficos (GPU) é um processador especializado projetado para acelerar a renderização de gráficos. As GPUs podem processar muitas peças de dados simultaneamente, tornando-as úteis para machine learning, edição de vídeo e aplicações de jogos. Além do CPU para fins gerais, os seus dispositivos GPU Azure Stack Edge Pro podem conter um ou dois GPUs Nvidia Tesla T4 para cargas de trabalho intensivos de computação, tais como inferencing acelerado de hardware. Para mais informações, consulte [o Tesla T4 GPU da Nvidia.](https://www.nvidia.com/en-us/data-center/tesla-t4/)


## <a name="about-gpu-sharing"></a>Sobre a partilha de GPU

Muitas aprendizagens automáticas ou outras cargas de trabalho computacional podem não precisar de uma GPU dedicada. As GPUs podem ser partilhadas e a partilha de GPUs entre cargas de trabalho contentorizadas ou VM ajuda a aumentar a utilização da GPU sem afetar significativamente os benefícios de desempenho da GPU.  

## <a name="using-gpu-with-vms"></a>Utilização de GPU com VMs

No seu dispositivo Azure Stack Edge Pro, uma GPU não pode ser partilhada ao implementar cargas de trabalho em VM. Uma GPU só pode ser mapeada para um VM. Isto implica que só pode ter um VM GPU num dispositivo com uma GPU e dois VMs num dispositivo que está equipado com duas GPUs. Existem outros fatores que também devem ser considerados quando se utilizam VMs GPU num dispositivo que tem Kubernetes configurados para cargas de trabalho contentorizadas. Para obter mais informações, consulte [VMs e Kubernetes da GPU.](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes)


## <a name="using-gpu-with-containers"></a>Utilização de GPU com contentores

Se estiver a implementar cargas de trabalho contentorizadas, uma GPU pode ser partilhada de várias formas na camada de hardware e software. Com o Tesla T4 GPU no seu dispositivo Azure Stack Edge Pro, limitamo-nos à partilha de software. No seu dispositivo, são utilizadas as seguintes duas abordagens para a partilha de software de GPU: 

- A primeira abordagem consiste na utilização de variáveis ambientais para especificar o número de GPUs que podem ser partilhados no tempo. Considere as seguintes ressalvas ao utilizar esta abordagem:

    - Pode especificar uma ou ambas as GPUs com este método. Não é possível especificar o uso fracional.
    - Vários módulos podem mapear para uma GPU, mas o mesmo módulo não pode ser mapeado para mais de um GPU.
    - Com a saída Nvidia SMI, você pode ver a utilização geral da GPU, incluindo a utilização da memória.
    
    Para obter mais informações, consulte como [implementar um módulo IoT Edge que utiliza GPU](azure-stack-edge-gpu-configure-gpu-modules.md) no seu dispositivo.

- A segunda abordagem requer que ative o Serviço Multi-Processo nas suas GPUs Nvidia. MPS é um serviço de tempo de execução que permite que vários processos usando CUDA para executar simultaneamente em uma única GPU compartilhada. O MPS permite a sobreposição de operações de kernel e memcopia de diferentes processos na GPU para obter a máxima utilização. Para mais informações, consulte [o Serviço Multi-Processo.](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)

    Considere as seguintes ressalvas ao utilizar esta abordagem:
    
    - O MPS permite especificar mais bandeiras na implantação da GPU.
    - Pode especificar a utilização fracionada através de MPS, limitando assim a utilização de cada aplicação implantada no dispositivo. Pode especificar a percentagem de GPU a utilizar para cada aplicação ao abrigo `env` da secção do `deployment.yaml` parâmetro, adicionando o seguinte parâmetro: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Utilização de GPU
 
Quando partilha a GPU em cargas de trabalho contentorizadas implantadas no seu dispositivo, pode utilizar a Interface de Gestão do Sistema Nvidia (nvidia-smi). Nvidia-smi é um utilitário de linha de comando que o ajuda a gerir e monitorizar dispositivos GPU da Nvidia. Para mais informações, consulte a [Nvidia System Management Interface.](https://developer.nvidia.com/nvidia-system-management-interface)

Para visualizar a utilização da GPU, ligue-se primeiro à interface PowerShell do dispositivo. Executar o `Get-HcsNvidiaSmi` comando e ver a saída Nvidia SMI. Também pode ver como a utilização da GPU muda, ativando o MPS e, em seguida, implantando várias cargas de trabalho no dispositivo. Para obter mais informações, consulte [Ativar o Serviço multi-processo.](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)


## <a name="next-steps"></a>Passos seguintes

- [Partilha de GPU para implantações de Kubernetes no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Partilha de GPU para implementações de IoT no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
