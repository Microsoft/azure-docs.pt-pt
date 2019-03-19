---
title: Como funciona o Gestor de tráfego do Azure | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como o Gestor de tráfego encaminha o tráfego de elevado desempenho e disponibilidade das suas aplicações web
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 52469cb2735b2270815191ec0815daee350882a4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108870"
---
# <a name="how-traffic-manager-works"></a>Como funciona o Gestor de tráfego

O Gestor de tráfego do Azure permite-lhe controlar a distribuição de tráfego entre os pontos de extremidade do aplicativo. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

Gestor de tráfego oferece duas vantagens principais:

- Distribuição de tráfego de acordo com um dos vários [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)
- [Monitorização contínua do Estado de funcionamento do ponto de extremidade](traffic-manager-monitoring.md) e ativação pós-falha automática quando a ativação de pontos finais

Quando um cliente tenta estabelecer ligação a um serviço, primeiro ele deverá resolver o nome DNS do serviço para um endereço IP. O cliente liga-se, em seguida, a esse endereço IP para aceder ao serviço.

**O ponto mais importante para compreender é que o Gestor de tráfego funciona ao nível do DNS.**  O Gestor de tráfego utiliza o DNS para direcionar clientes para pontos finais de serviço específico com base em regras do método de encaminhamento de tráfego. Os clientes ligam ao ponto de extremidade selecionado **diretamente**. O Gestor de tráfego não é um proxy ou de um gateway. O Gestor de tráfego não vê o tráfego que passa entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo de Gestor de tráfego

Contoso Corp desenvolveram um novo portal de parceiros. O URL para este portal é https://partners.contoso.com/login.aspx. O aplicativo é hospedado em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, utilizam o Gestor de tráfego para distribuir o tráfego de cliente para o ponto de extremidade disponível mais próximo.

Para obter esta configuração, eles concluírem os seguintes passos:

1. Implemente três instâncias do seu serviço. Os nomes DNS estas implementações são "contoso-us.cloudapp .net", "contoso-eu.cloudapp .net" e "contoso-asia.cloudapp .net".
1. Criar um perfil do Gestor de tráfego, com o nome "contoso.trafficmanager.net" e configurá-lo para usar o método de encaminhamento de tráfego de "Desempenho" em três pontos de extremidade.
1. Configure o respetivo nome de domínio personalizado, "partners.contoso.com", para apontar para 'contoso.trafficmanager.net", com um registo CNAME de DNS.

![Configuração de DNS do Gestor de tráfego][1]

> [!NOTE]
> Quando utilizar um domínio personalizado com o Gestor de tráfego do Azure, tem de utilizar um CNAME para apontar o seu nome de domínio personalizado para o seu nome de domínio do Gestor de tráfego. Normas DNS não permitem-lhe criar um CNAME no "apex" (ou raiz) de um domínio. Portanto, não é possível criar um CNAME para "contoso.com" (por vezes denominado um domínio de "sem" www ""). Só pode criar um CNAME para um domínio em "contoso.com", por exemplo, "www.contoso.com". Para contornar esta limitação, recomendamos que aloja o seu domínio DNS no [DNS do Azure](../dns/dns-overview.md) e a utilizar [registos de Alias](../dns/tutorial-alias-tm.md) para apontar para o perfil do traffic manager. Em alternativa, pode utilizar um redirecionamento HTTP simple para pedidos diretos para "contoso.com" para um nome alternativo, como "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes estabelecem ligação com o Gestor de tráfego

Continuando do exemplo anterior, quando um cliente solicita a página https://partners.contoso.com/login.aspx, o cliente executa os seguintes passos para resolver o DNS do nome e estabelecer uma ligação:

![Estabelecimento da conexão com o Gestor de tráfego][2]

1. O cliente envia uma consulta DNS ao serviço DNS recursivo configurado para resolver o nome "partners.contoso.com". Um serviço DNS recursivo, às vezes chamado de serviço de 'local DNS', não aloja domínios DNS diretamente. Em vez disso, o cliente off-loads o trabalho de entrar em contato com os vários serviços DNS autoritativos através da Internet necessária para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo localiza os servidores de nomes para o domínio "contoso.com". Em seguida, contacta os servidores de nomes para pedir o registo DNS "partners.contoso.com". Os servidores DNS de contoso.com devolvem o registo CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio de 'trafficmanager.net', que são fornecidas pelo serviço Gestor de tráfego do Azure. Em seguida, envia um pedido para o registo DNS 'contoso.trafficmanager.net' para esses servidores DNS.
4. Os servidores de nomes do Gestor de tráfego receberem o pedido. Eles escolherem um ponto de extremidade com base em:

    - O estado configurado de cada ponto de extremidade (pontos de extremidade desativados não são devolvidos)
    - Verifica o estado de funcionamento atual de cada ponto de extremidade, conforme determinado pelo Estado de funcionamento do Gestor de tráfego. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md).
    - O método de encaminhamento de tráfego escolhido. Para obter mais informações, consulte [métodos de encaminhamento do Gestor de tráfego](traffic-manager-routing-methods.md).

5. O ponto de final escolhido é retornado como outro registo de CNAME no DNS. Neste caso, informe-nos suponha que contoso-us.cloudapp.net é devolvido.
6. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio de "cloudapp.net". Contacta os servidores de nomes para pedir o '.net contoso us.cloudapp"registo de DNS. Um registo DNS "A" que contém o endereço IP do ponto de extremidade de serviço baseadas nos E.U.A. é devolvido.
7. O serviço DNS recursivo consolida os resultados e devolve uma única resposta DNS para o cliente.
8. O cliente recebe os resultados DNS e liga-se para o endereço IP indicado. O cliente liga-se para o ponto final de serviço de aplicações diretamente, não através do Gestor de tráfego. Uma vez que é um ponto final HTTPS, o cliente executa o handshake SSL/TLS necessário e, em seguida, faz um pedido HTTP GET para o ' / login. aspx ' página.

O serviço DNS recursivo armazena em cache as respostas DNS que recebe. O resolvedor DNS no dispositivo cliente também coloca em cache o resultado. Colocação em cache permite que as consultas DNS subsequentes que têm de ser respondidas mais rapidamente ao utilizar os dados da cache em vez de consultar outros servidores de nomes. A duração da cache é determinada pela propriedade (TTL) "time-to-live" de cada registo DNS. Valores menores resultam em mais rápida expiração do cache e, portanto, mais viagens para os servidores de nomes do Gestor de tráfego. Valores de mais tempo significam que pode demorar mais tempo para direcionar o tráfego para fora de um ponto de extremidade com falha. Gestor de tráfego permite-lhe configurar o valor de TTL utilizado nas respostas de DNS do Gestor de tráfego para ser tão elevada como 2,147,483,647 segundos e como tão baixo como 0 segundos (em conformidade com o intervalo de máximo [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), permitindo-lhe escolher o valor mais equilibra as necessidades da sua aplicação.


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Gestor de tráfego [ativação pós-falha automática e monitorização do ponto de extremidade](traffic-manager-monitoring.md).

Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

