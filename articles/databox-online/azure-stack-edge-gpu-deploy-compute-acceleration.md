---
title: Utilize GPU de aceleração computacional ou VPU em dispositivos Azure Stack Edge para implementações de Kubernetes| Microsoft Docs
description: Descreve como utilizar GPU ou VPU de aceleração computacional no gpu Azure Stack Edge Pro, Azure Stack Edge Pro R ou Azure Stack Edge Mini Ri para implantações de Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 3eb648af60a7be62d08f6b172347778d2358643c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440127"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Use a aceleração do cálculo no GPU Azure Stack Edge Pro para implantação de Kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como utilizar a aceleração do cálculo nos dispositivos Azure Stack Edge quando se utilizam implementações de Kubernetes. O artigo aplica-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R.


## <a name="about-compute-acceleration"></a>Sobre a aceleração do cálculo 

A Unidade Central de Processamento (CPU) é o seu cálculo de finalidade geral padrão para a maioria dos processos em execução num computador. Muitas vezes, um hardware de computador especializado é usado para executar algumas funções de forma mais eficiente do que executar as que estão no software em um CPU. Por exemplo, uma Unidade de Processamento de Gráficos (GPU) pode ser usada para acelerar o processamento de dados de pixels.  

A aceleração do cálculo é um termo usado especificamente para dispositivos Azure Stack Edge onde uma Unidade de Processamento Gráfico (GPU), uma Unidade de Processamento de Visão (VPU) ou um Field Programable Gate Array (FPGA) são usados para a aceleração do hardware. A maioria das cargas de trabalho implementadas no seu dispositivo Azure Stack Edge envolvem o tempo crítico, vários fluxos de câmaras e/ou altas taxas de fotogramas, que requerem uma aceleração específica do hardware.

O artigo discutirá a aceleração do cálculo apenas utilizando GPU ou VPU para os seguintes dispositivos:

- **Azure Stack Edge Pro GPU** - Estes dispositivos podem ter 1 ou 2 Nvidia T4 Tensor Core CORE GPU. Para mais informações, consulte [a NVIDIA T4.](https://www.nvidia.com/en-us/data-center/tesla-t4/)
- **Azure Stack Edge Pro R** - Estes dispositivos têm 1 Nvidia T4 Tensor Core GPU. Para mais informações, consulte [a NVIDIA T4.](https://www.nvidia.com/en-us/data-center/tesla-t4/)
- **Azure Stack Edge Mini R** - Estes dispositivos têm 1 Intel Movidius Myriad X VPU. Para mais informações, consulte [Intel Movidius Myriad X VPU.](https://www.movidius.com/MyriadX)


## <a name="use-gpu-for-kubernetes-deployment"></a>Use GPU para implantação de Kubernetes

O exemplo a seguir `yaml` pode ser usado para um GPU Azure Stack Edge Pro ou um dispositivo Azure Stack Edge Pro R com uma GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Utilize VPU para implantação de Kubernetes

O exemplo a seguir `yaml` pode ser usado para um dispositivo Azure Stack Edge Mini R que tem uma VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Passos seguintes

Saiba como [utilizar o kubectl para executar uma aplicação stateful Kubernetes com um PersistenteVolume no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
