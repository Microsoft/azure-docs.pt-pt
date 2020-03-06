---
title: Problemas de resolução de problemas Da rede virtual naT problemas de conectividade
titleSuffix: Azure Virtual Network NAT troubleshooting
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
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302989"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Problemas de resolução de problemas Da rede virtual naT problemas de conectividade

Este artigo ajuda os administradores a diagnosticar e resolver problemas de conectividade ao utilizar o NAT da Rede Virtual.

>[!NOTE] 
>A Rede Virtual NAT está disponível como pré-visualização pública neste momento. Atualmente só está disponível num conjunto limitado de [regiões.](nat-overview.md#region-availability) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter mais informações.

## <a name="problems"></a>Problemas

- [Exaustão sNAT.](#snat-exhaustion)
- [O ping do ICMP está a falhar.](#icmp-ping-is-failing)

Para resolver estes problemas, siga os passos na secção seguinte.

## <a name="resolution"></a>Resolução

### <a name="snat-exhaustion"></a>Exaustão sNAT

Um único [recurso de gateway NAT](nat-gateway-resource.md) suporta de 64.000 até 1 milhão de fluxos simultâneos.  Cada endereço IP fornece 64.000 portas SNAT ao inventário disponível. Pode utilizar até 16 endereços IP por recurso de gateway NAT.  O mecanismo SNAT é descrito [aqui](nat-gateway-resource.md#source-network-address-translation) com mais detalhes.

#### <a name="steps"></a>Passos:

1. Investigue como a sua aplicação está a criar conectividade de saída (por exemplo, revisão de códigos ou captura de pacotes). 
2. Determine se esta atividade é comportamento esperado ou se a aplicação está a portar-se mal.  Utilize [métricas](nat-metrics.md) no Monitor Azure para fundamentar as suas descobertas.
3. Avaliar se os padrões apropriados são seguidos.
4. Avaliar se a exaustão da porta SNAT deve ser atenuada com endereços IP adicionais atribuídos ao recurso de gateway NAT.

#### <a name="design-pattern"></a>Padrão de design:

Aproveite sempre que possível a reutilização da ligação e o agrupamento de ligação.  Este padrão evitará problemas de exaustão de recursos e resultará em comportamentos previsíveis. Os primitivos para estes padrões podem ser encontrados em muitas bibliotecas e quadros de desenvolvimento.
- Considere padrões de [sondagens assíncronos](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) para operações de longa duração para libertar recursos de ligação para outras operações.
- Os fluxos de longa duração (por exemplo, ligações TCP reutilizadas) devem utilizar vidas de manutenção de TCP ou manutenção da camada de aplicação para evitar o tempo de saída dos sistemas intermédios.
- Os padrões de [repetição graciosos](https://docs.microsoft.com/azure/architecture/patterns/retry) devem ser utilizados para evitar repetições/explosões agressivas durante a falha transitória ou recuperação de falhas.
Criar uma nova ligação TCP para cada operação HTTP (também conhecida como "conexões atómicas") é um anti-padrão.  As ligações atómicas impedirão que a sua aplicação escalda bem e desperdice recursos.  Conduza sempre várias operações para a mesma ligação.  A sua aplicação beneficiará na velocidade de transação e nos custos de recursos.  Quando a sua aplicação utiliza encriptação da camada de transporte (por exemplo TLS), há um custo significativo associado ao processamento de novas ligações.  Reveja os padrões de [design de nuvem azure](https://docs.microsoft.com/azure/architecture/patterns/) para padrões adicionais de boas práticas.

#### <a name="mitigations"></a>Mitigações

Pode escalar a conectividade de saída da seguinte forma:

| Cenário | Mitigação |
|---|---|
| Você está experimentando a disputa por portas SNAT e exaustão da porta SNAT durante períodos de alta utilização. | Determine se pode adicionar recursos adicionais de endereço ip público ou recursos públicos de prefixo IP. Esta adição permitirá até 16 endereços IP no total ao seu gateway NAT. Esta adição fornecerá mais inventário para as portas SNAT disponíveis (64.000 por endereço IP) e permitirá que você aumente ainda mais o seu cenário.|
| Já deu 16 endereços IP e ainda está a sentir exaustão por porta SNAT. | Distribua o seu ambiente de aplicação através de várias subredes e forneça um recurso de gateway NAT para cada subnet. |

>[!NOTE]
>É importante entender por que a exaustão do SNAT ocorre. Certifique-se de que está a usar os padrões certos para cenários escaláveis e fiáveis.  Adicionar mais portas SNAT a um cenário sem compreender a causa da procura deve ser um último recurso. Se não compreender porque é que o seu cenário está a aplicar pressão sobre o inventário da porta SNAT, adicionar mais portas SNAT ao inventário adicionando mais endereços IP só atrasará a mesma falha de exaustão que as escalas de aplicação.  Podes estar a mascarar outras ineficiências e anti-padrões.

### <a name="icmp-ping-is-failing"></a>ICMP ping está falhando

[A Rede Virtual NAT](nat-overview.md) suporta protocolos IPv4 UDP e TCP. O ICMP não é apoiado e espera-se que falhe.  Em vez disso, utilize testes de ligação TCP (por exemplo,"TCP ping") e testes de camada de aplicação específicos da UDP para validar a conectividade final.

A tabela a seguir pode ser utilizada num ponto de partida para o qual as ferramentas a utilizar para iniciar os testes.

| Sistema operativo | Ensaio genérico de ligação TCP | Teste de camada de aplicação TCP | UDP |
|---|---|---|---|
| Linux | nc (ensaio genérico de ligação) | caracóis (teste de camada de aplicação TCP) | aplicação específica |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplicação específica |

## <a name="next-steps"></a>Passos seguintes

- Conheça a [Rede Virtual NAT](nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT](nat-gateway-resource.md)
- Conheça [métricas e alertas para os recursos](nat-metrics.md)de gateway NAT .
