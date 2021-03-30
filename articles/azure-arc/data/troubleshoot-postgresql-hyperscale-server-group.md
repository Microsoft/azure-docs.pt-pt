---
title: Resolução de problemas Grupos de servidores de hiperescala pós-sql
description: Resolução de problemas Grupos de servidores de hiperescala postgresQL com um caderno Jupyter
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320221"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Resolução de problemas Grupos de servidores de hiperescala postgresQL
Este artigo descreve algumas técnicas que pode utilizar para resolver problemas no seu grupo de servidor. Além deste artigo, pode querer ler como usar [Kibana](monitor-grafana-kibana.md) para pesquisar os registos ou usar [Grafana](monitor-grafana-kibana.md) para visualizar métricas sobre o seu grupo de servidores. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Obter mais detalhes sobre a execução de um comando azdata
Pode adicionar o parâmetro **--depurar** a qualquer comando azdata que execute. Ao fazê-lo, apresentar-se-ão à sua consola informações adicionais sobre a execução desse comando. Deve achar útil obter detalhes para ajudá-lo a entender o comportamento desse comando.
Por exemplo, pode correr
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

ou
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Além disso, pode utilizar o parâmetro --ajuda em qualquer comando azdata para mostrar alguma ajuda, lista de parâmetros para um comando específico. Por exemplo:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Recolha de registos do controlador de dados e dos grupos de servidor
Leia o artigo sobre [a obtenção de registos para serviços de dados ativados pelo Azure Arc](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Resolução de problemas interativa com cadernos Jupyter no Azure Data Studio
Os blocos de notas podem documentar procedimentos ao incluir o conteúdo de markdown para descrever o que fazer/como o fazer. Além disso, podem disponibilizar código executável para automatizar um procedimento.  Este padrão é útil para tudo, desde procedimentos operacionais padrão a guias de resolução de problemas.

Por exemplo, vamos resolver um grupo de servidores de hiperescala PostgreSQL que pode ter alguns problemas usando o Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Instalar ferramentas

Instale o Azure Data Studio `kubectl` e na máquina cliente que está a usar para executar o caderno no [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Data Studio. Para isso, siga as instruções na [Instalação de ferramentas do cliente](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Atualizar a variável ambiente PATH

Certifique-se de que estas ferramentas podem ser invocadas de qualquer lugar desta máquina cliente. Por exemplo, numa máquina cliente Do Windows, atualize a variável ambiente do sistema PATH e adicione a pasta na qual instalou kubectl.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Inscreva-se com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Inicie súm em seu Arc Data Controller a partir desta máquina de cliente e antes de lançar Azure Data Studio. Para isso, executar um comando como:

```console
azdata login --endpoint https://<IP address>:<port>
```

`<IP address>`Substitua-o pelo endereço IP do seu cluster Kubernetes e `<port>` pela porta em que o Kubernetes está a ouvir. Será solicitado o nome de utilizador e a palavra-passe. Para ver mais detalhes, corra:_

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Inicie sessão no seu cluster Kubernetes com kubectl

Para isso, poderá querer utilizar os comandos de exemplo fornecidos [neste](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) post de blog.
Executaria comandos como:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>O caderno de resolução de problemas

Lance o Azure Data Studio e abra o caderno de resolução de problemas. 

Implementar os passos descritos em [033-manage-Postgres-with-AzureDataStudio.md:](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)

1. Ligue-se ao seu Controlador de Dados do Arco
2. Selecione a sua instância Postgres e escolha **[Gerir]**
3. Selecione o **dashboard [Diagnosticar e resolver problemas]**
4. Selecione o **link [Resolução de Problemas]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio - Open PostgreSQL troubleshooting Notebook":::

O **TSG100 - O Azure Arc habilitado para o notebook de resolução de problemas pós-escala pós-SQL** :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="abre:Azure Data Studio - Use o caderno de resolução de problemas postgreSQL":::

#### <a name="run-the-scripts"></a>Executar os scripts
Selecione o botão 'Executar Tudo' na parte superior para executar o caderno de uma só vez, ou pode passar e executar cada célula de código, uma a uma.

Ver a saída da execução das células de código para quaisquer problemas potenciais.

Vamos adicionar mais detalhes ao caderno ao longo do tempo sobre como reconhecer problemas comuns e como resolvê-los.

## <a name="next-step"></a>Passo seguinte
- Leia sobre [a obtenção de registos para serviços de dados habilitados para o Azure Arc](troubleshooting-get-logs.md)
- Leia sobre [a pesquisa de troncos com Kibana](monitor-grafana-kibana.md)
- Ler sobre [monitorização com Grafana](monitor-grafana-kibana.md)
- Crie os seus próprios cadernos
