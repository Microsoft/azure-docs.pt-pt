---
title: Ligação Privada Azure frequentemente feita perguntas (FAQ)
description: Saiba mais sobre o Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75349927"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Ligação Privada Azure frequentemente feita perguntas (FAQ)

## <a name="private-link"></a>Ligação Privada

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>O que é azure Private Endpoint e Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado por Azure Private Link. Pode utilizar pontos finais privados para se ligar a um serviço Azure PaaS que suporta private link ou ao seu próprio Serviço de Ligação Privada.
- Serviço de **[Link Privado Azure](private-link-service-overview.md)**: O serviço Azure Private Link é um serviço criado por um prestador de serviços. Atualmente, um serviço de Link Privado pode ser ligado à configuração IP frontal de um Balancer de Carga Padrão. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Como é que o tráfego é enviado quando se utiliza o Private Link?
O tráfego é enviado em privado usando a espinha dorsal da Microsoft. Não atravessa a internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Qual é a diferença entre um Ponto final de serviço e um Ponto final privado?
- Ao utilizar pontos finais privados, o acesso à rede é concedido a recursos específicos por detrás de um determinado serviço que fornece segmentação granular, também o tráfego pode chegar ao recurso de serviço a partir de instalações sem usar pontos finais públicos.
- Um ponto final de serviço continua a ser um endereço IP publicamente repreensível.  Um ponto final privado é um IP privado no espaço de endereço da rede virtual onde o ponto final privado é configurado.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço Private Link e o Private Endpoint?
O Private Endpoint fornece acesso a vários tipos de recursos de ligação privada, incluindo os serviços Azure PaaS e o seu próprio Serviço de Ligação Privada. É uma relação de um para muitos. Um serviço de Link Privado pode receber ligações de vários pontos finais privados. Por outro lado, um ponto final privado só pode ligar-se a um serviço de Link Privado.    

## <a name="private-endpoint"></a>Ponto Final Privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Posso criar vários pontos finais privados no mesmo VNet? Podem ligar-se a diferentes Serviços? 
Sim. Pode ter vários pontos finais privados na mesma VNet ou subnet. Podem ligar-se a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Preciso de uma sub-rede dedicada para pontos finais privados? 
Não. Não é preciso uma sub-rede dedicada para pontos finais privados. Pode escolher um IP de ponto final privado a partir de qualquer subnet a partir da VNet onde o seu serviço está implantado.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>O Private Endpoint pode ligar-se ao serviço Private Link através de Inquilinos de Diretório Ativo Azure? 
Sim. Os pontos finais privados podem ligar-se aos serviços de Private Link ou ao Azure PaaS através de inquilinos da AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto final privado pode ligar-se aos recursos do Azure PaaS em todas as regiões de Azure?
Sim. Os pontos finais privados podem ligar-se aos recursos do Azure PaaS em todas as regiões de Azure.

## <a name="private-link-service"></a>Serviço de Ligação Privada
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para a criação de um serviço private link? 
Os seus backends de serviço devem estar numa rede Virtual e atrás de um Balancer de Carga Padrão.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso escalar o meu serviço de Private Link? 
Pode escalar o seu serviço private link de várias maneiras diferentes: 
- Adicione VMs de backend à piscina atrás do seu Balancer de Carga Padrão 
- Adicione um IP ao serviço Private Link. Permitimos até 8 IPs por serviço private link.  
- Adicione um novo serviço de Link Privado ao Standard Load Balancer. Permitimos até oito serviços de Ligação Privada por equilibrador de carga.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso ligar o meu serviço a vários pontos finais privados?
Sim. Um serviço de Link Privado pode receber ligações de vários pontos finais privados. No entanto, um ponto final privado só pode ligar-se a um serviço de Link Privado.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço private link?
Pode controlar a exposição utilizando a configuração de visibilidade no serviço Private Link. A visibilidade suporta três configurações:

- **Nenhuma** - Apenas as subscrições com acesso RBAC podem localizar o serviço. 
- **Restritiva** - Apenas as subscrições que estão listadas em branco e com acesso RBAC podem localizar o serviço. 
- **Todos** podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de Link Privado com Balancer de Carga Básica? 
Não. O serviço de Link Privado sobre um Equilíbrio de Carga Básico não é suportado.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>É necessária uma subnet dedicada para o serviço Private Link? 
Não. O serviço Private Link não requer uma sub-rede dedicada. Pode escolher qualquer subrede no seu VNet onde o seu serviço esteja implantado.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um prestador de serviços usando o Azure Private Link. Preciso de ter a certeza que todos os meus clientes têm um espaço IP único e não se sobrepõem ao meu espaço IP? 
Não. O Azure Private Link fornece-lhe esta funcionalidade. Por isso, não é obrigado a ter espaço de endereço sem sobreposição com o espaço de endereço do seu cliente. 

##  <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Link Privado Azure](private-link-overview.md)
