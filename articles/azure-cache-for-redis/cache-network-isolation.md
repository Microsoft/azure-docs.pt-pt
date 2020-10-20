---
title: Cache Azure para opções de isolamento da rede Redis
description: Neste artigo, você vai aprender como determinar a melhor solução de isolamento de rede para suas necessidades. Vamos analisar os fundamentos da Azure Private Link, da injeção da Rede Virtual Azure (VNet) e das Regras de Firewall do Azure Com as suas vantagens e limitações.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: b01e7ca9ff05b6eed51e1c454b8064ab28bda0d5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222911"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Cache Azure para opções de isolamento da rede Redis 
Neste artigo, você vai aprender como determinar a melhor solução de isolamento de rede para suas necessidades. Vamos analisar os fundamentos da Azure Private Link, da injeção da Rede Virtual Azure (VNet) e das Regras de Firewall do Azure Com as suas vantagens e limitações.  

## <a name="azure-private-link-public-preview"></a>Azure Private Link (pré-visualização pública) 
O Azure Private Link fornece conectividade privada de uma rede virtual para os serviços Azure PaaS. Simplifica a arquitetura da rede e assegura a ligação entre pontos finais em Azure, eliminando a exposição de dados à internet pública. 

### <a name="advantages"></a>Vantagens
* Suportado em Cache Basic, Standard e Premium Azure para instâncias Redis. 
* Ao utilizar [o Azure Private Link,](/azure/private-link/private-link-overview)pode ligar-se a uma instância Azure Cache a partir da sua rede virtual através de um ponto final privado, que é atribuído um endereço IP privado numa sub-rede dentro da rede virtual. Com isto, as instâncias cache estão disponíveis tanto dentro do VNet como publicamente.  
* Uma vez criado um ponto final privado, o acesso à rede pública pode ser restringido através da `publicNetworkAccess` bandeira. Esta bandeira é definida `Enabled` por padrão, dando-lhe a opção de permitir o acesso de ligação pública e privada à cache. Se estiver `Disabled` definido, só permitirá o acesso de ligação privada. Pode definir o valor `Disabled` com um pedido PATCH. Para obter mais informações, consulte [Azure Cache for Redis com Azure Private Link (Preview)](cache-private-link.md). 
* Todas as dependências de cache externas não afetarão as regras NSG da VNet.

### <a name="limitations"></a>Limitações 
* Os grupos de segurança da rede (NSG) são desativados para pontos finais privados. No entanto, se existirem outros recursos na sub-rede, a aplicação da NSG aplicar-se-á a esses recursos.
* Geo-replicação, regras de firewall, suporte para consolas de portal, múltiplos pontos finais por cache agrupado, persistência na firewall e caches injetados VNet ainda não estão suportados. 
* Para se ligar a uma cache agrupada, `publicNetworkAccess` tem de ser configurada e só pode `Disabled` haver uma ligação de ponto final privado.

> [!NOTE]
> Ao adicionar um ponto final privado a uma instância de cache, todo o tráfego Redis será transferido para o ponto final privado por causa do DNS.
> Certifique-se de que as regras anteriores de firewall são ajustadas antes.  
>
>

## <a name="azure-virtual-network-injection"></a>Injeção de Rede Virtual Azure 
O VNet é o bloco de construção fundamental para a sua rede privada em Azure. O VNet permite que muitos recursos Azure se comuniquem de forma segura entre si, a internet e as redes no local. O VNet é como uma rede tradicional que operaria no seu próprio centro de dados, mas com os benefícios da infraestrutura Azure, escala, disponibilidade e isolamento. 

### <a name="advantages"></a>Vantagens
* Quando uma cache Azure para a instância Redis é configurada com um VNet, não é publicamente endereçada e só pode ser acedida a partir de máquinas virtuais e aplicações dentro do VNet.  
* Quando o VNet é combinado com políticas restritas de NSG, ajuda a reduzir o risco de exfiltração de dados. 
* A implementação da VNet proporciona uma maior segurança e isolamento para o seu Azure Cache para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso. 
* A replicação de geo é suportada. 

### <a name="limitations"></a>Limitações
* Os caches injetados VNet só estão disponíveis para o Premium Azure Cache para Redis. 
* Ao utilizar uma cache injetada em VNet, terá de abrir o seu VNet para cache dependências tais como CRLs/PKI, AKV, Azure Storage, Azure Monitor, e muito mais.  


## <a name="azure-firewall-rules"></a>Regras do Firewall de Azure
[O Azure Firewall](/azure/firewall/overview) é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos Azure VNet. É uma firewall totalmente imponente como um serviço com alta disponibilidade incorporada e escalabilidade de nuvem sem restrições. Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais.  

### <a name="advantages"></a>Vantagens
* Quando as regras de firewall são configuradas, apenas as ligações do cliente a partir das gamas de endereços IP especificadas podem ligar-se à cache. As ligações da Cache Azure para sistemas de monitorização Redis são sempre permitidas, mesmo que as regras de firewall estejam configuradas. As regras NSG que define também são permitidas.  

### <a name="limitations"></a>Limitações
* As regras de firewall podem ser usadas em conjunto com caches injetados VNet, mas não pontos finais privados atualmente. 


## <a name="next-steps"></a>Passos seguintes
* Saiba como configurar uma [cache injetada VNet para uma Cache Premium Azure para o exemplo de Redis](cache-how-to-premium-vnet.md).  
* Saiba como configurar [as regras de firewall para todos os níveis Azure Cache para os níveis Redis](cache-configure.md#firewall). 
* Saiba como [configurar pontos finais privados para todos os níveis Azure Cache para os níveis Redis](cache-private-link.md). 
