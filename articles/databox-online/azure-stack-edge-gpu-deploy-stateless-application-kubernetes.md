---
title: Implemente a aplicação apátrida da Kubernetes no dispositivo GPU Azure Stack Edge Pro usando kubectl| Microsoft Docs
description: Descreve como criar e gerir uma aplicação apátrida kubernetes usando kubectl num dispositivo Microsoft Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638661"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Implemente uma aplicação apátrida Kubernetes através de kubectl no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como implementar uma aplicação apátrida usando comandos kubectl num cluster Kubernetes existente. Este artigo também o acompanha através do processo de criação e configuração de cápsulas na sua aplicação apátrida.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um cluster Kubernetes e utilizar a `kubectl` ferramenta de linha de comando, tem de se certificar de que:

- Tem credenciais de entrada num dispositivo Azure Stack Edge Pro de 1 nó.

- O Windows PowerShell 5.0 ou mais tarde é instalado num sistema de clientes Windows para aceder ao dispositivo Azure Stack Edge Pro. Também pode ter qualquer outro cliente com um sistema operativo suportado. Este artigo descreve o procedimento quando se utiliza um cliente Windows. Para descarregar a versão mais recente do Windows PowerShell, aceda à [instalação do Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).

- O Compute está ativado no dispositivo Azure Stack Edge Pro. Para ativar o cálculo, aceda à página **compute** na UI local do dispositivo. Em seguida, selecione uma interface de rede que deseja ativar para calcular. Selecione **Ativar**. Permitir a computação resulta na criação de um interruptor virtual no seu dispositivo nessa interface de rede. Para obter mais informações, consulte [Enable compute network no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- O seu dispositivo Azure Stack Edge Pro tem um servidor de cluster Kubernetes em execução que é a versão v1.9 ou mais tarde. Para obter mais informações, consulte [Criar e gerir um cluster Kubernetes no dispositivo Microsoft Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- `kubectl`Instalou.

## <a name="deploy-a-stateless-application"></a>Implementar uma aplicação apátrida

Antes de começarmos, deve ter:

1. Criei um aglomerado de Kubernetes.
2. Crie um espaço para nomes.
3. Associou um utilizador ao espaço de nomes.
4. Guarde a configuração do utilizador para `C:\Users\<username>\.kube` .
5. Instalado `kubectl` .

Agora pode começar a executar e gerir implementações de aplicações apátridas num dispositivo Azure Stack Edge Pro. Antes de começar `kubectl` a utilizar, tem de verificar se tem a versão correta de `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Verifique se tem a versão correta de kubectl e configurar a configuração

Para verificar a versão `kubectl` de:

1. Verifique se a versão `kubectl` é maior ou igual a 1.9:

   ```powershell
   kubectl version
   ```
    
   Um exemplo da saída é mostrado abaixo:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   Neste caso, a versão cliente do kubectl é v1.15.2 e é compatível para continuar.

2. Obtenha uma lista das cápsulas em execução no seu cluster Kubernetes. Uma cápsula é um recipiente de aplicação, ou processo, que funciona no seu cluster Kubernetes.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Um exemplo de utilização do comando é mostrado abaixo:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   A saída deve indicar que não são encontrados recursos (pods) porque não existem aplicações em execução no seu cluster.

   O comando preencherá a estrutura do diretório de "C:\Users \\ &lt; username &gt; \\ .kube \" with configuration files. A ferramenta de linha de comando kubectl utilizará estes ficheiros para criar e gerir aplicações apátridas no seu cluster Kubernetes.  

3. Verifique manualmente a estrutura do diretório de "C:\Users \\ &lt; username &gt; \\ .kube \" para verificar se *kubectl* o povoou com as seguintes subclassificas:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Para visualizar uma lista de todos os comandos kubectl, escreva `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Criar uma aplicação apátrida usando uma implantação

Agora que verificou que a versão da linha de comando kubectl está correta e que tem os ficheiros de configuração necessários, pode criar uma implementação de aplicação apátrida.

Uma cápsula é a unidade de execução básica de uma aplicação Kubernetes, a unidade mais pequena e simples no modelo de objeto Kubernetes que cria ou implementa. Uma cápsula também encapsula recursos de armazenamento, um IP de rede único, e opções que regem como o(s) contentor(s) deve funcionar.

O tipo de aplicação apátrida que cria é uma implementação de servidor web nginx.

Todos os comandos kubectl que utiliza para criar e gerir implementações de aplicações apátridas precisam de especificar o espaço de nome associado à configuração. Criou o espaço de nomes enquanto estava ligado ao cluster no dispositivo Azure Stack Edge Pro no [Create e gere um cluster Kubernetes no tutorial do dispositivo Microsoft Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md) com `New-HcsKubernetesNamespace` .

Para especificar o espaço de nome num comando kubectl, utilize `kubectl <command> -n <namespace-string>` .

Siga estes passos para criar uma implementação nginx:

1. Aplicar uma aplicação apátrida criando um objeto de implantação De Kubernetes:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   Neste exemplo, o caminho para o ficheiro YAML da aplicação é uma fonte externa.

   Aqui está uma amostra de utilização do comando e sua saída:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Em alternativa, pode guardar a seguinte marcação para a sua máquina local e substituir o caminho e o nome de ficheiro no parâmetro *-f.* Por exemplo, "C:\Kubernetes\deployment.yaml". A configuração para a implementação da aplicação seria:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Este comando cria uma implementação nginx padrão que tem duas cápsulas para executar a sua aplicação.

2. Obtenha a descrição da implantação nginx de Kubernetes que criou:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Abaixo é mostrada uma utilização da amostra do comando, com saída:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Para a definição de *réplicas,* verá:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   A definição *de réplicas* indica que a especificação de implementação requer duas cápsulas e que essas cápsulas foram criadas e atualizadas e estão prontas para utilizar.

   > [!NOTE]
   > Um conjunto de réplicas substitui as cápsulas que são eliminadas ou terminadas por qualquer motivo, como no caso de falha no nó do dispositivo ou de uma atualização disruptiva do dispositivo. Por esta razão, recomendamos que utilize um conjunto de réplicas mesmo que a sua aplicação exija apenas uma única cápsula.

3. Para listar as cápsulas na sua implantação:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Abaixo é mostrada uma utilização da amostra do comando, com saída:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   A saída verifica que temos duas cápsulas com nomes únicos que podemos referenciar usando kubectl.

4. Para ver informações sobre uma cápsula individual na sua implementação:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Abaixo é mostrada uma utilização da amostra do comando, com saída:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Reescalifique a implementação da aplicação aumentando a contagem de réplicas

Cada cápsula deve executar uma única instância de uma determinada aplicação. Se pretender escalar a sua aplicação horizontalmente para executar várias instâncias, pode aumentar o número de cápsulas para uma para cada instância. Em Kubernetes, isto é referido como replicação.
Pode aumentar o número de cápsulas na sua implementação de aplicação aplicando um novo ficheiro YAML. O ficheiro YAML altera a definição de réplicas para 4, o que aumenta o número de cápsulas na sua implementação para quatro cápsulas. Para aumentar o número de cápsulas de 2 para 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Em alternativa, pode guardar a seguinte marcação na sua máquina local e substituir o caminho e o nome de ficheiro para o parâmetro *-f* para `kubectl apply` . Por exemplo, "C:\Kubernetes\deployment-scale.yaml". A configuração para a escala de implementação da aplicação seria:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Para verificar se a implantação tem quatro cápsulas:

```powershell
kubectl get pods -l app=nginx
```

Abaixo é mostrada a saída de exemplo para uma implantação de rescalamento de duas a quatro cápsulas:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Como pode ver pela saída, tem agora quatro cápsulas na sua implementação que podem executar a sua aplicação.

### <a name="delete-a-deployment"></a>Eliminar uma implementação

Para eliminar a implementação, incluindo todas as cápsulas, é necessário executar `kubectl delete deployment` especificando o nome da *implementação nginx-deployment* e o nome do espaço de nome. Para eliminar a implantação:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Um exemplo de utilização do comando, com saída, é mostrado abaixo:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Passos seguintes

[Visão geral de Kubernetes](azure-stack-edge-gpu-kubernetes-overview.md)