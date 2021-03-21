---
title: Gateway de Aplicação com dimensionamento automático e redundância entre zonas v2
description: Este artigo introduz a aplicação Azure Standard_v2 e WAF_v2 SKU, que inclui funcionalidades autoscaling e redundantes da zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059726"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Gateway de Aplicação com dimensionamento automático e redundância entre zonas v2 

O Gateway de Aplicações está disponível sob um Standard_v2 SKU. Web Application Firewall (WAF) está disponível sob um WAF_v2 SKU. O V2 SKU oferece melhorias de desempenho e adiciona suporte para novas funcionalidades críticas como autoscaling, redundância de zona e suporte para VIPs estáticos. As funcionalidades existentes ao abrigo da Norma e da WAF SKU continuam a ser suportadas no novo V2 SKU, com algumas exceções enumeradas na secção [de comparação.](#differences-from-v1-sku)

O novo V2 SKU inclui as seguintes melhorias:

- **Autoscalagem**: As implementações de Gateway de aplicação ou WAF sob o SKU auto-caling podem escalar para fora ou em com base na alteração dos padrões de carga de tráfego. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Este SKU oferece uma verdadeira elasticidade. No Standard_v2 e WAF_v2 SKU, o Application Gateway pode funcionar tanto em capacidade fixa (autoscaling desativada) como em modo de autoscalagem ativado. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de autoescalagem é benéfico em aplicações que vêem variação no tráfego de aplicações.
- **Redundância de zona**: Uma implementação de Gateway de Aplicação ou WAF pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de providenciar instâncias separadas do Gateway de aplicação em cada zona com um Gestor de Tráfego. Pode escolher uma única zona ou várias zonas onde são implementadas as instâncias do Gateway de Aplicação, o que o torna mais resistente à falha de zona. O pool backend para aplicações pode ser distribuído de forma semelhante em zonas de disponibilidade.

  O despedimento de zona só está disponível quando as Zonas Azure estiverem disponíveis. Noutras regiões, todas as outras características são apoiadas. Para mais informações, consulte [Regiões e Zonas de Disponibilidade em Azure](../availability-zones/az-overview.md)
- **Estática VIP**: O Gateway de Aplicação v2 SKU suporta exclusivamente o tipo VIP estático. Isto garante que o VIP associado ao gateway de aplicações não muda para o ciclo de vida da implementação, mesmo depois de um reinício.  Não existe um VIP estático em v1, por isso deve utilizar o URL do gateway de aplicação em vez do endereço IP para encaminhamento de nome de domínio para Os Serviços de Aplicação através do gateway de aplicações.
- **Reescrita do cabeçalho**: O Gateway de aplicações permite-lhe adicionar, remover ou atualizar pedidos HTTP e cabeçalhos de resposta com v2 SKU. Para obter mais informações, consulte [os cabeçalhos HTTP da Reescrita com o Gateway de Aplicações](rewrite-http-headers.md)
- **Integração do Cofre chave**: O Gateway de Aplicação v2 suporta a integração com o Key Vault para certificados de servidor que estão anexados aos ouvintes com ativação HTTPS. Para obter mais informações, veja [Terminação TLS com certificados do Key Vault](key-vault-certs.md).
- **Controlador de Ingress de Serviço Azure Kubernetes**: O Controlador de Entrada de Aplicação v2 Ingress permite que o Gateway de Aplicação Azure seja usado como entrada para um Serviço Azure Kubernetes (AKS) conhecido como Cluster AKS. Para mais informações, veja o que é o Controlador de Entradas de Gateway de [Aplicação?](ingress-controller-overview.md)
- **Melhorias de desempenho**: O V2 SKU oferece até 5X melhor desempenho de descarregamento TLS em comparação com o SKU Standard/WAF.
- **Tempo de implementação e atualização mais rápidos** O V2 SKU fornece um tempo de implementação e atualização mais rápido em comparação com o Standard/WAF SKU. Isto também inclui alterações na configuração DAA.

![Diagrama da zona de auto-dimensionamento.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões suportadas

A SKU Standard_v2 e WAF_v2 está disponível nas seguintes regiões: North Central US, South Central US, West US, West US 2, East US, East US 2, Central US, North Europe, West Europe, West Europe, Southeast Asia, France Central, UK West, Japan East, Japan West, Australia East, Australia Southeast, Brazil South, Canadá Central, Canadá East, East Asia, Coreia Do Sul, Coreia do Sul, Coreia do Sul, Coreia do Sul. , Reino Unido Sul, Índia Central, Índia Ocidental, Sul da Índia.

## <a name="pricing"></a>Preços

Com o V2 SKU, o modelo de preços é impulsionado pelo consumo e já não está ligado a contagens ou tamanhos de exemplo. O preço v2 SKU tem dois componentes:

- **Preço fixo** - Este preço é de hora a hora (ou hora parcial) para a disponibilização de um Standard_v2 ou WAF_v2 Gateway. Por favor, note que 0 instâncias mínimas adicionais ainda garantem alta disponibilidade do serviço que é sempre incluído com preço fixo.
- **Preço unitário de capacidade** - Trata-se de um custo baseado no consumo que é cobrado para além do custo fixo. A carga da unidade de capacidade também é calculada de hora a hora ou parcial. Existem três dimensões para a unidade de capacidade - unidade de computação, ligações persistentes e produção. A unidade de computação é uma medida da capacidade do processador consumida. Os fatores que afetam a unidade de computação são as ligações TLS/seg., as computações de Reescrita de URLs e o processamento de regras WAF. A ligação persistente é uma medida de conexões TCP estabelecidas ao gateway de aplicação num determinado intervalo de faturação. A produção é média de Megabits/seg processado pelo sistema num determinado intervalo de faturação.  A faturação é feita a um nível de Unidade de Capacidade para qualquer coisa acima da contagem de instância reservada.

Cada unidade de capacidade é composta por, no máximo: 1 unidade de computação, 2500 ligações persistentes e produção de 2,22-Mbps.

Para saber mais, consulte [os preços de Compreensão.](understanding-pricing.md)

## <a name="scaling-application-gateway-and-waf-v2"></a>Porta de aplicação de escala e WAF v2

O Gateway de Aplicações e a WAF podem ser configurados para escalar em dois modos:

- **Autoscalagem** - Com a autoscalagem ativada, o Gateway de Aplicação e OS SKUs WAF v2 escalam para cima ou para baixo com base nos requisitos de tráfego de aplicações. Este modo oferece uma melhor elasticidade à sua aplicação e elimina a necessidade de adivinhar o tamanho do gateway de aplicação ou contagem de exemplos. Este modo também permite economizar custos, não exigindo que o gateway corra na capacidade máxima prevista para a carga máxima de tráfego. Deve especificar uma contagem mínima e opcionalmente máxima de instância. A capacidade mínima garante que o Gateway de Aplicação e o WAF v2 não fiquem abaixo da contagem mínima de instância especificada, mesmo na ausência de trânsito. Cada instância é aproximadamente equivalente a 10 unidades de capacidade reservadas adicionais. Zero não significa capacidade reservada e é puramente auto-caling na natureza. Também pode especificar opcionalmente uma contagem máxima de instância, que garante que o Gateway de Aplicação não escala além do número especificado de casos. Só será cobrado pela quantidade de tráfego servido pelo Gateway. A contagem de casos pode variar de 0 a 125. O valor predefinido para a contagem de exemplos máximos é de 20 se não for especificado.
- **Manual** - Em alternativa, pode escolher o modo Manual onde o gateway não se autoescala. Neste modo, se houver mais tráfego do que o que o Application Gateway ou WAF podem suportar, pode resultar em perda de tráfego. Com o modo manual, especificar a contagem de instâncias é obrigatória. A contagem de casos pode variar de 1 a 125 instâncias.

## <a name="autoscaling-and-high-availability"></a>Autoscalagem e Alta Disponibilidade

Os Gateways de Aplicação Azure são sempre implantados de forma altamente disponível. O serviço é feito de múltiplas instâncias que são criadas como configuradas (se a autoscalagem for desativada) ou necessária pela carga da aplicação (se a autoscalagem estiver ativada). Note que, do ponto de vista do utilizador, não tem necessariamente visibilidade para os casos individuais, mas apenas para o serviço De Gateway de Aplicação como um todo. Se um determinado caso tiver um problema e deixar de ser funcional, o Azure Application Gateway criará de forma transparente um novo exemplo.

Por favor, note que mesmo que configuure autoscaling com zero instâncias mínimas o serviço ainda estará altamente disponível, que está sempre incluído com o preço fixo.

No entanto, a criação de um novo caso pode demorar algum tempo (cerca de seis ou sete minutos). Assim, se não quiser lidar com este tempo de inatividade, pode configurar uma contagem mínima de instância de 2, idealmente com o suporte da Zona de Disponibilidade. Desta forma terá pelo menos duas instâncias dentro do seu Azure Application Gateway em circunstâncias normais, por isso, se um deles tiver um problema, o outro tentará lidar com o tráfego, durante o tempo em que um novo caso está sendo criado. Note que uma instância Azure Application Gateway pode suportar cerca de 10 Unidades de Capacidade, por isso, dependendo da quantidade de tráfego que normalmente tem, poderá querer configurar a definição de autoscalização de instância mínima para um valor superior a 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de características entre v1 SKU e v2 SKU

A tabela seguinte compara as funcionalidades disponíveis com cada SKU.

| Funcionalidade                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância entre zonas                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de ingresss do Serviço Azure Kubernetes (AKS) |          | &#x2713; |
| Integração do Cofre de Chaves do Azure                       |          | &#x2713; |
| Reescrever cabeçalhos HTTP(S)                           |          | &#x2713; |
| Encaminhamento com base no URL                                 | &#x2713; | &#x2713; |
| Alojamento de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento de tráfego                               | &#x2713; | &#x2713; |
| Firewall de Aplicações Web (WAF)                    | &#x2713; | &#x2713; |
| Regras personalizadas de WAF                                  |          | &#x2713; |
| Segurança da camada de transporte (TLS)/Terminação da camada de tomadas seguras (SSL)            | &#x2713; | &#x2713; |
| Encriptação TLS de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte do WebSocket                                 | &#x2713; | &#x2713; |
| Suporte HTTP/2                                    | &#x2713; | &#x2713; |
| Drenagem de ligação                               | &#x2713; | &#x2713; |

> [!NOTE]
> O autoscaling v2 SKU agora suporta [sondas de saúde padrão](application-gateway-probe-overview.md#default-health-probe) para monitorizar automaticamente a saúde de todos os recursos na sua piscina traseira e destacar os membros backend que são considerados insalubres. A sonda de saúde predefinida é configurada automaticamente para backends que não têm nenhuma configuração personalizada da sonda. Para saber mais, consulte [as sondas de saúde no gateway de aplicações.](application-gateway-probe-overview.md)

## <a name="differences-from-v1-sku"></a>Diferenças em v1 SKU

Esta secção descreve características e limitações do V2 SKU que difere do V1 SKU.

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Não suportado.<br>Para obter mais informações, consulte [a visão geral do fim do TLS com o Gateway de aplicações.](ssl-overview.md#end-to-end-tls-with-the-v2-sku)|
|Mistura Standard_v2 e Gateway de aplicação padrão na mesma sub-rede|Não suportado|
|Rota User-Defined (UDR) na sub-rede do Gateway de Aplicação|Suportado (cenários específicos). Na pré-estreia.<br> Para obter mais informações sobre cenários suportados, consulte a [visão geral da configuração do Gateway de Aplicação](configuration-infrastructure.md#supported-user-defined-routes).|
|NSG para a gama porta de entrada| - 65200 a 65535 para Standard_v2 SKU<br>- 65503 a 65534 para a Standard SKU.<br>Para mais informações, consulte as [FAQ.](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet)|
|Registos de desempenho em diagnósticos Azure|Não suportado.<br>Devem ser utilizadas métricas azure.|
|Faturação|Faturação prevista para 1 de julho de 2019.|
|Modo FIPS|Estes não são atualmente apoiados.|
|Modo ILB|Atualmente, isto não é apoiado. O modo público e ilb em conjunto é suportado.|
|Integração de observadores da rede|Não suportado.|
|Integração do Centro de Segurança do Azure|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar do v1 para v2

Um script Azure PowerShell está disponível na galeria PowerShell para ajudá-lo a migrar do seu V1 Application Gateway/WAF para o V2 Autoscaling SKU. Este script ajuda-o a copiar a configuração do seu gateway V1. A migração do tráfego continua a ser da sua responsabilidade. Para mais informações, consulte [o Portal de Aplicações Migrate Azure de v1 a v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passos seguintes

- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure](quick-create-portal.md)
- [Crie um porta de aplicação redundante de zona de auto-estação com um endereço IP virtual reservado usando a Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [o Application Gateway](overview.md).
- Saiba mais sobre [a Azure Firewall](../firewall/overview.md).
