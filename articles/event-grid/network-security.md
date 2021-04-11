---
title: Segurança da rede para recursos da Grelha de Eventos Azure
description: Este artigo descreve como usar tags de serviço para saídas, regras de firewall IP para entrada e pontos finais privados para entrada com Azure Event Grid.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: d58e8b5ce9fb444fa501f897cca722613d9c51fe
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967579"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Segurança da rede para recursos da Grelha de Eventos Azure
Este artigo descreve como utilizar as seguintes funcionalidades de segurança com a Azure Event Grid: 

- Etiquetas de serviço para saídas
- Regras ip firewall para entrada
- Pontos finais privados para entrada


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre etiquetas de serviço, consulte [a visão geral das etiquetas de serviço.](../virtual-network/service-tags-overview.md)

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (por exemplo, **AzureEventGrid)** no campo de *origem* ou *destino* adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Grelha de Eventos Azure. | Ambos | No | No |


## <a name="ip-firewall"></a>Firewall de IP 
A Azure Event Grid suporta controlos de acesso baseados em IP para publicação de tópicos e domínios. Com controlos baseados em IP, pode limitar os editores a um tópico ou domínio a apenas um conjunto de máquinas e serviços de nuvem aprovados. Esta funcionalidade complementa os [mecanismos de autenticação suportados](security-authentication.md) pela Grade de Eventos.

Por predefinição, o tópico e o domínio estão acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IP ou intervalos de endereços IP na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida).

Para obter instruções passo a passo para configurar firewall IP para tópicos e domínios, consulte [a firewall IP configurar](configure-firewall.md).

## <a name="private-endpoints"></a>Pontos finais privados
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir ingresss de eventos diretamente da sua rede virtual para seus tópicos e domínios de forma segura através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. Um ponto final privado é uma interface de rede especial para um serviço Azure no seu VNet. Quando cria um ponto final privado para o seu tópico ou domínio, proporciona uma conectividade segura entre os clientes no seu VNet e o seu recurso Desempaço de Evento. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereço IP do seu VNet. A ligação entre o ponto final privado e o serviço Deresecação de Eventos utiliza uma ligação privada segura.

![Diagrama da arquitetura](./media/network-security/architecture-diagram.png)

A utilização de pontos finais privados para o seu recurso Desemboado permite-lhe:

- Acesso seguro ao seu tópico ou domínio a partir de um VNet sobre a rede de espinha dorsal da Microsoft em oposição à internet pública.
- Conectar-se de forma segura a partir de redes no local que se conectam ao VNet utilizando VPN ou Express Routes com o peering privado.

Quando cria um ponto final privado para um tópico ou domínio no seu VNet, é enviado um pedido de consentimento para aprovação ao titular do recurso. Se o utilizador que solicita a criação do ponto final privado for também proprietário do recurso, este pedido de consentimento é automaticamente aprovado. Caso contrário, a ligação está em estado **pendente** até ser aprovada. As aplicações no VNet podem ligar-se ao serviço de Grade de Eventos através do ponto final privado de forma perfeita, utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma. Os proprietários de recursos podem gerir os pedidos de consentimento e os pontos finais privados, através do **separador pontos finais Privados** para o recurso no portal Azure.

### <a name="connect-to-private-endpoints"></a>Ligar-se a pontos finais privados
Os editores de um VNet que utilizem o ponto final privado devem utilizar a mesma cadeia de ligação para o tópico ou domínio que os clientes que se ligam ao ponto final público. A resolução DNS liga automaticamente as ligações do VNet para o tópico ou domínio sobre um link privado. A Grade de Eventos cria uma [zona privada de DNS](../dns/private-dns-overview.md) anexada ao VNet com a atualização necessária para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de es fazer alterações adicionais na sua configuração DNS.

### <a name="dns-changes-for-private-endpoints"></a>DNS alterações para pontos finais privados
Quando se cria um ponto final privado, o registo DNS CNAME para o recurso é atualizado para um pseudónimo num subdomínio com o prefixo `privatelink` . Por padrão, é criada uma zona de DNS privada que corresponde ao subdomínio do link privado. 

Quando resolve o URL de ponto final tópico ou de ponto final de domínio de fora do VNet com o ponto final privado, ele resolve para o ponto final público do serviço. Os registos de recursos DNS para 'topicA', quando resolvidos de fora do **VNet** que hospeda o ponto final privado, serão:

| Nome                                          | Tipo      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Pode negar ou controlar o acesso a um cliente fora do VNet através do ponto final público utilizando a [firewall IP](#ip-firewall). 

Quando resolvido a partir do VNet que hospeda o ponto final privado, o URL de ponto final tópico ou domínio resolve-se para o endereço IP do ponto final privado. Os registos de recursos DNS para o tópico 'topicA', quando resolvidos a partir de **dentro do VNet** que alberga o ponto final privado, serão:

| Nome                                          | Tipo      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Esta abordagem permite o acesso ao tópico ou domínio utilizando a mesma cadeia de ligação para clientes no VNet que hospeda os pontos finais privados e clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes podem resolver o FQDN para o tópico ou ponto final de domínio para o endereço IP do ponto final privado. Configure o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet, ou configure os registos A para `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` com o endereço IP do ponto final privado.

O nome recomendado da zona DNS é `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Pontos finais privados e publicações

A tabela que se segue descreve os vários estados da ligação privada ao ponto final e os efeitos na publicação:

| Estado de Ligação   |  Publicar com sucesso (Sim/Não) |
| ------------------ | -------------------------------|
| Aprovado           | Yes                            |
| Rejeitado           | No                             |
| Pendente            | No                             |
| Desligado       | No                             |

Para que a publicação seja bem sucedida, o estado de ligação de ponto final privado deve ser **aprovado**. Se uma ligação for rejeitada, não pode ser aprovada usando o portal Azure. A única possibilidade é apagar a ligação e criar uma nova.

## <a name="pricing-and-quotas"></a>Preços e quotas
**Os pontos finais privados** estão disponíveis nos níveis básicos e premium da Grade de Eventos. A Grade de Eventos permite criar até 64 ligações privadas de ponto final por tópico ou domínio. 

**A** funcionalidade IP Firewall está disponível nos níveis básico e premium da Grade de Eventos. Permitimos criar até 16 regras ip firewall por tópico ou domínio.

## <a name="next-steps"></a>Passos seguintes
Pode configurar firewall IP para o seu recurso Desempaçar o acesso através da internet pública a partir de apenas um conjunto selecionado de endereços IP ou intervalos de endereços IP. Para obter instruções passo a passo, consulte [a firewall IP configurar](configure-firewall.md).

Pode configurar pontos finais privados para restringir o acesso apenas a partir de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [pontos finais privados configure](configure-private-endpoints.md).

Para resolver problemas de conectividade da rede, consulte [problemas de conectividade da rede troubleshoot](troubleshoot-network-connectivity.md)
