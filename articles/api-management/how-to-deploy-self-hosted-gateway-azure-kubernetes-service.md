---
title: Implementar uma porta de entrada auto-hospedada para o Serviço Azure Kubernetes [ Microsoft Docs
description: Saiba como implementar a componente de gateway auto-hospedada da Azure API Management para o Serviço Azure Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: abf7e6d7032e7e5dc35b2f4397a5630d45d762de
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205121"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Implementar no Azure Kubernetes Service

Este artigo fornece os passos para a implementação da componente de gateway auto-hospedada da Azure API Management para [o Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Para implementar porta de entrada auto-hospedada para um cluster Kubernetes, consulte este[documento](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Pré-requisitos

- [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
- [Criar um cluster Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Fornecer um recurso gateway na sua instância de Gestão API.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Desloque a porta de entrada auto-hospedada para a AKS

1. Selecione **Gateways** a partir de **definições**.
2. Selecione o recurso de gateway auto-hospedado que pretende implementar.
3. Selecione **Implementação**.
4. Note que um novo símbolo na caixa de texto **Token** foi autogerado para si usando os valores padrão **de Expiração** e **Chave Secreta.** Ajuste ambos ou ambos se desejarem e selecione **Generate** para criar um novo símbolo.
5. Certifique-se de que **os Kubernetes** são selecionados em **scripts de implantação**.
6. Selecione<link de ficheiro **>.yml** de nome de gateway ao lado da **Implementação** para descarregar o ficheiro.
7. Ajuste os mapeamentos da porta e o nome do recipiente no ficheiro yml, conforme necessário.
8. Dependendo do seu cenário, poderá ter de alterar o tipo de [serviço](https://docs.microsoft.com/azure/aks/concepts-network#services). O valor predefinido é `NodePort`.
9. Selecione o ícone de **cópia** localizado na extremidade `kubectl` direita da caixa de texto **de implantação** para guardar o comando para a área de sobre-a-trás.
10. Colar o comando à janela do terminal (ou comando). Note que o comando espera que o ficheiro ambiental descarregado esteja presente no atual diretório.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Execute o comando. O comando instrui o seu cluster AKS a executar o contentor, utilizando a imagem auto-hospedada do gateway descarregada do Registo de Contentores da Microsoft, e a configurar o recipiente para expor as portas HTTP (8080) e HTTPS (443).
12. Verifique se a cápsula de entrada está em funcionamento. Note que o nome da sua cápsula será diferente.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Verifique se o serviço de porta de entrada está em funcionamento. Note que o seu nome de serviço e endereços IP serão diferentes.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Volte ao portal Azure e confirme que o nó de gateway que acabou de implementar está a reportar um estado saudável.

> [!TIP]
> Utilize <code>kubectl logs <gateway-pod-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management](self-hosted-gateway-overview.md)
* Saiba mais sobre [o Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)
* Aprenda [a configurar e persistir troncos na nuvem](how-to-configure-cloud-metrics-logs.md)
* * Saiba [como configurar e persistir registos localmente](how-to-configure-local-metrics-logs.md)




