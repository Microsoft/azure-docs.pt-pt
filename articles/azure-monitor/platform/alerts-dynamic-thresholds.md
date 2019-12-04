---
title: Criando alertas com limites dinâmicos no Azure Monitor
description: Criar alertas com limites dinâmicos baseados em Machine Learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 750aded128804468ae557d7c016a50c5378d9217
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762522"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alertas de métrica com limites dinâmicos no Azure Monitor

O alerta de métrica com a detecção de limites dinâmicos aproveita o ML (aprendizado de máquina avançado) para aprender o comportamento histórico de métricas, identificar padrões e anomalias que indicam possíveis problemas de serviço. Ele oferece suporte a uma interface do usuário simples e a operações em escala, permitindo que os usuários configurem regras de alerta por meio da API de Azure Resource Manager, de maneira totalmente automatizada.

Depois que uma regra de alerta for criada, ela será acionada somente quando a métrica monitorada não se comportar conforme o esperado, com base em seus limites personalizados.

Adoraríamos ouvir seus comentários, mantê-los chegando em <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Por que e quando o está usando o tipo de condição dinâmica recomendado?

1. **Alertas escalonáveis** – as regras de alerta de limite dinâmico podem criar limites personalizados para centenas de séries métricas de cada vez, ainda fornecendo a mesma facilidade de definir uma regra de alerta em uma única métrica. Eles oferecem a você menos alertas para criar e gerenciar. Você pode usar portal do Azure ou a API Azure Resource Manager para criá-los. A abordagem escalonável é especialmente útil ao lidar com dimensões métricas ou ao se aplicar a vários recursos, como a todos os recursos de assinatura.  [Saiba mais sobre como configurar alertas de métrica com limites dinâmicos usando modelos](alerts-metric-create-templates.md).

1. **Reconhecimento de padrão de métrica inteligente** – usando nossa tecnologia ml, podemos detectar automaticamente padrões de métricas e adaptar-se às alterações de métrica ao longo do tempo, o que pode incluir sazonalidade (por hora/dia/semana). Adaptar-se ao comportamento de métricas ao longo do tempo e o alerta com base em desvios de seu padrão alivia a carga de saber o limite "certo" para cada métrica. O algoritmo ML usado em limites dinâmicos foi projetado para evitar limites de ruído (baixa precisão) ou largo (recall) que não têm um padrão esperado.

1. **Configuração intuitiva** – limites dinâmicos permite configurar alertas de métricas usando conceitos de alto nível, aliviando a necessidade de ter amplo conhecimento de domínio sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar regras de alertas com limites dinâmicos?

Alertas com limites dinâmicos podem ser configurados por meio de alertas de métrica no Azure Monitor. [Saiba mais sobre como configurar alertas de métricas](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Como os limites são calculados?

Os limites dinâmicos aprende continuamente os dados da série métrica e tenta modelar usando um conjunto de algoritmos e métodos. Ele detecta padrões nos dados, como sazonalidade (por hora/dia/semana), e é capaz de lidar com métricas ruidosas (como CPU ou memória do computador), bem como métricas com pouca dispersão (como disponibilidade e taxa de erros).

Os limites são selecionados de forma que um desvio desses limites indica uma anomalia no comportamento da métrica.

> [!NOTE]
> A detecção de padrão sazonal é definida como um intervalo de hora, dia ou semana. Isso significa que outros padrões como padrão bihora ou semiweekly podem não ser detectados.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que significa a configuração ' sensibilidade ' em limites dinâmicos?

A sensibilidade do limite de alerta é um conceito de alto nível que controla a quantidade de desvio do comportamento de métrica necessário para disparar um alerta.
Essa opção não exige conhecimento do domínio sobre a métrica, como o limite estático. As opções disponíveis são:

- Alta – os limites serão apertados e próximos ao padrão da série métrica. Uma regra de alerta será disparada no menor desvio, resultando em mais alertas.
- Média – limites menos rígidos e mais equilibrados, menos alertas do que com alta sensibilidade (padrão).
- Baixo – os limites serão soltos com mais distância do padrão da série métrica. Uma regra de alerta só será disparada em grandes desvios, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de configuração ' operator ' em limites dinâmicos?

A regra de alertas de limites dinâmicos pode criar limites personalizados com base no comportamento da métrica para os limites superior e inferior usando a mesma regra de alerta.
Você pode escolher o alerta a ser disparado em uma das três condições a seguir:

- Maior que o limite superior ou menor que o limite inferior (padrão)
- Maior que o limite superior
- Menor que o limite inferior.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que significam as configurações avançadas em limites dinâmicos?

**Períodos com falha** -os limites dinâmicos também permitem que você configure "violações de número para disparar o alerta", um número mínimo de desvios necessários em uma determinada janela de tempo para o sistema gerar um alerta (a janela de tempo padrão é de quatro desvios em 20 minutos). O usuário pode configurar períodos com falha e escolher o que será alertado alterando os períodos com falha e a janela de tempo. Essa capacidade reduz o ruído de alerta gerado por picos transitórios. Por exemplo:

Para disparar um alerta quando o problema for contínuo por 20 minutos, 4 vezes consecutivas em um determinado período de agrupamento de 5 minutos, use as seguintes configurações:

![Configurações de períodos com falha para o problema contínuo por 20 minutos, 4 vezes consecutivas em um determinado período de agrupamento de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para disparar um alerta quando houve uma violação de limites dinâmicos em 20 minutos dos últimos 30 minutos com o período de 5 minutos, use as seguintes configurações:

![Configurações de períodos com falha para o problema de 20 minutos dos últimos 30 minutos com agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignorar dados antes** -os usuários também podem definir, opcionalmente, uma data de início a partir da qual o sistema deve começar a calcular os limites. Um caso de uso típico pode ocorrer quando um recurso estava em execução em um modo de teste e agora é promovido para atender a uma carga de trabalho de produção e, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser desconsiderado.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Como descobrir por que um alerta de limites dinâmicos foi disparado?

Você pode explorar as instâncias de alerta disparadas no modo de exibição de alertas clicando no link no email ou na mensagem de texto ou no navegador para ver a exibição de alertas no portal do Azure. [Saiba mais sobre a exibição de alertas](alerts-overview.md#alerts-experience).

A exibição de alerta exibe:

- Todos os detalhes de métrica no momento em que o alerta de limites dinâmicos foi acionado.
- Um gráfico do período em que o alerta foi disparado, incluindo os limites dinâmicos usados nesse ponto no tempo.
- Capacidade de fornecer comentários sobre o alerta de limites dinâmicos e a experiência de exibição de alertas, que pode melhorar as detecções futuras.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>As alterações de comportamento serão lentas no gatilho de métrica de um alerta?

Provavelmente não. Os limites dinâmicos são bons para detectar desvios significativos em vez de problemas em evolução lentos.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Qual a quantidade de dados usada para visualizar e calcular os limites?

Quando e alerta é criado pela primeira vez, os limites que aparecem no gráfico são calculados com base em dados históricos suficientes para calcular a hora ou os padrões sazonais diários (10 dias). Depois que uma regra de alerta é criada, os limites dinâmicos usam todos os dados históricos necessários que estão disponíveis e irão aprender e se adaptar continuamente com base nos novos dados para tornar os limites mais precisos. Isso significa que, após esse cálculo, o gráfico também exibirá padrões semanais.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Qual é a quantidade de dados necessária para disparar um alerta?

Se você tiver um novo recurso ou dados de métrica ausentes, os limites dinâmicos não dispararão alertas antes que três dias de dados estejam disponíveis para garantir limites precisos.

## <a name="dynamic-thresholds-best-practices"></a>Práticas recomendadas de limites dinâmicos

Limites dinâmicos podem ser aplicados a qualquer plataforma ou métrica personalizada em Azure Monitor e também foi ajustado para as métricas comuns de aplicativo e infraestrutura.
Os itens a seguir são práticas recomendadas sobre como configurar alertas em algumas dessas métricas usando limites dinâmicos.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Limites dinâmicos em métricas de porcentagem de CPU de máquina virtual

1. Em [portal do Azure](https://portal.azure.com), clique em **Monitor**. A exibição do monitor consolida todas as suas configurações e dados de monitoramento em uma exibição.

2. Clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **alertas** em seu menu de recursos, em **monitoramento**, você pode criar alertas a partir daí também.

3. Clique em **Selecionar destino**, no painel de contexto que é carregado, selecione um recurso de destino que você deseja alertar. Use os menus suspensos de **assinatura** e **tipo de recurso de "máquinas virtuais"** para localizar o recurso que você deseja monitorar. Você também pode usar a barra de pesquisa para localizar o recurso.

4. Depois de selecionar um recurso de destino, clique em **Adicionar condição**.

5. Selecione a **' porcentagem de CPU '** .

6. Opcionalmente, refine a métrica ajustando o **período** e a **agregação**. Não é recomendável usar o tipo de agregação ' Maximum ' para esse tipo de métrica, pois ele é menos representativo de comportamento. Para o limite estático de tipo de agregação ' Maximum ', talvez mais apropriado.

7. Você verá um gráfico para a métrica das últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** – escolha a opção ' dinâmica '.
    1. **Sensibilidade** – escolha a sensibilidade média/baixa para reduzir o ruído de alerta.
    1. **Operador** -escolha ' maior que ', a menos que o comportamento represente o uso do aplicativo.
    1. **Frequência** -considere reduzir com base no impacto nos negócios do alerta.
    1. **Períodos com falha** (opção avançada)-a janela de pesquisa deve ser pelo menos 15 minutos. Por exemplo, se o período for definido como cinco minutos, os períodos com falha deverão ser pelo menos três ou mais.

8. O gráfico de métrica exibirá os limites calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha os **detalhes do alerta** , como **nome da regra de alerta**, **Descrição**e **severidade**.

11. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.

12. Clique em **concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> As regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Limites dinâmicos em Application Insights tempo de execução da solicitação HTTP

1. Em [portal do Azure](https://portal.azure.com), clique em **Monitor**. A exibição do monitor consolida todas as suas configurações e dados de monitoramento em uma exibição.

2. Clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **alertas** em seu menu de recursos, em **monitoramento**, você pode criar alertas a partir daí também.

3. Clique em **Selecionar destino**, no painel de contexto que é carregado, selecione um recurso de destino que você deseja alertar. Use os menus suspensos de **assinatura** e **tipo de recurso ' Application insights '** para localizar o recurso que você deseja monitorar. Você também pode usar a barra de pesquisa para localizar o recurso.

4. Depois de selecionar um recurso de destino, clique em **Adicionar condição**.

5. Selecione o **' tempo de execução da solicitação HTTP '** .

6. Opcionalmente, refine a métrica ajustando o **período** e a **agregação**. Não é recomendável usar o tipo de agregação ' Maximum ' para esse tipo de métrica, pois ele é menos representativo de comportamento. Para o limite estático de tipo de agregação ' Maximum ', talvez mais apropriado.

7. Você verá um gráfico para a métrica das últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** – escolha a opção ' dinâmica '.
    1. **Operador** -escolha ' maior que ' para reduzir os alertas disparados na duração da melhoria.
    1. **Frequência** -considere reduzir com base no impacto nos negócios do alerta.

8. O gráfico de métrica exibirá os limites calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha os **detalhes do alerta** , como **nome da regra de alerta**, **Descrição**e **severidade**.

11. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.

12. Clique em **concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> As regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretando gráficos de limites dinâmicos

A seguir está um gráfico mostrando uma métrica, seus limites de limite dinâmico e alguns alertas acionados quando o valor estava fora dos limites permitidos.

![Saiba mais sobre como configurar alertas de métricas](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Use as informações a seguir para interpretar o gráfico anterior.

- **Linha azul** – a métrica medida real ao longo do tempo.
- **Área sombreada azul** – mostra o intervalo permitido para a métrica. Desde que os valores de métrica permaneçam dentro desse intervalo, nenhum alerta ocorrerá.
- **Pontos azuis** -se você tiver clicado em parte do gráfico e passar o mouse sobre a linha azul, verá um ponto azul aparecendo no cursor mostrando um valor de métrica agregada individual.
- **Pop-up com ponto azul** – mostra o valor medido da métrica (o ponto azul) e os valores superior e inferior do intervalo permitido.  
- **Ponto vermelho com um círculo preto** – mostra o primeiro valor de métrica fora do intervalo permitido. Esse é o valor que dispara um alerta de métrica e o coloca em um estado ativo.
- **Pontos vermelhos**-indicam valores mediis adicionais fora do intervalo permitido. Eles não irão disparar alertas de métrica adicionais, mas o alerta permanece no modo ativo.
- **Área vermelha** – mostra a hora em que o valor da métrica estava fora do intervalo permitido. O alerta permanece no estado ativo, desde que os valores medidos subsequentes estejam fora do intervalo permitido, mas nenhum alerta novo será acionado.
- **Fim da área vermelha** – quando a linha azul está de volta dentro dos valores permitidos, a área vermelha é interrompida e a linha de valor medido fica azul. O status do alerta de métrica acionado no momento do ponto vermelho com contorno preto é definido como resolvido. 
