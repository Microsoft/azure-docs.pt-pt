---
title: Como funciona o Gerenciador de tráfego do Azure | Microsoft Docs
description: Este artigo o ajudará a entender como o Traffic Manager roteia o tráfego para alto desempenho e disponibilidade de seus aplicativos Web
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 281e1e591d7c3cc31b77a116fb42af49dc27798c
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312138"
---
# <a name="how-traffic-manager-works"></a>Como funciona o Gerenciador de tráfego

O Gerenciador de tráfego do Azure permite controlar a distribuição de tráfego entre os pontos de extremidade do aplicativo. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gerenciador de tráfego fornece dois benefícios principais:

- Distribuição de tráfego de acordo com um dos vários [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)
- [Monitoramento contínuo da integridade do ponto de extremidade](traffic-manager-monitoring.md) e failover automático quando os pontos de extremidade falharem

Quando um cliente tenta se conectar a um serviço, ele deve primeiro resolver o nome DNS do serviço para um endereço IP. Em seguida, o cliente se conecta a esse endereço IP para acessar o serviço.

**O ponto mais importante a entender é que o Gerenciador de tráfego funciona no nível do DNS.**  O Gerenciador de tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam **diretamente**ao ponto de extremidade selecionado. O Gerenciador de tráfego não é um proxy ou um gateway. O Gerenciador de tráfego não vê o tráfego que passa entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo do Gerenciador de tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. A URL para este portal é https://partners.contoso.com/login.aspx. O aplicativo é hospedado em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, elas usam o Gerenciador de tráfego para distribuir o tráfego do cliente para o ponto de extremidade mais próximo disponível.

Para obter essa configuração, eles concluem as seguintes etapas:

1. Implante três instâncias de seu serviço. Os nomes DNS dessas implantações são ' contoso-us.cloudapp.net ', ' contoso-eu.cloudapp.net ' e ' contoso-asia.cloudapp.net '.
1. Crie um perfil do Gerenciador de tráfego, chamado ' contoso.trafficmanager.net ', e configure-o para usar o método de roteamento de tráfego de ' desempenho ' nos três pontos de extremidade.
1. Configure seu nome de domínio intuitivo, ' partners.contoso.com ', para apontar para ' contoso.trafficmanager.net ', usando um registro DNS CNAME.

![Configuração de DNS do Gerenciador de tráfego][1]

> [!NOTE]
> Ao usar um domínio intuitivo com o Gerenciador de tráfego do Azure, você deve usar um CNAME para apontar o nome de domínio do intuitivo para o nome de domínio do Traffic Manager. Os padrões de DNS não permitem que você crie um CNAME no ' Apex ' (ou raiz) de um domínio. Portanto, você não pode criar um CNAME para ' contoso.com ' (às vezes chamado de domínio ' naked '). Você só pode criar um CNAME para um domínio em ' contoso.com ', como ' www.contoso.com '. Para contornar essa limitação, é recomendável hospedar seu domínio DNS no [DNS do Azure](../dns/dns-overview.md) e usar [registros de alias](../dns/tutorial-alias-tm.md) para apontar para o perfil do Gerenciador de tráfego. Como alternativa, você pode usar um redirecionamento HTTP simples para direcionar solicitações para ' contoso.com ' para um nome alternativo, como ' www.contoso.com '.

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gerenciador de tráfego

Continuando do exemplo anterior, quando um cliente solicita a página https://partners.contoso.com/login.aspx, o cliente executa as seguintes etapas para resolver o nome DNS e estabelecer uma conexão:

![Estabelecimento de conexão usando o Gerenciador de tráfego][2]

1. O cliente envia uma consulta DNS para seu serviço DNS recursivo configurado para resolver o nome ' partners.contoso.com '. Um serviço DNS recursivo, às vezes chamado de serviço "DNS local", não hospeda domínios DNS diretamente. Em vez disso, o cliente não carrega o trabalho de entrar em contato com os vários serviços DNS autoritativos na Internet necessários para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo localiza os servidores de nomes para o domínio ' contoso.com '. Em seguida, ele contata os servidores de nomes para solicitar o registro DNS ' partners.contoso.com '. Os servidores DNS contoso.com retornam o registro CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio ' trafficmanager.net ', que são fornecidos pelo serviço do Gerenciador de tráfego do Azure. Em seguida, ele envia uma solicitação para o registro DNS ' contoso.trafficmanager.net ' para esses servidores DNS.
4. Os servidores de nome do Gerenciador de tráfego recebem a solicitação. Eles escolhem um ponto de extremidade com base em:

    - O estado configurado de cada ponto de extremidade (pontos de extremidades desabilitados não são retornados)
    - A integridade atual de cada ponto de extremidade, conforme determinado pelas verificações de integridade do Gerenciador de tráfego. Para obter mais informações, consulte [monitoramento de ponto de extremidade do Traffic Manager](traffic-manager-monitoring.md).
    - O método de roteamento de tráfego escolhido. Para obter mais informações, consulte [métodos de roteamento do Traffic Manager](traffic-manager-routing-methods.md).

5. O ponto de extremidade escolhido é retornado como outro registro DNS CNAME. Nesse caso, vamos supor que contoso-us.cloudapp.net seja retornado.
6. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio ' cloudapp.net '. Ele contata os servidores de nomes para solicitar o registro DNS ' contoso-us.cloudapp.net '. Um registro de DNS ' A ' contendo o endereço IP do ponto de extremidade de serviço baseado nos EUA é retornado.
7. O serviço DNS recursivo consolida os resultados e retorna uma única resposta DNS para o cliente.
8. O cliente recebe os resultados de DNS e se conecta ao endereço IP fornecido. O cliente se conecta diretamente ao ponto de extremidade do serviço de aplicativo, não ao Gerenciador de tráfego. Como é um ponto de extremidade HTTPS, o cliente executa o handshake SSL/TLS necessário e, em seguida, faz uma solicitação HTTP GET para a página '/login.aspx '.

O serviço DNS recursivo armazena em cache as respostas DNS recebidas. O resolvedor de DNS no dispositivo cliente também armazena em cache o resultado. O cache permite que as consultas DNS subsequentes sejam respondidas mais rapidamente usando dados do cache em vez de consultar outros servidores de nomes. A duração do cache é determinada pela propriedade TTL (time-to-Live) de cada registro DNS. Valores menores resultam em expiração de cache mais rápida e, portanto, mais viagens de ida e volta aos servidores de nome do Gerenciador de tráfego. Valores mais longos significam que pode levar mais tempo para direcionar o tráfego para longe de um ponto de extremidade com falha. O Gerenciador de tráfego permite configurar o TTL usado nas respostas de DNS do Gerenciador de tráfego para que seja tão baixo quanto 0 segundos e tão alto quanto 2.147.483.647 segundos (o intervalo máximo em conformidade com [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), permitindo que você escolha o valor que melhor equilibra as necessidades de seu aplicativo.

## <a name="faqs"></a>FAQs

* [Qual endereço IP o Gerenciador de tráfego usa?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Quais tipos de tráfego podem ser roteados usando o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [O Gerenciador de tráfego dá suporte a sessões "adesivas"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Por que estou vendo um erro HTTP ao usar o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Qual é o impacto no desempenho do uso do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Quais protocolos de aplicativo posso usar com o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Posso usar o Gerenciador de tráfego com um nome de domínio "Naked"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [O Gerenciador de tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [O que é o TTL do DNS e como ele afeta meus usuários?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [O quão alto ou baixo posso definir o TTL das respostas do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Como posso entender o volume de consultas que estão chegando ao meu perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [monitoramento de ponto de extremidade](traffic-manager-monitoring.md)do Traffic Manager e o failover automático.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

