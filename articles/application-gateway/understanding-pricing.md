---
title: Compreensão dos preços - Azure Application Gateway
description: Este artigo descreve o processo de faturação para Azure Application Gateway e Web Application Firewall para ambos v1 a v2 SKUs
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601616"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Compreender preços para gateway de aplicações Azure e firewall de aplicação web

>[!NOTE]
>Os preços apresentados neste artigo são exemplos e são apenas para fins de ilustração. Para obter informações sobre preços de acordo com a sua região, consulte [a página de Preços](https://azure.microsoft.com/pricing/details/application-gateway/).

Azure Application Gateway é uma solução de equilíbrio de carga de camada 7, que permite a entrega de aplicações web escaláveis, altamente disponíveis e seguras no Azure.

Não existem custos iniciais ou de rescisão associados ao Application Gateway.
Será cobrado apenas pelos recursos pré-a provisionados e utilizados com base no consumo real de hora a hora. Os custos associados ao Gateway de Aplicação são classificados em dois componentes: custos fixos e custos variáveis. Os custos reais dentro de cada componente variarão de acordo com a utilização do SKU.

Este artigo descreve os custos associados a cada SKU e recomenda-se que os utilizadores utilizem este documento para planear e gerir os custos associados ao Gateway de Aplicação Azure.

## <a name="v1-skus"></a>V1 SKUs

O Portal de Aplicações Standard e os SKUs WAF V1 são faturados como uma combinação de:

* Custo Fixo

    Custos baseados no tempo que um determinado tipo de Gateway/WAF de aplicação é a provisionado e em execução para pedidos de processamento.
    A componente de custo fixo tem em conta os seguintes fatores:
    * Tier - Porta de aplicação padrão ou WAF
    * Tamanho - Pequeno, Médio & Grande
    * Contagem de ocorrências - Número de casos a implementar

    Em casos N, os custos associados serão N * custo de uma instância de uma determinada combinação tier (Standard & WAF) & Size (Small, Medium & Large).

* Custo Variável

    Custo com base na quantidade de dados processados pela Aplicação Gateway/WAF. Tanto o pedido como os bytes de resposta processados pelo Gateway de aplicação seriam considerados para faturação.

Custo Total = Custo Fixo + Custo Variável

### <a name="standard-application-gateway"></a>Gateway de aplicação padrão

Custo Fixo & Custo Variável será calculado de acordo com o tipo Gateway de Aplicação.
A tabela que se segue mostra preços de exemplo baseados numa imagem dos preços dos EUA orientais e destinam-se apenas a ilustrações.

#### <a name="fixed-cost-east-us-region-pricing"></a>Custo Fixo (preços da região leste dos EUA)

|              Tipo de Gateway de Aplicação             |  Custos ($/hr)  |
| ------------------------------------------------- | ---------------|
|                     Pequeno                         |    $0,025      |
|                     Médio                        |    $0,07       |
|                     Grande                         |    $0,32       |

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

#### <a name="variable-cost-east-us-region-pricing"></a>Custo Variável (preços da região leste dos EUA)

|              Dados Processados             |  Pequeno ($/GB)  |  Médio ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primeiros 10 TB/mês                       |     $0.008     |      Gratuito      |     Gratuito      |
| Próximo 30 TB (10-40 TB)/mês             |     $0.008     |     $0.007     |     Gratuito      |
| Mais de 40 TB/mês                        |     $0.008     |     $0.007     |     $0.0035   |

Para obter mais informações sobre preços de acordo com a sua região, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/application-gateway/)

### <a name="waf-v1"></a>WAF V1

Custo Fixo & Custos Variáveis Serão calculados de acordo com o tipo de Gateway de aplicação previsto.

A tabela que se segue mostra preços de exemplo baseados numa imagem dos preços dos EUA orientais e são apenas para fins de ilustração.

#### <a name="fixed-cost-east-us-region-pricing"></a>Custo Fixo (preços da região leste dos EUA)

|              Tipo de Gateway de Aplicação             |  Custos ($/hr)  |
| ------------------------------------------------- | ---------------|
|                     Pequeno                         |       ND       |
|                     Médio                        |     $0.126     |
|                     Grande                         |     $0.448     |

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

#### <a name="variable-cost-east-us-region-pricing"></a>Custo Variável (preços da região leste dos EUA)

|              Dados Processados             |  Pequeno ($/GB)  |  Médio ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primeiros 10 TB/mês                       |     $0.008     |      Gratuito      |     Gratuito      |
| Próximo 30 TB (10-40 TB)/mês             |     $0.008     |     $0.007     |     Gratuito      |
| Mais de 40 TB/mês                        |     $0.008     |     $0.007     |     $0.0035   |

Para obter mais informações sobre preços de acordo com a sua região, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/application-gateway/)

> [!NOTE]
> Transferências de dados de saída - os dados que saem dos centros de dados do Azure a partir de gateways de aplicações serão cobrados com taxas padrão [de transferência de dados](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Exemplo 1 (a) - Gateway de aplicação padrão com contagem de 1 instância

Vamos supor que a provisionou um Gateway de aplicação padrão de tipo médio com 1 instância e processa 500 GB num mês. Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

Preço Fixo = $0,07 * 730 (Horas) = $51,1 As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Custos Variáveis = Free (Nível médio não tem custos para os primeiros 10 TB processados por mês) Custos Totais = $51,1 + 0 = $51,1 

> [!NOTE]
> Para suportar cenários de elevada disponibilidade, é necessário configurar um mínimo de 2 instâncias para SKUs V1. Ver [SLA para Gateway de Aplicação](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Exemplo 1 (b) - Gateway de aplicação padrão com > contagem de exemplos 1

Vamos supor que forneceste um Gateway de aplicação padrão de tipo médio com cinco instâncias e processa 500 GB num mês. Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

Preço Fixo = 5 (Contagem de exemplos) * $0,07 * 730 (Horas) = $255,5 As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Custos Variáveis = Free (Nível médio não tem custos para os primeiros 10 TB processados por mês) Custos Totais = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Exemplo 2 - Porta de aplicação DA WAF

Vamos supor que adifou um pequeno tipo de application gateway de aplicação e um gateway de aplicação tipo grande WAF para os primeiros 15 dias do mês. O pequeno gateway de aplicação processa 15 TB na duração em que está ativo e o grande gateway de aplicação WAF processa 100 TB na duração em que está ativo. Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma: 

###### <a name="small-instance-standard-application-gateway"></a>Gateway de aplicação padrão de pequeno por exemplo

24 Horas * 15 Dias = 360 Horas

Preço Fixo = $0.025 * 360 (Horas) = $9

Custos Variáveis = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Custos Totais = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Gateway de aplicação waf de grande instância
24 Horas * 15 Dias = 360 Horas

Preço Fixo = $0.448 * 360 (Horas) = $161,28

Custos Variáveis = 60 * 1000 * $0,0035/GB = $210 (Grande nível não tem custos para os primeiros 40 TB processados por mês)

Custos Totais = $161.28 + $210 = $371,28

## <a name="v2-skus"></a>V2 SKUs  

O Gateway V2 e o WAF V2 SKUs suportam a autoscalagem e garantem uma elevada disponibilidade por padrão.

### <a name="key-terms"></a>Termos Chave

##### <a name="capacity-unit"></a>Unidade de Capacidade 
A Unidade de Capacidade é a medida de utilização da capacidade para um Gateway de Aplicação em vários parâmetros.

Uma unidade única de capacidade consiste nos seguintes parâmetros:
* 2500 Ligações persistentes
* Produção de 2.22-Mbps
* 1 Unidade computacional

Se algum destes parâmetros for ultrapassado, então outra unidade de capacidade n é necessária, mesmo que os outros dois parâmetros não excedam os limites desta unidade de capacidade única.
O parâmetro com maior utilização entre as três acima será utilizado internamente para o cálculo das unidades de capacidade, que por sua vez é faturado.

##### <a name="compute-unit"></a>Unidade de Cálculo
A Unidade de Cálculo é a medida da capacidade computacional consumida. Os fatores que afetam o consumo de unidades de cálculo são as ligações TLS/seg, os cálculos url rewrite e o processamento de regras WAF. O número de pedidos que uma unidade de computação pode lidar depende de vários critérios como o tamanho da chave do certificado TLS, algoritmo de troca de chaves, reescritas de cabeçalho, e no caso de WAF - tamanho de pedido de entrada.

Orientação da unidade computacional:
* Standard_v2 - Cada unidade de computação é capaz de aproximadamente 50 ligações por segundo com o certificado TLS de chave RSA 2048.

* WAF_v2 - Cada unidade de computação pode suportar aproximadamente 10 pedidos simultâneos por segundo para uma mistura de tráfego de 70-30% com 70% de pedidos inferiores a 2 KB GET/POST e permanecer mais alto. O desempenho da WAF não é afetado pelo tamanho da resposta atualmente.

##### <a name="instance-count"></a>Contagem de Instâncias 
O pré-fornecimento de recursos para o Gateway V2 SKUs é definido em termos de contagem de exemplos. Cada instância garante um mínimo de 10 unidades de capacidade em termos de capacidade de processamento. A mesma instância poderia potencialmente suportar mais de 10 unidades de capacidade para diferentes padrões de tráfego, dependendo dos parâmetros da Unidade de Capacidade.

A escala e os limites definidos manualmente para a autoscalagem (mínimo ou máximo) são definidos em termos de Contagem de Exemplos. A escala definida manualmente, por exemplo, conta e a contagem mínima de exemplos em config de autoescala reservará 10 unidades de capacidade/instância. Estas CUs reservadas serão faturadas enquanto o Gateway de aplicação estiver ativo, independentemente do consumo real de recursos. Se o consumo real atravessar o limiar de 10 unidades de capacidade/instância, as unidades de capacidade adicionais serão faturadas sob a componente variável.

Os SKUs V2 são faturados com base no consumo e constituem duas partes:

* Custos Fixos

    Custo com base no tempo em que o Gateway V2/WAF V2 é a provisionado e disponível para pedidos de processamento. Isto garante uma elevada disponibilidade - mesmo que 0 instâncias sejam reservadas especificando 0 na contagem de instâncias mínimas como parte da autoscalagem. 
    
    O custo fixo também inclui o custo associado ao IP público anexado ao Gateway de Aplicação.

    O número de casos em execução em qualquer momento não é considerado como um fator para os custos fixos dos SKUs V2. Os custos fixos de funcionamento de um Standard_V2 (ou WAF_V2) seriam os mesmos por hora, independentemente do número de ocorrências que se desemcorriam na mesma região de Azure.

* Custos unitários de capacidade 

    Custo baseado no número de unidades de capacidade reservadas ou utilizadas adicionalmente, conforme necessário para o processamento dos pedidos de entrada.  Os custos baseados no consumo são calculados de hora em hora.

Custos Totais = Custos Fixos + Custos Unitários de Capacidade

> [!NOTE]
> Uma hora parcial é cobrada como uma hora inteira.

A tabela que se segue mostra preços de exemplo baseados numa imagem dos preços dos EUA orientais e são apenas para fins de ilustração.

#### <a name="fixed-costs-east-us-region-pricing"></a>Custos Fixos (preços da região leste dos EUA)

|              SKU V2             |  Custos ($/hr)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0.246     |
|              WAF_V2             |     $0.443     |

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

#### <a name="variable-costs-east-us-region-pricing"></a>Custos Variáveis (preços da região leste dos EUA)

|        Unidade de Capacidade         |  Standard_V2 ($/hr)  |  WAF_V2 ($/hr) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0.008        |     $0.0144    |

Para obter mais informações sobre preços de acordo com a sua região, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/application-gateway/)

> [!NOTE]
> Transferências de dados de saída - os dados que saem dos centros de dados do Azure a partir de gateways de aplicações serão cobrados com taxas padrão [de transferência de dados](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--manual-scaling"></a>Exemplo 1 (a) - Escala manual 
Vamos supor que forneceste um gateway de aplicação Standard_V2 com escala manual definida para 8 instâncias durante todo o mês. Durante este período, recebe uma transferência média de dados de 88,8-Mbps.

Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

1 CU pode manusear produção de 2,22-Mbps.

CUs necessários para manusear 88,8 Mbps = 88,8 / 2,22 = 40 CUs 

CUs pré-a provisionados = 8 (Contagem de exemplos) * 10 = 80 

Uma vez que 80 (capacidade reservada) > 40 (capacidade necessária), não são necessárias CUs adicionais. 

Preço Fixo = $0.246 * 730 (Horas) = $179,58

Custos Variáveis = $0.008 * 8 (Unidades de Exemplo) * 10 (unidades de capacidade) * 730 (Horas) = $467,2

Custos Totais = $179,58 + $467,2 = $646,78

![Diagrama de escala manual 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Exemplo 1 (b) - Escala manual Com tráfego que vai além da capacidade prevista

Vamos supor que forneceste um gateway de aplicação Standard_V2 com escala manual definida para 3 instâncias durante todo o mês. Durante este período, recebe uma transferência média de dados de 88,8-Mbps.

Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

1 CU pode manusear 2,22 Mbps de produção.

CUs necessários para manusear 88,8 Mbps = 88,8 / 2,22 = 40 

CUs pré-a provisionados = 3 (Contagem de exemplos) * 10 = 30 

Uma vez que são necessárias 40 (capacidade necessária) > 30 (capacidade reservada), são necessárias CUs adicionais.
O número de CUs adicionais utilizados dependeria da capacidade gratuita disponível em cada instância.

Se a capacidade de processamento equivale a 10 CUs adicionais estivesse disponível para utilização nos 3 casos reservados.

Preço Fixo = $0.246 * 730 (Horas) = $179,58

Custos Variáveis = $0.008 * (3(Unidades de Instância) * 10 (unidades de capacidade) + 10 (unidades de capacidade adicional) ) * 730 (Horas) = $233,6

Custos Totais = $179,58 + $233,6 = $413,18

No entanto, se a capacidade de processamento equivaler a apenas dizer 7 CUs adicionais estava disponível para utilização nos 3 casos reservados.
Neste cenário, o recurso Application Gateway está em escala e pode potencialmente levar ao aumento da latência ou pedidos de abandono.

Preço Fixo = $0.246 * 730 (Horas) = $179,58

Custos Variáveis = $0.008 * (3(Unidades de Instância) * 10 (unidades de capacidade) + 7 (unidades de capacidade adicional) * 730 (Horas) = $216,08

Custos Totais = $179,58 + $216,08 = $395,66


![Diagrama de escala manual 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> Em caso de Escala Manual, quaisquer pedidos adicionais que excedam a capacidade máxima de processamento das instâncias reservadas podem causar impacto na disponibilidade da sua aplicação. Em situações de carga elevada, os casos reservados podem fornecer mais de 10 unidades de capacidade de processamento, dependendo da configuração e tipo de pedidos de entrada. Mas recomenda-se a provisionação do número de casos de acordo com os seus requisitos de tráfego.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Exemplo 2 - WAF_V2 instância com Autoscaling

Vamos supor que forneceste um WAF_V2 com autoscalagem ativada e definiste a contagem mínima de instância para 6 durante todo o mês. A carga de pedido fez com que a instância da WAF se escalonava e utilizasse 65 unidades de capacidade (escala de 5 unidades de capacidade, enquanto 60 unidades foram reservadas) durante todo o mês.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Preço Fixo = $0.443 * 730 (Horas) = $323,39

Custos Variáveis = $0.0144 * 65 (unidades de capacidade) * 730 (Horas) = $683,28

Custos Totais = $323,39 + $683,28 = $1006,67

![Diagrama de auto-escala 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> O tráfego real observado para o seu Gateway de aplicação é improvável que tenha um padrão de tráfego tão constante e a carga observada no seu Gateway de aplicação flutuaria de acordo com o uso real.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Exemplo 3 (a) – WAF_V2 instância com Autoscaling e config escala de 0 Min

Vamos supor que forneceste um WAF_V2 com autoscalagem ativada e definiu a contagem mínima de instância como 0 durante todo o mês. A carga de pedido na WAF é mínima, mas consistentemente presente por hora durante todo o mês. A carga está abaixo da capacidade de uma única unidade de capacidade.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Preço Fixo = $0.443 * 730 (Horas) = $323,39

Custos Variáveis = $0.0144 * 1 (unidades de capacidade) * 730 (Horas) = $10.512

Custos Totais = $323,39 + $10.512 = $333.902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Exemplo 3 (b) – WAF_V2 instância com Autoscaling com contagem de exemplos de 0 Min

Vamos supor que forneceste um WAF_V2 com autoscalagem ativada e definiste a contagem mínima de instância para 0 durante todo o mês. No entanto, há 0 tráfego direcionado para a instância da WAF durante todo o mês.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

Preço Fixo = $0.443 * 730 (Horas) = $323,39

Custos Variáveis = $0.0144 * 0 (unidades de capacidade) * 730 (Horas) = $0

Custos Totais = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Exemplo 3 (C) – WAF_V2 instância com escala manual definida para 1 instância

Vamos supor que a provisionou um WAF_V2 e definiu-o para escalamento manual com o valor mínimo aceitável de 1 instância para todo o mês. No entanto, há 0 tráfego direcionado para a WAF durante todo o mês.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Preço Fixo = $0.443 * 730 (Horas) = $323,39

Custos Variáveis = $0.0144 * 1 (Contagem de exemplos) * 10 (unidades de capacidade) * 730 (Horas) = $105,12

Custos Totais = $323,39 + $105.12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Exemplo 4 – WAF_V2 com autoscaling, cálculos de unidades de capacidade

Vamos supor que forneceste um WAF_V2 com autoscalagem ativada e definiste a contagem mínima de instância para 0 durante todo o mês. Durante este período, recebe 25 novas ligações TLS/seg com uma média de transferência de dados de 8,88-Mbps.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

As estimativas mensais de preços baseiam-se em 730 horas de utilização por mês.

Preço Fixo = $0.443 * 730 (Horas) = $323,39

Custos Variáveis = $0.0144 * 730 (Horas) * {Max (25/50, 8.88/2.22)} = $23,36 (4 Unidades de capacidade necessárias para manusear 8,88 Mbps)

Custos Totais = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Exemplo 5 (a) – Standard_V2 com cálculos de autoscalagem, baseados no tempo

Vamos supor que forneceste um standard_V2 com autoscalagem ativada e definiu a contagem mínima de instância para 0 e este gateway de aplicação está ativo durante 2 horas.
Durante a primeira hora, recebe tráfego que pode ser manuseado por 10 Unidades de Capacidade e durante a segunda hora recebe tráfego que exigiu 20 Unidades de Capacidade para lidar com a carga.
Os custos do Gateway de Aplicação utilizando os preços acima mencionados serão calculados da seguinte forma:

Preço Fixo = $0.246 * 2 (Horas) = $0,492

Custos Variáveis = $0.008 * 10 (unidades de capacidade) * 1 (Horas) + $0.008 * 20 (unidades de capacidade) * 1 (Horas) = $0,24

Custos Totais = $0.492 + $0.24 = $0,732


## <a name="monitoring-billed-usage"></a>Monitorização do Uso faturado

Pode ver a quantidade de consumo para diferentes parâmetros (unidade de cálculo, produção & conexões persistentes), bem como as Unidades de Capacidade que estão a ser utilizadas como parte das métricas do Gateway de Aplicação na secção **de Monitorização.**

![Diagrama da secção de métricas.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Métricas úteis para a estimativa de custos

* Unidades de capacidade atuais

    Contagem de unidades de capacidade consumidas para carregar equilibrar o tráfego através dos três parâmetros - Ligações correntes, Unidade de Produção e Computação

* Unidades de Capacidade Faturadas Fixas

    O número mínimo de unidades de capacidade manteve-se a provisionado de acordo com a definição de contagem de instâncias mínimas (uma instância traduz-se em 10 unidades de capacidade) na configuração do Gateway de Aplicação.

* Unidades de Capacidade Faturadas Estimadas

    As Unidades de Capacidade Faturadas Estimadas indicam o número de unidades de capacidade em utilização em que a faturação é estimada. Isto é calculado como o maior valor entre as Unidades de capacidade atuais (as unidades de capacidade necessárias para realizar o balanceamento de carga do tráfego) e as Unidades de capacidade faturadas fixas (as unidades de capacidade mínimas mantidas em aprovisionamento).

Mais métricas como a produção, as ligações atuais e as unidades de computação também estão disponíveis para compreender estrangulamentos e estimar o número de unidades de capacidade necessárias. Informações detalhadas estão disponíveis nas [Métricas do Gateway de Aplicação](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Exemplo - Estimar unidades de capacidade a serem utilizadas

**Métricas observadas:**

* Unidades de Computação = 17.38
* Produção = 1.37M Bytes/sec - 10,96 Mbps
* Ligações atuais = 123,08k
* Unidades de capacidade calculadas = máx(17.38, 10.96/2.22, 123.08k/2500) = 49.232

Unidades de Capacidade Observadas em métricas = 49,23

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre como os preços funcionam no Gateway de Aplicação Azure:

* [Página de preços do Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Calculadora de preços do Azure Application Gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
