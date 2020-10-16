---
title: Verifique as definições do Gestor de Tráfego do Azure
description: Neste artigo, aprenda a verificar as definições do seu Gestor de Tráfego e teste o método de encaminhamento de tráfego.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401525"
---
# <a name="verify-traffic-manager-settings"></a>Verificar definições do Gestor de Tráfego

Para testar as definições do Gestor de Tráfego, precisa de ter vários clientes, em vários locais, a partir dos quais pode realizar os seus testes. Em seguida, traga os pontos finais no seu perfil de Gerente de Tráfego um de cada vez.

* De modo a que as alterações se propaguem rapidamente (por exemplo, 30 segundos).
* Conheça os endereços IP dos seus serviços em nuvem Azure e websites no perfil que está a testar.
* Utilize ferramentas que lhe permitam resolver um nome DNS num endereço IP e exibir esse endereço.

Está a verificar se os nomes DNS resolvem os endereços IP dos pontos finais no seu perfil. Os nomes devem ser resolvidos de forma consistente com o método de encaminhamento de tráfego definido no perfil de Gestor de Tráfego. Pode utilizar as ferramentas como **nslookup** ou **escavar** para resolver os nomes dns.

Os exemplos a seguir ajudam-no a testar o seu perfil de Gestor de Tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o perfil do Gestor de Tráfego utilizando nslookup e ipconfig no Windows

1. Abra um comando ou o pedido do Windows PowerShell como administrador.
2. Digite `ipconfig /flushdns` para lavar a cache de resolver DNS.
3. Escreva `nslookup <your Traffic Manager domain name>`. Por exemplo, o seguinte comando verifica o nome de domínio com o prefixo *myapp.contoso*

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Um resultado típico mostra as seguintes informações:

    + O nome DNS e o endereço IP do servidor DNS que estão a ser acedidos para resolver este nome de domínio do Gestor de Tráfego.
    + O nome de domínio do Gestor de Tráfego que escreveu na linha de comando após "nslookup" e o endereço IP ao qual o domínio do Gestor de Tráfego resolve. O segundo endereço IP é o importante a verificar. Deve corresponder a um endereço IP virtual público (VIP) para um dos serviços de nuvem ou websites no perfil de Gestor de Tráfego que está a testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de falha

1. Deixe todos os pontos finais para cima.
2. Utilizando um único cliente, solicite a resolução DNS para o nome de domínio da sua empresa utilizando o nslookup ou um utilitário similar.
3. Certifique-se de que o endereço IP resolvido corresponde ao ponto final primário.
4. Derrube o seu ponto final principal ou remova o ficheiro de monitorização para que o Gestor de Tráfego pense que a aplicação está em baixo.
5. Aguarde o DNS Time-to-Live (TTL) do perfil de Gestor de Tráfego mais dois minutos adicionais. Por exemplo, se o seu DNS TTL for de 300 segundos (5 minutos), deve esperar sete minutos.
6. Lave a cache do seu cliente DNS e solicite a resolução DNS usando o nslookup. No Windows, pode lavar a cache DNS com o comando ipconfig /flushdns.
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto final secundário.
8. Repita o processo, derrubando cada ponto final por sua vez. Verifique se o DNS devolve o endereço IP do próximo ponto final da lista. Quando todos os pontos finais estiverem em baixo, deverá obter novamente o endereço IP do ponto final primário.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego ponderado

1. Deixe todos os pontos finais para cima.
2. Utilizando um único cliente, solicite a resolução DNS para o nome de domínio da sua empresa utilizando o nslookup ou um utilitário similar.
3. Certifique-se de que o endereço IP resolvido corresponde a um dos seus pontos finais.
4. Lave a cache do cliente DNS e repita os passos 2 e 3 para cada ponto final. Deverá ver diferentes endereços IP devolvidos para cada um dos seus pontos finais.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de desempenho

Para testar eficazmente um método de encaminhamento de tráfego de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Pode criar clientes em diferentes regiões do Azure que podem ser usadas para testar os seus serviços. Se tiver uma rede global, pode iniciar sôs remotamente os seus acessos a clientes noutras partes do mundo e fazer os seus testes a partir daí.

Em alternativa, existem serviços gratuitos de procura e escavação de DNS baseados na Web disponíveis. Algumas destas ferramentas dão-lhe a capacidade de verificar a resolução do nome DNS de vários locais do mundo. Faça uma pesquisa em "DNS searchup" por exemplo. Serviços de terceiros como Gomez ou Keynote podem ser usados para confirmar que os seus perfis estão a distribuir tráfego como esperado.

## <a name="next-steps"></a>Passos seguintes

* [Sobre os métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Resolução de problemas do estado degradado do Traffic Manager](traffic-manager-troubleshooting-degraded.md)
