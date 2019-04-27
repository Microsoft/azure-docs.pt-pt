---
title: Verifique as definições do Gestor de tráfego do Azure
description: Este artigo irá ajudá-lo a verificar as definições do Gestor de tráfego.
services: traffic-manager
author: rockboyfor
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/16/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 1e954e3c4ebba245d91cfb84ab583b314150e5b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771622"
---
# <a name="verify-traffic-manager-settings"></a>Verificar definições do Gestor de Tráfego

Para testar as definições do Gestor de tráfego, tem de ter vários clientes, em vários locais, a partir da qual pode executar seus testes. Em seguida, inclua os pontos finais de seu perfil do Gestor de tráfego baixo um de cada vez.

* Defina o valor de TTL de DNS baixa, para que as alterações serão propagadas rapidamente (por exemplo, 30 segundos).
* Sabe os endereços IP dos seus serviços cloud do Azure e o Web sites no perfil de que se estiver a testar.
* Utilize as ferramentas que permitem resolver um nome DNS para um endereço IP e apresentar esse endereço.

Verificado para ver que os nomes DNS resolver endereços IP dos pontos finais no seu perfil. Os nomes devem resolver de maneira consistente com o método de encaminhamento de tráfego definido no perfil do Gestor de tráfego. Pode usar as ferramentas, como **nslookup** ou **aprofundar** para resolver nomes DNS.

Os exemplos seguintes ajudar a testar o perfil do Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o perfil do Gestor de tráfego com nslookup e ipconfig no Windows

1. Abra um comando ou a linha de comandos do Windows PowerShell como administrador.
2. Tipo de `ipconfig /flushdns` para liberar o cache do resolvedor DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o seguinte comando verifica o nome de domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.cn

    Um resultado típico mostra as seguintes informações:

    + O nome DNS e endereço IP do servidor DNS que está sendo acessado para resolver este nome de domínio do Gestor de tráfego.
    + O nome de domínio do Gestor de tráfego escritas na linha de comando após a "nslookup" e o endereço IP ao qual o domínio do Gestor de tráfego é resolvido. O segundo endereço IP é a importante para verificar. Ele deve corresponder a um público endereço IP virtual (VIP) para um dos serviços cloud ou Web sites no perfil do Gestor de tráfego que está a testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de ativação pós-falha

1. Deixe todos os pontos finais de cópia de segurança.
2. Utilizar um único cliente, pedido de resolução DNS para o seu nome de domínio da empresa com o nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde ao ponto final primário.
4. Interromper o seu ponto final primário ou remova o ficheiro de monitorização, de modo que o Gestor de tráfego pensa que o aplicativo está desativado.
5. Aguarde que o DNS Time-to-Live (TTL) do perfil do Gestor de tráfego mais de dois minutos adicionais. Por exemplo, se o valor de TTL de DNS é de 300 segundos (5 minutos), tem de aguardar sete minutos.
6. Esvaziar a DNS cliente cache e o pedido de resolução de DNS com nslookup. No Windows, pode esvaziar a cache DNS com o comando ipconfig /flushdns.
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto final secundário.
8. Repita o processo, por sua vez interrupções cada ponto de extremidade. Certifique-se de que o DNS retorna o endereço IP do ponto de extremidade seguinte na lista. Quando todos os pontos finais para baixo, deve obter novamente o endereço IP do ponto final primário.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego ponderada

1. Deixe todos os pontos finais de cópia de segurança.
2. Utilizar um único cliente, pedido de resolução DNS para o seu nome de domínio da empresa com o nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde a um dos seus pontos finais.
4. Libere o cache de cliente DNS e repita os passos 2 e 3 para cada ponto de extremidade. Deverá ver endereços IP diferentes devolvidos para cada um dos seus pontos de extremidade.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de desempenho

Para testar com eficiência um método de encaminhamento de tráfego de desempenho, tem de ter clientes localizados em diferentes partes do mundo. Pode criar clientes em diferentes regiões do Azure que podem ser utilizados para testar seus serviços. Se tiver uma rede global, pode iniciar sessão para os clientes em outras partes do mundo remotamente e executar seus testes a partir daí.

Em alternativa, existem gratuito baseado na web pesquisa de DNS e aprofundar os serviços disponíveis. Algumas dessas ferramentas dão-lhe a capacidade de verificar a resolução de nomes DNS de vários locais em todo o mundo. Fazer uma pesquisa no "Pesquisa de DNS" para obter exemplos. Serviços de terceiros, como Gomez ou apresentação podem ser utilizados para confirmar que os perfis de estão a distribuir o tráfego, conforme o esperado.

## <a name="next-steps"></a>Passos Seguintes

* [Sobre métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)

<!-- Update_Description: update meta properties -->