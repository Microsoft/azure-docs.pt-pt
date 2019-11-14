---
title: Verificar as configurações do Gerenciador de tráfego do Azure
description: Neste artigo, saiba como verificar as configurações do Traffic Manager e testar o método de roteamento de tráfego.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: ad74e5c51d5939218ebb546993d416b3df1cd04b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023512"
---
# <a name="verify-traffic-manager-settings"></a>Verificar definições do Gestor de Tráfego

Para testar as configurações do Traffic Manager, você precisa ter vários clientes, em vários locais, dos quais você pode executar seus testes. Em seguida, traga os pontos de extremidade em seu perfil do Gerenciador de tráfego um de cada vez.

* Defina o valor de TTL do DNS como baixo para que as alterações se propaguem rapidamente (por exemplo, 30 segundos).
* Conheça os endereços IP dos seus serviços de nuvem e sites do Azure no perfil que você está testando.
* Use ferramentas que permitem resolver um nome DNS para um endereço IP e exibir esse endereço.

Você está verificando se os nomes DNS são resolvidos para endereços IP dos pontos de extremidade em seu perfil. Os nomes devem ser resolvidos de maneira consistente com o método de roteamento de tráfego definido no perfil do Gerenciador de tráfego. Você pode usar as ferramentas como **nslookup** ou **aprofundar** -se para resolver nomes DNS.

Os exemplos a seguir ajudam você a testar seu perfil do Gerenciador de tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verificar o perfil do Gerenciador de tráfego usando Nslookup e ipconfig no Windows

1. Abra um prompt de comando ou do Windows PowerShell como administrador.
2. Digite `ipconfig /flushdns` para liberar o cache do resolvedor de DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o comando a seguir verifica o nome de domínio com o prefixo *MyApp. contoso*

        nslookup myapp.contoso.trafficmanager.net

    Um resultado típico mostra as seguintes informações:

    + O nome DNS e o endereço IP do servidor DNS que está sendo acessado para resolver esse nome de domínio do Traffic Manager.
    + O nome de domínio do Traffic Manager que você digitou na linha de comando após "nslookup" e o endereço IP para o qual o domínio do Traffic Manager é resolvido. O segundo endereço IP é o importante a ser verificado. Ele deve corresponder a um endereço VIP (IP virtual) público para um dos serviços de nuvem ou sites no perfil do Gerenciador de tráfego que você está testando.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de failover

1. Deixe todos os pontos de extremidade ativos.
2. Usando um único cliente, solicite a resolução de DNS para o nome de domínio da sua empresa usando Nslookup ou um utilitário semelhante.
3. Verifique se o endereço IP resolvido corresponde ao ponto de extremidade primário.
4. Desative seu ponto de extremidade primário ou remova o arquivo de monitoramento para que o Gerenciador de tráfego pense que o aplicativo está inoperante.
5. Aguarde a TTL (vida útil) do DNS do perfil do Traffic Manager mais dois minutos adicionais. Por exemplo, se a TTL do DNS for de 300 segundos (5 minutos), você deverá aguardar por sete minutos.
6. Libere o cache do cliente DNS e solicite a resolução de DNS usando Nslookup. No Windows, você pode liberar o cache DNS com o comando ipconfig/flushdns.
7. Verifique se o endereço IP resolvido corresponde ao seu ponto de extremidade secundário.
8. Repita o processo, desativando cada ponto de extremidade por vez. Verifique se o DNS retorna o endereço IP do próximo ponto de extremidade na lista. Quando todos os pontos de extremidade estiverem inativos, você deverá obter o endereço IP do ponto de extremidade primário novamente.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de roteamento de tráfego ponderado

1. Deixe todos os pontos de extremidade ativos.
2. Usando um único cliente, solicite a resolução de DNS para o nome de domínio da sua empresa usando Nslookup ou um utilitário semelhante.
3. Verifique se o endereço IP resolvido corresponde a um de seus pontos de extremidade.
4. Libere o cache do cliente DNS e repita as etapas 2 e 3 para cada ponto de extremidade. Você deverá ver endereços IP diferentes retornados para cada um dos seus pontos de extremidade.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de desempenho

Para testar efetivamente um método de roteamento de tráfego de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Você pode criar clientes em diferentes regiões do Azure que podem ser usadas para testar seus serviços. Se você tiver uma rede global, poderá entrar remotamente em clientes em outras partes do mundo e executar seus testes a partir daí.

Como alternativa, há serviços gratuitos de pesquisa de DNS baseados na Web e de busca. Algumas dessas ferramentas oferecem a capacidade de verificar a resolução de nomes DNS de vários locais em todo o mundo. Faça uma pesquisa em "pesquisa de DNS" para obter exemplos. Serviços de terceiros, como Gomez ou palestras, podem ser usados para confirmar que os perfis estão distribuindo o tráfego conforme o esperado.

## <a name="next-steps"></a>Passos seguintes

* [Sobre métodos de roteamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)
