---
title: Verifique as definições do Gestor de Tráfego Azure
description: Neste artigo, aprenda a verificar as definições do Seu Gestor de Tráfego e teste o método de encaminhamento de tráfego.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938385"
---
# <a name="verify-traffic-manager-settings"></a>Verificar definições do Gestor de Tráfego

Para testar as definições do Gestor de Tráfego, precisa de ter vários clientes, em vários locais, a partir dos quais pode realizar os seus testes. Em seguida, traga os pontos finais do seu perfil de Gestor de Tráfego um de cada vez.

* Desajuste o valor DNS TTL para que as alterações se propaguem rapidamente (por exemplo, 30 segundos).
* Conheça os endereços IP dos seus serviços e websites em nuvem Azure no perfil que está a testar.
* Utilize ferramentas que lhe permitam resolver um nome DNS num endereço IP e mostrar esse endereço.

Está a verificar se os nomes dNS resolvem os endereços IP dos pontos finais do seu perfil. Os nomes devem ser resolvidos de forma consistente com o método de encaminhamento de tráfego definido no perfil do Gestor de Tráfego. Pode utilizar as ferramentas como **nslookup** ou **escavar** para resolver nomes de DNS.

Os seguintes exemplos ajudam-no a testar o seu perfil de Gestor de Tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Consulte o perfil do Gestor de Tráfego utilizando nslookup e ipconfig no Windows

1. Abra um comando ou aviso do Windows PowerShell como administrador.
2. Digite `ipconfig /flushdns` para descarregar a cache de resolver DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o seguinte comando verifica o nome de domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Um resultado típico mostra as seguintes informações:

    + O nome DNS e o endereço IP do servidor DNS estão a ser acedidos para resolver este nome de domínio do Gestor de Tráfego.
    + O nome de domínio do Gestor de Tráfego que escreveu na linha de comando após "nslookup" e o endereço IP ao qual o domínio do Gestor de Tráfego resolve. O segundo endereço IP é o importante a verificar. Deve corresponder a um endereço IP virtual (VIP) público para um dos serviços ou websites na nuvem no perfil do Gestor de Tráfego que está a testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de falhas

1. Deixe todos os pontos finais para cima.
2. Utilizando um único cliente, solicite a resolução dNS para o nome de domínio da sua empresa usando nslookup ou um utilitário similar.
3. Certifique-se de que o endereço IP resolvido corresponde ao ponto final primário.
4. Derrube o seu ponto final primário ou remova o ficheiro de monitorização para que o Gestor de Tráfego pense que a aplicação está em baixo.
5. Aguarde o perfil dNS Time-to-Live (TTL) do perfil do Gestor de Tráfego mais dois minutos adicionais. Por exemplo, se o seu DNS TTL for de 300 segundos (5 minutos), deve esperar sete minutos.
6. Lave a cache do seu cliente DNS e solicite a resolução de DNS utilizando o nslookup. No Windows, pode descarregar a cache DNS com o comando ipconfig/flushdns.
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto final secundário.
8. Repita o processo, derrubando cada ponto final por sua vez. Verifique se o DNS devolve o endereço IP do próximo ponto final da lista. Quando todos os pontos finais estiverem em baixo, deve obter novamente o endereço IP do ponto final primário.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego ponderado

1. Deixe todos os pontos finais para cima.
2. Utilizando um único cliente, solicite a resolução dNS para o nome de domínio da sua empresa usando nslookup ou um utilitário similar.
3. Certifique-se de que o endereço IP resolvido corresponde a um dos seus pontos finais.
4. Lave a cache do cliente DNS e repita os passos 2 e 3 para cada ponto final. Deve ver diferentes endereços IP devolvidos para cada um dos seus pontos finais.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de desempenho

Para testar eficazmente um método de encaminhamento de tráfego de desempenho, deve ter clientes localizados em diferentes partes do mundo. Pode criar clientes em diferentes regiões do Azure que podem ser usadas para testar os seus serviços. Se tiver uma rede global, pode iniciar sessão remotamente com clientes noutras partes do mundo e fazer os seus testes a partir daí.

Em alternativa, existem serviços gratuitos de procura e escavação de DNS baseados na Web. Algumas destas ferramentas dão-lhe a capacidade de verificar a resolução de nomes DNS de vários locais do mundo. Faça uma pesquisa no "DNS lookup" por exemplo. Serviços de terceiros como Gomez ou Keynote podem ser usados para confirmar que os seus perfis estão a distribuir tráfego como esperado.

## <a name="next-steps"></a>Passos seguintes

* [Sobre os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Resolução de problemas do estado degradado do Traffic Manager](traffic-manager-troubleshooting-degraded.md)
