---
title: Use GPUs no serviço Azure Kubernetes (AKS)
description: Saiba como utilizar GPUs para cargas de trabalho de alto desempenho ou de alta performance intensiva em Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929145"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Utilize GPUs para cargas de trabalho computacionalmente intensivas no Serviço Azure Kubernetes (AKS)

As unidades de processamento gráfico (GPUs) são frequentemente utilizadas para cargas de trabalho intensivas de computação, tais como gráficos e cargas de trabalho de visualização. A AKS apoia a criação de piscinas de nóinerdo ativadas pela GPU para executar estas cargas de trabalho intensivas em Kubernetes. Para obter mais informações sobre VMs disponíveis, consulte [os tamanhos de VM otimizados da GPU em Azure][gpu-skus]. Para os nós AKS, recomendamos um tamanho mínimo de *Standard_NC6*.

> [!NOTE]
> Os VMs habilitados pela GPU contêm hardware especializado que está sujeito a preços mais elevados e disponibilidade de região. Para mais informações, consulte a ferramenta [de preços][azure-pricing] e [a disponibilidade da região.][azure-availability]

Atualmente, a utilização de piscinas de nó ativadas pela GPU só está disponível para piscinas de nól de linux.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente com nós que suportam GPUs. O seu cluster AKS deve executar Kubernetes 1.10 ou mais tarde. Se precisar de um cluster AKS que satisfaça estes requisitos, consulte a primeira secção deste artigo para [criar um cluster AKS](#create-an-aks-cluster).

Também precisa da versão Azure CLI 2.0.64 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Se precisar de um cluster AKS que satisfaça os requisitos mínimos (nó ativado por GPU e versão 1.10 ou mais tarde), complete os seguintes passos. Se já tem um cluster AKS que satisfaça estes requisitos, [salte para a secção seguinte](#confirm-that-gpus-are-schedulable).

Em primeiro lugar, crie um grupo de recursos para o cluster utilizando o [grupo az criar][az-group-create] comando. O exemplo a seguir cria um nome de grupo de recursos *myResourceGroup* na região *leste:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora crie um cluster AKS usando os [az aks criar][az-aks-create] comando. O exemplo a seguir cria um cluster com um único nó de `Standard_NC6` tamanho:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Obtenha as credenciais para o seu cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Instalar plugin de dispositivo NVIDIA

Antes de as GPUs nos nós poderem ser utilizadas, tem de implantar um DaemonSet para o plugin do dispositivo NVIDIA. Este DaemonSet executa uma cápsula em cada nó para fornecer os controladores necessários para as GPUs.

Em primeiro lugar, crie um espaço de nome utilizando o [comando de espaço de nome de criação de nome,][kubectl-create] como os recursos *gpu:*

```console
kubectl create namespace gpu-resources
```

Crie um ficheiro chamado *nvidia-device-plugin-ds.yaml* e cole o seguinte manifesto YAML. Este manifesto é fornecido como parte do plugin de [dispositivo NVIDIA para o projeto Kubernetes.][nvidia-github]

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
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
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
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

Agora use o comando [de aplicação kubectl][kubectl-apply] para criar o DaemonSet e confirme que o plugin do dispositivo NVIDIA foi criado com sucesso, como mostra a saída do exemplo seguinte:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Utilize a imagem gpu especializada da AKS (pré-visualização)

Como alternativa a estes passos, a AKS está a fornecer uma imagem AKS totalmente configurada que já contém o plugin de [dispositivo NVIDIA para Kubernetes.][nvidia-github]

> [!WARNING]
> Não deve instalar manualmente o conjunto de plugin daemon do dispositivo NVIDIA para clusters utilizando a nova imagem de GPU especializada em AKS.


Registar a `GPUDedicatedVHDPreview` função:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
```

Para atualizar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Utilize a imagem de GPU especializada da AKS em novos clusters (pré-visualização)    

Configure o cluster para usar a imagem de GPU especializada da AKS quando o cluster é criado. Use a `--aks-custom-headers` bandeira para os nós de agente da GPU no seu novo cluster para usar a imagem de GPU especializada da AKS.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Se pretender criar um cluster utilizando as imagens AKS regulares, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. Você também pode optar por adicionar piscinas de nó de gPU mais especializadas, conforme abaixo.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Utilize a imagem de GPU especializada da AKS em clusters existentes (pré-visualização)

Configure uma nova piscina de nós para usar a imagem de GPU especializada da AKS. Use a `--aks-custom-headers` bandeira de bandeira para os nós de agente da GPU na sua nova piscina de nós para usar a imagem de GPU especializada da AKS.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Se quiser criar uma piscina de nó usando as imagens AKS regulares, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. 

> [!NOTE]
> Se o seu sku gpu necessitar de máquinas virtuais de geração 2, pode criar fazer:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Confirme que as GPUs são agendaveis

Com o seu cluster AKS criado, confirme que as GPUs são agendaveis em Kubernetes. Em primeiro lugar, enumere os nó de nó no seu cluster utilizando o comando [kubectl get nodes:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Agora use o [comando do nó de descreveção kubectl][kubectl-describe] para confirmar que as GPUs são agendaveis. Sob a secção *capacidade,* a GPU deve listar como `nvidia.com/gpu:  1` .

O exemplo condensado que se segue mostra que uma GPU está disponível no nó denominado *aks-nodepool1-18821093-0*:

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

## <a name="run-a-gpu-enabled-workload"></a>Executar uma carga de trabalho ativada pela GPU

Para ver a GPU em ação, agende uma carga de trabalho ativada pela GPU com o pedido de recursos apropriado. Neste exemplo, vamos executar um trabalho [de tensorflow](https://www.tensorflow.org/) contra o [conjunto de dados do MNIST](http://yann.lecun.com/exdb/mnist/).

Crie um ficheiro chamado *samples-tf-mnist-demo.yaml* e cole o seguinte manifesto YAML. O seguinte manifesto de trabalho inclui um limite de recursos `nvidia.com/gpu: 1` de:

> [!NOTE]
> Se receber um erro de incompatibilidade da versão ao ligar para os condutores, como, por exemplo, a versão do condutor CUDA é insuficiente para a versão de tempo de execução cuda, reveja o gráfico de compatibilidade da matriz do condutor NVIDIA - [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Utilize o [comando de aplicação de kubectl][kubectl-apply] para executar o trabalho. Este comando analisa o ficheiro manifesto e cria os objetos Kubernetes definidos:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Ver o estado e a saída da carga de trabalho ativada pela GPU

Monitorize o progresso do trabalho usando o [kubectl obter][kubectl-get] o comando de emprego com o `--watch` argumento. Pode levar alguns minutos para primeiro puxar a imagem e processar o conjunto de dados. Quando a coluna *COMPLETIONS* mostrar *1/1,* o trabalho terminou com sucesso. Saia do `kubetctl --watch` comando com *Ctrl-C:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Para ver a saída da carga de trabalho ativada pela GPU, primeiro obtenha o nome da cápsula com o comando [kubectl get pods:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Agora use o comando [de registos kubectl][kubectl-logs] para visualizar os registos do pod. Os registos de cápsulas de exemplo a seguir confirmam que o dispositivo GPU adequado foi descoberto, `Tesla K80` . Forneça o nome para a sua própria cápsula:

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

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover os objetos Kubernetes associados criados neste artigo, utilize o comando [de trabalho de eliminação de kubectl][kubectl delete] da seguinte forma:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Passos seguintes

Para gerir os empregos da Apache Spark, consulte [os trabalhos da Run Apache Spark na AKS.][aks-spark]

Para obter mais informações sobre a execução de cargas de trabalho de machine learning (ML) em Kubernetes, consulte [a Kubeflow Labs][kubeflow-labs].

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
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
