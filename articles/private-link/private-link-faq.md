---
title: Perguntas mais frequentes (FAQ) sobre o Azure Private Link
description: Saiba mais sobre a Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496478"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) sobre o Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>O que é Azure Private Endpoint e Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. Pode utilizar os Pontos Finais Privados para se ligar a um serviço Azure PaaS que suporta o Private Link ou ao seu próprio Serviço de Ligação Privada.
- **[Azure Private Link Service](private-link-service-overview.md)**: Azure Private Link service é um serviço criado por um prestador de serviços. Atualmente, um serviço de Ligação Privada pode ser anexado à configuração IP frontal de um Balanceador de Carga Padrão. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Como é enviado o tráfego quando se utiliza o Link Privado?
O tráfego é enviado em privado usando a espinha dorsal da Microsoft. Não atravessa a internet. O Azure Private Link não armazena os dados dos clientes.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Qual é a diferença entre pontos finais de serviço e pontos finais privados?
- Os Pontos Finais Privados concedem acesso à rede a recursos específicos por detrás de um determinado serviço que presta segmentação granular. O tráfego pode chegar ao recurso de serviço a partir das instalações sem utilizar pontos finais públicos.
- Um ponto final de serviço continua a ser um endereço IP publicamente rotaível.  Um ponto final privado é um IP privado no espaço de endereço da rede virtual onde o ponto final privado está configurado.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço Private Link e o Private Endpoint?
Vários tipos de recursos de ligação privada suportam o acesso via Private Endpoint. Os recursos incluem os serviços Azure PaaS e o seu próprio Serviço de Ligação Privada. É uma relação de um para muitos. 

Um serviço private link recebe ligações de vários pontos finais privados. Um ponto final privado conecta-se a um serviço private link.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Preciso de desativar as políticas de rede para o Private Link
Sim. Tanto o ponto final privado como o Serviço de Ligação Privada precisam de desativar as políticas de rede para funcionar corretamente. Ambos têm propriedades independentes umas das outras.

## <a name="private-endpoint"></a>Ponto Final Privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Posso criar vários Pontos Finais Privados no mesmo VNet? Podem ligar-se a diferentes Serviços? 
Sim. Pode ter vários pontos finais privados na mesma VNet ou sub-rede. Podem ligar-se a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Preciso de uma sub-rede dedicada para pontos finais privados? 
N.º Não precisa de uma sub-rede dedicada para pontos finais privados. Pode escolher um IP de ponto final privado a partir de qualquer sub-rede do VNet onde o seu serviço é implantado.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Um ponto final privado pode ligar-se aos serviços de Ligação Privada através dos inquilinos do Azure Ative Directory? 
Sim. Os pontos finais privados podem ligar-se a serviços de Ligação Privada ou a um Azure PaaS através dos inquilinos do Azure Ative Directory. Os pontos finais privados que se conectam entre inquilinos requerem uma aprovação manual do pedido. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto final privado pode ligar-se aos recursos do Azure PaaS em todas as regiões de Azure?
Sim. Os pontos finais privados podem ligar-se aos recursos do Azure PaaS em todas as regiões de Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Posso modificar a minha Interface de Rede de Pontos Finais (NIC) ?
Quando um ponto final privado é criado, um NIC apenas de leitura é atribuído. Isto não pode ser modificado e permanecerá para o ciclo de vida do ponto final privado.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Como posso obter disponibilidade enquanto uso pontos finais privados em caso de falhas regionais?

Os Pontos Finais Privados são recursos altamente disponíveis com 99,99% SLA [[SLA for Azure Private Link]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/). No entanto, uma vez que são recursos regionais, qualquer paralisação da região de Azure pode ter impacto na disponibilidade. Para conseguir disponibilidade em caso de falhas regionais, vários EE ligados ao mesmo recurso de destino poderiam ser implantados em diferentes regiões. Desta forma, se uma região cair, ainda pode encaminhar o tráfego para os seus cenários de recuperação através de PE em diferentes regiões para aceder ao recurso de destino. Para obter informações sobre como as falhas regionais são tratadas no lado do serviço de destino, por favor reveja a documentação do serviço sobre failover e recuperação. O tráfego de Ligação Privada segue a resolução Azure DNS para o ponto final de destino. 


## <a name="private-link-service"></a>Serviço de Ligação Privada
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para a criação de um serviço de Ligação Privada? 
Os seus backends de serviço devem estar numa rede Virtual e atrás de um Balancer de Carga Standard.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso escalar o meu serviço de Ligação Privada? 
Pode escalar o seu serviço Private Link de várias maneiras: 
- Adicione VMs de backend à piscina atrás do seu Balanceador de Carga Padrão 
- Adicione um IP ao serviço Private Link. Permitimos até 8 IPs por serviço private Link.  
- Adicione o novo serviço de Ligação Privada ao Balanceador de Carga Padrão. Permitimos até oito serviços de Ligação Privada por balançador de carga.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>O que é a configuração IP DA NAT (Tradução de Endereços de Rede) utilizada no Serviço de Ligação Privada? Como posso escalar em termos de portas e ligações disponíveis? 

A configuração NAT IP garante que não existe um conflito IP entre o espaço de endereço de origem (lado do consumidor) e o destino (prestador de serviços), fornecendo a origem NAT no tráfego de Ligação Privada no lado do destino (lado do prestador de serviços). O endereço NAT IP aparecerá como SOURCE IP para todos os pacotes recebidos pelo seu serviço e destino IP para todos os pacotes enviados pelo seu serviço.  O NAT IP pode ser escolhido a partir de qualquer sub-rede da rede virtual de um prestador de serviços. 

Cada NAT IP fornece ligações TCP de 64k (portas de 64k) por VM atrás do Balanceador de Carga Padrão. Para escalar e adicionar mais ligações, pode adicionar novos IPs NAT ou adicionar mais VMs atrás do Balanceador de Carga Padrão. Ao fazê-lo, irá escalar a disponibilidade da porta e permitir mais ligações. As ligações serão distribuídas por IPs e VMs NAT por trás do Balanceador de Carga Padrão.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso ligar o meu serviço a vários Pontos Finais Privados?
Sim. Um serviço de Ligação Privada pode receber ligações de vários Pontos Finais Privados. No entanto, um Ponto Final Privado só pode ligar-se a um serviço de Ligação Privada.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço private link?
Pode controlar a exposição utilizando a configuração de visibilidade no serviço Private Link. A visibilidade suporta três configurações:

- **Nenhuma** - Apenas as subscrições com acesso Azure RBAC podem localizar o serviço. 
- **Restritivo** - Apenas as subscrições aprovadas e com acesso Azure RBAC podem localizar o serviço. 
- **Todos** podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de Ligação Privada com um equilibrador de carga básico? 
N.º O serviço private Link sobre um balanceador de carga básico não é suportado.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>É necessária uma sub-rede dedicada para o serviço Private Link? 
N.º O serviço private Link não requer uma sub-rede dedicada. Pode escolher qualquer sub-rede no seu VNet onde o seu serviço está implantado.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um prestador de serviços que usa a Azure Private Link. Preciso de ter a certeza de que todos os meus clientes têm espaço IP único e não se sobrepõem ao meu espaço IP? 
N.º O Azure Private Link fornece esta funcionalidade para si. Não é obrigado a ter espaço de endereço não sobreposto com o espaço de endereço do seu cliente. 

##  <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure Private Link](private-link-overview.md)
