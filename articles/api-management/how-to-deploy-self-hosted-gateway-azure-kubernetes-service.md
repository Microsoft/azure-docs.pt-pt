---
title: Implemente uma porta de entrada auto-hospedada para o Serviço Azure Kubernetes | Microsoft Docs
description: Saiba como implementar o componente de gateway auto-hospedado da Azure API Management para o Serviço Azure Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015226"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Implementar no Azure Kubernetes Service

Este artigo fornece os passos para a implementação do componente de gateway auto-hospedado da Azure API Management para o [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Para implementar uma porta de entrada auto-hospedada para um cluster Kubernetes consulte este[documento](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Pré-requisitos

- [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
- [Criar um cluster Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Disponibilização de um recurso de gateway no seu caso de Gestão de API.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Implemente a porta de entrada auto-hospedada para a AKS

1. Selecione **Gateways** a partir de **implementação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que pretende implementar.
3. Selecione **Implantação**.
4. Note que um novo token na caixa de texto **Token** foi autogerido para si usando os valores padrão **de Expiração** e **Chave Secreta.** Ajuste ou ambos se desejar e **selecione Gerar** para criar um novo token.
5. Certifique-se de que **kubernetes** é selecionado sob **scripts de implementação**.
6. Selecione **<link de ficheiro>.yml** com o nome de gateway ao lado do **Deployment** para descarregar o ficheiro.
7. Ajuste os mapeamentos da porta e o nome do recipiente no ficheiro yml, conforme necessário.
8. Dependendo do seu cenário, poderá ter de alterar o [tipo de serviço](../aks/concepts-network.md#services). O valor predefinido é `NodePort`.
9. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **Implementar** para guardar o `kubectl` comando para a área de transferência.
10. Cole o comando até à janela do terminal (ou comando). Note que o comando espera que o ficheiro ambiente descarregado esteja presente no diretório atual.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Execute o comando. O comando instrui o seu cluster AKS a executar o contentor, utilizando a imagem do Gateway auto-hospedado descarregada do Registo de Contentores da Microsoft, e a configurar o recipiente para expor as portas HTTP (8080) e HTTPS (443).
12. Verifique o comando abaixo para verificar se a cápsula de gateway está a funcionar. Note que o seu nome de vagem será diferente.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Verifique o comando abaixo para verificar se o serviço de gateway está em funcionamento. Note que o seu nome de serviço e endereços IP serão diferentes.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Volte ao portal Azure e confirme que o nó de gateway que acabou de implantar está a reportar um estado saudável.

> [!TIP]
> Use <code>kubectl logs <gateway-pod-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a Azure API Management auto-hospedada gateway overview](self-hosted-gateway-overview.md)
* Saiba mais sobre [o Serviço Azure Kubernetes](../aks/intro-kubernetes.md)
* Aprenda [a configurar e persistir registos na nuvem](how-to-configure-cloud-metrics-logs.md)
* * Aprenda [a configurar e persistir registos localmente](how-to-configure-local-metrics-logs.md)
