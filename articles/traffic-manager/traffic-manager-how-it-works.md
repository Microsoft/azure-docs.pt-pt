---
title: Como funciona o Gestor de Tráfego azure [ Microsoft Docs
description: Este artigo irá ajudá-lo a entender como o Traffic Manager encaminha o tráfego para o alto desempenho e disponibilidade das suas aplicações web
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294858"
---
# <a name="how-traffic-manager-works"></a>Como funciona o Gestor de Tráfego

O Gestor de Tráfego Azure permite controlar a distribuição do tráfego através dos pontos finais da sua aplicação. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gestor de Tráfego oferece dois benefícios fundamentais:

- Distribuição do tráfego de acordo com um dos vários [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)
- [Monitorização contínua da saúde do ponto final](traffic-manager-monitoring.md) e falha automática quando os pontos finais falham

Quando um cliente tenta estabelecer ligação a um serviço, primeiro tem de resolver o nome DNS do serviço para um endereço IP. Em seguida, o cliente estabelece ligação a esse endereço IP para aceder ao serviço.

**O ponto mais importante a entender é que o Gestor de Tráfego trabalha ao nível do DNS.**  O Traffic Manager utiliza o DNS para direcionar os clientes para pontos finais de serviço específicos com base nas regras do método de encaminhamento de tráfego. Os clientes **ligam-se diretamente**ao ponto final selecionado . O Gestor de Tráfego não é um representante ou um portal. O Gestor de Tráfego não vê o trânsito a passar entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo de Gestor de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. O URL para `https://partners.contoso.com/login.aspx`este portal é . A aplicação está alojada em três regiões de Azure. Para melhorar a disponibilidade e maximizar o desempenho global, eles usam o Traffic Manager para distribuir o tráfego de clientes para o ponto final mais próximo disponível.

Para alcançar esta configuração, completam os seguintes passos:

1. Implementar três instâncias do seu serviço. Os nomes dNS destas implantações são "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" e "contoso-asia.cloudapp.net".
1. Crie um perfil de Gestor de Tráfego, denominado 'contoso.trafficmanager.net', e configure-o para utilizar o método de encaminhamento de tráfego 'Performance' através dos três pontos finais.
1. Configure o seu nome de domínio de vaidade, 'partners.contoso.com', para apontar para 'contoso.trafficmanager.net', utilizando um registo DNS CNAME.

![Configuração DNS do Gestor de Tráfego][1]

> [!NOTE]
> Ao utilizar um domínio de vaidade com o Gestor de Tráfego Azure, deve utilizar um CNAME para indicar o seu nome de domínio de vaidade para o seu nome de domínio do Gestor de Tráfego. As normas DNS não permitem criar um CNAME no 'ápice' (ou raiz) de um domínio. Assim, não é possível criar um CNAME para 'contoso.com' (por vezes chamado de domínio 'nu'). Só é possível criar um CNAME para um domínio em 'contoso.com', como 'www.contoso.com'. Para contornar esta limitação, recomendamos hospedar o seu domínio DNS no [Azure DNS](../dns/dns-overview.md) e utilizar [registos da Alias](../dns/tutorial-alias-tm.md) para apontar para o seu perfil de gestor de tráfego. Em alternativa, pode utilizar um simples redirecionamento HTTP para pedidos diretos de 'contoso.com' para um nome alternativo como 'www.contoso.com'.

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gestor de Tráfego

Continuando a partir do exemplo anterior, `https://partners.contoso.com/login.aspx`quando um cliente solicita a página, o cliente executa os seguintes passos para resolver o nome DNS e estabelecer uma ligação:

![Estabelecimento de ligação utilizando gestor de tráfego][2]

1. O cliente envia uma consulta dNS ao seu serviço de DNS recursivo configurado para resolver o nome 'partners.contoso.com'. Um serviço de DNS recursivo, por vezes chamado de serviço 'DNS local', não acolhe diretamente domínios DNS. Pelo contrário, o cliente descarrega o trabalho de contactar os vários serviços dNS autoritários em toda a Internet necessários para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo encontra os servidores de nome para o domínio 'contoso.com'. Em seguida, contacta esses servidores de nome para solicitar o registo DNS 'partners.contoso.com'. Os servidores DNS contoso.com devolvem o registo CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo encontra os servidores de nome para o domínio 'trafficmanager.net', que são fornecidos pelo serviço Azure Traffic Manager. Em seguida, envia um pedido para o registo DNS 'contoso.trafficmanager.net' para os servidores DNS.
4. Os servidores de nome do Gestor de Tráfego recebem o pedido. Escolhem um ponto final baseado em:

    - O estado configurado de cada ponto final (os pontos finais desativados não são devolvidos)
    - A saúde atual de cada ponto final, determinada pelos controlos de saúde do Gestor de Tráfego. Para mais informações, consulte a [Monitorização do Ponto final do Gestor de Tráfego](traffic-manager-monitoring.md).
    - O método de encaminhamento de tráfego escolhido. Para mais informações, consulte os métodos de [encaminhamento do Gestor de Tráfego](traffic-manager-routing-methods.md).

5. O ponto final escolhido é devolvido como outro disco DNS CNAME. Neste caso, suponhamos que contoso-us.cloudapp.net seja devolvido.
6. Em seguida, o serviço DNS recursivo encontra os servidores de nome para o domínio 'cloudapp.net'. Contacta esses servidores de nomes para solicitar o registo DNS 'contoso-us.cloudapp.net'. É devolvido um registo DNS 'A' contendo o endereço IP do ponto final do serviço baseado nos EUA.
7. O serviço DNS recursivo consolida os resultados e devolve uma única resposta DNS ao cliente.
8. O cliente recebe os resultados do DNS e liga-se ao endereço IP dado. O cliente liga-se diretamente ao ponto final do serviço de aplicação, não através do Traffic Manager. Uma vez que se trata de um ponto final HTTPS, o cliente realiza o necessário aperto de mão SSL/TLS e, em seguida, faz um pedido HTTP GET para a página '/login.aspx'.

O serviço DNS recursivo caches as respostas DNS que recebe. O resolver DNS no dispositivo cliente também caches o resultado. O caching permite que as consultas subsequentes do DNS sejam respondidas mais rapidamente usando dados da cache em vez de consultar outros servidores de nome. A duração da cache é determinada pela propriedade "time-to-live" (TTL) de cada registo dNS. Valores mais curtos resultam em expiração de cache mais rápida e, portanto, mais viagens de ida e volta para os servidores de nome do Gestor de Tráfego. Valores mais longos significam que pode demorar mais tempo a direcionar o tráfego para longe de um ponto final falhado. O Gestor de Tráfego permite-lhe configurar o TTL utilizado nas respostas DNS do Gestor de Tráfego para ser tão baixo como 0 segundos e até 2.147.483,647 segundos (o intervalo máximo em conformidade com o [RFC-1035),](https://www.ietf.org/rfc/rfc1035.txt)permitindo-lhe escolher o valor que melhor equilibra as necessidades da sua aplicação.

## <a name="faqs"></a>FAQs

* [Que endereço IP usa o Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Que tipos de tráfego podem ser encaminhados através do Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [O Gestor de Tráfego apoia sessões "pegajosas"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Porque é que estou a ver um erro http quando uso o Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Qual é o impacto de desempenho da utilização do Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Que protocolos de aplicação posso usar com o Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Posso usar o Gerente de Tráfego com um nome de domínio "nu"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [O Gestor de Tráfego considera o endereço de subnet do cliente ao lidar com consultas de DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [O que é DNS TTL e como é que isso afeta os meus utilizadores?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Até que ponto posso definir o TTL para respostas do Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Como posso entender o volume de consultas que chegam ao meu perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a monitorização do ponto final](traffic-manager-monitoring.md)do Gestor de Tráfego e a falha automática .

Saiba mais sobre os métodos de [encaminhamento de tráfego](traffic-manager-routing-methods.md)do Gestor de Tráfego.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

