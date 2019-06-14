---
title: Dimensionamento automático e o Gateway de aplicação com redundância de zona v2
description: Este artigo apresenta o Standard_v2 de aplicação do Azure e o SKU de WAF_v2, que inclui funcionalidades de dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: 7cf6b4984f3941da3b2cd0e4eada5eb1d87f2b01
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054738"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Dimensionamento automático e o Gateway de aplicação com redundância de zona v2 

Gateway de aplicação e de Firewall de aplicações Web (WAF) também estão disponíveis num Standard_v2 e WAF_v2 SKU. O SKU de v2 oferece aprimoramentos de desempenho e adiciona suporte para críticos novos recursos, como o dimensionamento automático, a redundância de zona e o suporte para VIPs estáticos. Os recursos existentes em Standard e do WAF SKU continuam a ser suportados no novo SKU do v2, com algumas exceções listadas na [comparação](#differences-with-v1-sku) secção.

O novo SKU v2 inclui os seguintes melhoramentos:

- **Dimensionamento automático**: Gateway de aplicação ou WAF as implementações ao abrigo do SKU do dimensionamento automático podem aumentar ou para baixo com base nos padrões de carga do tráfego a alteração. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Este SKU oferece verdadeira elasticidade. Na Standard_v2 e WAF_v2 SKU, o Gateway de aplicação pode operar em capacidade fixa (dimensionamento automático desativada) e no modo de dimensionamento automático ativado. Modo de capacidade fixo é útil para cenários com cargas de trabalho consistentes e previsíveis. Modo de dimensionamento automático é benéfico em aplicativos que veem a variação no tráfego de aplicativo.
- **Redundância de zona**: Um Gateway de aplicação ou implementação de WAF pode abranger várias zonas de disponibilidade, eliminando a necessidade de aprovisionar instâncias separadas do Gateway de aplicação em cada zona com o Gestor de tráfego. Pode escolher uma zona única ou várias zonas de onde as instâncias de Gateway de aplicação estão implementadas, que torna mais resiliente a falhas de zona. Pode ser distribuído, da mesma forma, o conjunto de back-end para aplicações em zonas de disponibilidade.

  Redundância de zona está disponível apenas em que estão disponíveis as zonas do Azure. Em outras regiões, todas as outras funcionalidades são suportadas. Para obter mais informações, consulte [quais são as zonas de disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estático**: Tipo de gateway de aplicação v2 SKU suporta o VIP estático exclusivamente. Isto garante que o VIP associado ao gateway de aplicação não é alterado para o ciclo de vida da implementação, mesmo após um reinício.
- **Cabeçalho reescrita**: Gateway de aplicação permite-lhe adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP com o SKU da v2. Para obter mais informações, consulte [cabeçalhos de HTTP de reescrever com Gateway de aplicação](rewrite-http-headers.md)
- **Integração do Key Vault (pré-visualização)** : V2 do Gateway de aplicação suporta a integração com o Key Vault (em pré-visualização pública) para certificados de servidor que estão anexados a serviços de escuta HTTPS ativado. Para obter mais informações, consulte [terminação de SSL com certificados do Key Vault](key-vault-certs.md).
- **O Azure Kubernetes Service entrada controlador (pré-visualização)** : O controlador de entrada do Gateway de aplicação v2 permite que o Gateway de aplicação do Azure ser utilizado como a entrada para um Azure Kubernetes Service (AKS) conhecido como um Cluster do AKS. Para obter mais informações, consulte a [página de documentação](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Melhorias de desempenho**: A descarga de v2 SKU oferece até 5 X SSL de melhor desempenho em comparação com o SKU Standard/WAF.
- **Tempo de implantação e atualização mais rápido** o SKU de v2 proporciona um tempo de implantação e atualização mais rápido em comparação com a Standard/WAF SKU. Isso também inclui as alterações de configuração do WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões suportadas

O SKU de WAF_v2 e Standard_v2 está disponível nas seguintes regiões: Centro-Norte, EUA Centro-Sul, E.U.A. oeste, E.U.A. oeste 2, E.U.A. leste, E.U.A. Leste 2, E.U.A. Central, Europa do Norte, Europa Ocidental, Sudeste asiático, França Central, do Reino Unido oeste, leste do Japão, oeste do Japão. Mais regiões serão adicionados no futuro.

## <a name="pricing"></a>Preços

Com o SKU de v2, o modelo de preços é orientado pelo consumo e já não está ligado a contagens de instâncias ou de tamanhos. Os preços da SKU v2 tem dois componentes:

- **Preço fixo** -esta é uma vez por hora (ou as horas parciais) preço para aprovisionar um Standard_v2 ou WAF_v2 Gateway.
- **Preço unitário de capacidade** -este é o custo baseado no consumo que é cobrado além do custo fixo. Custo da unidade de capacidade é também calculado por hora ou parcial à hora. Existem três dimensões para a unidade de capacidade - unidade, conexões persistentes e débito de computação. Computação a unidade é uma medida de capacidade de processador consumida. Fatores que afetam a unidade de computação são TLS ligações/seg, cálculos de reescrita de URLs e processamento da regra de WAF. Ligação persistente é uma medida de conexões TCP estabelecidas para o gateway de aplicação num determinado intervalo de faturação. O débito é médio Megabits por segundo processado pelo sistema num determinado intervalo de faturação.

Cada unidade de capacidade é composta de no máximo: 1 de computação unidade, ou ligações persistentes de 2500 ou débito 2.22 Mbps.

Orientações de unidade de computação:

- **Standard_v2** -cada unidade de computação é capaz de aproximadamente 50 conexões por segundo com o certificado de TLS de chave RSA de 2048 bits.
- **WAF_v2** - cada computação unidade pode suportar aproximadamente 10 solicitações simultâneas por segundo para pedidos de 70 a 30% mistura de tráfego com 70% menor do que 2 KB obter/POSTAR e restantes superior. Desempenho de WAF não é afetado pelo tamanho da resposta atualmente.

> [!NOTE]
> Cada instância conseguem atualmente suportar aproximadamente 10 unidades de capacidade.
> O número de pedidos que pode manipular uma unidade de computação depende de diversos critérios, como o tamanho de chave do certificado TLS, o algoritmo de troca de chaves, o cabeçalho reescritas e, em caso de tamanho do pedido de entrada WAF. Recomendamos que efetue testes de aplicativo para determinar a taxa de pedidos por unidade de computação. Unidade de capacidade e unidade de computação serão disponibilizadas como uma métrica antes de faturação começa.

A tabela seguinte mostra os preços de exemplo e é apenas para fins ilustrativos.

**Preços em E.U.A. Leste**:

|              Nome do SKU                             | Preço fixo ($/ hora)  | Preço da unidade de capacidade ($/ CU-h)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Para obter mais informações sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/). Faturação está agendada para começar a partir de 1 de Julho de 2019.

**Exemplo 1**

Um Standard_v2 de Gateway de aplicação é aprovisionado sem dimensionamento automático no modo de dimensionamento manual com capacidade fixa de cinco instâncias.

Preço fixo = 744(hours) * US $0.20 = us $148.8 <br>
Unidades de capacidade = 744 (horas) * 10 unidades de capacidade por instância * 5 instâncias * US $0.008 por hora de unidade de capacidade = us $297.6

Preço total = us $148.8 + us $297.6 = us $446.4

**Exemplo 2**

Standard_v2 um Gateway de aplicação é aprovisionado durante um mês e durante este período recebe 25 novo SSL ligações/seg, média de transferência de dados de 8.88 Mbps. Partindo do princípio de que as ligações tiverem vida curtas, o preço seria:

Preço fixo = 744(hours) * US $0.20 = us $148.8

Preço da unidade de capacidade = 744(hours) * Max (unidade de computação de 25/50 para conexões por segundo, 8.88/2.22 a unidade de capacidade de débito) * US $0.008 = 744 * 4 * 0.008 = us $23.81

Preço total = us $148. 23.81 8 + = $172.61

> [!NOTE]
> A função Max devolve o maior valor num par de valores.

**Exemplo 3**

Um WAF_v2 de Gateway de aplicação é aprovisionada para um mês. Durante este período, ele recebe 25 novo SSL ligações/seg, média de transferência de dados de 8.88 Mbps e faz 80 pedidos por segundo. Partindo do princípio de que as ligações são de curtas duração e que o cálculo de unidade de computação para a aplicação suporta 10 RPS por unidade de computação, o preço seria:

Preço fixo = 744(hours) * US $0.36 = us $267.84

Preço da unidade de capacidade = 744(hours) * Max (computação unidade Max(25/50 for connections/sec, 80/10 WAF RPS), 8.88/2.22 a unidade de capacidade de débito) * US $0.0144 = 744 * 8 * 0.0144 = us $85.71

Preço total = us $267.84 + us $85.71 = us $353.55

> [!NOTE]
> A função Max devolve o maior valor num par de valores.

## <a name="scaling-application-gateway-and-waf-v2"></a>Dimensionar o Gateway de aplicação e WAF v2

Gateway de aplicação e WAF podem ser configuradas para dimensionamento em dois modos:

- **Dimensionamento automático** – com dimensionamento automático ativado, o Gateway de aplicação e v2 de WAF SKUs aumentar ou reduzir verticalmente com base nos requisitos de tráfego de aplicativo. Esse modo oferece melhor elasticidade à sua aplicação e elimina a necessidade de adivinhar a contagem de instâncias ou de tamanho de gateway do aplicativo. Este modo também permite-lhe poupar no custo sem exigir a execução gateways atingiu o limite de pico aprovisionado para a carga de tráfego máximo previsto. Os clientes tem de especificar uma contagem de instâncias mínima e máxima opcionalmente. Capacidade mínima garante que o Gateway de aplicação e WAF v2 não são inferiores a contagem de instâncias mínima especificada, até mesmo na ausência de tráfego. Será cobrado para esta capacidade mínima até mesmo na ausência de qualquer tráfego. Opcionalmente, também pode especificar um número máximo de instâncias, que garante que o Gateway de aplicação não pode ser escalonada para além do número especificado de instâncias. Irá continuar a ser faturados a quantidade de tráfego atendido pelo Gateway. As contagens de instâncias podem variar entre 0 e 125. O valor predefinido para o número máximo de instâncias é 20 se não for especificado.
- **Manual** -, pode optar em que o gateway não o dimensionamento automático de modo Manual. Neste modo, se houver mais tráfego do que o que o Gateway de aplicação ou WAF pode lidar, ele poderia resultar na perda de tráfego. Com o modo manual, especificar o número de instâncias é obrigatório. Contagem de instâncias pode variar de 1 para 125 instâncias.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de funcionalidades entre o SKU de v1 e v2 SKU

A tabela seguinte compara as funcionalidades disponíveis com cada SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador do Azure Kubernetes Service (AKS) de entrada |          | &#x2713; |
| Integração do Cofre de Chaves do Azure                       |          | &#x2713; |
| Reescreva os cabeçalhos de HTTP (S)                           |          | &#x2713; |
| Encaminhamento com base no URL                                 | &#x2713; | &#x2713; |
| Alojamento de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento de tráfego                               | &#x2713; | &#x2713; |
| Firewall de aplicações Web (WAF)                    | &#x2713; | &#x2713; |
| Terminação de SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Encriptação SSL ponto a ponto                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte do WebSocket                                 | &#x2713; | &#x2713; |
| Suporte HTTP/2                                    | &#x2713; | &#x2713; |
| Drenagem de ligação                               | &#x2713; | &#x2713; |

> [!NOTE]
> O v2 de dimensionamento automático agora oferece suporte a SKU [sondas de estado de funcionamento padrão](application-gateway-probe-overview.md#default-health-probe) para monitorizar o estado de funcionamento de todos os recursos no seu conjunto de back-end e destacar esses membros de back-end que são considerados mau estado de funcionamento automaticamente. A sonda de estado de funcionamento predefinido é configurada automaticamente para o back-ends que não têm qualquer configuração de sonda personalizada. Para obter mais informações, consulte [sondas de estado de funcionamento no gateway de aplicação](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Diferenças com v1 SKU

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Não suportado.<br>Para obter mais informações, consulte [descrição geral do SSL ponto a ponto com o Gateway de aplicação](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Misturar Standard_v2 e o padrão Gateway de aplicação na mesma sub-rede|Não suportado|
|Definido rota utilizador (UDR) na sub-rede de Gateway de aplicação|Não suportado|
|NSG para o intervalo de portas de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 à 65534 para Standard SKU.<br>Para obter mais informações, consulte a [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Registos de desempenho no diagnóstico do Azure|Não suportado.<br>Métricas do Azure devem ser utilizadas.|
|Faturação|Faturação agendada para começar a partir de 1 de Julho de 2019.|
|Modo FIPS|Estes não são atualmente suportadas.|
|Modo só de ILB|Isso não é atualmente suportado. Público e o modo ILB é suportado em conjunto.|
|Integração de Netwatcher|Não suportado.|
|Integração do Centro de segurança do Azure|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar do v1 para v2

Um script do PowerShell do Azure está disponível na galeria do PowerShell para o ajudar a migrar a partir do seu v1 aplicação/WAF do Gateway para o SKU do dimensionamento automático do v2. Este script ajuda-o a copiar a configuração do seu gateway v1. Migração de tráfego é sua responsabilidade. Para obter mais informações, consulte [migrar Gateway de aplicação Azure da v1 para v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Tráfego da web direto com o Gateway de aplicação do Azure - portal do Azure](quick-create-portal.md)
- [Criar um dimensionamento automático, o gateway de aplicação com redundância de zona com um endereço IP virtual reservado com o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicação](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
