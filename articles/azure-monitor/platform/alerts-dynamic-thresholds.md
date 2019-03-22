---
title: Criar alertas com limiares dinâmicos no Azure Monitor
description: Criar alertas com limiares de dinâmica de machine learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 30f853bd65c83b922faf008fbb5279c28f197f68
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339011"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Alertas de métricas com limiares dinâmicos no Azure Monitor (pré-visualização pública)

Alerta de métrica com a detecção de limiares dinâmicos tira partido da avançada de machine learning (ML) para obter informações sobre comportamento de históricos dos métricas, identificar padrões e anomalias que indicam problemas de serviço possíveis. Fornece suporte de uma interface do Usuário simple e operações em escala, permitindo que os utilizadores configurar regras de alerta através da API do Azure Resource Manager, de forma totalmente automatizada.

Depois de criar uma regra de alerta, será acionado apenas quando a métrica monitorizada não se comporta como esperado, com base nos respetivos limiares sob medidas.

Gostaríamos muito de ouvir os seus comentários, mantê-lo a chegar em <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Por que e quando está a utilizar o tipo de condição dinâmica recomendado?

1. **Alertar dimensionável** – limiares dinâmicos, podem criar regras de alertas adaptadas limiares para centenas de série de métrica ao mesmo tempo. Ainda, fornecendo a mesma facilidade de definir uma regra de alerta numa única métrica. Utilizar a interface do Usuário ou os resultados da API do Azure Resource Manager em menos regras de alerta para gerir. A abordagem dimensionável é especialmente útil ao lidar com dimensões de métrica ou ao aplicar a vários recursos, como todos os recursos de subscrição. Que se traduz num tempo significativo, poupando no gerenciamento e a criação de regras de alertas. [Saiba mais sobre como configurar alertas de métrica com limiares dinâmicos através de modelos](alerts-metric-create-templates.md).

1. **Reconhecimento de padrões de métrica do smart** – utilizando a nossa tecnologia de ML exclusiva, temos a capacidade detetar padrões de métrica e adaptar-se a métrica alterações ao longo do tempo, o que, muitas vezes, pode incluir a sazonalidade (por hora / dia / semanais) automaticamente. Adaptar ao comportamento das métricas ao longo do tempo e os alertas com base em desvios em relação a seu padrão libera a responsabilidade de saber o limiar "right" para cada uma. O algoritmo de ML utilizado no limiares dinâmicos foi concebido para impedir que ruidosos (baixa de precisão simples) ou em limites de toda a (Lembre-se baixa) que não têm um padrão esperado.

1. **Configuração intuitiva** – limiares dinâmicos permitem configurar alertas de métricas com os conceitos de alto nível, aliviando a necessidade de ter conhecimento do domínio amplo sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar regras de alertas com limiares dinâmicos?

Alertas com limiares dinâmicos podem ser configuradas através de alertas de métrica no Azure Monitor. [Saiba mais sobre como configurar alertas de métrica](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Como são calculados os limiares?

Limiares dinâmicos continuamente aprende os dados da série de métrica e tenta modelá-lo usando um conjunto de algoritmos e métodos. Detetar padrões nos dados, tais como a sazonalidade (por hora / dia / semanais) e é capaz de lidar com métricas ruidosos (por exemplo, CPU do computador ou memória), bem como as métricas com dispersão baixa (por exemplo, a taxa de disponibilidade e o erro).

Os limiares são selecionados de forma que um desvio destes limiares indica uma anomalia no comportamento da métrica.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que faz a definição de "Sensibilidade" na média de limiares dinâmicos?

Sensibilidade do limiar de alerta é um conceito de alto nível que controla a quantidade de desvio de comportamento da métrica necessário para acionar um alerta.
Esta opção não exige o conhecimento do domínio sobre a métrica como limiar estático. As opções disponíveis são:

- Alta – os limiares será forte e próximo o padrão de série de métricas. Regra de alerta será acionada no desvio menor, resultando em mais alertas.
- Médio – menos limiares forte e mais equilibradas, menos alertas que com alta sensibilidade (predefinição).
- Baixa – os limiares será menos com mais de distância do padrão de série de métricas. Regra de alerta irá acionar apenas no desvio grandes, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de configuração de "operador no limiares dinâmicos?

Limiares dinâmicos, pode criar a regra de alertas adaptadas limiares com base no comportamento da métrica para ambos os superior e os limites inferiores usando a mesma regra de alerta.
Pode escolher o alerta das três condições a seguir:

- Superior ao limiar superior ou inferior ao limiar mais baixo (predefinição)
- Superior ao limiar superior
- Inferior ao limiar mais baixo.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que as definições avançadas na média de limiares dinâmicos?

**Falhar períodos** -limiares dinâmicos também permite-lhe configurar o "Número de violações para acionar o alerta", um número mínimo de desvios necessários no âmbito de uma determinada janela de tempo para o sistema emitir um alerta (a janela de tempo predefinido é de quatro desvios em 20 minutos). O utilizador pode configurar a falhar períodos e escolha o que quer ser alertado em ao alterar o período de tempo e períodos de falha. Esta capacidade reduz o ruído de alertas gerado pelo picos transitórios. Por exemplo:

Para acionar um alerta quando o problema é contínuo para 20 minutos, o 4 vezes consecutivas num determinado agrupamento período de 5 minutos, utilize as seguintes definições:

![Definições de períodos para problema contínua a falhar para 20 minutos, 4 vezes consecutivas num determinado agrupamento período de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para acionar um alerta quando ocorreu uma violação de um limiares dinâmicos em 20 minutos sem os últimos 30 minutos com o período de 5 minutos, utilize as seguintes definições:

![Definições de períodos para o problema a falhar para 20 minutos sem os últimos 30 minutos com o agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignorar dados antes de** -os utilizadores podem, opcionalmente, também, definir uma data de início a partir da qual o sistema deve começar a calcular os limiares de. Um caso de utilização típica pode ocorrer quando um recurso era uma em execução no modo de teste e agora é promovido para servir uma carga de trabalho de produção e, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser ignorado.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Comportamento lento será alterado no acionador métrico um alerta?

Provavelmente não. Limiares dinâmicos são bons para detetar desvios significativos, em vez de lentamente evoluindo problemas.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>A quantidade de dados é utilizado para visualização e, em seguida, calcular limiares?

Os limiares que aparece no gráfico, antes da criação de uma regra de alerta em métrica, são calculados com base em dados históricos suficiente para calcular a hora ou diariamente padrões sazonais (10 dias). Pressionar "Padrão de semanal de exibição" irá adquirir dados históricos suficientes para calcular padrões sazonais semanais (28 dias). Depois de criar uma regra de alerta, os limiares dinâmica irá utilizar necessários de todos os dados históricos que estão disponível e irão aprender continuamente e adeptos com base nos dados novos para fazer com que os limiares mais precisos.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>A quantidade de dados é necessário para acionar um alerta?

Se tiver um novo recurso ou dados de métricos em falta, limiares dinâmicos não aciona alertas antes de três dias de dados estão disponíveis para garantir que os limiares precisos.

## <a name="dynamic-thresholds-best-practices"></a>Práticas recomendadas de limiares dinâmicas

Limiares dinâmicos podem ser aplicados a qualquer plataforma ou de uma métrica personalizada no Azure Monitor e ele também foi otimizado para as métricas comuns de aplicação e infraestrutura.
Os itens seguintes são as práticas recomendadas sobre como configurar alertas em alguns destas métricas com limiares dinâmicos.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Limiares dinâmicos em métricas de percentagem de CPU de máquinas virtuais

1. Na [portal do Azure](https://portal.azure.com), clique em **Monitor**. O modo de exibição de Monitor consolida todas as suas monitorização definições e dados numa vista.

2. Clique em **alertas** , em seguida, clique em **+ nova regra de alerta**.

    > [!TIP]
    > A maioria dos painéis de recursos também tem **alertas** no respetivo menu de recursos sob **monitorização**, poderia criar alertas a partir do mesmo.

3. Clique em **selecionar destino**, no painel de contexto que carrega, selecione um recurso de destino que quer receber o alerta. Uso **subscrição** e **tipo de recurso de "Máquinas virtuais"** listas pendentes para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Assim que tiver selecionado um recurso de destino, clique em **adicionar condição**.

5. Selecione o **percentagem de CPU**.

6. Opcionalmente, refinar a métrica ao ajustar **período** e **agregação**. Ele não é recomendado para utilizar o tipo de agregação "Maximum" para este tipo de métrica, que é menos representativos de comportamento. Para o limiar 'Máximo' de estático do tipo agregação talvez mais apropriado.

7. Verá um gráfico para a métrica para as últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** -escolher a opção 'Dynamic'.
    1. **Sensibilidade** -sensibilidade de escolha médio ou com pouco para reduzir o ruído de alerta.
    1. **Operador** -escolher 'Maior que' a menos que o comportamento representa a utilização da aplicação.
    1. **Frequência** -considere reduzir com base no impacto comercial do alerta.
    1. **Falhar períodos** (opção avançada), a janela de back-olhada deve ser, pelo menos, 15 minutos. Se o período for definido para cinco minutos, por exemplo, em seguida, falhar períodos deve ser, pelo menos, três ou mais.

8. O gráfico de métricas irá apresentar os limiares calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha **detalhes do alerta** como **nome da regra de alerta**, **Descrição**, e **gravidade**.

11. Adicione um grupo de ação para o alerta ao selecionar um grupo de ação existente ou criar um novo grupo de ação.

12. Clique em **feito** para guardar a regra de alerta de métrica.

> [!NOTE]
> Alerta de métrica regras criadas através do portal são criadas no mesmo grupo de recursos como o recurso de destino.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Limiares dinâmicos no tempo de execução do pedido de HTTP do Application Insights

1. Na [portal do Azure](https://portal.azure.com), clique em **Monitor**. O modo de exibição de Monitor consolida todas as suas monitorização definições e dados numa vista.

2. Clique em **alertas** , em seguida, clique em **+ nova regra de alerta**.

    > [!TIP]
    > A maioria dos painéis de recursos também tem **alertas** no respetivo menu de recursos sob **monitorização**, poderia criar alertas a partir do mesmo.

3. Clique em **selecionar destino**, no painel de contexto que carrega, selecione um recurso de destino que quer receber o alerta. Uso **subscrição** e **tipo de recurso de "Application Insights"** listas pendentes para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Assim que tiver selecionado um recurso de destino, clique em **adicionar condição**.

5. Selecione o **'Tempo de execução do pedido HTTP'**.

6. Opcionalmente, refinar a métrica ao ajustar **período** e **agregação**. Ele não é recomendado para utilizar o tipo de agregação "Maximum" para este tipo de métrica, que é menos representativos de comportamento. Para o limiar 'Máximo' de estático do tipo agregação talvez mais apropriado.

7. Verá um gráfico para a métrica para as últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** -escolher a opção 'Dynamic'.
    1. **Operador** -escolher 'maior do que"para reduzir alertas acionados no aprimoramento de duração.
    1. **Frequência** -considere reduzir com base no impacto comercial do alerta.

8. O gráfico de métricas irá apresentar os limiares calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha **detalhes do alerta** como **nome da regra de alerta**, **Descrição**, e **gravidade**.

11. Adicione um grupo de ação para o alerta ao selecionar um grupo de ação existente ou criar um novo grupo de ação.

12. Clique em **feito** para guardar a regra de alerta de métrica.

> [!NOTE]
> Alerta de métrica regras criadas através do portal são criadas no mesmo grupo de recursos como o recurso de destino.
