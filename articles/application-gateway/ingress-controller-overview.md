---
title: O que é o Controlador de Entrada de Gateway de Aplicação Azure?
description: Este artigo fornece uma introdução ao que é o Controlador de Entrada de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714413"
---
# <a name="what-is-application-gateway-ingress-controller"></a>O que é o Controlador de Entrada de Gateway de Aplicação?
O Controlador de Entrada de Gateway (AGIC) é uma aplicação kubernetes, que permite aos clientes [do Serviço Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) aproveitar o equilibrador de carga de [aplicação](https://azure.microsoft.com/services/application-gateway/) nativa da Azure Gateway L7 para expor o software em nuvem à Internet. A AGIC monitoriza o cluster Kubernetes em que está hospedado e atualiza continuamente um Gateway de aplicações, de modo a que os serviços selecionados sejam expostos à Internet.

O Controlador Ingress funciona na sua própria cápsula na AKS do cliente. A AGIC monitoriza um subconjunto de Recursos Kubernetes para alterações. O estado do cluster AKS é traduzido para configuração específica do Gateway de Aplicação e aplicado ao [Gestor de Recursos Azure (ARM)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Benefícios do Controlador de Entrada de Gateway de Aplicação
A AGIC ajuda a eliminar a necessidade de ter outro balanceador de carga/IP público em frente ao cluster AKS e evita vários lúpulo no seu caminho de dados antes que os pedidos cheguem ao cluster AKS. A Application Gateway fala diretamente com os pods que utilizam o seu IP privado e não requer serviços NodePort ou KubeProxy. Isto também traz um melhor desempenho para as suas implementações.

O Controlador Ingress é suportado exclusivamente por SKUs Standard_v2 e WAF_v2, o que também lhe traz benefícios autoscalantes. O Application Gateway pode reagir em resposta a um aumento ou diminuição da carga de tráfego e escala em conformidade, sem consumir quaisquer recursos do seu cluster AKS.

A utilização do Application Gateway para além da AGIC também ajuda a proteger o seu cluster AKS, fornecendo a política TLS e a funcionalidade Web Application Firewall (WAF).

![Gateway de aplicação Azure + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

O AGIC é configurado através do recurso Kubernetes [Ingress,](https://kubernetes.io/docs/user-guide/ingress/)juntamente com o Serviço e Implementações/Pods. Fornece uma série de funcionalidades, aproveitando o equilibrador de carga gateway L7 de aplicação nativa da Azure. Para citar alguns:
  - Encaminhamento do URL
  - Afinidade com base no cookie
  - Rescisão de TLS
  - TLS de ponta a ponta
  - Apoio a sites públicos, privados e híbridos
  - Firewall de aplicação web integrada

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Diferença entre a implantação do Helm e a Add-On AKS
Há duas maneiras de implantar a AGIC para o seu cluster AKS. A primeira maneira é através de Helm; a segunda é através da AKS como um complemento. O principal benefício de implantar o AGIC como um addon AKS é que é muito mais simples do que implementar através do Helm. Para uma nova configuração, pode implementar um novo Gateway de Aplicações e um novo cluster AKS com AGIC ativado como um add-on numa linha em Azure CLI. O addon é também um serviço totalmente gerido, que proporciona benefícios adicionais, tais como atualizações automáticas e suporte acrescido. Ambas as formas de implantação do AGIC (Helm e AKS add-on) são totalmente suportadas pela Microsoft. Além disso, o addon permite uma melhor integração com a AKS como um complemento de primeira classe.

O addon AGIC ainda é implantado como um casulo no cluster AKS do cliente, no entanto, existem algumas diferenças entre a versão de implementação Helm e a versão add-on da AGIC. Abaixo está uma lista de diferenças entre as duas versões: 
  - Os valores de implantação do leme não podem ser modificados no addon AKS:
    - `verbosityLevel` será definido para 5 por padrão
    - `usePrivateIp` serão definidos como falsos por defeito; isto pode ser substituído pela [anotação use-private-ip](ingress-controller-annotations.md#use-private-ip)
    - `shared` não é suportado em add-on 
    - `reconcilePeriodSeconds` não é suportado em add-on
    - `armAuth.type` não é suportado em add-on
  - A AGIC implantada via Helm suporta Os Orçamentos Proibidos, o que significa que a AGIC pode configurar o Gateway de aplicação especificamente para clusters AKS sem afetar outros backends existentes. O addon AGIC não suporta atualmente isto. 
  - Uma vez que o addon AGIC é um serviço gerido, os clientes serão automaticamente atualizados para a versão mais recente do add-on AGIC, ao contrário da AGIC implementada através da Helm, onde o cliente deve atualizar manualmente a AGIC. 

> [!NOTE]
> Os clientes só podem implementar um addon AGIC por cluster AKS, e cada addon AGIC atualmente só pode visar um Gateway de aplicações. Para implementações que requerem mais de um AGIC por cluster ou múltiplos AGICs direcionados para um Gateway de aplicação, continue a utilizar o AGIC implantado através do Helm. 

## <a name="next-steps"></a>Passos seguintes
- [**AKS Add-On Implantação de Greenfield**](tutorial-ingress-controller-add-on-new.md): Instruções sobre a instalação do addon AGIC, AKS e Application Gateway na infraestrutura de ardósia em branco.
- [**AKS Add-On Implementação brownfield**](tutorial-ingress-controller-add-on-existing.md): Instale o addon AGIC num cluster AKS com um Gateway de aplicação existente.
- [**Helm Greenfield Deployment**](ingress-controller-install-new.md): Instale o AGIC através do Helm, novo cluster AKS e novo Gateway de aplicações em infraestrutura de ardósia em branco.
- [**Helm Brownfield Deployment**](ingress-controller-install-existing.md): Deploy AGIC através do Helm em um cluster AKS existente e Gateway de aplicação.