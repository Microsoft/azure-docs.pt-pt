---
title: Segurança da rede para recursos da Rede de Eventos Azure
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300157"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Segurança da rede para recursos da Rede de Eventos Azure
Este artigo descreve como usar as seguintes funcionalidades de segurança com a Grelha de Eventos Azure: 

- Etiquetas de serviço para saída (pré-visualização)
- Regras ip Firewall para ingresso (pré-visualização)
- Pontos finais privados para ingresso (pré-visualização)


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para mais informações sobre etiquetas de serviço, consulte a visão geral das [etiquetas](../virtual-network/service-tags-overview.md)de serviço .

Pode utilizar etiquetas de serviço para definir controlos de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou [firewall Azure](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Especificando o nome da etiqueta de serviço (por exemplo, **AzureEventGrid)** no campo de *origem* ou *destino* apropriado de uma regra, pode permitir ou negar o tráfego do serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com firewall Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Grelha de Eventos Azure. <br/><br/>*Nota:* Esta etiqueta abrange pontos finais da Azure Event Grid nos EUA South Central, US East, US East 2, US West 2 e US Central apenas. | Ambos | Não | Não |


## <a name="ip-firewall"></a>Firewall de IP 
A Azure Event Grid suporta controlos de acesso baseados em IP para publicação em tópicos e domínios. Com controlos baseados em IP, pode limitar os editores a um tópico ou domínio a apenas um conjunto de máquinas e serviços em nuvem aprovados. Esta funcionalidade complementa os mecanismos de autenticação suportados pela Rede de [Eventos.](security-authentication.md)

Por predefinição, o tópico e o domínio são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IP ou intervalos de endereços IP na notação [CIDR (Encaminhamento inter-domínio sem classe).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida).


## <a name="private-endpoints"></a>Pontos finais privados
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir a entrada de eventos diretamente da sua rede virtual para os seus tópicos e domínios de forma segura através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. Um ponto final privado é uma interface de rede especial para um serviço Azure no seu VNet. Ao criar um ponto final privado para o seu tópico ou domínio, proporciona uma conectividade segura entre os clientes no seu VNet e o seu recurso Event Grid. O ponto final privado é atribuído um endereço IP da gama de endereços IP do seu VNet. A ligação entre o ponto final privado e o serviço Event Grid utiliza um link privado seguro.

![Diagrama da arquitetura](./media/network-security/architecture-diagram.png)

A utilização de pontos finais privados para o seu recurso Da Grelha de Eventos permite-lhe:

- Acesso seguro ao seu tópico ou domínio a partir de um VNet através da rede de espinha dorsal da Microsoft em oposição à internet pública.
- Ligue-se de forma segura a partir de redes no local que se ligam à VNet utilizando VPN ou ExpressRoutes com o peering privado.

Quando cria um ponto final privado para um tópico ou domínio no seu VNet, é enviado um pedido de consentimento para aprovação ao proprietário do recurso. Se o utilizador que solicita a criação do ponto final privado for também proprietário do recurso, este pedido de consentimento é automaticamente aprovado. Caso contrário, a ligação está em **estado pendente** até ser aprovada. As aplicações no VNet podem ligar-se ao serviço Event Grid sobre o ponto final privado sem problemas, utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma. Os proprietários de recursos podem gerir os pedidos de consentimento e os pontos finais privados, através do separador **Private endpoints** para o recurso no portal Azure.

### <a name="connect-to-private-endpoints"></a>Ligar a pontos finais privados
As editoras de um VNet que utilizem o ponto final privado devem utilizar a mesma cadeia de ligação para o tópico ou domínio que os clientes que se ligam ao ponto final público. A resolução dNS direciona automaticamente as ligações da VNet para o tópico ou domínio sobre um link privado. A Rede de Eventos cria uma [zona Privada de DNS](../dns/private-dns-overview.md) anexada ao VNet com a atualização necessária para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de fazer alterações adicionais na configuração do DNS.

### <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos finais privados
Quando se cria um ponto final privado, o registo DNS CNAME para o recurso `privatelink`é atualizado para um pseudónimo num subdomínio com prefixo . Por padrão, é criada uma zona privada de DNS que corresponde ao subdomínio do link privado. 

Quando resolve o tópico ou o url final do ponto final de domínio de fora da VNet com o ponto final privado, resolve-se até ao ponto final público do serviço. Os registos de recursos dNS para 'topicA', quando resolvidos **de fora da VNet** que acolhe o ponto final privado, serão:

| Nome                                          | Tipo      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<perfil de gestor de tráfego azure\>

Pode negar ou controlar o acesso a um cliente fora do VNet através do ponto final público utilizando a [firewall IP](#ip-firewall). 

Quando resolvido a partir do VNet que acolhe o ponto final privado, o URL de ponto final de tópico ou domínio resolve-se para o endereço IP do ponto final privado. Os registos de recursos do DNS para o tópico 'topicA', quando resolvidos a partir de **dentro da VNet** que acolhe o ponto final privado, serão:

| Nome                                          | Tipo      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Esta abordagem permite o acesso ao tópico ou domínio utilizando a mesma cadeia de ligação para clientes no VNet que acolhe os pontos finais privados, e clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes podem resolver o FQDN para o tópico ou ponto final de domínio para o endereço IP do ponto final privado. Configure o seu servidor DNS para delegar o seu subdomínio de ligação privada `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` na zona privada de DNS para o VNet, ou configurar os registos A com o endereço IP final do ponto final privado.

O nome de zona `privatelink.eventgrid.azure.net`DNS recomendado é .

### <a name="private-endpoints-and-publishing"></a>Pontos finais privados e publicação

O quadro seguinte descreve os vários estados da ligação de ponto final privado e os efeitos na publicação:

| Estado de Ligação   |  Publicação com sucesso (Sim/Não) |
| ------------------ | -------------------------------|
| Aprovado           | Sim                            |
| Rejected           | Não                             |
| Pendente            | Não                             |
| Desligado       | Não                             |

Para que a publicação seja bem sucedida, o estado de ligação de pontofinal privado deve ser **aprovado.** Se uma ligação for rejeitada, não pode ser aprovada através do portal Azure. A única possibilidade é eliminar a ligação e criar uma nova.

## <a name="pricing-and-quotas"></a>Preços e quotas
**Os pontos finais privados** só estão disponíveis com tópicos e domínios de nível premium. A Grelha de Eventos permite que até 64 ligações de ponto final privados sejam criadas por tópico ou domínio. Para atualizar de nível básico para nível premium, consulte o artigo do nível de [preços da Atualização.](update-tier.md)

A funcionalidade **IP Firewall** está disponível tanto nos níveis básicos como premium da Grelha de Eventos. Permitimos que até 16 regras ip firewall sejam criadas por tópico ou domínio.


## <a name="next-steps"></a>Passos seguintes
Pode configurar a firewall IP para o seu recurso Da Rede de Eventos para restringir o acesso através da internet pública a partir de apenas um conjunto selecionado de endereços IP ou intervalos ip Address. Para obter instruções passo a passo, consulte a [firewall IP configurar](configure-firewall.md).

Pode configurar pontos finais privados para restringir o acesso a partir de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [os pontos finais privados da Configuração](configure-private-endpoints.md).
