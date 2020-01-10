---
title: Perguntas frequentes sobre o link privado do Azure
description: Saiba mais sobre o link privado do Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349927"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o link privado do Azure

## <a name="private-link"></a>Ligação Privada

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>O que é o ponto de extremidade privado do Azure e o serviço de vínculo privado do Azure?

- **[Ponto de extremidade privado do Azure](private-endpoint-overview.md)** : o ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma de link privado do Azure. Você pode usar pontos de extremidade privados para se conectar a um serviço de PaaS do Azure que dá suporte ao link privado ou ao seu próprio serviço de vínculo privado.
- **[Serviço de vínculo privado do Azure](private-link-service-overview.md)** : o serviço de vínculo privado do Azure é um serviço criado por um provedor de serviços. Atualmente, um serviço de vínculo privado pode ser anexado à configuração de IP de front-end de um Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Como o tráfego é enviado ao usar o link privado?
O tráfego é enviado de forma privada usando o backbone da Microsoft. Ele não atravessa a Internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Qual é a diferença entre um ponto de extremidade de serviço e um ponto de extremidade privado?
- Ao usar pontos de extremidade privados, o acesso à rede é concedido a recursos específicos por trás de um determinado serviço, fornecendo segmentação granular, além disso, o tráfego pode acessar o recurso de serviço no local sem usar pontos de extremidade públicos.
- Um ponto de extremidade de serviço permanece um endereço IP roteável publicamente.  Um ponto de extremidade privado é um IP privado no espaço de endereço da rede virtual em que o ponto de extremidade privado está configurado.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço de link privado e o ponto de extremidade privado?
O ponto de extremidade privado fornece acesso a vários tipos de recursos de link privado, incluindo os serviços de PaaS do Azure e seu próprio serviço de vínculo privado. É uma relação um-para-muitos. Um serviço de vínculo privado pode receber conexões de vários pontos de extremidade privados. Por outro lado, um ponto de extremidade privado só pode se conectar a um serviço de vínculo privado.    

## <a name="private-endpoint"></a>Ponto Final Privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Posso criar vários pontos de extremidade privados na mesma VNet? Eles podem se conectar a diferentes serviços? 
Sim. Você pode ter vários pontos de extremidade privados na mesma VNet ou sub-rede. Eles podem se conectar a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Eu preciso de uma sub-rede dedicada para pontos de extremidade privados? 
Não. Você não precisa de uma sub-rede dedicada para pontos de extremidade privados. Você pode escolher um IP de ponto de extremidade privado de qualquer sub-rede da VNet na qual seu serviço está implantado.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>O ponto de extremidade privado pode conectar-se ao serviço de vínculo privado entre Azure Active Directory locatários? 
Sim. Pontos de extremidade privados podem se conectar a serviços de vínculo privado ou a PaaS do Azure entre locatários do AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto de extremidade privado pode se conectar aos recursos de PaaS do Azure nas regiões do Azure?
Sim. Pontos de extremidade privados podem se conectar aos recursos de PaaS do Azure nas regiões do Azure.

## <a name="private-link-service"></a>Serviço Ligação Privada
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para criar um serviço de link privado? 
Os back-ends de seu serviço devem estar em uma rede virtual e por trás de um Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso dimensionar meu serviço de vínculo privado? 
Você pode dimensionar seu serviço de vínculo privado de algumas maneiras diferentes: 
- Adicionar VMs de back-end ao pool por trás de seu Standard Load Balancer 
- Adicione um IP ao serviço de vínculo privado. Permitimos até 8 IPs por serviço de link privado.  
- Adicione um novo serviço de vínculo privado a Standard Load Balancer. Permitimos até oito serviços de link privado por balanceador de carga.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso conectar meu serviço a vários pontos de extremidade privados?
Sim. Um serviço de vínculo privado pode receber conexões de vários pontos de extremidade privados. No entanto, um ponto de extremidade privado só pode se conectar a um serviço de link privado.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço de vínculo privado?
Você pode controlar a exposição usando a configuração de visibilidade no serviço de vínculo privado. A visibilidade dá suporte a três configurações:

- **Nenhum** -somente assinaturas com acesso RBAC podem localizar o serviço. 
- Assinaturas somente **restritivas** que estão na lista de permissões e com acesso de RBAC podem localizar o serviço. 
- **Todos** -todos podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de vínculo privado com Load Balancer básica? 
Não. Não há suporte para o serviço de vínculo privado em um Load Balancer básico.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Uma sub-rede dedicada é necessária para o serviço de vínculo privado? 
Não. O serviço de vínculo privado não requer uma sub-rede dedicada. Você pode escolher qualquer sub-rede em sua VNet onde o serviço é implantado.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um provedor de serviços usando o link privado do Azure. É necessário ter certeza de que todos os meus clientes têm espaço IP exclusivo e não se sobrepõem ao meu espaço de IP? 
Não. O link privado do Azure fornece essa funcionalidade para você. Portanto, não é necessário ter espaço de endereço não sobreposto com o espaço de endereço do seu cliente. 

##  <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
