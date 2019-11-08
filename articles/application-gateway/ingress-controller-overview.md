---
title: O que é Aplicativo Azure controlador de entrada do gateway?
description: Este artigo fornece uma introdução ao que é o controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 9635798720667e38a767f26fc2e5f5374e420059
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795535"
---
# <a name="what-is-application-gateway-ingress-controller"></a>O que é o controlador de entrada do gateway de aplicativo?
O AGIC (controlador de entrada do gateway de aplicativo) é um aplicativo kubernetes, que possibilita aos clientes [do AKS (Azure kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/) aproveitar o balanceador de carga L7 do [Gateway de aplicativo](https://azure.microsoft.com/services/application-gateway/) nativo do Azure para expor o software de nuvem para a Internet. O AGIC monitora o cluster kubernetes em que ele está hospedado e atualiza continuamente um gateway de aplicativo, para que os serviços selecionados sejam expostos à Internet.

O controlador de entrada é executado em seu próprio pod no AKS do cliente. O AGIC monitora um subconjunto de recursos do kubernetes para alterações. O estado do cluster AKS é convertido na configuração específica do gateway de aplicativo e aplicado ao [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Benefícios do controlador de entrada do gateway de aplicativo
O AGIC permite que sua implantação controle vários clusters do AKS com um único controlador de entrada do gateway de aplicativo. O AGIC também ajuda a eliminar a necessidade de ter outro IP de balanceador de carga/público na frente do cluster AKS e evita vários saltos no caminho de seu DataPath antes que as solicitações alcancem o cluster AKS. O gateway de aplicativo conversa com pods usando seu IP privado diretamente e não requer serviços NodePort ou KubeProxy. Isso também traz um melhor desempenho para suas implantações.

O controlador de entrada tem suporte exclusivo por Standard_v2 e WAF_v2 SKUs, o que também traz os benefícios de dimensionamento automático. O gateway de aplicativo pode reagir em resposta a um aumento ou redução na carga de tráfego e no dimensionamento de acordo, sem consumir nenhum recurso do cluster AKS.

O uso do gateway de aplicativo além do AGIC também ajuda a proteger o cluster do AKS fornecendo a funcionalidade de política TLS e de firewall do aplicativo Web (WAF).

![Aplicativo Azure gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

O AGIC é configurado por meio do [recurso de entrada](http://kubernetes.io/docs/user-guide/ingress/)do kubernetes, juntamente com o serviço e as implantações/pods. Ele fornece uma série de recursos, aproveitando o balanceador de carga L7 do gateway de aplicativo nativo do Azure. Para citar alguns:
  - Encaminhamento do URL
  - Afinidade com base no cookie
  - Terminação de SSL
  - SSL de ponto a ponto
  - Suporte para sites públicos, privados e híbridos
  - Firewall de aplicação Web integrada

AGIC é capaz de lidar com vários namespaces e tem ProhibitedTargets, o que significa que AGIC pode configurar o gateway de aplicativo especificamente para clusters AKS sem afetar outros back-ends existentes. 

## <a name="next-steps"></a>Próximos Passos

- [**Implantação do Greenfield**](ingress-controller-install-new.md): instruções sobre como instalar o AGIC, o AKs e o gateway de aplicativo em uma infraestrutura de Slate em branco.
- [**Implantação de Brownfield**](ingress-controller-install-existing.md): Instale o AGIC em um AKs e um gateway de aplicativo existentes.

