---
title: Implantar um gateway de gerenciamento de API do Azure auto-hospedado para kubernetes | Microsoft Docs
description: Saiba como implantar um gateway de gerenciamento de API do Azure auto-hospedado para o kubernetes
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513838"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Implantar um gateway de gerenciamento de API do Azure auto-hospedado para kubernetes

Este artigo fornece as etapas para implantar o gateway de gerenciamento de API do Azure auto-hospedado em um cluster kubernetes.

> [!NOTE]
> O recurso de gateway auto-hospedado está em versão prévia. Durante a visualização, o gateway auto-hospedado está disponível apenas nas camadas desenvolvedor e Premium sem custo adicional. A camada de desenvolvedor é limitada a uma única implantação de gateway de hospedagem interna.


## <a name="prerequisites"></a>Pré-requisitos

- Concluir o início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
- Crie um cluster do Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) é uma boa opção para fins de desenvolvimento e avaliação. Para cargas de trabalho de produção, você pode usar o [serviço kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) ou um cluster kubernetes em uma nuvem estrangeira.
- [Provisione um recurso de gateway em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Implantar o gateway no kubernetes

1. Selecione **gateways** em **configurações**.
2. Selecione o recurso de gateway que você pretende implantar.
3. Selecione **implantação**.
4. Observe que um novo token na caixa de texto de **token** foi gerado automaticamente para você usando os valores padrão de **expiração** e **chave secreta** . Ajuste um ou ambos, se desejado, e selecione **gerar** para criar um novo token.
5. Verifique se **kubernetes** está selecionado em **scripts de implantação**.
6. Selecione **< nome do gateway >** o link do arquivo. yml ao lado da **implantação** para baixar o arquivo.
7. Ajuste os mapeamentos de porta e o nome do contêiner no arquivo yml, conforme necessário.
8. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **implantar** para salvar o comando `kubectl` na área de transferência. 
9. Cole o comando na janela de terminal (ou comando). Observe que o comando espera que o arquivo de ambiente baixado esteja presente no diretório atual.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Execute o comando. O comando instrui o cluster kubernetes a executar o contêiner, usando a imagem do gateway de hospedagem interna baixada do registro de contêiner da Microsoft e para configurar o contêiner para expor portas HTTP (8080) e HTTPS (443).
11. Execute o comando abaixo para verificar se o pod de gateway está em execução. Observe que o nome do pod será diferente. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Execute o comando abaixo para verificar se o serviço de gateway está em execução. Observe que o nome do serviço será diferente. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Volte para a portal do Azure e confirme se o nó de gateway que você acabou de implantar está relatando o status Íntegro.

![Status do gateway](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Use <code>kubectl logs <gateway-pod-name></code> comando para exibir um instantâneo do log de gateway auto-hospedado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)
* Saiba mais sobre o [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/intro-kubernetes)


