---
title: Como funciona o Gestor de Tráfego da Azure Microsoft Docs
description: Este artigo irá ajudá-lo a entender como o Traffic Manager encaminha o tráfego para um alto desempenho e disponibilidade das suas aplicações web
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 471895f1a615770521584a627e6bca850b87d0ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462637"
---
# <a name="how-traffic-manager-works"></a>Como funciona o Gestor de Tráfego

O Azure Traffic Manager permite-lhe controlar a distribuição de tráfego através dos seus pontos finais de aplicação. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gestor de Tráfego oferece dois benefícios fundamentais:

- Distribuição do tráfego de acordo com um dos vários [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)
- [Monitorização contínua da saúde do ponto final](traffic-manager-monitoring.md) e falha automática quando os pontos finais falham

Quando um cliente tenta estabelecer ligação a um serviço, primeiro tem de resolver o nome DNS do serviço para um endereço IP. Em seguida, o cliente estabelece ligação a esse endereço IP para aceder ao serviço.

**O ponto mais importante a compreender é que o Gestor de Tráfego trabalha ao nível do DNS.**  O Gestor de Tráfego utiliza o DNS para direcionar os clientes para pontos finais de serviço específicos com base nas regras do método de encaminhamento de tráfego. Os clientes ligam-se **diretamente**ao ponto final selecionado. O gerente de trânsito não é um representante ou um portal. O Gestor de Trânsito não vê o trânsito a passar entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo do Gestor de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. O URL deste portal é `https://partners.contoso.com/login.aspx` . A candidatura está hospedada em três regiões de Azure. Para melhorar a disponibilidade e maximizar o desempenho global, eles usam o Traffic Manager para distribuir o tráfego do cliente ao ponto final mais próximo disponível.

Para alcançar esta configuração, completam os seguintes passos:

1. Desdobre três instâncias do seu serviço. Os nomes do DNS destas implementações são "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" e "contoso-asia.cloudapp.net".
1. Crie um perfil de Gestor de Tráfego, denominado 'contoso.trafficmanager.net', e configuure-o para utilizar o método de encaminhamento de tráfego 'Performance' através dos três pontos finais.
1. Configure o seu nome de domínio de vaidade, "partners.contoso.com", para apontar para "contoso.trafficmanager.net", utilizando um registo DNS CNAME.

![Configuração DNS do Gestor de Tráfego][1]

> [!NOTE]
> Ao utilizar um domínio de vaidade com o Azure Traffic Manager, deve utilizar um CNAME para apontar o seu nome de domínio de vaidade para o nome de domínio do Gestor de Tráfego. As normas DNS não permitem criar um CNAME no 'ápice' (ou raiz) de um domínio. Assim, não é possível criar um CNAME para "contoso.com" (por vezes chamado de domínio 'nu'). Só é possível criar um CNAME para um domínio em "contoso.com", como o "www.contoso.com". Para contornar esta limitação, recomendamos hospedar o seu domínio DNS no [Azure DNS](../dns/dns-overview.md) e utilizar [registos Alias](../dns/tutorial-alias-tm.md) para indicar o seu perfil de gestor de tráfego. Em alternativa, pode utilizar um simples redirecionamento HTTP para pedidos diretos de "contoso.com" para um nome alternativo como "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gestor de Tráfego

Continuando a partir do exemplo anterior, quando um cliente solicita a `https://partners.contoso.com/login.aspx` página, o cliente executa os seguintes passos para resolver o nome DNS e estabelecer uma ligação:

![Estabelecimento de conexão usando Gestor de Tráfego][2]

1. O cliente envia uma consulta DNS ao seu serviço de DNS recursivo configurado para resolver o nome 'partners.contoso.com'. Um serviço DNS recursivo, por vezes chamado de serviço de DNS local, não acolhe diretamente os domínios DNS. Pelo contrário, o cliente descarrega o trabalho de contactar os vários serviços de DNS autoritários em toda a Internet necessários para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo encontra os servidores de nome para o domínio 'contoso.com'. Em seguida, contacta esses servidores de nomes para solicitar o registo DNS 'partners.contoso.com'. Os servidores DNS contoso.com devolvem o registo CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo encontra os servidores de nome para o domínio 'trafficmanager.net', que são fornecidos pelo serviço Azure Traffic Manager. Em seguida, envia um pedido para o registo DNS 'contoso.trafficmanager.net' para os servidores DNS.
4. Os servidores de nome do Gestor de Tráfego recebem o pedido. Escolhem um ponto final baseado em:

    - O estado configurado de cada ponto final (os pontos finais desativados não são devolvidos)
    - A saúde atual de cada ponto final, conforme determinado pelo Gestor de Tráfego de Verificação de Saúde. Para obter mais informações, consulte [a Monitorização endpoint do Gestor de Tráfego.](traffic-manager-monitoring.md)
    - O método de encaminhamento de tráfego escolhido. Para obter mais informações, consulte [os Métodos de Encaminhamento do Gestor de Tráfego](traffic-manager-routing-methods.md).

5. O ponto final escolhido é devolvido como outro registo do DNS CNAME. Neste caso, suponhamos contoso-eu.cloudapp.net é devolvido.
6. Em seguida, o serviço DNS recursivo encontra os servidores de nome para o domínio 'cloudapp.net'. Contacta esses servidores de nomes para solicitar o registo DNS 'contoso-eu.cloudapp.net'. É devolvido um registo "A" de DNS que contém o endereço IP do ponto final de serviço baseado na UE.
7. O serviço DNS recursivo consolida os resultados e devolve uma única resposta dns ao cliente.
8. O cliente recebe os resultados do DNS e conecta-se ao endereço IP dado. O cliente conecta-se diretamente ao ponto final do serviço de aplicação, não através do Gestor de Tráfego. Uma vez que se trata de um ponto final HTTPS, o cliente executa o aperto de mão SSL/TLS necessário e, em seguida, faz um pedido HTTP GET para a página '/login.aspx'.

O serviço DNS recursivo caches as respostas DNS que recebe. O DNS resolver no dispositivo cliente também cache o resultado. O caching permite que as consultas de DNS subsequentes sejam respondidas mais rapidamente usando dados da cache em vez de consultar outros servidores de nome. A duração da cache é determinada pela propriedade 'time-to-live' (TTL) de cada registo DNS. Valores mais curtos resultam em expiração mais rápida da cache e, assim, mais viagens de ida e volta aos servidores de nome do Gestor de Tráfego. Valores mais longos significam que pode demorar mais tempo a direcionar o tráfego para longe de um ponto final falhado. O Traffic Manager permite-lhe configurar o TTL utilizado nas respostas DNS do Gestor de Tráfego para ser tão baixo como 0 segundos e até 2.147.483,647 segundos (o alcance máximo em conformidade com [o RFC-1035), permitindo-lhe](https://www.ietf.org/rfc/rfc1035.txt)escolher o valor que melhor equilibra as necessidades da sua aplicação.

## <a name="faqs"></a>FAQs

* [Que endereço IP utiliza o Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Que tipo de tráfego pode ser encaminhado usando o Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [O Traffic Manager suporta sessões "pegajosas"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Porque estou a ver um erro HTTP ao usar o Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Qual é o impacto de desempenho da utilização do Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Que protocolos de aplicação posso usar com o Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Posso usar o Traffic Manager com um nome de domínio "nu"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [O Gestor de Tráfego considera o endereço da sub-rede do cliente ao manusear consultas de DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [O que é o DNS TTL e como é que afeta os meus utilizadores?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Até que ponto posso definir o TTL para respostas do Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Como posso entender o volume de consultas que chegam ao meu perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a monitorização do ponto final](traffic-manager-monitoring.md)do Traffic Manager e a falha automática.

Saiba mais sobre [os métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager .

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

