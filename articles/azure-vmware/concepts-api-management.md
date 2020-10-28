---
title: Conceitos - Gestão da API
description: Saiba como a API Management protege as APIs em funcionamento em máquinas virtuais Azure VMware Solution (VMs)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f412ee81fc77435f2586a31c1bf6f6bdf22c66e2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670369"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management para publicar e proteger APIs em execução em VMs baseados em soluções VMware Azure

A Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) permite-lhe publicar de forma segura para consumidores internos ou externos.  Apenas os SKUs de Desenvolvedor e Premium permitem a integração da Rede Virtual Azure para publicar APIs em execução nas cargas de trabalho da Azure VMware Solution.  Ambos os SKUs permitem de forma segura a conectividade entre o serviço de Gestão API e o backend. 

>[!NOTE]
>O Developer SKU destina-se ao desenvolvimento e teste, enquanto o SKU Premium se destina a implantações de produção.

A configuração de Gestão API é a mesma para serviços de backend que funcionam em cima de máquinas virtuais Azure VMware Solution (VMs) e no local. Para ambas as implementações, a API Management configura o IP virtual (VIP) no equilibrador de carga como ponto final de backend quando o servidor backend é colocado atrás de um Balançador de Carga NSX na Solução VMware Azure. 


## <a name="external-deployment"></a>Implantação externa

Uma implementação externa publica APIs consumidas por utilizadores externos usando um ponto final público. Os desenvolvedores e engenheiros de DevOps podem gerir APIs através do portal Azure ou PowerShell, e do portal de desenvolvimento da API Management.

O diagrama de implantação externa mostra todo o processo e os intervenientes envolvidos (mostrados no topo). Os atores são:

- **Administrador(s):** Representa a equipa de administração ou DevOps, que gere a Azure VMware Solution através do portal Azure e mecanismos de automação como o PowerShell ou o Azure DevOps.

- **Utilizadores:**  Representa os consumidores de APIs expostos e representa tanto os utilizadores como os serviços que consomem as APIs.

O fluxo de tráfego passa por exemplo de API Management, que abstrata os serviços de backend, ligados à rede virtual Hub. O ExpressRoute Gateway encaminha o tráfego para o canal ExpressRoute Global Reach e chega a um Equilibr de Carga NSX distribuindo o tráfego de entrada para as diferentes instâncias de serviços de backend.

A API Management tem uma API Pública Azure, e recomenda-se a ativação do Serviço de Proteção DDOS do Azure. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Implementação externa - API Management for Azure VMware Solution":::


## <a name="internal-deployment"></a>Implantação interna

Uma implementação interna publica APIs consumidas por utilizadores ou sistemas internos. Os desenvolvedores de devOps e API usam as mesmas ferramentas de gestão e portal de desenvolvedores que na implementação externa.

As implementações internas podem ser feitas [com o Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) para criar um ponto final público e seguro para a API.  As capacidades do gateway são usadas para criar uma implementação híbrida que permite diferentes cenários.  

* Utilize o mesmo recurso de Gestão API para consumo por parte de consumidores internos e externos.

* Dispor de um único recurso de Gestão de API com um subconjunto de APIs definido e disponível para consumidores externos.

* Proporcionar uma maneira fácil de mudar o acesso à API Management a partir da internet pública dentro e fora.

O diagrama de implementação abaixo mostra os consumidores que podem ser internos ou externos, com cada tipo a aceder às mesmas APIs ou diferentes.

Numa implementação interna, as APIs são expostas à mesma instância de Gestão da API. Em frente à API Management, o Application Gateway é implementado com a capacidade Azure Web Application Firewall (WAF) ativada. Também implementado, um conjunto de ouvintes HTTP e regras para filtrar o tráfego, expondo apenas um subconjunto dos serviços de backend em execução na Azure VMware Solution.


* Rotas de tráfego interno através do ExpressRoute Gateway para Azure Firewall e depois para a API Management, diretamente ou através das regras de tráfego.   

* O tráfego externo entra em Azure através do Application Gateway, que utiliza a camada de proteção externa para a Gestão da API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Implementação externa - API Management for Azure VMware Solution" lightbox="media/api-management/internal-deployment.png":::