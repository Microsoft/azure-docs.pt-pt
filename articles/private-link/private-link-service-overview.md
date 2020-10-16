---
title: O que é o serviço Azure Private Link?
description: Saiba mais sobre o serviço Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: a6bbb2abe24eba96fd2c55b7aaf15ccd8ae33530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760957"
---
# <a name="what-is-azure-private-link-service"></a>O que é o serviço Azure Private Link?

O serviço Azure Private Link é a referência ao seu próprio serviço que é alimentado pela Azure Private Link. O seu serviço que está a funcionar atrás do [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) pode ser ativado para acesso private Link para que os consumidores do seu serviço possam aceder a ele em privado a partir dos seus próprios VNets. Os seus clientes podem criar um ponto final privado dentro do seu VNet e mapeá-lo para este serviço. Este artigo explica conceitos relacionados com o lado do prestador de serviços. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Fluxo de trabalho do serviço de ligação privada" border="true":::

*Figura: Azure Private Link Service.*

## <a name="workflow"></a>Fluxo de trabalho

![Fluxo de trabalho do serviço private link](media/private-link-service-overview/private-link-service-workflow.png)


*Figura: Fluxo de trabalho do serviço Azure Private Link.*

### <a name="create-your-private-link-service"></a>Crie o seu Serviço de Ligação Privada

- Configure a sua aplicação para correr atrás de um balanceador de carga padrão na sua rede virtual. Se já tiver a sua aplicação configurada atrás de um balanceador de carga padrão, pode saltar este passo.   
- Crie um Serviço de Ligação Privada referindo o balançador de carga acima. No processo de seleção do balançador de carga, escolha a configuração IP frontal onde pretende receber o tráfego. Escolha uma sub-rede para endereços IP NAT para o Serviço de Ligação Privada. Recomenda-se ter pelo menos oito endereços NAT IP disponíveis na sub-rede. Todo o tráfego de consumidores parece ter origem neste conjunto de endereços IP privados para o prestador de serviços. Escolha as propriedades/configurações adequadas para o Serviço de Ligação Privada.    

    > [!NOTE]
    > O Serviço de Ligação Privada Azure só é suportado no Balancer de Carga Padrão. 
    
### <a name="share-your-service"></a>Partilhe o seu serviço

Depois de criar um serviço private link, o Azure gerará um nome globalmente único chamado "pseudónimo" com base no nome que fornece para o seu serviço. Pode partilhar o pseudónimo ou recurso URI do seu serviço com os seus clientes offline. Os consumidores podem iniciar uma ligação private link usando o pseudónimo ou o recurso URI.
 
### <a name="manage-your-connection-requests"></a>Gerencie os seus pedidos de ligação

Depois de um consumidor iniciar uma ligação, o prestador de serviços pode aceitar ou rejeitar o pedido de ligação. Todos os pedidos de ligação serão listados na propriedade **privateendpointconnections** no serviço Private Link.
 
### <a name="delete-your-service"></a>Elimine o seu serviço

Se o serviço Private Link já não estiver a ser utilizado, pode eliminá-lo. No entanto, antes de apagar o serviço, certifique-se de que não existem ligações privadas de ponto final associadas ao mesmo. Pode rejeitar todas as ligações e apagar o serviço.

## <a name="properties"></a>Propriedades

Um serviço de Ligação Privada especifica as seguintes propriedades: 

|Propriedade |Explicação  |
|---------|---------|
|Estado de provisionamento (Estado de provisionamento)  |Uma propriedade apenas de leitura que lista o estado atual de fornecimento para o serviço Private Link. Os estados de provisionamento aplicáveis são: "Supressão; Falhou; Conseguiu; Atualização". Quando o estado de provisionamento é "Bem sucedido", você tem conseguido o seu serviço de Ligação Privada.        |
|Pseudónimo (pseudónimo)     | O pseudónimo é uma cadeia de leitura única globalmente única para o seu serviço. Ajuda-o a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de partilhar para o seu serviço. Ao criar um serviço Private Link, o Azure gera o pseudónimo para o seu serviço que pode partilhar com os seus clientes. Os seus clientes podem usar este pseudónimo para solicitar uma ligação ao seu serviço.          |
|Visibilidade (visibilidade)     | A visibilidade é a propriedade que controla as definições de exposição para o seu serviço Private Link. Os prestadores de serviços podem optar por limitar a exposição ao seu serviço a subscrições com permissões de controlo de acesso baseado em funções (RBAC), um conjunto restrito de subscrições ou todas as subscrições da Azure.          |
|Aprovação automática (autoApproval)    |   A homologação automática controla o acesso automatizado ao serviço Private Link. As subscrições especificadas na lista de homologação automática são aprovadas automaticamente quando é solicitada uma ligação a partir de pontos finais privados nessas subscrições.          |
|Configuração IP frontend do balançador de carga (loadBalancerFrontendIpConfigurations)    |    O serviço Private Link está ligado ao endereço IP frontend de um Balancer de Carga Padrão. Todo o tráfego destinado ao serviço chegará à frente do SLB. Pode configurar as regras SLB para direcionar este tráfego para piscinas de backend apropriadas onde as suas aplicações estão em execução. As configurações IP do frontend do balançador de carga são diferentes das configurações IP do NAT.      |
|Configuração IP NAT (ipConfigurations)    |    Esta propriedade refere-se à configuração IP NAT (Network Address Translation) para o serviço Private Link. O NAT IP pode ser escolhido a partir de qualquer sub-rede na rede virtual de um prestador de serviços. O serviço Private Link realiza o lado do destino NAT-ing no tráfego de Ligação Privada. Isto garante que não existe um conflito IP entre o espaço de endereço de origem (lado do consumidor) e destino (prestador de serviços). No lado do destino (lado do prestador de serviços), o endereço NAT IP aparecerá como SOURCE IP para todos os pacotes recebidos pelo seu serviço e destino IP para todos os pacotes enviados pelo seu serviço.       |
|Conexões de ponto final privado (privateEndpointConnections)     |  Esta propriedade lista os pontos finais privados que ligam ao serviço Private Link. Vários pontos finais privados podem ligar-se ao mesmo serviço private Link e o prestador de serviços pode controlar o estado para pontos finais privados individuais.        |
|TCP Proxy V2 (EnableProxyProtocol)     |  Esta propriedade permite ao prestador de serviços utilizar o proxy v2 da TCP para obter informações de ligação sobre o consumidor de serviços. O Fornecedor de Serviços é responsável pela configuração dos configs recetores para poder analisar o cabeçalho do protocolo de procuração v2.        |
|||


### <a name="details"></a>Detalhes

- O serviço Private Link pode ser acedido a partir de pontos finais privados aprovados em qualquer região pública. O ponto final privado pode ser alcançado a partir da mesma rede virtual, VNets de observação regional, VNets de olhos globais e em instalações usando ligações privadas VPN ou ExpressRoute. 
 
- Ao criar um Serviço de Ligação Privada, é criada uma interface de rede para o ciclo de vida do recurso. Esta interface não é gerível pelo cliente.
 
- O Serviço de Ligação Privada deve ser implantado na mesma região que a rede virtual e o Balanceador de Carga Padrão.  
 
- Um único Serviço de Ligação Privada pode ser acedido a partir de vários Pontos Finais Privados pertencentes a diferentes VNets, subscrições e/ou inquilinos do Ative Directory. A ligação é estabelecida através de um fluxo de trabalho de ligação. 
 
- Vários serviços de Ligação Privada podem ser criados no mesmo Balanceador de Carga Padrão utilizando diferentes configurações IP frontais. Existem limites para o número de serviços de Private Link que pode criar por Balancer de Carga Padrão e por subscrição. Para mais detalhes, consulte [os limites de Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)
 
- O serviço Private Link pode ter mais de uma configuração NAT IP ligadas a ele. Escolher mais do que uma configuração DE IP NAT pode ajudar os prestadores de serviços a escalar. Hoje em dia, os prestadores de serviços podem atribuir até oito endereços IP NAT por serviço private Link. A cada endereço NAT IP, pode atribuir mais portas para as suas ligações TCP e, assim, escalonar. Depois de adicionar vários endereços IP NAT a um serviço de Ligação Privada, não é possível eliminar os endereços IP DO NAT. Isto é feito para garantir que as ligações ativas não sejam impactadas enquanto eliminam os endereços IP NAT.


## <a name="alias"></a>Alias

**O pseudónimo** é um nome globalmente único para o seu serviço. Ajuda-o a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de partilhar para o seu serviço. Ao criar um serviço Private Link, o Azure gera um pseudónimo para o seu serviço que pode partilhar com os seus clientes. Os seus clientes podem usar este pseudónimo para solicitar uma ligação ao seu serviço.

O pseudónimo é composto por três partes: *Prefix.* *GUID*. *Sufixo*

- Prefixo é o nome de serviço. Pode escolher o seu próprio prefixo. Depois de criar "Alias", não pode alterá-lo, por isso selecione o seu prefixo adequadamente.  
- O GUID será fornecido pela plataforma. Isto ajuda a tornar o nome globalmente único. 
- O sufixo é anexado pelo Azure: *região*.azure.privatelinkservice 

Pseudónimo completo:  *Prefixo*. {GUID}. *região*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Exposição ao serviço de controlo

O serviço Private Link fornece-lhe opções para controlar a exposição do seu serviço através da definição "Visibilidade". Pode tornar o serviço privado para consumo a partir de diferentes VNets que possui (apenas permissões RBAC), restringir a exposição a um conjunto limitado de subscrições em que confia, ou torná-lo público para que todas as subscrições do Azure possam solicitar ligações no serviço Private Link. As suas definições de visibilidade decidem se um consumidor pode ou não ligar-se ao seu serviço. 

## <a name="control-service-access"></a>Acesso ao serviço de controlo

Os consumidores que tenham exposição (controlada por definição de visibilidade) ao seu serviço Private Link podem criar um ponto final privado nos seus VNets e solicitar uma ligação ao seu serviço De Ligação Privada. A ligação de ponto final privado será criada num estado "pendente" no objeto de serviço Private Link. O prestador de serviços é responsável por agir sobre o pedido de ligação. Pode aprovar a ligação, rejeitar a ligação ou eliminar a ligação. Só as ligações aprovadas podem enviar tráfego para o serviço Private Link.

A ação de aprovação das ligações pode ser automatizada utilizando a propriedade de auto-aprovação no serviço Private Link. A Aprovação Automática é uma capacidade para os prestadores de serviços pré-aprovarem um conjunto de subscrições para acesso automatizado ao seu serviço. Os clientes terão de partilhar as suas subscrições offline para que os prestadores de serviços acrescentem à lista de auto-aprovação. A aprovação automática é um subconjunto do conjunto de visibilidade. A visibilidade controla as definições de exposição, enquanto a homologação automática controla as definições de aprovação do seu serviço. Se um cliente solicitar uma ligação a partir de uma subscrição na lista de homologação automática, a ligação é automaticamente aprovada e a ligação é estabelecida. Os prestadores de serviços já não precisam de aprovar manualmente o pedido. Por outro lado, se um cliente solicitar uma ligação a partir de uma subscrição no conjunto de visibilidade e não no conjunto de homologação automática, o pedido chegará ao prestador de serviços, mas o prestador de serviços tem de aprovar manualmente as ligações.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Obtenção de informação de ligação usando TCP Proxy v2

Ao utilizar o serviço de ligação privada, o endereço IP de origem dos pacotes provenientes do ponto final privado é o endereço de rede traduzido (NAT) do lado do prestador de serviços utilizando o NAT IP atribuído a partir da rede virtual do fornecedor. Assim, as aplicações recebem o endereço IP NAT atribuído em vez do endereço IP de origem real dos consumidores de serviço. Se a sua aplicação necessitar de um endereço IP de origem real do lado do consumidor, pode ativar o protocolo Proxy no seu serviço e recuperar as informações do cabeçalho do protocolo de procuração. Além do endereço IP de origem, o cabeçalho do protocolo proxy também transporta o LinkID do ponto final privado. A combinação do endereço IP de origem e do LinkID pode ajudar os prestadores de serviços a identificarem exclusivamente os seus consumidores. Para mais informações sobre o Proxy Protocol, visite [aqui.](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) 

Esta informação é codificada usando um vetor personalizado de valor de comprimento de tipo (TLV) da seguinte forma:

Detalhes personalizados da TLV:

|Campo |Comprimento (Octetos)  |Descrição  |
|---------|---------|----------|
|Tipo  |1        |PP2_TYPE_AZURE (0xEE)|
|Comprimento  |2      |Comprimento do valor|
|Valor  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) representando o LINKID do ponto final privado. Codificado em pequeno formato endiano.|

 > [!NOTE]
 > O prestador de serviços é responsável por garantir que o serviço por trás do balanceador de carga padrão está configurado para analisar o cabeçalho do protocolo de procuração de acordo com a [especificação](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) quando o protocolo de procuração é ativado no serviço de ligação privada. O pedido falhará se a definição do protocolo de procuração estiver ativada no serviço de ligação privada, mas o serviço do prestador de serviços não está configurado para analisar o cabeçalho. Da mesma forma, o pedido falhará se o serviço do prestador de serviços estiver à espera de um cabeçalho de protocolo de procuração enquanto a definição não estiver ativada no serviço de ligação privada. Uma vez ativada a definição do protocolo proxy, o cabeçalho do protocolo proxy também será incluído nas sondas de saúde HTTP/TCP do anfitrião para as máquinas virtuais de backend, mesmo que não haja informações do cliente no cabeçalho. 

## <a name="limitations"></a>Limitações

Seguem-se as limitações conhecidas ao utilizar o serviço Private Link:
- Suportado apenas no Balanceador de Carga Padrão 
- Suporta apenas o tráfego IPv4
- Suporta apenas o tráfego TCP

## <a name="next-steps"></a>Passos seguintes
- [Criar um serviço de ligação privada utilizando a Azure PowerShell](create-private-link-service-powershell.md)
- [Criar um serviço de ligação privada utilizando o Azure CLI](create-private-link-service-cli.md)
