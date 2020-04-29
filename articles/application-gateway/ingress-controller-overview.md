---
title: O que é o Controlador gateway de aplicação azure?
description: Este artigo fornece uma introdução ao que é o Controlador de Ingress ingresso de Gateway application.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335808"
---
# <a name="what-is-application-gateway-ingress-controller"></a>O que é o Controlador de Ingress ingresso de Gateway de Aplicação?
O Application Gateway Ingress Controller (AGIC) é uma aplicação Kubernetes, que permite aos clientes do [Serviço Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) alavancar o fornecedor de carga Gateway L7 da [Azure](https://azure.microsoft.com/services/application-gateway/) para expor software em nuvem à Internet. A AGIC monitoriza o cluster Kubernetes em que está hospedado e atualiza continuamente um Gateway de Aplicação, de modo a que os serviços selecionados sejam expostos à Internet.

O Controlador De Ingress executa no seu próprio casulo no AKS do cliente. A AGIC monitoriza um subconjunto de Recursos Kubernetes para alterações. O estado do cluster AKS é traduzido para configuração específica do Gateway de aplicação e aplicado ao Gestor de [Recursos Azure (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Benefícios do Controlador ingresso gateway de aplicação
A AGIC permite que a sua implementação controle vários clusters AKS com um único controlador de entrada de gateway de aplicação. A AGIC também ajuda a eliminar a necessidade de ter outro balanceor/IP público em frente ao cluster AKS e evita vários lúpulos no seu caminho de dados antes que os pedidos cheguem ao cluster AKS. O Application Gateway fala com cápsulas que usam diretamente o seu IP privado e não requer serviços NodePort ou KubeProxy. Isto também traz um melhor desempenho às suas implementações.

O Controlador De Ingress é suportado exclusivamente por Standard_v2 e WAF_v2 SKUs, o que também lhe traz benefícios autoscalcificantes. O Application Gateway pode reagir em resposta a um aumento ou diminuição da carga de tráfego e da escala em conformidade, sem consumir quaisquer recursos do seu cluster AKS.

A utilização do Application Gateway para além da AGIC também ajuda a proteger o seu cluster AKS, fornecendo a política TLS e a funcionalidade firewall de aplicação web (WAF).

![Gateway de aplicações azure + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

A AGIC é configurada através do recurso Kubernetes [Ingress,](https://kubernetes.io/docs/user-guide/ingress/)juntamente com serviços e implementações/cápsulas. Fornece uma série de funcionalidades, alavancando o equilíbrio de carga l7 de aplicação nativa do Azure. Para citar alguns:
  - Encaminhamento do URL
  - Afinidade com base no cookie
  - TLS rescisão
  - TLS de ponta a ponta
  - Apoio a sites públicos, privados e híbridos
  - Firewall de aplicação web integrada

A AGIC é capaz de lidar com vários espaços de nome e tem Alvos Proibidos, o que significa que a AGIC pode configurar o Gateway de Aplicação especificamente para clusters AKS sem afetar outros backends existentes. 

## <a name="next-steps"></a>Passos Seguintes

- [**Greenfield Deployment**](ingress-controller-install-new.md): Instruções sobre a instalação de AGIC, AKS e Application Gateway em infraestrutura de ardósia em branco.
- [**Implementação brownfield**](ingress-controller-install-existing.md): Instale a AGIC num AKS e gateway de aplicação existentes.

