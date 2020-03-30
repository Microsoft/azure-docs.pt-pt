---
title: Implementar uma porta de entrada de gestão azure autónoma para Kubernetes [ Microsoft Docs
description: Saiba como implementar uma porta de entrada de gestão de API Azure auto-hospedada para kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513838"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Implementar uma porta de entrada de gestão de API Azure auto-hospedada para Kubernetes

Este artigo fornece os passos para implantar a gateway de Gestão API Azure auto-hospedada num Cluster Kubernetes.

> [!NOTE]
> A funcionalidade de gateway auto-hospedada está em pré-visualização. Durante a pré-visualização, o gateway auto-hospedado só está disponível nos níveis Developer e Premium sem custos adicionais. O nível de desenvolvimento está limitado a uma única implementação de gateway auto-hospedada.


## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
- Crie um aglomerado de Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) é uma boa opção para fins de desenvolvimento e avaliação. Para cargas de trabalho de produção, pode utilizar o [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) ou um cluster Kubernetes numa nuvem estrangeira.
- [Fornecer um recurso gateway na sua instância de Gestão API.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-gateway-to-kubernetes"></a>Implementar a porta de entrada para Kubernetes

1. Selecione **Gateways** a partir de **definições**.
2. Selecione o recurso de gateway que pretende implementar.
3. Selecione **Implementação**.
4. Note que um novo símbolo na caixa de texto **Token** foi autogerado para si usando os valores padrão **de Expiração** e **Chave Secreta.** Ajuste ambos ou ambos se desejarem e selecione **Generate** para criar um novo símbolo.
5. Certifique-se de que **os Kubernetes** são selecionados em **scripts de implantação**.
6. Selecione<link de ficheiro **>.yml** de nome de gateway ao lado da **Implementação** para descarregar o ficheiro.
7. Ajuste os mapeamentos da porta e o nome do recipiente no ficheiro yml, conforme necessário.
8. Selecione o ícone de **cópia** localizado na extremidade `kubectl` direita da caixa de texto **de implantação** para guardar o comando para a área de sobre-a-trás. 
9. Colar o comando à janela do terminal (ou comando). Note que o comando espera que o ficheiro ambiental descarregado esteja presente no atual diretório.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Execute o comando. O comando instrui o seu cluster Kubernetes a executar o contentor, utilizando a imagem auto-hospedada do gateway descarregada do Registo de Contentores da Microsoft, e a configurar o recipiente para expor as portas HTTP (8080) e HTTPS (443).
11. Verifique se a cápsula de entrada está em funcionamento. Note que o nome da sua cápsula será diferente. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Verifique se o serviço de porta de entrada está em funcionamento. Note que o seu nome de serviço será diferente. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Volte ao portal Azure e confirme que o nó de gateway que acabou de implementar está a reportar um estado saudável.

![estado de gateway](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Utilize <code>kubectl logs <gateway-pod-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management](self-hosted-gateway-overview.md)
* Saiba mais sobre [o Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


