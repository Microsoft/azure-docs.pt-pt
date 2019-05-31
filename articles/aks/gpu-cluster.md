---
title: Utilizar GPUs no serviço Kubernetes do Azure (AKS)
description: Saiba como utilizar GPUs para computação de elevado desempenho ou cargas de trabalho de grande intensidade de gráficos no Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/16/2019
ms.author: zarhoads
ms.openlocfilehash: 00d15b960afd2c3b39908fb359cf9898b5257abc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239362"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Utilizar GPUs para cargas de trabalho intensivas de computação no Azure Kubernetes Service (AKS)

Unidades de processamento gráfico (GPUs), muitas vezes, são utilizadas para cargas de trabalho de computação intensiva, como gráficos e visualização de cargas de trabalho. AKS suporta a criação de conjuntos de nós ativadas para GPU para executar estas cargas de trabalho de computação intensiva no Kubernetes. Para obter mais informações sobre as VMs ativadas para GPU disponíveis, consulte [tamanhos de VM no Azure com otimização de GPU][gpu-skus]. Para nós do AKS, recomendamos um tamanho mínimo de *Standard_NC6*.

> [!NOTE]
> As VMs ativadas para GPU contêm hardware especializado que está sujeito a disponibilidade mais elevada de preços e região. Para obter mais informações, consulte a [preços] [ azure-pricing] ferramenta e [disponibilidade de região][azure-availability].

Atualmente, utilizar conjuntos de nós ativadas para GPU só está disponível para conjuntos de nós do Linux.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente com os nós que suportam GPUs. O cluster do AKS tem de executar Kubernetes 1.10 ou posterior. Se precisar de um cluster do AKS que cumpra estes requisitos, veja a primeira secção deste artigo para [criar um cluster do AKS](#create-an-aks-cluster).

Também precisa da versão 2.0.64 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Se precisar de um cluster do AKS que cumpra os requisitos mínimos (ativadas para GPU de nó e Kubernetes versão 1.10 ou posterior), conclua os seguintes passos. Se já tiver um cluster do AKS que cumpra estes requisitos, [avançar para a secção seguinte](#confirm-that-gpus-are-schedulable).

Primeiro, crie um grupo de recursos para o cluster com o [criar grupo az] [ az-group-create] comando. O exemplo seguinte cria um nome de grupo de recursos *myResourceGroup* no *eastus* região:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um cluster do AKS com o [criar az aks] [ az-aks-create] comando. O exemplo seguinte cria um cluster com um único nó de tamanho `Standard_NC6`:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Obter as credenciais para o seu cluster do AKS com o [az aks get-credentials] [ az-aks-get-credentials] comando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>Instalar controladores de nVidia

Antes de podem ser utilizadas GPUs em nós, tem de implementar um DaemonSet para o plug-in de dispositivo do NVIDIA. Este DaemonSet executa um pod em cada nó para fornecer os drivers necessários para as GPUs.

Primeiro, crie um espaço de nomes utilizando o [kubectl criar espaço de nomes] [ kubectl-create] comando, tal como *recursos de gpu*:

```console
kubectl create namespace gpu-resources
```

Crie um ficheiro denominado *nvidia-dispositivo-Plug-in-ds.yaml* e cole o manifesto YAML seguinte. Esse manifesto é fornecido como parte da [NVIDIA o dispositivo Plug-in para o projeto de Kubernetes][nvidia-github].

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: kube-system
spec:
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.0.0-beta
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Agora utilizar o [aplicam-se de kubectl] [ kubectl-apply] comando para criar o DaemonSet e confirmar o plug-in de dispositivo do nVidia é criado com êxito, conforme mostrado no seguinte exemplo:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Confirme que as GPUs são pode ser agendadas

Com o seu cluster do AKS criado, certifique-se que as GPUs são pode ser agendadas no Kubernetes. Em primeiro lugar, liste os nós no cluster com o [kubectl obter nós] [ kubectl-get] comando:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Agora utilizar o [kubectl descrevem o nó] [ kubectl-describe] comando para confirmar que as GPUs são pode ser agendadas. Sob o *capacidade* secção, deverá apresentar a GPU como `nvidia.com/gpu:  1`.

O exemplo condensado seguinte mostra que uma GPU está disponível no nó com o nome *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Executar uma carga de trabalho com GPU ativada

Para ver a GPU em ação, agende uma carga de trabalho com GPU ativada com a solicitação de recurso adequado. Neste exemplo, vamos executar uma [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) da tarefa em relação a [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/).

Crie um ficheiro denominado *exemplos-tf-mnist-demo.yaml* e cole o manifesto YAML seguinte. O manifesto de trabalho seguinte inclui um limite de recursos de `nvidia.com/gpu: 1`:

> [!NOTE]
> Se receber um erro de incompatibilidade de versão quando a chamada para drivers, tais como a versão do controlador CUDA é insuficiente para a versão de runtime CUDA, reveja o gráfico de compatibilidade de matriz do driver de nVidia- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Utilize o [aplicam-se de kubectl] [ kubectl-apply] comando para executar a tarefa. Este comando analisa o arquivo de manifesto e cria os objetos de Kubernetes definidos:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Ver o estado e o resultado da carga de trabalho com GPU ativada

Monitorizar o progresso da tarefa usando o [kubectl obter tarefas] [ kubectl-get] comando com o `--watch` argumento. Ele pode demorar alguns minutos a primeira solicitação, a imagem e processar o conjunto de dados. Quando o *conclusões* coluna mostra *1/1*, a tarefa foi concluída com êxito. Saída de `kubetctl --watch` comando com *Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Para ver a saída da carga de trabalho ativadas para GPU, primeiro de obter o nome de pod com o [kubectl obter pods] [ kubectl-get] comando:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Agora utilizar o [registos de kubectl] [ kubectl-logs] comando para ver os registos de pod. Os registos de pod de exemplo seguinte, confirme que o dispositivo GPU apropriado ser detetado, `Tesla K80`. Forneça o nome para o seu próprio pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os objetos de Kubernetes associados criados neste artigo, utilize o [kubectl eliminação da tarefa] [ kubectl delete] comando da seguinte forma:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Passos Seguintes

Para executar tarefas do Apache Spark, consulte [tarefas de executar o Apache Spark no AKS][aks-spark].

Para obter mais informações sobre a execução de cargas de trabalho do machine learning (ML) no Kubernetes, consulte [Kubeflow laboratórios][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
