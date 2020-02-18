---
title: Criação de alertas com limiares dinâmicos no Monitor Azure
description: Criar alertas com limiares dinâmicos baseados em machine learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2020
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: f67dcf7f1f4f39a11eb995995a8d0acc278b5d4a
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373406"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alertas métricos com limiares dinâmicos no Monitor Azure

Alerta métrico com limiardinâmico a deteção alavanca a aprendizagem avançada de máquinas (ML) para aprender o comportamento histórico das métricas, identificar padrões e anomalias que indicam possíveis problemas de serviço. Fornece suporte tanto de uma UI simples como de operações em escala, permitindo que os utilizadores configurem regras de alerta através da API do Gestor de Recursos Azure, de forma totalmente automatizada.

Uma vez criada uma regra de alerta, só disparará quando a métrica monitorizada não se comportar como esperado, com base nos seus limiares personalizados.

Adoraríamos ouvir o seu feedback, mantê-lo vindo em <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Por que e quando é recomendado o tipo de condição dinâmica?

1. **Alerta escalável** – As regras dinâmicas de alerta de limiar podem criar limiares personalizados para centenas de séries métricas de cada vez, proporcionando ainda a mesma facilidade de definir uma regra de alerta numa única métrica. Dão-lhe menos alerta para criar e gerir. Pode utilizar o portal Azure ou a API do Gestor de Recursos Azure para criá-los. A abordagem escalável é especialmente útil quando se trata de dimensões métricas ou quando se aplica a múltiplos recursos, como por exemplo a todos os recursos de subscrição.  [Saiba mais sobre como configurar alertas métricos com limiares dinâmicos usando modelos](alerts-metric-create-templates.md).

1. Reconhecimento de **Padrões Métricos Inteligentes** – Utilizando a nossa tecnologia ML, somos capazes de detetar automaticamente padrões métricos e adaptar-nos às alterações métricas ao longo do tempo, que podem muitas vezes incluir sazonalidade (hora/ dia/semanal). Adaptar-se ao comportamento das métricas ao longo do tempo e alertar com base em desvios do seu padrão alivia o fardo de conhecer o limiar "certo" para cada métrica. O algoritmo ML utilizado em Limiares Dinâmicos foi concebido para evitar limiares ruidosos (de baixa precisão) ou largos (baixa recolha) que não têm um padrão esperado.

1. **Configuração Intuitiva** – Limiares Dinâmicos permite configurar alertas métricos utilizando conceitos de alto nível, aliviando a necessidade de ter um vasto conhecimento de domínio sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar regras de alerta com limiares dinâmicos?

Os alertas com limiares dinâmicos podem ser configurados através de alertas métricos no Monitor Azure. [Saiba mais sobre como configurar alertas métricos.](alerts-metric.md)

## <a name="how-are-the-thresholds-calculated"></a>Como são calculados os limiares?

Dynamic Thresholds aprende continuamente os dados da série métrica e tenta modelá-lo usando um conjunto de algoritmos e métodos. Deteta padrões nos dados como sazonalidade (Hora/ Diária / Semanal), e é capaz de lidar com métricas ruidosas (como cpu da máquina ou memória) bem como métricas com baixa dispersão (como disponibilidade e taxa de erro).

Os limiares são selecionados de modo a que um desvio destes limiares indique uma anomalia no comportamento métrico.

> [!NOTE]
> A deteção de padrões sazonais é definida para um intervalo de hora, dia ou semana. Isto significa que outros padrões como o padrão bi-hora ou semi-semanalmente podem não ser detetados.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que significa a definição de "sensibilidade" nos limiares dinâmicos?

A sensibilidade do limiar de alerta é um conceito de alto nível que controla a quantidade de desvio do comportamento métrico necessário para desencadear um alerta.
Esta opção não requer conhecimento de domínio sobre a métrica como o limiar estático. As opções disponíveis são:

- Alto – Os limiares serão apertados e próximos do padrão da série métrica. Será desencadeada uma regra de alerta sobre o menor desvio, resultando em mais alertas.
- Médio – Limiares menos apertados e mais equilibrados, menos alertas do que com elevada sensibilidade (padrão).
- Baixo – Os limiares serão soltos com mais distância do padrão da série métrica. Uma regra de alerta só irá desencadear grandes desvios, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de definição do 'Operador' em Limiares Dinâmicos?

A regra dos alertas Dynamic Thresholds pode criar limiares personalizados com base no comportamento métrico para os limites superiores e inferiores utilizando a mesma regra de alerta.
Pode escolher o alerta a ser acionado numa das três seguintes condições:

- Maior do que o limiar superior ou inferior ao limiar inferior (predefinido)
- Maior do que o limiar superior
- Inferior ao limiar inferior.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que significam as definições avançadas em Limiares Dinâmicos?

**Períodos** de Falha - Limiares Dinâmicos também permite configurar "Violações de númeropara desencadear o alerta", um número mínimo de desvios necessários dentro de um determinado período de tempo para o sistema levantar um alerta (a janela de tempo padrão é de quatro desvios em 20 minutos). O utilizador pode configurar períodos de falha e escolher o que deve ser alertado alterando os períodos de falha e a janela de tempo. Esta capacidade reduz o ruído de alerta gerado por picos transitórios. Por exemplo:

Para desencadear um alerta quando o problema for contínuo durante 20 minutos, 4 vezes consecutivas num determinado agrupamento de períodos de 5 minutos, utilize as seguintes definições:

![Definições de períodos de falha para problema contínuo durante 20 minutos, 4 vezes consecutivas num determinado agrupamento de períodos de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para desencadear um alerta quando houve uma violação de um Limiar Dinâmico em 20 minutos dos últimos 30 minutos com o período de 5 minutos, utilize as seguintes definições:

![Definições de períodos falhados para emissão durante 20 minutos dos últimos 30 minutos com agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignore os dados antes** - Os utilizadores também podem definir opcionalmente uma data de início a partir da qual o sistema deve começar a calcular os limiares a partir. Um caso de utilização típica pode ocorrer quando um recurso estava em execução em modo de teste e é agora promovido para servir uma carga de trabalho de produção, e, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser ignorado.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Como é que descobres porque é que foi acionado um alerta dynamic Thresholds?

Pode explorar casos de alerta desencadeados na visualização de alertas, clicando no link no e-mail ou mensagem de texto, ou navegador para ver a visualização dos alertas no portal Azure. [Saiba mais sobre a visão dos alertas.](alerts-overview.md#alerts-experience)

A vista de alerta mostra:

- Todos os detalhes métricos no momento o alerta Dynamic Thresholds disparou.
- Um gráfico do período em que o alerta foi desencadeado que inclui os Limiares Dinâmicos utilizados nesse momento.
- Capacidade de fornecer feedback sobre o alerta de Limiares Dinâmicos e a experiência de visualização de alertas, o que poderia melhorar as deteções futuras.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Mudanças lentas de comportamento na métrica desencadearão um alerta?

Provavelmente não. Os limiares dinâmicos são bons para detetar desvios significativos em vez de questões em evolução lenta.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Quantos dados são usados para pré-visualizar e, em seguida, calcular limiares?

Quando uma regra de alerta é criada pela primeira vez, os limiares que aparecem no gráfico são calculados com base em dados históricos suficientes para calcular padrões sazonais de hora ou diárias (10 dias). Uma vez criada uma regra de alerta, a Dynamic Thresholds utiliza todos os dados históricos necessários que estão disponíveis e aprenderá e adaptar-se-á continuamente com base em novos dados para tornar os limiares mais precisos. Isto significa que, após este cálculo, o gráfico também exibirá padrões semanais.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Quantos dados são necessários para desencadear um alerta?

Se tiver um novo recurso ou dados métricos em falta, os Limiares Dinâmicos não desencadearão alertas antes de três dias ou 30 amostras de dados métricos estarem disponíveis para garantir limiares precisos.

## <a name="dynamic-thresholds-best-practices"></a>As melhores práticas dos Limiares Dinâmicos

Os Limiares Dinâmicos podem ser aplicados a qualquer plataforma ou métrica personalizada no Monitor Azure e também foi sintonizado para as métricas comuns de aplicação e infraestrutura.
Os seguintes itens são as melhores práticas de como configurar alertas em algumas destas métricas usando Limiares Dinâmicos.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Limiares Dinâmicos nas métricas percentuais de CPU da máquina virtual

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A visão do Monitor consolida todas as suas definições de monitorização e dados numa só vista.

2. Clique em **Alertas** e clique + **Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** você poderia criar alertas a partir daí também.

3. Clique em **Selecionar o alvo**, no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize a **subscrição** e as desistências do **tipo de recursos 'Máquinas Virtuais'** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Depois de ter selecionado um recurso-alvo, clique na **condição de Adicionar**.

5. Selecione a **"Percentagem de CPU".**

6. Opcionalmente, refine a métrica ajustando o **período** e **a agregação.** É desencorajado usar o tipo de agregação 'Máximo' para este tipo métrico, uma vez que é menos representativo do comportamento. Para o limiar estático do tipo de agregação "máximo", talvez mais apropriado.

7. Verá um gráfico para a métrica nas últimas 6 horas. Definir os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dinâmica'.
    1. **Sensibilidade** - Escolha a sensibilidade média/baixa para reduzir o ruído de alerta.
    1. **Operador** - Escolha 'Maior do que' a menos que o comportamento represente o uso da aplicação.
    1. **Frequência** - Considere baixar com base no impacto do negócio do alerta.
    1. **Períodos de falha** (Opção Avançada) - A janela de olhar para trás deve ser de pelo menos 15 minutos. Por exemplo, se o período for definido para cinco minutos, então os períodos de incisão devem ser pelo menos três ou mais.

8. O gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Clique em **Concluído**.

10. Preencha **os detalhes** do Alerta como Nome da Regra de **Alerta,** **Descrição**e **Gravidade**.

11. Adicione um grupo de ação ao alerta, quer selecionando um grupo de ação existente, quer criando um novo grupo de ação.

12. Clique **em Feito** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrico criadas através do portal são criadas no mesmo grupo de recursos que o recurso-alvo.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Limiares dinâmicos em Insights de Aplicação HTTP solicitar tempo de execução

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A visão do Monitor consolida todas as suas definições de monitorização e dados numa só vista.

2. Clique em **Alertas** e clique + **Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** você poderia criar alertas a partir daí também.

3. Clique em **Selecionar o alvo**, no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize a **subscrição** e as desistências do **tipo de recursos 'Application Insights'** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Depois de ter selecionado um recurso-alvo, clique na **condição de Adicionar**.

5. Selecione o **tempo de execução do pedido 'HTTP request'.**

6. Opcionalmente, refine a métrica ajustando o **período** e **a agregação.** É desencorajado usar o tipo de agregação 'Máximo' para este tipo métrico, uma vez que é menos representativo do comportamento. Para o limiar estático do tipo de agregação "máximo", talvez mais apropriado.

7. Verá um gráfico para a métrica nas últimas 6 horas. Definir os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dinâmica'.
    1. **Operador** - Escolha 'Maior do que' para reduzir os alertas disparados sobre a melhoria da duração.
    1. **Frequência** - Considere baixar com base no impacto do negócio do alerta.

8. O gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Clique em **Concluído**.

10. Preencha **os detalhes** do Alerta como Nome da Regra de **Alerta,** **Descrição**e **Gravidade**.

11. Adicione um grupo de ação ao alerta, quer selecionando um grupo de ação existente, quer criando um novo grupo de ação.

12. Clique **em Feito** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrico criadas através do portal são criadas no mesmo grupo de recursos que o recurso-alvo.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretar gráficos de limiar dinâmico

Segue-se um gráfico que mostra uma métrica, os seus limites dinâmicos de limiar, e alguns alertas disparados quando o valor estava fora dos limiares permitidos.

![Saiba mais sobre como configurar alertas métricos](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Utilize as seguintes informações para interpretar o gráfico anterior.

- **Linha azul** - A métrica medida real ao longo do tempo.
- **Área sombreada azul** - Mostra o alcance permitido para a métrica. Enquanto os valores métricos se mantiverem dentro deste intervalo, não ocorrerá nenhum alerta.
- **Pontos azuis** - Se deixar clicar em parte da tabela e, em seguida, pairar sobre a linha azul, verá um ponto azul aparecer sob o seu cursor mostrando um valor métrico agregado individual.
- **Pop-up com ponto azul** - Mostra o valor métrico medido (o ponto azul) e os valores superiores e inferiores da gama permitida.  
- **Ponto vermelho com um círculo preto** - Mostra o primeiro valor métrico fora do alcance permitido. Este é o valor que dispara um alerta métrico e o coloca em estado ativo.
- **Pontos vermelhos**- Indicar valores medidos adicionais fora do intervalo permitido. Não vão disparar alertas métricos adicionais, mas o alerta permanece no ativo.
- **Área vermelha** - Mostra a época em que o valor métrico estava fora do alcance permitido. O alerta permanece no estado ativo, desde que os valores medidos subsequentes estejam fora do alcance permitido, mas não são disparados novos alertas.
- **Fim da área vermelha** - Quando a linha azul está de volta dentro dos valores permitidos, a área vermelha para e a linha de valor medida fica azul. O estado do alerta métrico disparado no momento do ponto vermelho com contorno preto está definido para ser resolvido. 
