---
title: O que é o serviço Azure Private Link?
description: Saiba mais sobre o serviço Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280433"
---
# <a name="what-is-azure-private-link-service"></a>O que é o serviço Azure Private Link?

O serviço Azure Private Link é a referência ao seu próprio serviço que é alimentado pela Azure Private Link. O seu serviço que está a funcionar atrás do [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) pode ser ativado para o acesso ao Private Link para que os consumidores do seu serviço possam aceder-lhe de forma privada a partir dos seus próprios VNets. Os seus clientes podem criar um ponto final privado dentro do seu VNet e mapeá-lo para este serviço. Este artigo explica conceitos relacionados com o lado do prestador de serviços. 

## <a name="workflow"></a>Fluxo de trabalho

![Fluxo de trabalho de serviço private link](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Crie o seu Serviço de Ligação Privada

- Configure a sua aplicação para correr atrás de um equilibrador de carga padrão na sua rede virtual. Se já tem a sua aplicação configurada atrás de um equilibrador de carga padrão, pode saltar este passo.   
- Crie um Serviço de Ligação Privada que refira o equilibrador de carga acima. No processo de seleção do equilíbrio de carga, escolha a configuração IP frontal onde pretende receber o tráfego. Escolha uma sub-rede para endereços IP NAT para o Serviço de Ligação Privada. Recomenda-se que tenha pelo menos oito endereços IP NAT disponíveis na sub-rede. Todo o tráfego de consumidores parece ter origem neste conjunto de endereços IP privados para o prestador de serviços. Escolha as propriedades/configurações apropriadas para o Serviço de Ligação Privada.    

    > [!NOTE]
    > O Azure Private Link Service só é suportado no Standard Load Balancer. 
    
### <a name="share-your-service"></a>Partilhe o seu serviço

Depois de criar um serviço Private Link, o Azure gerará um nome exclusivo globalmente chamado "pseudónimo" com base no nome que fornece para o seu serviço. Pode partilhar o pseudónimo ou o recurso URI do seu serviço com os seus clientes offline. Os consumidores podem iniciar uma ligação de Private Link utilizando o pseudónimo ou o recurso URI.
 
### <a name="manage-your-connection-requests"></a>Gerencie os seus pedidos de ligação

Após o início de uma ligação por parte do consumidor, o prestador de serviços pode aceitar ou rejeitar o pedido de ligação. Todos os pedidos de ligação serão listados no âmbito da propriedade **privateendpointconnections** no serviço Private Link.
 
### <a name="delete-your-service"></a>Apague o seu serviço

Se o serviço Private Link já não estiver a ser utilizado, pode eliminá-lo. No entanto, antes de eliminar o serviço, certifique-se de que não existem ligações de ponto final privados associadas ao mesmo. Pode rejeitar todas as ligações e apagar o serviço.

## <a name="properties"></a>Propriedades

Um serviço de Link Privado especifica as seguintes propriedades: 

|Propriedade |Explicação  |
|---------|---------|
|Estado de provisionamento (Estado de provisionamento)  |Uma propriedade apenas para leitura que lista o estado de provisionamento atual para o serviço private link. Os estados de provisionamento aplicáveis são: "A pagando; Falhou; Conseguiu; Atualização". Quando o estado de provisionamento for "Bem sucedido", você disponibilizou com sucesso o seu serviço de Link Privado.        |
|Pseudónimo (pseudónimo)     | Alias é uma corda exclusiva para leitura globalmente única para o seu serviço. Ajuda-o a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de partilhar para o seu serviço. Ao criar um serviço Private Link, o Azure gera o pseudónimo para o seu serviço que pode partilhar com os seus clientes. Os seus clientes podem utilizar este pseudónimo para solicitar uma ligação ao seu serviço.          |
|Visibilidade (visibilidade)     | Visibilidade é a propriedade que controla as definições de exposição para o seu serviço Private Link. Os prestadores de serviços podem optar por limitar a exposição ao seu serviço a subscrições com permissões de controlo de acesso (RBAC) baseadas em papéis, um conjunto restrito de subscrições ou todas as subscrições do Azure.          |
|Aprovação automática (autoAprovação)    |   A homologação automática controla o acesso automatizado ao serviço Private Link. As assinaturas especificadas na lista de homologação são aprovadas automaticamente quando uma ligação é solicitada a partir de pontos finais privados nessas assinaturas.          |
|Configuração IP frontend do balanceor de carga (loadBalancerFrontendIpConfiguras)    |    O serviço Private Link está ligado ao endereço IP frontal de um Balancer de Carga Padrão. Todo o tráfego destinado ao serviço chegará à frente do SLB. Pode configurar as regras do SLB para direcionar este tráfego para piscinas de backend apropriadas onde as suas aplicações estão em execução. As configurações ip frontend do balanceor de carga são diferentes das configurações IP na NAT.      |
|Configuração IP NAT (ipConfigurations)    |    Esta propriedade refere-se à configuração IP NAT (Tradução de Endereços de Rede) para o serviço de Link Privado. O IP NAT pode ser escolhido a partir de qualquer subnet na rede virtual de um prestador de serviços. O serviço Private Link executa o lado de destino NAT-ing no tráfego de Private Link. Isto garante que não existe um conflito de IP entre o espaço de endereço de origem (do lado do consumidor) e o destino (prestador de serviços). Do lado do destino (lado do prestador de serviços), o endereço IP NAT aparecerá como FONTE IP para todos os pacotes recebidos pelo seu serviço e IP de destino para todos os pacotes enviados pelo seu serviço.       |
|Ligações de ponto final privado (privateEndpointConnections)     |  Esta propriedade lista os pontos finais privados que ligam ao serviço Private Link. Vários pontos finais privados podem ligar-se ao mesmo serviço private link e o prestador de serviços pode controlar o Estado para pontos finais individuais.        |
|TCP Proxy V2 (EnableProxyProtocol)     |  Este imóvel permite ao prestador de serviços utilizar o tcp proxy v2 para obter informações de ligação sobre o consumidor de serviços. O Prestador de Serviços é responsável pela criação de configs recetores para poder analisar o cabeçalho do protocolo proxy v2.        |
|||


### <a name="details"></a>Detalhes

- O serviço private link pode ser acedido a partir de pontos finais privados aprovados na mesma região. O ponto final privado pode ser alcançado a partir da mesma rede virtual, VNets com preços regionais, VNets globalmente espreitados e em instalações usando ligações privadas VPN ou ExpressRoute. 
 
- Ao criar um Serviço de Link Privado, é criada uma interface de rede para o ciclo de vida do recurso. Esta interface não é gerível pelo cliente.
 
- O Serviço de Ligação Privada deve ser implantado na mesma região que a rede virtual e o Standard Load Balancer.  
 
- Um único Serviço de Link Privado pode ser acedido a partir de vários Pontos Finais Privados pertencentes a diferentes VNets, subscrições e/ou inquilinos de Diretório Ativo. A ligação é estabelecida através de um fluxo de trabalho de ligação. 
 
- Vários serviços de Link Privado podem ser criados no mesmo Balancer de Carga Padrão utilizando diferentes configurações IP front-end. Existem limites para o número de serviços de Link Privado que pode criar por Standard Load Balancer e por subscrição. Para mais detalhes, consulte [os limites de Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)
 
- O serviço Private Link pode ter mais de uma configuração IP NAT ligada a ele. Escolher mais do que uma configuração IP NAT pode ajudar os prestadores de serviços a escalar. Hoje em dia, os prestadores de serviços podem atribuir até oito endereços IP NAT por serviço private link. Com cada endereço IP NAT, pode atribuir mais portas para as suas ligações TCP e, assim, escalar para fora. Depois de adicionar vários endereços IP NAT a um serviço de Link Privado, não pode eliminar os endereços IP NAT. Isto é feito para garantir que as ligações ativas não sejam afetadas ao mesmo tempo que se apagando os endereços IP NAT.


## <a name="alias"></a>Alias

**Alias** é um nome globalmente único para o seu serviço. Ajuda-o a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de partilhar para o seu serviço. Ao criar um serviço Private Link, o Azure gera um pseudónimo para o seu serviço que pode partilhar com os seus clientes. Os seus clientes podem utilizar este pseudónimo para solicitar uma ligação ao seu serviço.

O pseudónimo é composto por três partes: *Prefixo*. *GUIA.* *Sufixo*

- Prefixo é o nome do serviço. Pode escolher o seu próprio prefixo. Depois de criado o "Alias", não pode mudá-lo, por isso selecione o prefixo adequadamente.  
- O GUID será fornecido pela plataforma. Isto ajuda a tornar o nome globalmente único. 
- Sufixo é anexado por Azure: *região*.azure.privatelinkservice 

Pseudónimo completo: *Prefixo*. {GUID}. *região*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Exposição ao serviço de controlo

O serviço Private Link oferece-lhe opções para controlar a exposição do seu serviço através da definição de "Visibilidade". Você pode tornar o serviço privado para consumo de diferentes VNets que você possui (apenas permissões RBAC), restringir a exposição a um conjunto limitado de subscrições em que você confia, ou torná-lo público para que todas as subscrições Azure possam solicitar conexões no Private Link serviço. As suas definições de visibilidade decidem se um consumidor pode ou não ligar-se ao seu serviço. 

## <a name="control-service-access"></a>Acesso ao serviço de controlo

Os consumidores que tenham exposição (controlada por definição de visibilidade) ao seu serviço Private Link podem criar um ponto final privado nos seus VNets e solicitar uma ligação ao seu serviço de Link Privado. A ligação de ponto final privado será criada num estado "Pendente" no objeto de serviço private link. O prestador de serviços é responsável por agir sobre o pedido de ligação. Pode aprovar a ligação, rejeitar a ligação ou apagar a ligação. Apenas as ligações que são aprovadas podem enviar tráfego para o serviço Private Link.

A ação de aprovação das ligações pode ser automatizada utilizando a propriedade de aprovação automática no serviço Private Link. A Aprovação Automática é uma capacidade para os prestadores de serviços pré-aprovarem um conjunto de subscrições para acesso automatizado ao seu serviço. Os clientes terão de partilhar as suas subscrições offline para os prestadores de serviços adicionarem à lista de aprovação automática. A homologação automática é um subconjunto da matriz de visibilidade. A visibilidade controla as definições de exposição, enquanto a aprovação automática controla as definições de aprovação para o seu serviço. Se um cliente solicitar uma ligação a partir de uma subscrição na lista de homologação, a ligação é automaticamente aprovada e a ligação é estabelecida. Os prestadores de serviços já não precisam de aprovar manualmente o pedido. Por outro lado, se um cliente solicitar uma ligação a partir de uma subscrição no conjunto de visibilidade e não no matriz de aprovação automática, o pedido chegará ao prestador de serviços, mas o prestador de serviços tem de aprovar manualmente as ligações.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Obtenção de informações de ligação utilizando O Proxy TCP v2

Ao utilizar o serviço de ligação privada, o endereço IP fonte dos pacotes provenientes de ponto final privado é o endereço de rede traduzido (NAT) do lado do prestador de serviços utilizando o IP NAT atribuído a partir da rede virtual do fornecedor. Assim, as aplicações recebem o endereço IP NAT atribuído em vez do endereço IP de origem real dos consumidores de serviços. Se a sua aplicação necessitar de endereço IP de origem real do lado do consumidor, pode ativar o protocolo Proxy no seu serviço e obter a informação do cabeçalho do protocolo proxy. Além do endereço IP de origem, o cabeçalho do protocolo proxy também transporta o LinkID do ponto final privado. A combinação de endereço IP de origem e LinkID pode ajudar os prestadores de serviços a identificar exclusivamente os seus consumidores. Para mais informações sobre o Protocolo proxy, visite [aqui](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Esta informação é codificada utilizando um vetor personalizado de valor de tipo (TLV) da seguinte forma:

Detalhes personalizados da TLV:

|Campo |Comprimento (Octets)  |Descrição  |
|---------|---------|----------|
|Tipo  |1        |PP2_TYPE_AZURE (0xEE)|
|Comprimento  |2      |Comprimento do valor|
|Valor  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) que representam o LINKID do ponto final privado. Codificado em formato endiano.|

 > [!NOTE]
 > O prestador de serviços é responsável por se certificar de que o serviço por detrás do equilibrador de carga padrão está configurado para analisar o cabeçalho do protocolo de procuração de acordo com a [especificação](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) quando o protocolo de procuração é ativado no serviço de ligação privada. O pedido falhará se a definição do protocolo proxy estiver ativada no serviço de ligação privada, mas o serviço do prestador de serviços não está configurado para analisar o cabeçalho. Da mesma forma, o pedido falhará se o serviço do prestador de serviços estiver à espera de um cabeçalho de protocolo de procuração enquanto a definição não estiver ativada no serviço de ligação privada. Uma vez ativada a definição do protocolo proxy, o cabeçalho do protocolo proxy também será incluído em sondas de saúde HTTP/TCP do hospedeiro às máquinas virtuais de backend, mesmo que não haja informações do cliente no cabeçalho. 

## <a name="limitations"></a>Limitações

Seguem-se as limitações conhecidas ao utilizar o serviço Private Link:
- Suportado apenas no Equilíbrio de Carga Padrão 
- Suporta apenas o tráfego IPv4
- Suporta apenas o tráfego da TCP

## <a name="next-steps"></a>Passos seguintes
- [Criar um serviço de ligação privada usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Criar um serviço de ligação privada utilizando o Azure CLI](create-private-link-service-cli.md)
