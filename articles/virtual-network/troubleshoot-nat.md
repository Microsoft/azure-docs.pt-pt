---
title: Conectividade na rede virtual Desfilmo Azure NaT
titleSuffix: Azure Virtual Network
description: Problemas de resolução de problemas com a Rede Virtual NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2020
ms.author: allensu
ms.openlocfilehash: c9b5aaefeb8ab21eed850f5bf291d38981239aab
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508433"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Conectividade na rede virtual Desfilmo Azure NaT

Este artigo ajuda os administradores a diagnosticar e resolver problemas de conectividade ao utilizar o NAT da Rede Virtual.

## <a name="problems"></a>Problemas

* [Exaustão sNAT](#snat-exhaustion)
* [ICMP ping está falhando](#icmp-ping-is-failing)
* [Falhas de conectividade](#connectivity-failures)
* [Coexistência IPv6](#ipv6-coexistence)

Para resolver estes problemas, siga os passos na secção seguinte.

## <a name="resolution"></a>Resolução

### <a name="snat-exhaustion"></a>Exaustão sNAT

Um único [recurso de gateway NAT](nat-gateway-resource.md) suporta de 64.000 até 1 milhão de fluxos simultâneos.  Cada endereço IP fornece 64.000 portas SNAT ao inventário disponível. Pode utilizar até 16 endereços IP por recurso de gateway NAT.  O mecanismo SNAT é descrito [aqui](nat-gateway-resource.md#source-network-address-translation) com mais detalhes.

Frequentemente, a causa principal da exaustão do SNAT é um antipadrão para a forma como a conectividade de saída é estabelecida, gerida ou configurável os temporizadores alterados dos seus valores padrão.  Leia esta secção atentamente.

#### <a name="steps"></a>Passos

1. Verifique se modificou o tempo de paragem predefinido para um valor superior a 4 minutos.
2. Investigue como a sua aplicação está a criar conectividade de saída (por exemplo, revisão de códigos ou captura de pacotes). 
3. Determine se esta atividade é comportamento esperado ou se a aplicação está a portar-se mal.  Utilize [métricas](nat-metrics.md) no Monitor Azure para fundamentar as suas descobertas. Utilize a categoria "Falhada" para a métrica de Ligações SNAT.
4. Avaliar se os padrões apropriados são seguidos.
5. Avaliar se a exaustão da porta SNAT deve ser atenuada com endereços IP adicionais atribuídos ao recurso de gateway NAT.

#### <a name="design-patterns"></a>Padrões de estrutura

Aproveite sempre que possível a reutilização da ligação e o agrupamento de ligação.  Estes padrões evitarão problemas de exaustão de recursos e resultarão em comportamentos previsíveis. Os primitivos para estes padrões podem ser encontrados em muitas bibliotecas e quadros de desenvolvimento.

_**Solução:**_ Utilize padrões e boas práticas apropriados

- Os recursos de gateway na NAT têm um prazo de inatividade padrão de TCP de 4 minutos.  Se esta definição for alterada para um valor mais elevado, o NAT manterá os fluxos mais longos e poderá causar uma pressão desnecessária no inventário da [porta SNAT](nat-gateway-resource.md#timers).
- Os pedidos atómicos (um pedido por ligação) são uma má escolha de design. Esta escala de limites anti-padrão, reduz o desempenho e diminui a fiabilidade. Em vez disso, reutilizar as ligações HTTP/S para reduzir o número de ligações e portas SNAT associadas. A escala de aplicação aumentará e o desempenho melhorará devido à redução dos apertos de mão, despesas gerais e de operação criptográfica ao utilizar TLS.
- O DNS pode introduzir muitos fluxos individuais em volume quando o cliente não está a cortar o resultado dos resolvers dNS. Use o cache.
- Os fluxos uDP (por exemplo, as despesas com DNS) atribuem portas SNAT durante o tempo de paragem. Quanto mais tempo o tempo de paragem, maior a pressão sobre as portas SNAT. Utilize um tempo de paragem curto (por exemplo, 4 minutos).
- Utilize piscinas de ligação para moldar o volume de ligação.
- Nunca abandone silenciosamente um fluxo de TCP e confie em temporizadores de TCP para limpar o fluxo. Se não deixar que o TCP feche explicitamente a ligação, o Estado permanece alocado em sistemas intermédios e pontos finais e torna as portas SNAT indisponíveis para outras ligações. Isto pode desencadear falhas na aplicação e exaustão de SNAT. 
- Não altere os valores de temporizador estomato de Nível OS sem conhecimento especializado do impacto. Enquanto a pilha de TCP irá recuperar, o desempenho da sua aplicação pode ser impactado negativamente quando os pontos finais de uma ligação têm expectativas desajustadas. O desejo de mudar os temporizadores é geralmente um sinal de um problema de design subjacente. Rever seguindo recomendações.

Muitas vezes, a exaustão do SNAT também pode ser amplificada com outros anti-padrões na aplicação subjacente. Reveja estes padrões e boas práticas adicionais para melhorar a escala e a fiabilidade do seu serviço.

- Explore o impacto da redução do [tempo inativo](nat-gateway-resource.md#timers) do TCP para valores mais baixos, incluindo o tempo inativo padrão de 4 minutos para libertar o inventário da porta SNAT mais cedo.
- Considere padrões de [sondagens assíncronos](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) para operações de longa duração para libertar recursos de ligação para outras operações.
- Os fluxos de longa duração (por exemplo, ligações TCP reutilizadas) devem utilizar vidas de manutenção de TCP ou manutenção da camada de aplicação para evitar o tempo de saída dos sistemas intermédios. Aumentar o tempo inativo é um último recurso e pode não resolver a causa principal. Um período de tempo prolongado pode causar falhas de taxas baixas quando o tempo expira e introduzir atrasos e falhas desnecessárias.
- Os padrões de [repetição graciosos](https://docs.microsoft.com/azure/architecture/patterns/retry) devem ser utilizados para evitar repetições/explosões agressivas durante a falha transitória ou recuperação de falhas.
Criar uma nova ligação TCP para cada operação HTTP (também conhecida como "conexões atómicas") é um anti-padrão.  As ligações atómicas impedirão que a sua aplicação escalda bem e desperdice recursos.  Conduza sempre várias operações para a mesma ligação.  A sua aplicação beneficiará na velocidade de transação e nos custos de recursos.  Quando a sua aplicação utiliza encriptação da camada de transporte (por exemplo TLS), há um custo significativo associado ao processamento de novas ligações.  Reveja os padrões de [design de nuvem azure](https://docs.microsoft.com/azure/architecture/patterns/) para padrões adicionais de boas práticas.

#### <a name="additional-possible-mitigations"></a>Atenuações possíveis adicionais

_**Solução:**_ Conectividade de saída de escala da seguinte forma:

| Cenário | Evidência |Mitigação |
|---|---|---|
| Você está experimentando a disputa por portas SNAT e exaustão da porta SNAT durante períodos de alta utilização. | A categoria "falhada" para a [métrica](nat-metrics.md) de ligações SNAT no Monitor Azure mostra falhas transitórias ou persistentes ao longo do tempo e volume de ligação elevado.  | Determine se pode adicionar recursos adicionais de endereço ip público ou recursos públicos de prefixo IP. Esta adição permitirá até 16 endereços IP no total ao seu gateway NAT. Esta adição fornecerá mais inventário para as portas SNAT disponíveis (64.000 por endereço IP) e permitirá que você aumente ainda mais o seu cenário.|
| Já deu 16 endereços IP e ainda está a sentir exaustão por porta SNAT. | Tente adicionar falhas adicionais no endereço IP. O número total de endereços IP provenientes de recursos públicos de endereços IP ou recursos públicos de prefixo IP excede um total de 16. | Distribua o seu ambiente de aplicação através de várias subredes e forneça um recurso de gateway NAT para cada subnet.  Reavalie os seus padrões de design para otimizar com base na [orientação](#design-patterns)anterior . |

>[!NOTE]
>É importante entender por que a exaustão do SNAT ocorre. Certifique-se de que está a usar os padrões certos para cenários escaláveis e fiáveis.  Adicionar mais portas SNAT a um cenário sem compreender a causa da procura deve ser um último recurso. Se não compreender porque é que o seu cenário está a aplicar pressão sobre o inventário da porta SNAT, adicionar mais portas SNAT ao inventário adicionando mais endereços IP só atrasará a mesma falha de exaustão que as escalas de aplicação.  Podes estar a mascarar outras ineficiências e anti-padrões.

### <a name="icmp-ping-is-failing"></a>ICMP ping está falhando

[A Rede Virtual NAT](nat-overview.md) suporta protocolos IPv4 UDP e TCP. O ICMP não é apoiado e espera-se que falhe.  

_**Solução:**_ Em vez disso, utilize testes de ligação TCP (por exemplo,"TCP ping") e testes de camada de aplicação específicos da UDP para validar a conectividade final.

A tabela a seguir pode ser utilizada num ponto de partida para o qual as ferramentas a utilizar para iniciar os testes.

| Sistema operativo | Ensaio genérico de ligação TCP | Teste de camada de aplicação TCP | UDP |
|---|---|---|---|
| Linux | nc (ensaio genérico de ligação) | caracóis (teste de camada de aplicação TCP) | aplicação específica |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplicação específica |

### <a name="connectivity-failures"></a>Falhas de conectividade

Problemas de conectividade com a [Rede Virtual NAT](nat-overview.md) podem ser causados por várias questões diferentes:

* falhas permanentes devido a erros de configuração.
* [exaustão sNAT](#snat-exhaustion) transitória ou persistente do portal NAT,
* falhas transitórias na infraestrutura Azure, 
* falhas transitórias no caminho entre Azure e o destino público da Internet, 
* falhas transitórias ou persistentes no destino público da Internet.

Utilize ferramentas como a seguinte para validação da conectividade. [O iCMP ping não é suportado.](#icmp-ping-is-failing)

| Sistema operativo | Ensaio genérico de ligação TCP | Teste de camada de aplicação TCP | UDP |
|---|---|---|---|
| Linux | nc (ensaio genérico de ligação) | caracóis (teste de camada de aplicação TCP) | aplicação específica |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplicação específica |

#### <a name="configuration"></a>Configuração

Verifique o seguinte:
1. O recurso nat gateway tem pelo menos um recurso IP público ou um recurso público de pré-fixação IP? Deve pelo menos ter um endereço IP associado à porta de entrada NAT para que possa fornecer conectividade de saída.
2. A subnet da rede virtual está configurada para usar o portal NAT?
3. Está a usar o UDR (rota definida pelo utilizador) e está a sobrepor-se ao destino?  Os recursos de gateway NAT tornam-se a rota padrão (0/0) em subredes configuradas.

#### <a name="snat-exhaustion"></a>Exaustão sNAT

Secção de revisão sobre [exaustão sNAT](#snat-exhaustion) neste artigo.

#### <a name="azure-infrastructure"></a>Infraestrutura do Azure

A Azure monitoriza e opera a sua infraestrutura com grande cuidado. Falhas transitórias podem ocorrer, não há garantias de que as transmissões são inperdas.  Utilize padrões de design que permitam retransmissões syn para aplicações TCP. Utilize intervalos de ligação suficientemente grandes para permitir a retransmissão do TCP SYN para reduzir os impactos transitórios causados por um pacote SYN perdido.

_**Solução:**_

* Verifique se a [exaustão do SNAT.](#snat-exhaustion)
* O parâmetro de configuração numa pilha de TCP que controla o comportamento de retransmissão SYN chama-se RTO ([Tempo de Retransmissão).](https://tools.ietf.org/html/rfc793) O valor rto é ajustável, mas tipicamente 1 segundo ou superior por padrão com back-off exponencial.  Se o tempo de ligação da sua aplicação for demasiado curto (por exemplo, 1 segundo), poderá ver intervalos de ligação esporádicos.  Aumente o tempo de ligação da aplicação.
* Se observar intervalos mais longos e inesperados com comportamentos de aplicação padrão, abra um caso de suporte para mais resolução de problemas.

Não recomendamos reduzir artificialmente o tempo de ligação do TCP ou afinar o parâmetro RTO.

#### <a name="public-internet-transit"></a>Trânsito público da Internet

As probabilidades de falhas transitórias aumentam com um caminho mais longo para o destino e sistemas mais intermédios. Espera-se que falhas transitórias possam aumentar de frequência sobre [a infraestrutura azure.](#azure-infrastructure) 

Siga as mesmas orientações que a secção de [infraestruturas Azure](#azure-infrastructure) anterior.

#### <a name="internet-endpoint"></a>Ponto final da Internet

As secções anteriores aplicam-se, juntamente com o ponto final da Internet com o que a comunicação é estabelecida. Outros fatores que podem ter impacto no sucesso da conectividade são:

* gestão do tráfego do lado do destino, incluindo
- Limitação da taxa API imposta pelo lado do destino
- Atenuações volutrícias do DDoS ou modelação de tráfego de camadas de transporte
* firewall ou outros componentes no destino 

Normalmente, as capturas de pacotes na fonte e o destino (se disponível) são necessários para determinar o que está a acontecer.

_**Solução:**_

* Verifique se a [exaustão do SNAT.](#snat-exhaustion) 
* Valide a conectividade a um ponto final na mesma região ou em qualquer outro lugar para comparação.  
* Se estiver a criar testes de alta taxa de volume ou de transação, explore se a redução da taxa reduz a ocorrência de falhas.
* Se a alteração da taxa tiver impacto na taxa de falhas, verifique se os limites da taxa de API ou outros constrangimentos no lado do destino poderiam ter sido atingidos.
* Se a sua investigação for inconclusiva, abra um caso de apoio para mais resolução de problemas.

#### <a name="tcp-resets-received"></a>Resets tCP recebidos

O portal NAT gera resets de TCP na fonte VM para tráfego que não é reconhecido como em curso.

Uma razão possível é que a ligação TCP tem prazo inativo.  Pode ajustar o tempo de inatividade de 4 minutos a até 120 minutos.

Os resets tCP não são gerados no lado público dos recursos de gateway na NAT. Os resets de TCP no lado do destino são gerados pela fonte VM, e não pelo recurso de gateway NAT.

_**Solução:**_

* Rever recomendações de padrões de [design.](#design-patterns)  
* Abra um caso de apoio para novas resoluções de problemas, se necessário.

### <a name="ipv6-coexistence"></a>Coexistência IPv6

[O NAT da Rede Virtual](nat-overview.md) suporta protocolos IPv4 UDP e TCP e a implantação numa [subnet com prefixo IPv6 não é suportada](nat-overview.md#limitations).

_**Solução:**_ Desloque o gateway NAT numa subnet sem prefixo IPv6.

Pode indicar interesse em capacidades adicionais através da [Rede Virtual NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passos seguintes

* Conheça a [Rede Virtual NAT](nat-overview.md)
* Saiba mais sobre o [recurso de gateway NAT](nat-gateway-resource.md)
* Conheça [métricas e alertas para os recursos](nat-metrics.md)de gateway NAT .
* [Diga-nos o que construir a seguir para a Rede Virtual NAT no UserVoice](https://aka.ms/natuservoice).

