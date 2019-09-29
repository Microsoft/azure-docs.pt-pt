---
title: O que é o serviço de vínculo privado do Azure?
description: Saiba mais sobre o serviço de vínculo privado do Azure.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: cad8e3e4f32a8773fe914362b637d39765a23c21
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672521"
---
# <a name="what-is-azure-private-link-service"></a>O que é o serviço de vínculo privado do Azure?

O serviço de vínculo privado do Azure é a referência para seu próprio serviço que é alimentado pelo link privado do Azure. Seu serviço que está sendo executado por trás [do Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) pode ser habilitado para acesso de link privado para que os consumidores de seu serviço possam acessá-lo de forma privada em seu próprio VNets. Seus clientes podem criar um ponto de extremidade privado dentro de sua VNet e mapeá-lo para esse serviço. Este artigo explica os conceitos relacionados ao lado do provedor de serviço. 

## <a name="workflow"></a>Fluxo de trabalho

![Fluxo de trabalho do serviço de vínculo privado](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Criar seu serviço de vínculo privado

- Configure seu aplicativo para executar por trás de um balanceador de carga padrão em sua rede virtual. Se você já tiver seu aplicativo configurado por trás de um balanceador de carga padrão, poderá ignorar esta etapa.   
- Crie um serviço de vínculo privado referenciando o balanceador de carga acima. No processo de seleção do balanceador de carga, escolha a configuração de IP de front-end onde você deseja receber o tráfego. Escolha uma sub-rede para endereços IP de NAT para o serviço de link privado. É recomendável ter pelo menos oito endereços IP de NAT disponíveis na sub-rede. Todo o tráfego do consumidor parecerá originar esse pool de endereços IP privados para o provedor de serviços. Escolha as propriedades/configurações apropriadas para o serviço de link privado.    

    > [!NOTE]
    > O serviço de vínculo privado do Azure só tem suporte em Standard Load Balancer. 
    
### <a name="share-your-service"></a>Compartilhar seu serviço

Depois de criar um serviço de vínculo privado, o Azure gerará um moniker nomeado globalmente exclusivo chamado "alias" com base no nome que você fornecer para o serviço. Você pode compartilhar o alias ou o URI de recurso do seu serviço com seus clientes offline. Os consumidores podem iniciar uma conexão de link privado usando o alias ou o URI de recurso.
 
### <a name="manage-your-connection-requests"></a>Gerenciar suas solicitações de conexão

Depois que um consumidor inicia uma conexão, o provedor de serviços pode aceitar ou rejeitar a solicitação de conexão. Todas as solicitações de conexão serão listadas na propriedade **privateendpointconnections** no serviço de link privado.
 
### <a name="delete-your-service"></a>Excluir seu serviço

Se o serviço de vínculo privado não estiver mais em uso, você poderá excluí-lo. No entanto, antes de excluir o serviço, verifique se não há nenhuma conexão de ponto de extremidade privada associada a ele. Você pode rejeitar todas as conexões e excluir o serviço.

## <a name="properties"></a>properties

Um serviço de vínculo privado especifica as seguintes propriedades: 

|Propriedade |Explicação  |
|---------|---------|
|Estado de provisionamento (provisioningState)  |Uma propriedade somente leitura que lista o estado de provisionamento atual para o serviço de vínculo privado. Os Estados de provisionamento aplicáveis são: Excluído Falha ao Foi Atualizando ". Quando o estado de provisionamento for "êxito", você terá provisionado com êxito o serviço de vínculo privado.        |
|Alias (alias)     | Alias é uma cadeia de caracteres somente leitura globalmente exclusiva para seu serviço. Ele ajuda a mascarar os dados do cliente para seu serviço e, ao mesmo tempo, cria um nome de fácil compartilhamento para seu serviço. Quando você cria um serviço de vínculo privado, o Azure gera o alias para o serviço que você pode compartilhar com seus clientes. Seus clientes podem usar esse alias para solicitar uma conexão ao seu serviço.          |
|Visibilidade (visibilidade)     | Visibility é a propriedade que controla as configurações de exposição para seu serviço de vínculo privado. Os provedores de serviços podem optar por limitar a exposição a seu serviço a assinaturas com permissões de RBAC (controle de acesso baseado em função), um conjunto restrito de assinaturas ou todas as assinaturas do Azure.          |
|Aprovação automática (autoaprovação)    |   A aprovação automática controla o acesso automatizado ao serviço de vínculo privado. As assinaturas especificadas na lista aprovação automática são aprovadas automaticamente quando uma conexão é solicitada de pontos de extremidade privados nessas assinaturas.          |
|Load Balancer configuração de IP de front-end (loadBalancerFrontendIpConfigurations)    |    O serviço de vínculo privado está vinculado ao endereço IP de front-end de um Standard Load Balancer. Todo o tráfego destinado ao serviço alcançará o front-end do SLB. Você pode configurar regras SLB para direcionar esse tráfego para os pools de back-end apropriados onde seus aplicativos estão em execução. As configurações de IP de front-end do balanceador de carga são diferentes das configurações de IP NAT.      |
|Configuração de IP de NAT (ipConfigurations)    |    Essa propriedade refere-se à configuração de IP NAT (conversão de endereços de rede) para o serviço de link privado. O IP de NAT pode ser escolhido de qualquer sub-rede em uma rede virtual do provedor de serviços. O serviço de vínculo privado executa NAT-ing no tráfego do link privado. Isso garante que não haja nenhum conflito de IP entre a origem (lado do consumidor) e o espaço de endereço de destino (provedor de serviço). No lado de destino (lado do provedor de serviço), o endereço IP de NAT aparecerá como IP de origem para todos os pacotes recebidos pelo seu serviço e IP de destino para todos os pacotes enviados pelo seu serviço.       |
|Conexões de ponto de extremidade privado (privateEndpointConnections)     |  Essa propriedade lista os pontos de extremidade privados que se conectam ao serviço de vínculo privado. Vários pontos de extremidade privados podem se conectar ao mesmo serviço de vínculo privado e o provedor de serviços pode controlar o estado de pontos de extremidade privados individuais.        |
|||


### <a name="details"></a>Detalhes

- O serviço de vínculo privado pode ser acessado de pontos de extremidade privados aprovados na mesma região. O ponto de extremidade privado pode ser alcançado da mesma rede virtual, VNets emparelhada de modo regional, VNets emparelhada globalmente e local usando conexões VPN privadas ou ExpressRoute. 
 
- Ao criar um serviço de vínculo privado, uma interface de rede é criada para o ciclo de vida do recurso. Essa interface não é gerenciável pelo cliente.
 
- O serviço de vínculo privado deve ser implantado na mesma região que a rede virtual e a Standard Load Balancer.  
 
- Um único serviço de vínculo privado pode ser acessado de vários pontos de extremidade privados que pertencem a diferentes VNets, assinaturas e/ou locatários Active Directory. A conexão é estabelecida por meio de um fluxo de trabalho de conexão. 
 
- Vários serviços de vínculo privado podem ser criados no mesmo Standard Load Balancer usando configurações de IP de front-end diferentes. Há limites para o número de serviços de vínculo privado que você pode criar por Standard Load Balancer e por assinatura. Para obter detalhes, consulte [limites do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).
 
- O serviço de vínculo privado pode ter mais de uma configuração de IP NAT vinculada a ela. A escolha de mais de uma configuração de IP NAT pode ajudar os provedores de serviços a dimensionar. Hoje, os provedores de serviços podem atribuir até oito endereços IP de NAT por serviço de vínculo privado. Com cada endereço IP de NAT, você pode atribuir mais portas para suas conexões TCP e, portanto, escalar horizontalmente. Depois de adicionar vários endereços IP NAT a um serviço de vínculo privado, você não pode excluir os endereços IP de NAT. Isso é feito para garantir que as conexões ativas não sejam afetadas durante a exclusão dos endereços IP de NAT.


## <a name="alias"></a>Alias

**Alias** é um nome globalmente exclusivo para seu serviço. Ele ajuda a mascarar os dados do cliente para seu serviço e, ao mesmo tempo, cria um nome de fácil compartilhamento para seu serviço. Quando você cria um serviço de vínculo privado, o Azure gera um alias para o serviço que você pode compartilhar com seus clientes. Seus clientes podem usar esse alias para solicitar uma conexão ao seu serviço.

O alias é composto por três partes: *Prefixo*. *GUID*. *Sufixo*

- Prefixo é o nome do serviço. Você pode escolher seu próprio prefixo. Depois que "alias" for criado, você não poderá alterá-lo; portanto, selecione o prefixo adequadamente.  
- O GUID será fornecido pela plataforma. Isso ajuda a tornar o nome globalmente exclusivo. 
- O sufixo é acrescentado pelo Azure: *Region*. Azure. privatelinkservice 

Alias completo:  *Prefixo*. {GUID}. *Region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Controlar a exposição do serviço

O serviço de vínculo privado fornece opções para controlar a exposição de seu serviço por meio da configuração de "visibilidade". Você pode tornar o serviço particular para consumo de diferentes VNets que você possui (somente permissões RBAC), restringir a exposição a um conjunto limitado de assinaturas em que você confia ou torná-lo público para que todas as assinaturas do Azure possam solicitar conexões no link privado serviço. Suas configurações de visibilidade decidem se um consumidor pode se conectar ao seu serviço ou não. 

## <a name="control-service-access"></a>Acesso ao serviço de controle

Os consumidores que têm exposição (controlada pela configuração de visibilidade) para o serviço de vínculo privado podem criar um ponto de extremidade privado em seu VNets e solicitar uma conexão com o serviço de vínculo privado. A conexão de ponto de extremidade particular será criada em um estado "pendente" no objeto de serviço de link privado. O provedor de serviços é responsável por agir na solicitação de conexão. Você pode aprovar a conexão, rejeitar a conexão ou excluir a conexão. Somente as conexões aprovadas podem enviar tráfego para o serviço de link privado.

A ação de aprovar as conexões pode ser automatizada usando a propriedade aprovação automática no serviço de vínculo privado. A aprovação automática é uma capacidade para os provedores de serviços aprovarem um conjunto de assinaturas para acesso automatizado ao serviço. Os clientes precisarão compartilhar suas assinaturas offline para que os provedores de serviços adicionem à lista de aprovação automática. A aprovação automática é um subconjunto da matriz de visibilidade. A visibilidade controla as configurações de exposição, enquanto a aprovação automática controla as configurações de aprovação do seu serviço. Se um cliente solicitar uma conexão de uma assinatura na lista aprovação automática, a conexão será aprovada automaticamente e a conexão será estabelecida. Os provedores de serviço não precisam mais aprovar manualmente a solicitação. Por outro lado, se um cliente solicitar uma conexão de uma assinatura na matriz de visibilidade e não na matriz de aprovação automática, a solicitação atingirá o provedor de serviços, mas o provedor de serviços precisará aprovar as conexões manualmente.

## <a name="limitations"></a>Limitações

A seguir estão as limitações conhecidas ao usar o serviço de link privado:
- Com suporte apenas em Standard Load Balancer 
- Dá suporte apenas ao tráfego IPv4
- Dá suporte apenas ao tráfego TCP
- Acessível somente de pontos de extremidade privados na mesma região
- Não há suporte para criar e gerenciar a experiência do portal do Azure
- As informações de conexão dos clientes usando o protocolo proxy não estão disponíveis para o provedor de serviços

## <a name="next-steps"></a>Passos seguintes
- [Criar um serviço de vínculo privado usando Azure PowerShell](create-private-link-service-powershell.md)
- [Criar um serviço de vínculo privado usando CLI do Azure](create-private-link-service-cli.md)
