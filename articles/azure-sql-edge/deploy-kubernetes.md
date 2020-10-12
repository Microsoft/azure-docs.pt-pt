---
title: Implementar um recipiente Azure SQL Edge em Kubernetes - Azure SQL Edge
description: Saiba mais sobre a implantação de um recipiente Azure SQL Edge em Kubernetes
keywords: SQL Edge, recipiente, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 16ad757fc00439bb390a7e0dea902901c468dd1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90946614"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Implementar um recipiente Azure SQL Edge em Kubernetes

O Azure SQL Edge pode ser implantado num cluster Kubernetes, tanto como um módulo IoT Edge através do Azure IoT Edge que funciona em Kubernetes ou como uma cápsula de recipiente autónoma. Para o resto deste artigo, vamos focar-nos na implantação autónoma de contentores num cluster de kubernetes. Para obter informações sobre a implementação do Azure IoT Edge em Kubernetes, consulte [Azure IoT Edge em Kubernetes (pré-visualização)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

Este tutorial demonstra como configurar uma instância Azure SQL Edge altamente disponível num recipiente num cluster de kubernetes. 

> [!div class="checklist"]
> * Criar uma senha SA
> * Criar armazenamento
> * Criar a implantação
> * Conecte-se com o SQL Server Management Studio (SSMS)
> * Verificar falha e recuperação

Kubernetes 1.6 e mais tarde tem suporte para [aulas de armazenamento,](https://kubernetes.io/docs/concepts/storage/storage-classes/) [reivindicações persistentes de volume](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims), e o [tipo de volume de disco Azure](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Pode criar e gerir os seus casos Azure SQL Edge de forma nativa em Kubernetes. O exemplo neste artigo mostra como criar uma [implementação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) para obter uma configuração de alta disponibilidade semelhante a uma instância de cluster de falha de disco partilhada. Nesta configuração, Kubernetes desempenha o papel de orquestrador de cluster. Quando uma instância Azure SQL Edge num recipiente falha, o orquestrador arranca outra instância do recipiente que se liga ao mesmo armazenamento persistente.

![Azure SQL Edge em um cluster Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

No diagrama anterior, `azure-sql-edge` encontra-se um recipiente numa [vagem.](https://kubernetes.io/docs/concepts/workloads/pods/pod/) Kubernetes orquestra os recursos no cluster. Um [conjunto de réplica](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garante que a cápsula é automaticamente recuperada após uma falha no nó. As aplicações ligam-se ao serviço. Neste caso, o serviço representa um equilibrador de carga que acolhe um endereço IP que permanece o mesmo após a falha do `azure-sql-edge` .

No diagrama seguinte, o `azure-sql-edge` recipiente falhou. Como orquestrador, Kubernetes garante a contagem correta de instâncias saudáveis no conjunto de réplicas, e inicia um novo recipiente de acordo com a configuração. O orquestrador inicia uma nova cápsula no mesmo nó, e `azure-sql-edge` reconecta-se com o mesmo armazenamento persistente. O serviço liga-se à `azure-sql-edge` recriação.

![Azure SQL Edge em um cluster Kubernetes após falha do pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

No diagrama seguinte, o nó que alberga o `azure-sql-edge` recipiente falhou. O orquestrador inicia a nova cápsula num nó diferente, e `azure-sql-edge` reconecta-se com o mesmo armazenamento persistente. O serviço liga-se à `azure-sql-edge` recriação.

![Azure SQL Edge em um cluster Kubernetes após falha no nó](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Pré-requisitos

* **Aglomerado de Kubernetes**
   - O tutorial requer um aglomerado de Kubernetes. Os passos usam [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) para gerir o cluster. 

   - Para efeitos deste tutorial, vamos usar o Serviço Azure Kubernetes para implantar o Azure SQL Edge. Consulte [implementar um cluster Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-deploy-cluster) para criar e ligar a um cluster kubernetes de nó único em AKS com `kubectl` . 

   >[!NOTE]
   >Para proteger contra a falha do nó, um cluster Kubernetes requer mais do que um nó.

* **CLI do Azure**
   - As instruções deste tutorial foram validadas contra o Azure CLI 2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Crie um espaço de nome de kubernetes para implantação de SQL Edge

Crie um novo espaço de nome no cluster kubernetes. Este espaço de nome será utilizado para implantar SQL Edge e todos os artefactos necessários. Para obter mais informações sobre os espaços de nomes de Kubernetes, consulte [os espaços de nome .](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Criar uma senha SA

Crie uma palavra-passe SA no cluster Kubernetes. Kubernetes pode gerir informações de configuração sensíveis, como palavras-passe como [segredos](https://kubernetes.io/docs/concepts/configuration/secret/).

O seguinte comando cria uma senha para a conta SA:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Substitua `MyC0m9l&xP@ssw0rd` por uma senha complexa.

## <a name="create-storage"></a>Criar armazenamento

Configure um [volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) e [uma reivindicação persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) de volume no cluster Kubernetes. Siga os seguintes passos: 

1. Crie um manifesto para definir a classe de armazenamento e a reivindicação persistente do volume.  O manifesto especifica o provisionador de armazenamento, parâmetros e [política de recuperação](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). O cluster Kubernetes usa este manifesto para criar o armazenamento persistente. 

   O exemplo yaml seguinte define uma classe de armazenamento e reivindicação persistente de volume. O provisionador de classe de armazenamento é `azure-disk` , porque este cluster Kubernetes está em Azure. O tipo de conta de armazenamento é `Standard_LRS` . A reivindicação persistente do volume é denominada `mssql-data` . Os metadados persistentes de reclamação de volume incluem uma anotação que o liga à classe de armazenamento. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Guarde o ficheiro (por exemplo, **pvc.yaml).**

2. Crie a reivindicação persistente do volume em Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` é o local onde guardou o ficheiro.

   O volume persistente é automaticamente criado como uma conta de armazenamento Azure, e ligado à reivindicação persistente do volume. 

    ![Screenshot do comando de reivindicação de volume persistente](media/deploy-kubernetes/pvc-cmd.png)

3. Verifique a reclamação persistente do volume.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` é o nome da reivindicação persistente do volume.

   Na etapa anterior, a reivindicação persistente do volume é nomeada `mssql-data` . Para ver os metadados sobre a reivindicação de volume persistente, executar o seguinte comando:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Os metadados devolvidos incluem um valor chamado `Volume` . Este valor mapeia o nome da bolha.

   ![Screenshot dos metadados devolvidos, incluindo volume](media/deploy-kubernetes/describe-volume.png)

4. Verifique o volume persistente.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` devolve metadados sobre o volume persistente que foi automaticamente criado e ligado à reivindicação persistente do volume. 

## <a name="create-the-deployment"></a>Criar a implantação

Neste exemplo, o recipiente que alberga a instância Azure SQL Edge é descrito como um objeto de implantação de Kubernetes. A implementação cria um conjunto de réplicas. O conjunto de réplicas cria a cápsula. 

Neste passo, crie um manifesto para descrever o recipiente com base na imagem do Azure SQL Edge Docker. O manifesto refere a reivindicação persistente do `mssql-data` volume, e o `mssql` segredo que já aplicaste ao cluster Kubernetes. O manifesto também descreve um [serviço.](https://kubernetes.io/docs/concepts/services-networking/service/) Este serviço é um equilibrador de carga. O equilibrador de carga garante que o endereço IP persiste após a recuperação do exemplo do Azure SQL Edge. 

1. Crie um manifesto (um ficheiro YAML) para descrever a implantação. O exemplo a seguir descreve uma implantação, incluindo um recipiente baseado na imagem do recipiente Azure SQL Edge.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Copie o código anterior num novo ficheiro, denominado `sqldeployment.yaml` . Atualizar os seguintes valores: 

   * MSSQL_PID : `value: "Developer"` Define o recipiente para executar a edição do Azure SQL Edge Developer. A edição do programador não está licenciada para dados de produção. Se a implantação for para uso de produção, desa um conjunto da edição para `Premium` . 

      >[!NOTE]
      >Para mais informações, consulte [Como licenciar a Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Este valor requer uma entrada para `claimName:` esse mapa ao nome utilizado para a reivindicação persistente do volume. Este tutorial utiliza `mssql-data`. 

   * `name: SA_PASSWORD`: Configura a imagem do recipiente para definir a palavra-passe SA, tal como definida nesta secção.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Quando Kubernetes implanta o recipiente, refere-se ao segredo nomeado `mssql` para obter o valor da senha. 

   >[!NOTE]
   >Ao utilizar o `LoadBalancer` tipo de serviço, a instância Azure SQL Edge é acessível remotamente (através da internet) na porta 1433.

   Guarde o ficheiro (por exemplo, **sqledgedeploy.yaml).**

2. Criar a implantação.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` é o local onde guardou o ficheiro.

   ![Screenshot do comando de implantação](media/deploy-kubernetes/deploy-sql-cmd.png)

   A implantação e o serviço são criados. A instância Azure SQL Edge encontra-se num recipiente, ligada ao armazenamento persistente.

   Para visualizar o estado da vagem, escreva `kubectl get pod -n <namespace name>` .

   ![Screenshot do comando do casulo get](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Na imagem anterior, a cápsula tem um estatuto de `Running` . Este estado indica que o recipiente está pronto. Este processo pode demorar vários minutos.

   >[!NOTE]
   >Após a implantação ser criada, pode demorar alguns minutos até que a cápsula seja visível. O atraso deve-se ao facto de o cluster retirar a imagem do contentor Azure SQL Edge do centro do Docker. Depois de a imagem ser puxada pela primeira vez, as implementações subsequentes podem ser mais rápidas se a implementação for para um nó que já tem a imagem em cache nela. 

3. Verifique se os serviços estão a funcionar. Execute o seguinte comando:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Este comando devolve serviços que estão em execução, bem como os endereços IP internos e externos para os serviços. Note o endereço IP externo para o `mssql-deployment` serviço. Utilize este endereço IP para ligar ao Azure SQL Edge. 

   ![Screenshot do comando de serviço get](media/deploy-kubernetes/get-service-cmd.png)

   Para obter mais informações sobre o estado dos objetos no cluster Kubernetes, corra:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Ligue-se à instância Azure SQL Edge

Se configurar o recipiente como descrito, pode ligar-se a uma aplicação de fora da rede virtual Azure. Utilize a `sa` conta e o endereço IP externo para o serviço. Use a palavra-passe que configura como o segredo de Kubernetes. Para obter mais informações sobre a ligação a uma instância Azure SQL Edge, consulte [Connect to Azure SQL Edge](connect.md).

## <a name="verify-failure-and-recovery"></a>Verificar falha e recuperação

Para verificar falha e recuperação, pode eliminar a cápsula. Faça os seguintes passos:

1. Listar o casulo que funciona Azure SQL Edge.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Note o nome da cápsula que funciona Azure SQL Edge.

2. Apague a cápsula.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` é o valor devolvido do passo anterior para o nome do pod. 

Kubernetes recria automaticamente o casulo para recuperar uma instância Azure SQL Edge e ligar-se ao armazenamento persistente. Utilize `kubectl get pods` para verificar se uma nova cápsula está implantada. Utilize `kubectl get services` para verificar se o endereço IP do novo recipiente é o mesmo. 

## <a name="summary"></a>Resumo

Neste tutorial, aprendeu a implantar recipientes Azure SQL Edge num cluster Kubernetes para uma elevada disponibilidade. 

> [!div class="checklist"]
> * Criar uma senha SA
> * Criar armazenamento
> * Criar a implantação
> * Conecte-se com a Azure SQL Edge Management Studios (SSMS)
> * Verificar falha e recuperação

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados em Azure SQL Edge](stream-data.md)

