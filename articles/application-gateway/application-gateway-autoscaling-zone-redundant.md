---
title: Gateway de Aplicação com dimensionamento automático e redundância entre zonas v2
description: Este artigo introduz a aplicação Azure Standard_v2 e WAF_v2 SKU, que inclui funcionalidades autoscalantes e redundantes em zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 39b7e94747f556b61f661968f7126d122156d9cf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280017"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Gateway de Aplicação com dimensionamento automático e redundância entre zonas v2 

O Gateway de aplicações e a Firewall de aplicações web (WAF) também estão disponíveis sob um Standard_v2 e WAF_v2 SKU. O V2 SKU oferece melhorias de desempenho e adiciona suporte para novas funcionalidades críticas como autoscalcificação, redundância de zona e suporte para VIPs estáticos. As funcionalidades existentes sob a Norma e WAF SKU continuam a ser suportadas no novo V2 SKU, com algumas exceções listadas na secção [de comparação.](#differences-with-v1-sku)

O novo V2 SKU inclui as seguintes melhorias:

- **Autoscalcificação**: As implementações de Gateway de aplicação ou WAF sob o SKU autoscalcificador podem escalar para cima ou para baixo com base na alteração dos padrões de carga de tráfego. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Este SKU oferece uma verdadeira elasticidade. No Standard_v2 e WAF_v2 SKU, o Application Gateway pode funcionar tanto em capacidade fixa (autoscalcificação) como no modo ativado por autoescala. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de autoscalcificação é benéfico em aplicações que vêem variação no tráfego de aplicações.
- **Redundância de zona**: Uma implementação de Gateway de aplicação ou WAF pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de fornecer instâncias separadas de Gateway de aplicação em cada zona com um Gestor de Tráfego. Pode escolher uma única zona ou várias zonas onde as instâncias de Gateway de Aplicação são implementadas, o que o torna mais resistente à falha da zona. O backend pool para aplicações pode ser distribuído de forma semelhante em zonas de disponibilidade.

  O despedimento da zona só está disponível quando as Zonas Azure estiverem disponíveis. Noutras regiões, todas as outras características são apoiadas. Para mais informações, consulte [O que são Zonas de Disponibilidade em Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estático**: O Gateway v2 SKU suporta exclusivamente o tipo VIP estático. Isto garante que o VIP associado ao gateway de aplicação não se altere para o ciclo de vida da implementação, mesmo após um reinício.  Não existe um VIP estático em v1, por isso deve utilizar o URL de gateway da aplicação em vez do endereço IP para o encaminhamento de nome de domínio para Serviços de Aplicação através do gateway da aplicação.
- **Cabeçalho Reescrever**: O Gateway da aplicação permite-lhe adicionar, remover ou atualizar os cabeçalhos de pedido e resposta http com v2 SKU. Para mais informações, consulte [cabeçalhos http reescrever com Gateway de aplicação](rewrite-http-headers.md)
- **Integração do cofre chave**: O Gateway de aplicação v2 suporta a integração com o Key Vault para certificados de servidor que estão ligados aos ouvintes ativados por HTTPS. Para mais informações, consulte a [rescisão do SSL com certificados key vault](key-vault-certs.md).
- Controlador de **ingresso de serviço Azure Kubernetes**: O controlador de entrada de aplicações v2 Ingress permite que o Portal de Aplicações Azure seja utilizado como entrada para um Serviço Azure Kubernetes (AKS) conhecido como Cluster AKS. Para mais informações, consulte [o que é o Controlador de Ingress ingresso de Gateway de aplicação?](ingress-controller-overview.md)
- **Melhorias de desempenho**: O V2 SKU oferece até 5X melhor desempenho de descarga SSL em comparação com o SKU Standard/WAF.
- Tempo de **implementação e atualização mais rápido** O V2 SKU fornece um tempo de implementação e atualização mais rápido em comparação com o Standard/WAF SKU. Isto também inclui alterações de configuração WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões suportadas

A Standard_v2 e WAF_v2 SKU estão disponíveis nas seguintes regiões: North Central US, South Central US, West US, West US 2, East US, East US 2, Central US, North Europe, Europa Ocidental, Sudeste Asiático, França Central, Reino Unido Ocidental, Japão Leste, Japão Ocidental, Austrália Leste - Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Ásia Oriental, Coreia Central, Coreia do Sul, Reino Unido Sul, Índia Central, Índia Ocidental, Índia Do Sul.

## <a name="pricing"></a>Preços

Com o V2 SKU, o modelo de preços é impulsionado pelo consumo e já não está ligado a contagens ou tamanhos de instância. O preço v2 SKU tem dois componentes:

- **Preço fixo** - Este é o preço de hora (ou hora parcial) para fornecer um Standard_v2 ou WAF_v2 Gateway. Por favor, note que 0 instâncias mínimas adicionais ainda garantem uma elevada disponibilidade do serviço que está sempre incluído com preço fixo.
- **Preço unitário** de capacidade - Este é um custo baseado no consumo que é cobrado para além do custo fixo. A carga da unidade de capacidade também é calculada de hora em hora ou parcialmente. Existem três dimensões para a unidade de capacidade - unidade de computação, conexões persistentes e entrada. A unidade computacional é uma medida da capacidade do processador consumida. Os fatores que afetam a unidade computacional são ligações TLS/seg, computações de reescrita de URL e processamento de regras WAF. A ligação persistente é uma medida de conexões TCP estabelecidas à porta de aplicação num determinado intervalo de faturação. A produção é megabits/seg média processada pelo sistema num determinado intervalo de faturação.  A faturação é feita a nível da Unidade de Capacidade para qualquer coisa acima da contagem de instâncias reservada.

Cada unidade de capacidade é composta no máximo por: 1 unidade de cálculo, ou 2500 ligações persistentes, ou 2,22 Mbps de potência.

Orientação da unidade computacional:

- **Standard_v2** - Cada unidade de cálculo é capaz de aproximadamente 50 ligações por segundo com certificado TLS chave RSA 2048.
- **WAF_v2** - Cada unidade de cálculo pode suportar aproximadamente 10 pedidos simultâneos por segundo para uma mistura de tráfego de 70-30% com pedidos de 70% menos de 2 KB GET/POST e permanecendo mais alto. O desempenho do WAF não é afetado pelo tamanho da resposta atualmente.

> [!NOTE]
> Cada instância pode atualmente suportar aproximadamente 10 unidades de capacidade.
> O número de pedidos que uma unidade de computação pode suportar depende de vários critérios como o tamanho da chave do certificado TLS, algoritmo de troca de chaves, reescritas de cabeçalho e no caso de tamanho de pedido de entrada de WAF. Recomendamos que efetue testes de aplicação para determinar a taxa de pedido por unidade de cálculo. Tanto a unidade de capacidade como a unidade de cálculo serão disponibilizadas como métricas antes do início da faturação.

A tabela que se segue apresenta preços de exemplo e são apenas para fins de ilustração.

**Preços no Leste dos EUA:**

|              Nome SKU                             | Preço fixo ($/hr)  | Preço unitário de capacidade ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Para mais informações sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Exemplo 1**

Um Gateway de aplicação Standard_v2 é aprovisionado sem autoscalcificar em modo de escala manual com capacidade fixa de cinco instâncias.

Preço fixo = 744 horas * $0,20 = $148,8 <br>
Unidades de capacidade = 744 (horas) * Unidade de 10 capacidadepor instância * 5 instâncias * $0,008 por hora unitária de capacidade = $297,6

Preço total = $148,8 + $297,6 = $446.4

**Exemplo 2**

Um Gateway de aplicação standard_v2 é previsto para um mês, com zero instâncias mínimas, e durante este tempo recebe 25 novas ligações SSL/seg, média de transferência de dados de 8,88 Mbps. Assumindo que as ligações são de curta duração, o seu preço seria:

Preço fixo = 744 horas * $0,20 = $148,8

Preço unitário de capacidade = 744 horas * Max (unidade de computação 25/50 para ligações/seg, 8.88/2,22 unidade de capacidade para a entrada) * $0,008 = 744 * 4 * 0,008 = $23,81

Preço total = $148.8+23,81 = $172,61

Como pode ver, só está cobrado por quatro Unidades de Capacidade, não por toda a instância. 

> [!NOTE]
> A função Max devolve o maior valor num par de valores.


**Exemplo 3**

Um Gateway de aplicação standard_v2 está previsto para um mês, com um mínimo de cinco instâncias. Assumindo que não há tráfego e as ligações são de curta duração, o seu preço seria:

Preço fixo = 744 horas * $0,20 = $148,8

Preço unitário de capacidade = 744 horas * Max (unidade de computação 0/50 para ligações/sec, 0/2,22 unidade de capacidade para a entrada) * $0,008 = 744 * 50 * 0,008 = $297,60

Preço total = $148.80+297,60 = $446,4

Neste caso, é cobrado por todos os cinco casos, mesmo que não haja trânsito.

**Exemplo 4**

Um Gateway de aplicação standard_v2 está previsto para um mês, com um mínimo de cinco instâncias, mas desta vez há uma média de transferência de dados de 125 mbps, e 25 ligações SSL por segundo. Assumindo que não há tráfego e as ligações são de curta duração, o seu preço seria:

Preço fixo = 744 horas * $0,20 = $148,8

Preço unitário de capacidade = 744 horas * Max (unidade de computação 25/50 para ligações/seg, unidade de capacidade de 125/2,22 para a entrada) * $0,008 = 744 * 57 * 0,008 = $339,26

Preço total = $148.80+339,26 = $488,06

Neste caso, é cobrado para os cinco casos completos, mais sete Unidades de Capacidade (que é 7/10 de um exemplo).  

**Exemplo 5**

Um gateway de aplicação WAF_v2 está previsto para um mês. Durante este período, recebe 25 novas ligações SSL/seg, média de transferência de dados de 8,88 Mbps e faz 80 pedidos por segundo. Assumindo que as ligações são de curta duração, e que o cálculo da unidade de cálculo para a aplicação suporta 10 RPS por unidade de cálculo, o seu preço seria:

Preço fixo = 744 horas * $0,36 = $267,84

Preço unitário de capacidade = 744 horas * Max (unidade computacional Max (25/50 para ligações/seg, 80/10 WAF RPS), 8,88/2,22 unidade de capacidade para a entrada) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Preço total = $267,84 + $85,71 = $353,55

> [!NOTE]
> A função Max devolve o maior valor num par de valores.

## <a name="scaling-application-gateway-and-waf-v2"></a>Porta de aplicação de escala e WAF v2

O Gateway de aplicações e o WAF podem ser configurados para escalar em dois modos:

- **Autoscalcificação** - Com autoscalcificação ativada, o Gateway de aplicação e a escala WAF v2 SKUs para cima ou para baixo com base nos requisitos de tráfego da aplicação. Este modo oferece uma melhor elasticidade à sua aplicação e elimina a necessidade de adivinhar o tamanho do gateway da aplicação ou a contagem de instâncias. Este modo também permite economizar custos não exigindo que a porta de entrada corra no pico da capacidade prevista para a carga máxima de tráfego prevista. Deve especificar uma contagem mínima e opcionalmente máxima de instância. A capacidade mínima garante que o Gateway de Aplicação e o V2 WAF não fiquem abaixo da contagem mínima de instânciaespecificada, mesmo na ausência de tráfego. Cada instância conta como 10 Unidades de Capacidade reservadas adicionais. Zero significa nenhuma capacidade reservada e é puramente autoscalcificante na natureza. Por favor, note que zero instâncias mínimas adicionais ainda garantem uma elevada disponibilidade do serviço que está sempre incluído com preço fixo. Também pode especificar opcionalmente uma contagem máxima de instância, que garante que o Gateway da Aplicação não escala para além do número especificado de instâncias. Continuará a ser cobrado pela quantidade de tráfego servido pelo Gateway. As contagens de exemplo podem variar de 0 a 125. O valor predefinido para a contagem máxima por exemplo é de 20 se não especificado.
- **Manual** - Pode, em alternativa, escolher o modo Manual onde o gateway não escala automaticamente. Neste modo, se houver mais tráfego do que o que o Application Gateway ou o WAF podem suportar, pode resultar em perda de tráfego. Com o modo manual, especificar a contagem de instâncias é obrigatório. A contagem de instâncias pode variar de 1 a 125 casos.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de características entre v1 SKU e v2 SKU

A tabela seguinte compara as funcionalidades disponíveis com cada SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância da zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de ingresso do Serviço Azure Kubernetes (AKS) |          | &#x2713; |
| Integração do Cofre de Chaves do Azure                       |          | &#x2713; |
| Reescrever cabeçalhos HTTP(S)                           |          | &#x2713; |
| Encaminhamento com base no URL                                 | &#x2713; | &#x2713; |
| Alojamento de vários sites                             | &#x2713; | &#x2713; |
| Reorientação do trânsito                               | &#x2713; | &#x2713; |
| Firewall de Aplicações Web (WAF)                    | &#x2713; | &#x2713; |
| Regras personalizadas de WAF                                  |          | &#x2713; |
| Terminação de SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Encriptação SSL de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte do WebSocket                                 | &#x2713; | &#x2713; |
| Suporte HTTP/2                                    | &#x2713; | &#x2713; |
| Drenagem de ligação                               | &#x2713; | &#x2713; |

> [!NOTE]
> O autoscalcificante v2 SKU agora suporta sondas de [saúde padrão](application-gateway-probe-overview.md#default-health-probe) para monitorizar automaticamente a saúde de todos os recursos no seu pool back-end e destacar os membros backend que são considerados insalubres. A sonda de saúde padrão é configurada automaticamente para backends que não têm qualquer configuração de sonda personalizada. Para saber mais, consulte sondas de saúde no gateway da [aplicação.](application-gateway-probe-overview.md)

## <a name="differences-with-v1-sku"></a>Diferenças com v1 SKU

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Não suportado.<br>Para mais informações, consulte [a visão geral do fim para terminar o SSL com o Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)da Aplicação .|
|Misturade Standard_v2 e Gateway de Aplicação Padrão na mesma subnet|Não suportado|
|Rota Definida do Utilizador (UDR) na subnet gateway da aplicação|Não suportado|
|NSG para gama de portas de entrada| - 65200 a 65535 para Standard_v2 SKU<br>- 65503 a 65534 para a Standard SKU.<br>Para mais informações, consulte as [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Registos de desempenho em diagnósticos Azure|Não suportado.<br>As métricas azure devem ser utilizadas.|
|Faturação|Faturação prevista para começar a 1 de julho de 2019.|
|Modo FIPS|Estes não são atualmente apoiados.|
|Modo iLB|Isto não é atualmente apoiado. O modo público e iLB em conjunto é apoiado.|
|Integração netwatcher|Não suportado.|
|Integração do Azure Security Center|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar do v1 para v2

Um script Azure PowerShell está disponível na galeria PowerShell para ajudá-lo a migrar do seu V1 Application Gateway/WAF para o V2 Autoscaling SKU. Este script ajuda-o a copiar a configuração a partir do seu gateway V1. A migração de tráfego continua a ser da sua responsabilidade. Para mais informações, consulte [migrate Azure Application Gateway de v1 a v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Tráfego web direto com Portal de Aplicação Azure - portal Azure](quick-create-portal.md)
- [Criar um gateway de aplicação redundante de zona autoscalcificada com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre o Gateway de [Aplicação.](overview.md)
- Saiba mais sobre [o Azure Firewall.](../firewall/overview.md)
