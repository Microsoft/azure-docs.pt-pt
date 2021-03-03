---
title: Azure Arc viabilizou a alta disponibilidade de Instância Gerida
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Saiba como implementar O Arco Azure ativado Em Instância Gerida com elevada disponibilidade.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e04b9b98db26b3b9a024a60c6f82820fe20fcbf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693227"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure Arc viabilizou a alta disponibilidade de Instância Gerida

A azure Arc enabled Managed Instance é implantado em Kubernetes como uma aplicação contentorizada e usa kubernetes construídos como conjuntos imponentes e armazenamento persistente para fornecer mecanismos de monitorização de saúde incorporados, deteção de falhas e mecanismos de falha para manter a saúde do serviço. Para uma maior fiabilidade, também pode configurar a Azure Arc habilitada a implementar com réplicas extra numa configuração de alta disponibilidade. A monitorização, deteção de falhas e falha automática são geridas pelo controlador de dados dos serviços de dados do Arco. Este serviço é prestado sem intervenção do utilizador – tudo desde a configuração do grupo de disponibilidade, configuração de pontos finais espelhados da base de dados, até à adição de bases de dados ao grupo de disponibilidade ou à coordenação de failover e upgrade. Este documento explora ambos os tipos de alta disponibilidade.

## <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada 

A alta disponibilidade incorporada é fornecida pela Kubernetes quando o armazenamento remoto persistente é configurado e partilhado com nós utilizados pela implementação do serviço de dados Arc. Nesta configuração, Kubernetes desempenha o papel de orquestrador de cluster. Quando a instância gerida num recipiente ou no nó subjacente falha, o orquestrador arranca outra instância do recipiente e fixa-se ao mesmo armazenamento persistente. Este tipo é ativado por padrão quando implementa Azure Arc ativado Exemplo Gerido.

### <a name="verify-built-in-high-availability"></a>Verifique a alta disponibilidade incorporada

Nesta secção, você verifica a alta disponibilidade incorporada fornecida pela Kubernetes. Quando segue os passos para testar esta funcionalidade, elimina o pod de uma instância gerida existente e verifica se a Kubernetes recupera desta ação. 

### <a name="prerequisites"></a>Pré-requisitos

- O cluster Kubernetes deve ter [partilhado armazenamento remoto](https://docs.microsoft.com/en-us/azure/azure-arc/data/storage-configuration#factors-to-consider-when-choosing-your-storage-configuration) 
- Um Arco Azure ativado Instância Gerida implantada com uma réplica (padrão)

1. Veja as cápsulas. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Elimine a cápsula de instância gerida.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Por exemplo

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Veja as cápsulas para verificar se a instância gerida está a recuperar.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Por exemplo

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Depois de recuperados todos os recipientes dentro da cápsula, pode ligar-se à instância gerida.

## <a name="deploy-with-always-on-availability-groups"></a>Implementar com sempre em grupos de disponibilidade

Para uma maior fiabilidade, pode configurar a Azure Arc habilitada a implementar com réplicas extra numa configuração de alta disponibilidade. 

Capacidades que os grupos de disponibilidade permitem:

- Quando implantado com várias réplicas, é criado um único grupo de disponibilidade `containedag` nomeado. Por padrão, `containedag` tem três réplicas, incluindo primárias. Todas as operações CRUD para o grupo de disponibilidade são geridas internamente, incluindo a criação do grupo de disponibilidade ou a junção de réplicas ao grupo de disponibilidade criado. Grupos de disponibilidade adicionais não podem ser criados no Arco Azure ativado Instância Gerida.

- Todas as bases de dados são automaticamente adicionadas ao grupo de disponibilidade, incluindo todas as bases de dados de utilizadores e sistemas como `master` e `msdb` . Esta capacidade proporciona uma visão única do sistema através das réplicas do grupo de disponibilidade. Note ambas `containedag_master` e `containedag_msdb` bases de dados se ligar diretamente ao caso. As `containedag_*` bases de dados representam o grupo de `master` disponibilidade e o grupo de `msdb` disponibilidade.

- É automaticamente previsto um ponto final externo para a ligação às bases de dados dentro do grupo de disponibilidade. Este ponto final `<managed_instance_name>-svc-external` desempenha o papel do ouvinte do grupo de disponibilidade.

### <a name="deploy"></a>Implementar

Para implementar uma instância gerida com grupos de disponibilidade, executar o seguinte comando.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Verificar o estado
Uma vez que a instância tenha sido implantada, executar os seguintes comandos para verificar o estado da sua instância:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Exemplo de saída:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Note o número adicional `Replicas` de e o campo `AGstatus` indicando a saúde do grupo de disponibilidade. Se todas as réplicas estiverem em cima e sincronizadas, então este valor é `healthy` . 

### <a name="restore-a-database"></a>Restaurar uma base de dados 
São necessárias medidas adicionais para restaurar uma base de dados num grupo de disponibilidade. Os seguintes passos demonstram como restaurar uma base de dados numa instância gerida e adicioná-la a um grupo de disponibilidade. 

1. Exponha o ponto final externo de instância primária criando um novo serviço Kubernetes.

    Determine a cápsula que acolhe a réplica primária ligando-se à instância gerida e executado:

    ```sql
    SELECT @@SERVERNAME
    ```
    Crie o serviço kubernetes para a primeira instância executando o comando abaixo se o seu cluster kubernetes utilizar serviços nodePort. `podName`Substitua-o pelo nome do servidor devolvido na etapa anterior, `serviceName` com o nome preferido para o serviço Kubernetes criado.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Para um serviço LoadBalancer, executar o mesmo comando, exceto que o tipo de serviço criado é `LoadBalancer` . Por exemplo: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Aqui está um exemplo desta corrida de comando contra o Serviço Azure Kubernetes, onde o pod que hospeda a primária `sql2-0` é:

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Obtenha o IP do serviço Kubernetes criado:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Restaurar a base de dados para o ponto final da instância primária.

    Adicione o ficheiro de cópia de segurança da base de dados no recipiente de instância primária.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Exemplo

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Restaurar o ficheiro de backup da base de dados executando o comando abaixo.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Exemplo

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Adicione a base de dados ao grupo de disponibilidade.

    Para que a base de dados seja adicionada à AG, deve funcionar em modo de recuperação total e tem de ser feita uma cópia de segurança de registo. Execute as declarações de TSQL abaixo para adicionar a base de dados restaurada no grupo de disponibilidade.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    O exemplo a seguir acrescenta uma base de dados `WideWorldImporters` denominada que foi restaurada no caso:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Como uma boa prática, você deve limpar eliminando o serviço Kubernetes criado acima executando este comando:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Limitações

Os grupos de disponibilidade de instância gerida ativado Azure Arc têm as [mesmas limitações que os grupos de disponibilidade do Big Data Cluster. Clique aqui para saber mais.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [funcionalidades e capacidades do Arco Azure habilitado para a SQL Gestão de Instância](managed-instance-features.md)
