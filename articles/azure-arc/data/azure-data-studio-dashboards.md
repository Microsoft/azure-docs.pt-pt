---
title: Dashboards Azure Data Studio
description: Dashboards Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92107577"
---
# <a name="azure-data-studio-dashboards"></a>Dashboards Azure Data Studio

[O Azure Data Studio](/sql/azure-data-studio/what-is) proporciona uma experiência semelhante ao portal Azure para visualizar informações sobre os seus recursos Azure Arc.  Estas vistas são chamadas dashboards e têm um layout e opções **semelhantes** ao que você poderia ver sobre um dado recurso no portal Azure, mas dar-lhe a flexibilidade de ver essa informação localmente no seu ambiente em casos em que você não tem uma ligação disponível para Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Ligação a um controlador de dados

### <a name="prerequisites"></a>Pré-requisitos

- Baixar [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- Extensão Azure Arc é instalada

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Determine o URL do ponto final do servidor de dados API

Em primeiro lugar, terá de ligar o Azure Data Studio ao seu serviço de tratamento de dados API ENDpoint URL.

Para obter este ponto final pode executar o seguinte comando:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Verá uma saída que se parece com esta:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Se estiver a utilizar um tipo LoadBalancer, deverá copiar o endereço IP externo e o número de porta. Se estiver a utilizar o NodePort, deverá utilizar o endereço IP do seu servidor API de Kubernetes e o número de porta listado na coluna PORT(S).

Agora, você vai querer construir um URL para o seu ponto final, combinando esta informação assim:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Tome nota do seu endereço IP como irá usá-lo no próximo passo.

### <a name="connect"></a>Ligar

1. Aberto Azure Data Studio

1. Selecione o **separador Ligações** à esquerda

Para o fundo, expanda o painel chamado **Azure Arc Controllers**.

Clique no ícone + para adicionar uma nova ligação de controlador de dados.

Na parte superior do ecrã na paleta de comando, insira o URL que construíste no Passo 1, clique em introduzir.
Introduza o nome de utilizador para o controlador de dados.  Este foi o valor do nome de utilizador que passou durante a implementação do controlador de dados.  Clique em entrar.
Introduza a palavra-passe para o controlador de dados.  Este foi o valor da palavra-passe que passou durante a implementação do controlador de dados. Clique em entrar.

Agora que está ligado a um controlador de dados, pode visualizar os dashboards para o controlador de dados e quaisquer instâncias geridas pelo SQL ou recursos do grupo de servidores de hiperescala PostgreSQL que tenha.

## <a name="view-the-data-controller-dashboard"></a>Ver o painel de controlo de dados

Clique com o botão direito no controlador de dados no painel De Ligações no painel expansível **dos Controladores de Arco** e escolha **Gerir**.

Aqui pode ver detalhes sobre o recurso do controlador de dados, tais como nome, região, modo de conexão, grupo de recursos, subscrição, ponto final do controlador e espaço de nome.  Pode ver uma lista de todos os recursos de base de dados geridos pelo controlador de dados também.

Você vai notar que o layout é semelhante ao que você pode ver no portal Azure.

Convenientemente, pode lançar a criação de um exemplo gerido pelo SQL ou do grupo de servidores de hiperescala PostgreSQL clicando no botão + New Instance.

Também pode abrir o portal Azure em contexto a este controlador de dados clicando no botão De abertura no portal Azure.

## <a name="view-the-sql-managed-instance-dashboards"></a>Ver os dashboards de instância geridos sql

Se criou alguns casos geridos pelo SQL, pode vê-los listados no painel De Ligações no painel expansível dos Controladores de Dados Azure por baixo do controlador de dados que os está a gerir.

Para ver o painel de exemplo gerido pelo SQL para um dado exemplo, clique com o botão direito no caso e escolha Gerir.

O painel 'Ligação' aparece à direita e pede-lhe que o login/palavra-passe se conecte a essa instância SQL. Se souber a informação de ligação pode inseri-la e clicar em Connect.  Se não sabe, pode clicar em Cancelar.  Seja como for, serão levados para o painel quando o painel 'Ligação' fechar.

No separador Visão Geral pode ver detalhes sobre o exemplo gerido pelo SQL, como grupo de recursos, controlador de dados, ID de subscrição, estado, região e muito mais.  Também pode ver o link que pode clicar para entrar nos dashboards Grafana ou Kibana em contexto para que o SQL tenha gerido o exemplo.

Se conseguir ligar-se ao exemplo de gestão SQL, pode ver informações adicionais aqui.

Pode eliminar a instância gerida pelo SQL a partir daqui ou abrir o portal Azure para ver a ocorrência gerida pelo SQL no portal Azure.

Se clicar no separador 'Cordas de Ligação' à esquerda, pode ver uma lista de cadeias de ligação pré-construídas para essa instância gerida pelo SQL, facilitando a cópia/colar em várias outras aplicações ou códigos.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Ver os dashboards do grupo de servidores de hiperescala PostgreSQL

Se criou alguns grupos de servidores de hiperescala PostgreSQL, pode vê-los listados no painel de Ligações no painel expansível dos Controladores de Dados Azure por baixo do controlador de dados que os está a gerir.

Para ver o painel do grupo de servidores de hiperescala PostgreSQL para um determinado grupo de servidor, clique com o botão direito no grupo de servidor e escolha Gerir.

No separador 'Vista Geral' pode ver detalhes sobre o grupo de servidores como grupo de recursos, controlador de dados, ID de subscrição, estado, região e muito mais.  Também pode ver o link que pode clicar para entrar nos dashboards Grafana ou Kibana em contexto para esse grupo de servidores.

Pode eliminar o grupo de servidor a partir daqui ou abrir o portal Azure para ver o grupo de servidores no portal Azure.

Se clicar no separador 'Cordas de Ligação' à esquerda, pode ver uma lista de cadeias de ligação pré-construídas para esse grupo de servidor, facilitando a cópia/colar em várias outras aplicações ou códigos.

Se clicar no separador Propriedades à esquerda, pode ver detalhes adicionais.

Se clicar no separador de saúde de Recursos à esquerda, pode ver a saúde atual de alto nível desse grupo de servidor.

Se clicar no separador Diagnosticar e resolver problemas à esquerda, pode lançar o caderno de resolução de problemas PostgreSQL.

Se clicar no novo separador de pedido de suporte à esquerda, pode lançar o portal Azure em contexto para o grupo de servidor e criar um pedido de suporte Azure a partir daí.