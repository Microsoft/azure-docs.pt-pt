---
title: Cluster Connect - Azure Arc habilitado a Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceptual da capacidade cluster Connect de Azure Arc habilitado Kubernetes
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451097"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Cluster conecta-se em Azure Arc habilitado a Kubernetes

O arco Azure permitiu que a funcionalidade *de ligação* do cluster Kubernetes proporciona conectividade ao `apiserver` cluster sem que qualquer porta de entrada seja ativada na firewall. Um agente de procuração inversa que esteja a funcionar no cluster pode iniciar uma sessão com o serviço Azure Arc de forma de saída. 

O cluster connect permite que os desenvolvedores acedam aos seus clusters a partir de qualquer lugar para desenvolvimento interativo e depuração. Também permite que os utilizadores e administradores do cluster acedam ou gerem os seus clusters a partir de qualquer lugar. Pode até utilizar agentes/corredores hospedados de Azure Pipelines, GitHub Actions ou qualquer outro serviço de CI/CD hospedado para implementar aplicações em clusters on-prem, sem necessitar de agentes auto-hospedados.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arquitetura

[![Cluster conecta arquitetura ](./media/conceptual-cluster-connect.png)](./media/conceptual-cluster-connect.png#lightbox)

Do lado do cluster, um agente de procuração invertido chamado `clusterconnect-agent` implantado como parte do gráfico de leme do agente, faz chamadas de saída para o serviço Azure Arc para estabelecer a sessão.

Quando o utilizador `az connectedk8s proxy` telefona:
1. Azure Arc proxy binary é descarregado e girado como um processo na máquina do cliente. 
1. Azure Arc proxy procura um `kubeconfig` ficheiro associado ao Arco Azure habilitado o cluster Kubernetes no qual o é `az connectedk8s proxy` invocado.
    * O proxy Azure Arc usa o token de acesso Azure do autor da chamada e o nome de ID do Gestor de Recursos Azure. 
1. O `kubeconfig` ficheiro, guardado na máquina por Azure Arc proxy, aponta o URL do servidor para um ponto final no processo de procuração do Azure Arc.

Quando um utilizador envia um pedido utilizando este `kubeconfig` ficheiro:
1. Azure Arc proxy mapeia o ponto final recebendo o pedido para o serviço Azure Arc. 
1. O serviço Azure Arc reencaminha então o pedido para `clusterconnect-agent` a execução no cluster. 
1. O `clusterconnect-agent` pedido passa no pedido para o componente, que realiza a `kube-aad-proxy` autenticação AZure AD na entidade de chamadas. 
1. Após a autenticação AZure AD, `kube-aad-proxy` utiliza a [função de imitação](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) do utilizador Kubernetes para encaminhar o pedido para o cluster `apiserver` .

## <a name="next-steps"></a>Passos seguintes

* Use o nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* [Aceda](./cluster-connect.md) o seu cluster de forma segura a partir de qualquer lugar que utilize a ligação cluster.