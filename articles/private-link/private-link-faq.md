---
title: Perguntas frequentes sobre o link privado do Azure
description: Saiba mais sobre o link privado do Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9239917599b40ac159e1cdbd4d3db6f6e70e12d2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019036"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o link privado do Azure

## <a name="private-link"></a>Link privado

### <a name="what-is-azure-private-link-service-and-private-endpoint"></a>O que é o serviço de vínculo privado do Azure e o ponto de extremidade privado?

- **Ponto de extremidade privado do Azure**: O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. Você pode usar pontos de extremidade privados para se conectar a um serviço de PaaS do Azure que dá suporte ao link privado ou ao seu próprio serviço de vínculo privado.
- **Serviço de link privado do Azure**: O serviço de vínculo privado do Azure é um serviço criado por um provedor de serviços. Atualmente, um serviço de vínculo privado pode ser anexado à configuração de IP de front-end de um Standard Load Balancer. 

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso conectar meu serviço a vários pontos de extremidade privados?
Sim. Um serviço de vínculo privado pode receber conexões de vários pontos de extremidade privados. No entanto, um ponto de extremidade privado só pode se conectar a um serviço de link privado.  
 
### <a name="is-data-transferred-over-private-link-always-private"></a>Os dados são transferidos sobre o link privado sempre privado?
Sim. Todos os dados sobre o link privado do Azure permanecem no backbone da Microsoft. Ele não atravessa a Internet.  
 
### <a name="what-is-the-difference-between-a-vnet-service-endpoint-and-a-private-endpoint"></a>Qual é a diferença entre um ponto de extremidade de serviço de VNet e um ponto de extremidade privado?
- Os pontos de extremidade de serviço de VNet estendem seu espaço de endereço privado de rede virtual e a identidade de sua VNet para os serviços do Azure, em uma conexão direta. Os pontos de extremidade de serviço permitem que você proteja seus recursos críticos de serviço do Azure apenas para suas redes virtuais, enquanto o tráfego é otimizado para rota e permanece na rede de backbone da Microsoft que é sdestined para o endereço IP público do serviço.
- O ponto de extremidade privado é um recurso de rede que atua como um ponto de entrada em sua rede virtual e usa o endereço IP privado para acessar os serviços fornecidos pelo link privado. O tráfego é otimizado para rota e permanece na rede de backbone da Microsoft.   

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço de link privado e o ponto de extremidade privado?
É uma relação um-para-muitos. Um serviço de vínculo privado pode receber conexões de vários pontos de extremidade privados. Por outro lado, um ponto de extremidade privado só pode se conectar a um serviço de vínculo privado.    
 
### <a name="will-vnet-service-endpoints-be-deprecated-once-private-endpoints-are-available"></a>Os pontos de extremidade de serviço da VNet serão preteridos quando pontos de extremidade privados estiverem disponíveis? 
Não. Pontos de extremidade de serviço de VNet e pontos de extremidade privados são tecnologias/recursos independentes. Eles podem complementar uns aos outros, e ambos coexistirão. Algumas funcionalidades e casos de uso podem se sobrepor, você pode escolher o modelo que atenda às suas necessidades.  
 
### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um provedor de serviços usando o link privado do Azure. É necessário ter certeza de que todos os meus clientes têm espaço IP exclusivo e não se sobrepõem ao meu espaço de IP? 
Não. O link privado do Azure fornece essa funcionalidade para você. Portanto, não é necessário ter espaço de endereço não sobreposto com o espaço de endereço do seu cliente. 
 
## <a name="private-link-service"></a>Serviço de vínculo privado
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para criar um serviço de link privado? 
Os back-ends de seu serviço devem estar em uma rede virtual e por trás de um Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso dimensionar meu serviço de vínculo privado? 
Você pode dimensionar seu serviço de vínculo privado de algumas maneiras diferentes: 
- Adicionar VMs de back-end ao pool por trás de seu Standard Load Balancer 
- Adicione um IP ao serviço de vínculo privado. Permitimos até 8 IPs por serviço de link privado.  
- Adicione um novo serviço de vínculo privado a Standard Load Balancer. Permitimos até oito serviços de link privado por balanceador de carga.   


### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço de vínculo privado?
Você pode controlar a exposição usando a configuração de visibilidade no serviço de vínculo privado. A visibilidade dá suporte a três configurações:

- **Nenhum** -somente assinaturas com acesso RBAC podem localizar o serviço. 
- Assinaturas somente **restritivas** que estão na lista de permissões e com acesso de RBAC podem localizar o serviço. 
- **Todos** -todos podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de vínculo privado com Load Balancer básica? 
Não. Não há suporte para o serviço de vínculo privado em um Load Balancer básico.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Uma sub-rede dedicada é necessária para o serviço de vínculo privado? 
Não. O serviço de vínculo privado não requer uma sub-rede dedicada. Você pode escolher qualquer sub-rede em sua VNet onde o serviço é implantado.   

## <a name="private-endpoint"></a>Ponto Final Privado 
 
### <a name="cani-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>CanI criar vários pontos de extremidade privados na mesma VNet? Eles podem se conectar a diferentes serviços? 
Sim. Você pode ter vários pontos de extremidade privados na mesma VNet ou sub-rede. Eles podem se conectar a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Eu preciso de uma sub-rede dedicada para pontos de extremidade privados? 
Não. Você não precisa de uma sub-rede dedicada para pontos de extremidade privados. Você pode escolher um IP de ponto de extremidade privado de qualquer sub-rede da VNet na qual seu serviço está implantado.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>O ponto de extremidade privado pode conectar-se ao serviço de vínculo privado entre Azure Active Directory locatários? 
Sim. Pontos de extremidade privados podem se conectar a serviços de vínculo privado ou a PaaS do Azure entre locatários do AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto de extremidade privado pode se conectar aos recursos de PaaS do Azure nas regiões do Azure?
Sim. Pontos de extremidade privados podem se conectar aos recursos de PaaS do Azure nas regiões do Azure.

##  <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
