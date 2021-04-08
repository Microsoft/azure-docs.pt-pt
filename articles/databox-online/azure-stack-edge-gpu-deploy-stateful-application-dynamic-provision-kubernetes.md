---
title: Use kubectl para implementar a aplicação stateful kubernetes através de uma partilha dinamicamente aprovisionada no dispositivo GPU Azure Stack Edge Pro| Microsoft Docs
description: Descreve como criar e gerir uma implementação de aplicações stateful Kubernetes através de uma partilha dinamicamente atada usando kubectl num dispositivo GPU Microsoft Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520698"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Use kubectl para executar uma aplicação stateful Kubernetes com StorageClass no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra-lhe como implementar uma aplicação stateful de instância única em Kubernetes usando uma StorageClass para armazenamento de provisões dinâmicas e uma implementação. A implementação utiliza `kubectl` comandos num cluster Kubernetes existente e implementa a aplicação MySQL. 

Este procedimento destina-se a quem tenha revisto o [armazenamento kubernetes no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md) e esteja familiarizado com os conceitos de armazenamento [kubernetes.](https://kubernetes.io/docs/concepts/storage/)


## <a name="prerequisites"></a>Pré-requisitos

Antes de poder implementar a aplicação imponente, preencha os seguintes pré-requisitos no seu dispositivo e o cliente que utilizará para aceder ao dispositivo:

### <a name="for-device"></a>Para o dispositivo

- Tem credenciais de entrada num dispositivo Azure Stack Edge Pro de 1 nó.
    - O dispositivo está ativado. Ver [Ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    - O dispositivo tem o papel computacional configurado através do portal Azure e tem um cluster Kubernetes. Consulte [o cálculo Configure](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Para o cliente aceder ao dispositivo

- Tem um sistema de clientes Windows que será utilizado para aceder ao dispositivo Azure Stack Edge Pro.
    - O cliente está a executar o Windows PowerShell 5.0 ou mais tarde. Para descarregar a versão mais recente do Windows PowerShell, aceda à [Instalação do Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Também pode ter qualquer outro cliente com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) Este artigo descreve o procedimento quando se utiliza um cliente Windows. 
    
    - Completou o procedimento descrito no [Access the Kubernetes cluster no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Tu:
      - Criei um `userns1` espaço de nome através do `New-HcsKubernetesNamespace` comando. 
      - Criei um utilizador `user1` através do `New-HcsKubernetesUser` comando. 
      - Concedeu o `user1` acesso `userns1` através do `Grant-HcsKubernetesNamespaceAccess` comando.       
      - Instalado `kubectl` no cliente e guardado o `kubeconfig` ficheiro com a configuração do utilizador para C: \\ Nome de utilizador dos utilizadores \\ &lt; &gt; \\ .kube. 
    
    - Certifique-se de que a `kubectl` versão do cliente não é distorcida mais do que uma versão da versão master kubernetes em execução no seu dispositivo Azure Stack Edge Pro. 
        - Utilize `kubectl version` para verificar a versão do kubectl em execução no cliente. Tome nota da versão completa.
        - Na UI local do seu dispositivo Azure Stack Edge Pro, vá ao **Overview** e note o número de software Kubernetes. 
        - Verifique estas duas versões para compatibilidade a partir do mapeamento fornecido na versão Kubernetes Suportada<!-- insert link-->. 


Está pronto para implementar uma aplicação imponente no seu dispositivo Azure Stack Edge Pro. 


## <a name="deploy-mysql"></a>Implementar o MySQL

Irá agora executar uma aplicação imponente criando uma Implantação de Kubernetes e ligando-a à StorageClass incorporada utilizando um PersistenteVolumeClaim (PVC). 

Todos os `kubectl` comandos que utiliza para criar e gerir implementações de aplicações estatais precisam de especificar o espaço de nome associado à configuração. Para especificar o espaço de nome num comando kubectl, utilize `kubectl <command> -n <your-namespace>` .

1. Obtenha uma lista das cápsulas que estão a correr no seu cluster Kubernetes no seu espaço de nome. Uma cápsula é um recipiente de aplicação, ou processo, que funciona no seu cluster Kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Aqui está um exemplo de uso do comando:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   A saída deve indicar que não são encontrados recursos (pods) porque não existem aplicações em execução no seu cluster.

1. Utilizará os seguintes ficheiros YAML. O `mysql-deployment.yml` ficheiro descreve uma implementação que executa o MySQL e faz referência ao PVC. O ficheiro define um suporte de volume para `/var/lib/mysql` , e depois cria um PVC que procura um volume de 20 GB. Um PV dinâmico é a provisionado e o PVC está ligado a este PV.

    Copie e guarde o seguinte `mysql-deployment.yml` ficheiro para uma pasta no cliente Windows que está a utilizar para aceder ao dispositivo Azure Stack Edge Pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim-sc
    ```
    
2. Copie e guarde como `mysql-pvc.yml` ficheiro para a mesma pasta onde guardou o `mysql-deployment.yml` . Para utilizar o dispositivo Builtin StorageClass que o dispositivo Azure Stack Edge Pro num disco de dados anexado, defina o `storageClassName` campo no objeto de PVC e `ase-node-local` aceda aModes `ReadWriteOnce` . 

    > [!NOTE] 
    > Certifique-se de que os ficheiros YAML têm um entalhe correto. Pode verificar com [o forto YAML](http://www.yamllint.com/) para validar e, em seguida, guardar.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Desdobre o `mysql-pvc.yaml` ficheiro.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Aqui está uma amostra da saída da implantação.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Note o nome do PVC criado - neste exemplo, `mysql-pv-claim-sc` . Vais usá-lo mais tarde.

4. Implemente o conteúdo do `mysql-deployment.yml` ficheiro.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Aqui está uma amostra da saída da implantação.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Mostrar informações sobre a implantação.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Listar as cápsulas criadas pela implantação.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Aqui está uma amostra de saída.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Inspecione o PersistenteVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Aqui está uma amostra de saída.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verifique se o MySQL está a funcionar

Para verificar se o pedido está em execução, escreva:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Quando solicitado, forneça a senha. A senha está no seu `mysql-deployment` ficheiro.

Aqui está uma amostra de saída.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## <a name="delete-a-deployment"></a>Eliminar uma implantação

Para eliminar a implantação, elimine os objetos implantados pelo nome. Estes objetos incluem implantação, serviço e PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Aqui está a saída da amostra de quando apaga a implantação e o serviço.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Aqui está a saída da amostra de quando apagar o PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Passos seguintes

Para entender como configurar a rede através do kubectl, consulte [implementar uma aplicação apátrida num dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)