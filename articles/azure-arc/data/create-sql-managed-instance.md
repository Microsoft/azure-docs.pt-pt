---
title: Criar um exemplo gerido pelo Azure SQL em Azure Arc
description: Criar um exemplo gerido pelo Azure SQL em Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280405"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Criar um exemplo gerido pelo Azure SQL em Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Faça login no controlador de dados Azure Arc

Antes de criar um caso, inicie sessão no controlador de dados Azure Arc se ainda não tiver iniciado sessão.

```console
azdata login
```

Em seguida, será solicitado o nome de utilizador, palavra-passe e o espaço de nome do sistema.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Criar uma Instância Gerida do SQL do Azure

Para visualizar as opções disponíveis criar opções para Exemplos GeridosQL, utilize o seguinte comando:
```console
azdata arc sql mi create --help
```

Para criar uma SqL Managed Instance, utilize o seguinte comando:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Exemplo:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Os nomes devem ter menos de 13 caracteres de comprimento e estar em conformidade com [as convenções de nomeação de DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Ao especificar a atribuição de memória e alocação vCore, utilize esta fórmula para garantir que a sua criação é bem sucedida - para cada 1 vCore você precisa de pelo menos 4GB de RAM de capacidade disponível no nó Kubernetes onde o pod de instância gerido SQL será executado.
>
>  Ao criar uma instância do SQL, não utilize maiúsculas no nome se estiver a aprovisionar no Azure
>
>  Para listar as aulas de armazenamento disponíveis no seu cluster Kubernetes `kubectl get storageclass` 


> [!NOTE]
> Se pretender automatizar a criação de instâncias SQL e evitar a solicitação interativa para a palavra-passe de administração, pode definir as `AZDATA_USERNAME` variáveis e `AZDATA_PASSWORD` ambientais para o nome de utilizador e palavra-passe pretendidos antes de executar o `azdata arc sql mi create` comando.
> 
>  Se criou o controlador de dados utilizando AZDATA_USERNAME e AZDATA_PASSWORD na mesma sessão de terminais, então os valores para AZDATA_USERNAME e AZDATA_PASSWORD serão usados para criar também o exemplo gerido pelo SQL.

> [!NOTE]
> Criar Azure SQL Managed Instance não registará os recursos em Azure. As medidas para registar o recurso constam dos seguintes artigos: 
> - [Ver registos e métricas usando Kibana e Grafana](monitor-grafana-kibana.md)
> - [Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Ver instância em Azure Arc

Para visualizar o caso, utilize o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser assim:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se estiver a utilizar AKS `kubeadm` ou OpenShift, etc., pode copiar o número de IP e porta externo a partir daqui e conectá-lo usando a sua ferramenta preferida para se ligar a uma instância SQL SqL SQL como Azure Data Studio ou SQL Server Management Studio. No entanto, se estiver a utilizar o VM de arranque rápido, consulte o artigo [de Instância Gerida do SqL ativado por Connect to Azure Para](connect-managed-instance.md) obter instruções especiais.


## <a name="next-steps"></a>Passos seguintes
- [Ligue ao Arco Azure ativado sql instância gerida](connect-managed-instance.md)
- [Registe o seu caso com o Azure e carre fique com métricas e registos sobre o seu caso](upload-metrics-and-logs-to-azure-monitor.md)
- [Implementar exemplo gerido pela Azure SQL utilizando o Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
