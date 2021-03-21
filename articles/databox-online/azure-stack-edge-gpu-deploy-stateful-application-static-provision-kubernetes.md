---
title: Utilize kubectl para implementar a aplicação stateful da Kubernetes através de uma partilha estática no dispositivo Azure Stack Edge Pro| Microsoft Docs
description: Descreve como criar e gerir uma implementação de aplicações stateful Kubernetes através de uma partilha estática atada usando kubectl num dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610489"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Use kubectl para executar uma aplicação stateful Kubernetes com um PersistenteVolume no seu dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra-lhe como implementar uma aplicação stateful de instância única em Kubernetes usando um PersistentVolume (PV) e uma implementação. A implementação utiliza `kubectl` comandos num cluster Kubernetes existente e implementa a aplicação MySQL. 

Este procedimento destina-se a quem tenha revisto o [armazenamento kubernetes no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md) e esteja familiarizado com os conceitos de armazenamento [kubernetes.](https://kubernetes.io/docs/concepts/storage/) 

O Azure Stack Edge Pro também suporta a execução de recipientes Azure SQL Edge e estes podem ser implantados de forma semelhante à detalhada aqui para o MySQL. Para mais informações, consulte [Azure SQL Edge](../azure-sql-edge/overview.md).


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
        - Verifique estas duas versões para compatibilidade a partir do mapeamento fornecido na versão Kubernetes suportada.<!-- insert link--> 


Está pronto para implementar uma aplicação imponente no seu dispositivo Azure Stack Edge Pro. 

## <a name="provision-a-static-pv"></a>Provisionamento de um PV estático

Para provisões estáticas de um PV, é necessário criar uma parte no seu dispositivo. Siga estes passos para providenciar um PV contra a sua parte SMB. 

> [!NOTE]
> - O exemplo específico utilizado neste artigo de como fazer não funciona com ações da NFS. Em geral, as ações NFS podem ser acumuedadas no seu dispositivo Azure Stack Edge com aplicações não-base de dados.
> - Para implementar aplicações estatais que utilizem volumes de armazenamento para fornecer armazenamento persistente, recomendamos que utilize `StatefulSet` . Este exemplo utiliza `Deployment` apenas uma réplica e é adequado para desenvolvimento e teste. 

1. Escolha se deseja criar uma partilha Edge ou uma participação local edge. Siga as instruções em [Adicionar uma partilha](azure-stack-edge-manage-shares.md#add-a-share) para criar uma partilha. Certifique-se de selecionar a caixa de verificação para **utilizar a partilha com o cálculo Edge**.

    ![Edge partilha local para PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Em vez de criar uma nova parte, se decidir utilizar uma parte existente, terá de montar a parte.
    
        No portal Azure para o seu recurso Azure Stack Edge, vá a **Shares**. A partir da lista de ações existente, selecione e clique numa ação que pretende utilizar.

        ![Selecione a partilha local existente para PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Selecione **o Monte** e confirme a montagem quando solicitado.  

        ![Monte a quota local existente para PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Tome nota do nome da partilha. Quando esta partilha é criada, um objeto de volume persistente é automaticamente criado no cluster Kubernetes correspondente à partilha SMB que criou. 

## <a name="deploy-mysql"></a>Implementar o MySQL

Irá agora executar uma aplicação imponente criando uma Implantação de Kubernetes e ligando-a ao PV que criou no passo anterior utilizando um PersistentVolumeClaim (PVC). 

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

1. Utilizará os seguintes ficheiros YAML. O `mysql-deployment.yml` ficheiro descreve uma implementação que executa o MySQL e faz referência ao PVC. O ficheiro define um suporte de volume para `/var/lib/mysql` , e depois cria um PVC que procura um volume de 20 GB. 

    Esta alegação é satisfeita por qualquer PV existente que tenha sido estaticamente a provisionado quando criou a participação no passo anterior. No seu dispositivo, é criado um grande PV de 32 TB para cada ação. O PV cumpre os requisitos estabelecidos pelo PVC e o PVC deve estar ligado a este PV.

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
              claimName: mysql-pv-claim
    ```
    
2. Copie e guarde como `mysql-pv.yml` ficheiro para a mesma pasta onde guardou o `mysql-deployment.yml` . Para utilizar a partilha SMB que criou `kubectl` anteriormente, defina o `volumeName` campo no objeto de PVC para o nome da partilha. 

    > [!NOTE] 
    > Certifique-se de que os ficheiros YAML têm um entalhe correto. Pode verificar com [o forto YAML](http://www.yamllint.com/) para validar e, em seguida, guardar.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Desdobre o `mysql-pv.yaml` ficheiro.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Aqui está uma amostra da saída da implantação.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Note o nome do PVC criado. Vais usá-lo mais tarde. 

4. Implemente o conteúdo do `mysql-deployment.yml` ficheiro.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Aqui está uma amostra da saída da implantação.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Mostrar informações sobre a implantação.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
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
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Listar as cápsulas criadas pela implantação.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Aqui está uma amostra de saída.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Inspecione o PersistenteVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Aqui está uma amostra de saída.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verifique se o MySQL está a funcionar


Para executar um comando contra um contentor numa cápsula que está a executar o MySQL, escreva:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Aqui está uma amostra de saída.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
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
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

O PV já não está ligado ao PVC, uma vez que o PVC foi eliminado. Como o PV foi a provisionado quando a ação foi criada, terá de apagar a parte. Siga estes passos:

1. Desmontar a parte. No portal Azure, aceda ao seu **recurso Azure Stack Edge > Shares** e selecione e clique na partilha que pretende desmontar. Selecione **Desmonte** e confirme a operação. Espera até que a parte seja desmontada. A desmontagem liberta a parte (e, portanto, a persistentevolume associada) do cluster Kubernetes. 

    ![Desmonte a quota local para pv](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Agora pode selecionar **Eliminar** e confirmar a eliminação para eliminar a sua parte. Isto também deve eliminar a partilha e o PV correspondente.

    ![Eliminar ações locais para PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Passos seguintes

Para entender como obter armazenamento de fornecimento dinâmico, consulte [Implementar uma aplicação imponente através de um provisionamento dinâmico num dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)
