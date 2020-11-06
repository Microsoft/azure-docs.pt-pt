---
title: O que é o Controlador de Entrada de Gateway de Aplicação Azure?
description: Este artigo fornece uma introdução ao que é o Controlador de Entrada de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 26f53a8f93d4d51ec8f8fd91051496a46670f432
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397353"
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
Há duas maneiras de implantar a AGIC para o seu cluster AKS. A primeira maneira é através de Helm; a segunda é através da AKS como um complemento. O principal benefício de implantar o AGIC como um addon AKS é que é muito mais simples do que implementar através do Helm. Para uma nova configuração, pode implementar um novo Gateway de Aplicações e um novo cluster AKS com AGIC ativado como um add-on numa linha em Azure CLI. O addon é também um serviço totalmente gerido, que proporciona benefícios adicionais, tais como atualizações automáticas e suporte acrescido. A AGIC implantada através do Helm não é suportada pela AKS, no entanto, o AGIC implantado como um addon AKS é suportado pela AKS. 

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
> O método de implantação do addon AGIC AKS está atualmente em pré-visualização. Não recomendamos a execução de cargas de trabalho de produção em funcionalidades ainda em pré-visualização, por isso, se estiver curioso para experimentar, recomendamos a criação de um novo cluster para testá-lo. 

As tabelas que se seguem classificam quais os cenários que são atualmente suportados com a versão de implantação Helm e a versão add-on AKS da AGIC. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS add-on AGIC (único cluster AKS)
|                  |1 Gateway de aplicação |2+ Gateways de aplicação |
|------------------|---------|--------|
|**1 AGIC**|Sim, isto é apoiado |Não, isto está no nosso atraso. |
|**2+ AGICs**|Não, apenas 1 AGIC suportado/cluster |Não, apenas 1 AGIC suportado/cluster |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm implantado AGIC (único cluster AKS)
|                  |1 Gateway de aplicação |2+ Gateways de aplicação |
|------------------|---------|--------|
|**1 AGIC**|Sim, isto é apoiado |Não, isto está no nosso atraso. |
|**2+ AGICs**|Deve usar a funcionalidade Dedestarget Proibido compartilhado e ver espaços de nome separados |Sim, isto é apoiado |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm implantado AGIC (2+ clusters AKS)
|                  |1 Gateway de aplicação |2+ Gateways de aplicação |
|------------------|---------|--------|
|**1 AGIC**|N/D |N/D |
|**2+ AGICs**|Deve usar a funcionalidade Dedestarget Proibido partilhado |N/D |

## <a name="next-steps"></a>Passos seguintes
- [**AKS Add-On Implantação de Greenfield**](tutorial-ingress-controller-add-on-new.md): Instruções sobre a instalação do addon AGIC, AKS e Application Gateway na infraestrutura de ardósia em branco.
- [**AKS Add-On Implementação brownfield**](tutorial-ingress-controller-add-on-existing.md): Instale o addon AGIC num cluster AKS com um Gateway de aplicação existente.
- [**Helm Greenfield Deployment**](ingress-controller-install-new.md): Instale o AGIC através do Helm, novo cluster AKS e novo Gateway de aplicações em infraestrutura de ardósia em branco.
- [**Helm Brownfield Deployment**](ingress-controller-install-existing.md): Deploy AGIC através do Helm em um cluster AKS existente e Gateway de aplicação.