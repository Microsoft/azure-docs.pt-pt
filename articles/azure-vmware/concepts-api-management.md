---
title: Conceitos - Gestão da API
description: Saiba como a API Management protege as APIs em funcionamento em máquinas virtuais Azure VMware Solution (AVS)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306948"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>API Management para publicar e proteger APIs em execução em VMs baseados em AVS

A Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) permite que os desenvolvedores e equipas de DevOps publiquem de forma segura, quer para consumidores internos ou externos.

Apesar de oferecidos em vários SKUs, apenas os SKUs de Desenvolvimento e Premium permitem a integração da Rede Virtual Azure para publicar APIs em execução nas cargas de trabalho da Azure VMware Solution (AVS). Estes dois SKUs permitem de forma segura a conectividade entre o serviço de Gestão API e o backend. O Developer SKU destina-se ao desenvolvimento e teste, enquanto o SKU Premium se destina a implantações de produção.

Para serviços de backend que funcionam em cima de máquinas virtuais AVS (VMs), a configuração na Gestão API, por padrão, é a mesma que os serviços de backend no local. Para implementações internas e externas, a API Management configura o IP virtual (VIP) do equilibrador de carga como ponto final de backend quando o servidor de backend é colocado atrás de um Balancer de Carga NSX no lado AVS.

## <a name="external-deployment"></a>Implantação externa

Uma implementação externa publica APIs consumidas por utilizadores externos usando um ponto final público. Desenvolvedores e engenheiros de DevOps podem gerir APIs através do portal Azure ou PowerShell, e o portal de desenvolvedores de Gestão API.

O diagrama de implantação externa mostra todo o processo e os intervenientes envolvidos (mostrados no topo). Os atores são:

- **Administrador(s):** Representa a equipa de administração ou DevOps, que gere o AVS através do portal Azure e mecanismos de automação como o PowerShell ou o Azure DevOps.

- **Utilizadores:**  Representa os consumidores das APIs expostas e representa tanto os utilizadores como os serviços que consomem as APIs.

O fluxo de tráfego passa por exemplo de API Management, que abstrata os serviços de backend, ligados à rede virtual Hub. O ExpressRoute Gateway encaminha o tráfego para o canal ExpressRoute Global Reach e chega a um Equilibr de Carga NSX distribuindo o tráfego de entrada para as diferentes instâncias de serviços de backend.

A API Management tem uma API Pública Azure e recomenda-se a ativação do Serviço de Proteção DDOS do Azure. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Implantação externa - Gestão da API para AVS":::


## <a name="internal-deployment"></a>Implantação interna

Uma implementação interna publica APIs consumidas por utilizadores ou sistemas internos. Os desenvolvedores de devOps e API usam as mesmas ferramentas de gestão e portal de desenvolvedores que na implementação externa.

As implementações internas podem ser [com o Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) para criar um ponto final público e seguro para a API alavancando as suas capacidades e criando uma implementação híbrida que permita diferentes cenários.  A API aproveita as suas capacidades e cria uma implementação híbrida que permite diferentes cenários.

* Utilize o mesmo recurso de Gestão API para consumo por parte de consumidores internos e externos.

* Dispor de um único recurso de Gestão de API com um subconjunto de APIs definido e disponível para consumidores externos.

* Proporcionar uma maneira fácil de mudar o acesso à API Management a partir da internet pública dentro e fora.

O diagrama de implementação abaixo mostra os consumidores que podem ser internos ou externos, com cada tipo a aceder às mesmas APIs ou diferentes.

Numa implementação interna, as APIs são expostas à mesma instância de Gestão da API. Em frente à API Management, o Application Gateway é implementado com a capacidade Azure Web Application Firewall (WAF) ativada e um conjunto de ouvintes HTTP e regras para filtrar o tráfego, expondo apenas um subconjunto dos serviços de backend em execução em AVS.

* O tráfego interno é encaminhado através do ExpressRoute Gateway para Azure Firewall e depois para a API Management se as regras de tráfego forem estabelecidas ou diretamente para a API Management.  

* O tráfego externo entra em Azure através do Application Gateway, que utiliza a camada de proteção externa para a Gestão da API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Implantação interna - Gestão da API para AVS":::