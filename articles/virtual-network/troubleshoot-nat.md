---
title: Resolução de problemas Azure Rede Virtual NAT conectividade
titleSuffix: Azure Virtual Network
description: Problemas de resolução de problemas com a Rede Virtual NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 690543ebc91e346e77509fbf993493f6978374ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836110"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Resolução de problemas Azure Rede Virtual NAT conectividade

Este artigo ajuda os administradores a diagnosticar e resolver problemas de conectividade ao utilizar o NAT de Rede Virtual.

## <a name="problems"></a>Problemas

* [Exaustão SNAT](#snat-exhaustion)
* [O ping do ICMP está a falhar](#icmp-ping-is-failing)
* [Falhas de conectividade](#connectivity-failures)
* [Coexistência IPv6](#ipv6-coexistence)
* [A ligação não se origina do NAT gateway IP(s)](#connection-doesnt-originate-from-nat-gateway-ips)

Para resolver estes problemas, siga os passos na secção seguinte.

## <a name="resolution"></a>Resolução

### <a name="snat-exhaustion"></a>Exaustão SNAT

Um único [recurso de gateway NAT](nat-gateway-resource.md) suporta de 64.000 até 1 milhão de fluxos simultâneos.  Cada endereço IP fornece 64.000 portas SNAT para o inventário disponível. Pode utilizar até 16 endereços IP por recurso de gateway NAT.  O mecanismo SNAT é descrito [aqui](nat-gateway-resource.md#source-network-address-translation) mais detalhadamente.

Frequentemente, a causa principal da exaustão do SNAT é um anti-padrão para a forma como a conectividade de saída é estabelecida, gerida ou configurável dos seus valores padrão.  Leia esta secção atentamente.

#### <a name="steps"></a>Passos

1. Verifique se modificou o tempo limite de marcha lenta por defeito para um valor superior a 4 minutos.
2. Investigue como a sua aplicação está a criar conectividade de saída (por exemplo, revisão de código ou captura de pacotes). 
3. Determine se esta atividade é um comportamento esperado ou se a aplicação está a portar-se mal.  Utilize [métricas](nat-metrics.md) no Monitor Azure para fundamentar as suas descobertas. Utilize a categoria "Falhado" para a métrica de ligações SNAT.
4. Avalie se os padrões adequados são seguidos.
5. Avalie se a exaustão da porta SNAT deve ser atenuada com endereços IP adicionais atribuídos ao recurso de gateway NAT.

#### <a name="design-patterns"></a>Padrões de estrutura

Aproveite sempre que possível a reutilização da ligação e a ligação.  Estes padrões evitarão problemas de exaustão de recursos e resultarão em comportamentos previsíveis. Os primitivos para estes padrões podem ser encontrados em muitas bibliotecas e estruturas de desenvolvimento.

_**Solução:**_ Utilize padrões e boas práticas apropriados

- Os recursos de gateway NAT têm um tempo limite de 4 minutos.  Se esta definição for alterada para um valor mais elevado, a NAT manterá os fluxos por mais tempo e poderá causar [uma pressão desnecessária no inventário portuário SNAT](nat-gateway-resource.md#timers).
- Os pedidos atómicos (um pedido por ligação) são uma má escolha de design. Tal escala de limites anti-padrão, reduz o desempenho e diminui a fiabilidade. Em vez disso, reutilizar as ligações HTTP/S para reduzir o número de ligações e portas SNAT associadas. A escala de aplicação aumentará e o desempenho melhorará devido à redução dos custos de operação por apertos de mão, sobrecarga e operação criptográfica ao utilizar o TLS.
- O DNS pode introduzir muitos fluxos individuais em volume quando o cliente não está a caching o resultado do DNS. Use o caching.
- Os fluxos de UDP (por exemplo, pesquisas de DNS) alocam portas SNAT durante o tempo limite de marcha lenta. Quanto mais tempo o tempo de pressão, maior a pressão sobre as portas SNAT. Utilize um tempo curto de marcha lenta (por exemplo, 4 minutos).
- Utilize piscinas de ligação para moldar o seu volume de ligação.
- Nunca abandone silenciosamente um fluxo de TCP e confie nos temporizadores TCP para limpar o fluxo. Se não deixar a TCP fechar explicitamente a ligação, o estado permanece alocado em sistemas intermédios e pontos finais e torna as portas SNAT indisponíveis para outras ligações. Este padrão pode desencadear falhas de aplicação e exaustão SNAT. 
- Não altere os valores de temporizador de proximidade de nível de OS sem conhecimento especializado do impacto. Enquanto a stack TCP vai recuperar, o desempenho da sua aplicação pode ser impactado negativamente quando os pontos finais de uma ligação têm expectativas desajustadas. O desejo de mudar os temporizadores é geralmente um sinal de um problema de design subjacente. Reveja as recomendações.

A exaustão do SNAT também pode ser amplificada com outros anti-padrões na aplicação subjacente. Reveja estes padrões e boas práticas adicionais para melhorar a escala e a fiabilidade do seu serviço.

- Explore o impacto da redução do [tempo de inatividade da TCP](nat-gateway-resource.md#timers) para valores mais baixos, incluindo o tempo de inatividade padrão de 4 minutos para libertar o inventário portuário SNAT mais cedo.
- Considere [padrões de sondagens assíncronos](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) para operações de longa duração para libertar recursos de ligação para outras operações.
- Os fluxos de longa duração (por exemplo, ligações TCP reutilizadas) devem utilizar keepalives TCP ou keepalives de camadas de aplicação para evitar o tempo de funcionamento dos sistemas intermédios. Aumentar o tempo limite é um último recurso e pode não resolver a causa principal. Uma pausa prolongada pode causar falhas de taxas baixas quando o tempo limite expira e introduzir atrasos e falhas desnecessárias.
- Devem ser [utilizados padrões](https://docs.microsoft.com/azure/architecture/patterns/retry) de retenção graciosos para evitar recauchutagens/explosões agressivas durante a falha transitória ou recuperação de falhas.
Criar uma nova ligação TCP para cada operação HTTP (também conhecida como "conexões atómicas") é um anti-padrão.  As ligações atómicas impedirão que a sua aplicação escale bem e os recursos de resíduos.  Gasoduto sempre várias operações na mesma ligação.  A sua aplicação beneficiará em velocidade de transação e custos de recursos.  Quando a sua aplicação utiliza encriptação da camada de transporte (por exemplo, TLS), há um custo significativo associado ao processamento de novas ligações.  Reveja [os padrões de design da nuvem de Azure](https://docs.microsoft.com/azure/architecture/patterns/) para padrões adicionais de boas práticas.

#### <a name="additional-possible-mitigations"></a>Eventuais mitigações adicionais

_**Solução:**_ Conectividade de saída de escala da seguinte forma:

| Cenário | Evidência |Mitigação |
|---|---|---|
| Está a sentir discórdia por portas SNAT e exaustão portuária SNAT durante períodos de alta utilização. | A categoria "Falhada" para [a métrica](nat-metrics.md) de conexões SNAT no Monitor Azure mostra falhas transitórias ou persistentes ao longo do tempo e volume de ligação elevado.  | Determine se pode adicionar recursos adicionais de endereço IP público ou recursos prefixos ip públicos. Esta adição permitirá até 16 endereços IP no total para o seu gateway NAT. Esta adição irá fornecer mais inventário para as portas SNAT disponíveis (64.000 por endereço IP) e permitir-lhe-á escalar ainda mais o seu cenário.|
| Já deu 16 endereços IP e ainda está a passar pela exaustão da porta SNAT. | A tentativa de adicionar o endereço IP adicional falha. O número total de endereços IP provenientes de recursos de endereços IP públicos ou recursos prefixos IP públicos excede um total de 16. | Distribua o ambiente de aplicação através de várias sub-redes e forneça um recurso de gateway NAT para cada sub-rede.  Reavalie os seus padrões de design para otimizar com base na [orientação](#design-patterns)anterior . |

>[!NOTE]
>É importante entender por que a exaustão do SNAT ocorre. Certifique-se de que está a utilizar os padrões certos para cenários escaláveis e fiáveis.  Adicionar mais portas SNAT a um cenário sem compreender a causa da procura deve ser um último recurso. Se não entender porque é que o seu cenário está a aplicar pressão sobre o inventário portuário SNAT, adicionar mais portas SNAT ao inventário adicionando mais endereços IP só atrasará a mesma falha de exaustão que as escalas de aplicação.  Podes estar a mascarar outras ineficiências e anti-padrões.

### <a name="icmp-ping-is-failing"></a>O ping do ICMP está a falhar

[A Rede Virtual NAT](nat-overview.md) suporta os protocolos IPv4 UDP e TCP. O ICMP não é apoiado e espera-se que falhe.  

_**Solução:**_ Em vez disso, utilize testes de ligação TCP (por exemplo, "TCP ping") e testes de camada de aplicação específicos da UDP para validar a conectividade final.

A tabela a seguir pode ser utilizada como ponto de partida para as ferramentas a utilizar para iniciar os testes.

| Sistema operativo | Teste de ligação genérico de TCP | Teste da camada de aplicação TCP | UDP |
|---|---|---|---|
| Linux | nc (teste de ligação genérica) | caracóis (teste da camada de aplicação TCP) | aplicação específica |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplicação específica |

### <a name="connectivity-failures"></a>Falhas de conectividade

Problemas de conectividade com [a Rede Virtual NAT](nat-overview.md) podem ser causados por vários problemas diferentes:

* falhas permanentes devido a erros de configuração.
* exaustão transitória ou persistente [do SNAT](#snat-exhaustion) do gateway NAT,
* falhas transitórias na infraestrutura de Azure, 
* falhas transitórias no caminho entre Azure e o destino público da Internet, 
* falhas transitórias ou persistentes no destino público da Internet.

Utilize ferramentas como as seguintes para validar a conectividade. [O ping ICMP não é suportado.](#icmp-ping-is-failing)

| Sistema operativo | Teste de ligação genérico de TCP | Teste da camada de aplicação TCP | UDP |
|---|---|---|---|
| Linux | nc (teste de ligação genérica) | caracóis (teste da camada de aplicação TCP) | aplicação específica |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplicação específica |

#### <a name="configuration"></a>Configuração

Verifique a sua configuração:
1. O recurso NAT gateway tem pelo menos um recurso IP público ou um recurso de prefixo de IP público? Deve pelo menos ter um endereço IP associado ao NAT gateway para que possa fornecer conectividade de saída.
2. A sub-rede da rede virtual está configurada para utilizar o gateway NAT?
3. Está a utilizar o UDR (rota definida pelo utilizador) e está a sobrevassar o destino?  Os recursos de gateway NAT tornam-se a rota predefinida (0/0) nas sub-redes configuradas.

#### <a name="snat-exhaustion"></a>Exaustão SNAT

Revisão da secção sobre [a exaustão do SNAT](#snat-exhaustion) neste artigo.

#### <a name="azure-infrastructure"></a>Infraestrutura do Azure

A Azure monitoriza e opera a sua infraestrutura com muito cuidado. Falhas transitórias podem ocorrer, não há garantias de que as transmissões são induídas.  Utilize padrões de design que permitam retransmissão SYN para aplicações TCP. Utilize intervalos de ligação suficientemente grandes para permitir a retransmissão do TCP SYN para reduzir os impactos transitórios causados por um pacote SYN perdido.

_**Solução:**_

* Verifique se a [exaustão do SNAT.](#snat-exhaustion)
* O parâmetro de configuração numa pilha de TCP que controla o comportamento de retransmissão SYN chama-se RTO[(Retransmissão Time-Out](https://tools.ietf.org/html/rfc793)). O valor RTO é ajustável, mas tipicamente 1 segundo ou superior por padrão com recuo exponencial.  Se o tempo de intervalo de ligação da sua aplicação for demasiado curto (por exemplo, 1 segundo), poderá ver intervalos de ligação esporádicos.  Aumente o tempo de intervalo da ligação da aplicação.
* Se observar intervalos mais longos e inesperados com comportamentos de aplicação predefinidos, abra um caso de suporte para uma resolução de problemas mais aprofundada.

Não recomendamos a redução artificial do tempo de ligação TCP ou afinação do parâmetro RTO.

#### <a name="public-internet-transit"></a>Trânsito público na Internet

As probabilidades de falhas transitórias aumentam com um caminho mais longo para o destino e sistemas mais intermédios. Espera-se que falhas transitórias possam aumentar a frequência sobre [a infraestrutura Azure.](#azure-infrastructure) 

Siga as mesmas orientações que a secção de [infraestrutura Azure](#azure-infrastructure) anterior.

#### <a name="internet-endpoint"></a>Ponto final da Internet

As secções anteriores aplicam-se, juntamente com o ponto final da Internet com o qual a comunicação é estabelecida. Outros fatores que podem ter impacto no sucesso da conectividade são:

* gestão do tráfego no lado do destino, incluindo
- Limite da taxa de API imposta pelo lado do destino
- Mitigações volumosas do DDoS volumetric ou formação de tráfego de camadas de transporte
* firewall ou outros componentes no destino 

Normalmente, as capturas de pacotes na fonte e no destino (se disponível) são necessárias para determinar o que está a acontecer.

_**Solução:**_

* Verifique se a [exaustão do SNAT.](#snat-exhaustion) 
* Valide a conectividade a um ponto final na mesma região ou em qualquer outro lugar para comparação.  
* Se estiver a criar testes de alta taxa de volume ou de transação, explore se a redução da taxa reduz a ocorrência de falhas.
* Se a alteração da taxa tiver impacto na taxa de falhas, verifique se os limites da taxa de API ou outros constrangimentos no lado do destino poderiam ter sido atingidos.
* Se a sua investigação for inconclusiva, abra um caso de apoio para mais resolução de problemas.

#### <a name="tcp-resets-received"></a>Resets TCP recebidos

O gateway NAT gera resets TCP na fonte VM para tráfego que não é reconhecido como em andamento.

Uma razão possível é que a ligação TCP tem o tempo inativo.  Pode ajustar o tempo de 4 minutos para o intervalo de marcha lenta a 120 minutos.

Os resets TCP não são gerados no lado público dos recursos de gateway da NAT. Os resets TCP no lado do destino são gerados pela fonte VM, e não pelo recurso de gateway NAT.

_**Solução:**_

* Rever [recomendações de padrões de design.](#design-patterns)  
* Abra um caso de apoio para uma resolução de problemas mais aprofundada, se necessário.

### <a name="ipv6-coexistence"></a>Coexistência IPv6

[O Virtual Network NAT](nat-overview.md) suporta os protocolos IPv4 UDP e TCP e a implantação numa [sub-rede com um prefixo IPv6 não é suportada](nat-overview.md#limitations).

_**Solução:**_ Desdobre o gateway NAT numa sub-rede sem prefixo IPv6.

Pode indicar interesse em capacidades adicionais através [do Número de Utilizadores NAT da Rede Virtual.](https://aka.ms/natuservoice)

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>A ligação não se origina do NAT gateway IP(s)

Configura o gateway NAT, endereço IP(es) para usar, e qual sub-rede deve utilizar um recurso de gateway NAT. No entanto, as ligações de casos de máquinas virtuais que existiam antes da implantação do gateway NAT não utilizam o endereço IP(es).  Parecem estar a utilizar endereços IP(es) não utilizados com o recurso de gateway NAT.

_**Solução:**_

[O VIRTUAL Network NAT](nat-overview.md) substitui a conectividade de saída para a sub-rede em que está configurada. Ao transitar do SNAT predefinido ou do balanceador de carga para a utilização de gateways NAT, novas ligações começarão imediatamente a utilizar o endereço IP(es) associado ao recurso de gateway NAT.  No entanto, se uma máquina virtual ainda tiver uma ligação estabelecida durante o comutador para o recurso de gateway NAT, a ligação continuará a utilizar o antigo endereço IP SNAT que foi atribuído quando a ligação foi estabelecida.  Certifique-se de que está realmente a estabelecer uma nova ligação em vez de reutilizar uma ligação que já existia porque o SO ou o navegador estavam a caching as ligações em um pool de conexão.  Por exemplo, ao utilizar _o caracol_ no PowerShell, certifique-se de especificar o parâmetro _-DisableKeepalive_ para forçar uma nova ligação.  Se estiver a utilizar um browser, as ligações também podem ser agrizadas.

Não é necessário reiniciar uma máquina virtual configurando uma sub-rede para um recurso de gateway NAT.  No entanto, se uma máquina virtual for reiniciada, o estado de ligação é lavado.  Quando o estado de ligação tiver sido lavado, todas as ligações começarão a utilizar o endereço IP do recurso (es) do recurso GATEWAY NAT.  No entanto, este é um efeito colateral da máquina virtual sendo reiniciada e não um indicador de que é necessário um reboot.

Se ainda tiver problemas, abra um caso de apoio para mais resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Rede Virtual NAT](nat-overview.md)
* Saiba mais sobre [o recurso de gateway NAT](nat-gateway-resource.md)
* Saiba mais [sobre métricas e alertas para os recursos de gateway DA NAT.](nat-metrics.md)
* [Diga-nos o que construir a seguir para o NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).

