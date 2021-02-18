---
title: Criar alertas com limiares dinâmicos no Monitor Azure
description: Criar alertas com limiares dinâmicos baseados em aprendizagem automática
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 31a034a7f52efd915b7a07e1abb953a14839892e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614711"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alertas métricos com limiares dinâmicos no Monitor Azure

O Alerta Métrico com A Deteção de Limiares Dinâmicos aproveita a aprendizagem de máquinas avançadas (ML) para aprender o comportamento histórico das métricas, identificar padrões e anomalias que indicam possíveis problemas de serviço. Fornece suporte tanto de uma UI simples como de operações em escala, permitindo aos utilizadores configurar as regras de alerta através da Azure Resource Manager API, de forma totalmente automatizada.

Uma vez criada uma regra de alerta, só disparará quando a métrica monitorizada não se comportar como esperado, com base nos seus limiares personalizados.

Adoraríamos ouvir o seu feedback, mantê-lo <azurealertsfeedback@microsoft.com> vindo.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Porquê e quando é recomendado o tipo de condição dinâmica?

1. **Alerta escalável** – As regras dinâmicas de alerta de limiares podem criar limiares adaptados para centenas de séries métricas de cada vez, mas proporcionando a mesma facilidade de definir uma regra de alerta numa única métrica. Dão-lhe menos alerta para criar e gerir. Pode utilizar o portal Azure ou a API do Gestor de Recursos Azure para os criar. A abordagem escalável é especialmente útil quando se lida com dimensões métricas ou quando se aplica a múltiplos recursos, tais como todos os recursos de subscrição.  [Saiba mais sobre como configurar alertas métricos com limiares dinâmicos usando modelos.](../platform/alerts-metric-create-templates.md)

1. **Reconhecimento de Padrões Métricos Inteligentes** – Utilizando a nossa tecnologia ML, somos capazes de detetar automaticamente padrões métricos e adaptar-nos a mudanças métricas ao longo do tempo, que podem muitas vezes incluir sazonalidade (hora/dia/semana). Adaptar-se ao comportamento das métricas ao longo do tempo e alertar com base em desvios do seu padrão alivia o fardo de conhecer o limiar "certo" para cada métrica. O algoritmo ML utilizado em Limiares Dinâmicos é projetado para prevenir limiares ruidosos (de baixa precisão) ou largos (baixa recuperação) que não têm um padrão esperado.

1. **Configuração Intuitiva** – Os Limiares Dinâmicos permitem a configuração de alertas métricos utilizando conceitos de alto nível, aliviando a necessidade de ter um vasto conhecimento de domínio sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar regras de alerta com limiares dinâmicos?

Os alertas com limiares dinâmicos podem ser configurados através de Alertas Métricos no Monitor Azure. [Saiba mais sobre como configurar alertas métricos.](alerts-metric.md)

## <a name="how-are-the-thresholds-calculated"></a>Como são calculados os limiares?

Os Limiares Dinâmicos aprendem continuamente os dados da série métrica e tentam modelá-lo usando um conjunto de algoritmos e métodos. Deteta padrões nos dados como a sazonalidade (Hora/Dia/Semanal), e é capaz de lidar com métricas ruidosas (como CPU ou memória) da máquina, bem como métricas com baixa dispersão (como disponibilidade e taxa de erro).

Os limiares são selecionados de forma a que um desvio destes limiares indique uma anomalia no comportamento métrico.

> [!NOTE]
> Os Limiares Dinâmicos podem detetar a sazonalidade para padrões horários, diários ou semanais. Outros padrões como a sazonalidade bi-horária ou semi-semanal podem não ser detetados. Para detetar a sazonalidade semanal, são necessárias pelo menos três semanas de dados históricos. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que significa a definição de "Sensibilidade" nos Limiares Dinâmicos?

A sensibilidade ao limiar de alerta é um conceito de alto nível que controla a quantidade de desvio do comportamento métrico necessário para desencadear um alerta.
Esta opção não requer conhecimento de domínio sobre a métrica como limiar estático. As opções disponíveis são:

- Alto – Os limiares serão apertados e próximos do padrão da série métrica. Uma regra de alerta será desencadeada no menor desvio, resultando em mais alertas.
- Médio – Limiares menos apertados e mais equilibrados, menos alertas do que com alta sensibilidade (padrão).
- Baixo – Os limiares serão soltos com mais distância do padrão de série métrica. Uma regra de alerta só irá desencadear grandes desvios, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de definição de 'Operador' em Limiares Dinâmicos?

A regra dos alertas de limiares dinâmicos pode criar limiares personalizados com base no comportamento métrico para os limites superior e inferior usando a mesma regra de alerta.
Pode escolher o alerta a ser acionado numa das três condições seguintes:

- Maior do que o limiar superior ou inferior ao limiar inferior (padrão)
- Maior do que o limiar superior
- Inferior ao limiar inferior.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que significam as definições avançadas em Limiares Dinâmicos?

**Períodos de Falha** - Os Limiares Dinâmicos também permitem configurar "Violações de números para desencadear o alerta", um número mínimo de desvios exigidos dentro de um determinado intervalo de tempo para o sistema levantar um alerta (a janela de tempo padrão é de quatro desvios em 20 minutos). O utilizador pode configurar períodos de insívaria e escolher o que deve ser alertado alterando os períodos de falha e a janela de tempo. Esta capacidade reduz o ruído de alerta gerado por picos transitórios. Por exemplo:

Para acionar um alerta quando o problema for contínuo durante 20 minutos, 4 vezes consecutivas num determinado período de agrupamento de 5 minutos, utilize as seguintes definições:

![Definições de períodos de falha para emissão contínua durante 20 minutos, 4 vezes consecutivas num determinado período de agrupamento de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para desencadear um alerta quando houve uma violação de um Limiar Dinâmico em 20 minutos dos últimos 30 minutos com o período de 5 minutos, utilize as seguintes definições:

![Definições de períodos de falha para emissão durante 20 minutos dos últimos 30 minutos com agrupamento de períodos de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignore os dados antes** - Os utilizadores também podem definir opcionalmente uma data de início a partir da qual o sistema deve começar a calcular os limiares a partir. Um caso de utilização típico pode ocorrer quando um recurso era um funcionamento em modo de teste e agora é promovido para servir uma carga de trabalho de produção, e, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser ignorado.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Como é que descobres porque é que um alerta dynamic thresholds foi acionado?

Pode explorar casos de alerta desencadeados na visualização de alertas, clicando no link no e-mail ou mensagem de texto, ou no navegador para ver a vista de alertas no portal Azure. [Saiba mais sobre a vista de alertas.](../platform/alerts-overview.md#alerts-experience)

A vista de alerta apresenta:

- Todos os detalhes métricos no momento o alerta Dynamic Thresholds disparou.
- Um gráfico do período em que o alerta foi desencadeado que inclui os Limiares Dinâmicos utilizados nessa altura.
- Capacidade de fornecer feedback sobre o alerta Dynamic Thresholds e a experiência de visualização de alertas, o que pode melhorar as deteções futuras.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>As mudanças de comportamento lentas na métrica desencadearão um alerta?

Provavelmente não. Os limiares dinâmicos são bons para detetar desvios significativos em vez de evoluir lentamente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Quantos dados são usados para pré-visualizar e, em seguida, calcular limiares?

Quando uma regra de alerta é criada pela primeira vez, os limiares que aparecem no gráfico são calculados com base em dados históricos suficientes para calcular padrões sazonais de hora ou diárias (10 dias). Uma vez criada uma regra de alerta, a Dynamic Thresholds utiliza todos os dados históricos necessários que estão disponíveis e aprenderá e adaptar-se-á continuamente com base em novos dados para tornar os limiares mais precisos. Isto significa que após este cálculo, o gráfico também apresentará padrões semanais.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Quantos dados são necessários para desencadear um alerta?

Se tiver um novo recurso ou dados métricos em falta, os Limiares Dinâmicos não desencadearão alertas antes de três dias e pelo menos 30 amostras de dados métricos estão disponíveis, para garantir limiares precisos.
Para os recursos existentes com dados métricos suficientes, os Limiares Dinâmicos podem desencadear alertas imediatamente.

## <a name="dynamic-thresholds-best-practices"></a>Melhores práticas de Limiares Dinâmicos

Os Limiares Dinâmicos podem ser aplicados na maioria das métricas de plataforma e personalizados no Azure Monitor e também foi sintonizado para as métricas comuns de aplicação e infraestrutura.
Os seguintes itens são as melhores práticas sobre como configurar alertas em algumas destas métricas usando Limiares Dinâmicos.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Limiares dinâmicos em métricas percentuais de CPU de máquina virtual

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A visualização do Monitor consolida todas as suas definições e dados de monitorização numa única vista.

2. Clique **em Alertas** e clique **em + Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** podendo também criar alertas a partir daí.

3. Clique em **Selecionar o alvo,** no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize **a subscrição** e as **reduções do tipo de recursos 'Máquinas Virtuais'** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Uma vez selecionado um recurso-alvo, clique na **condição de Adicionar**.

5. Selecione a **'Percentagem cpu'.**

6. Opcionalmente, refinar a métrica ajustando **o período** e **a agregação**. É desencorajado a utilização do tipo de agregação 'Máximo' para este tipo métrico, uma vez que é menos representativo do comportamento. Para o limiar estático do tipo de agregação 'máximo' talvez mais adequado.

7. Verá um gráfico para a métrica nas últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dynamic'.
    1. **Sensibilidade** - Escolha sensibilidade média/baixa para reduzir o ruído de alerta.
    1. **Operador** - Escolha 'Greater Than' a menos que o comportamento represente a utilização da aplicação.
    1. **Frequência** - Considere baixar com base no impacto do negócio do alerta.
    1. **Períodos de falha** (Opção Avançada) - A janela de olhar para trás deve ser de pelo menos 15 minutos. Por exemplo, se o período estiver definido para cinco minutos, os períodos de insaluição devem ser de, pelo menos, três ou mais.

8. O gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Clique em **Concluído**.

10. Preencha **detalhes de alerta** como Nome da Regra de **Alerta,** **Descrição** e **Gravidade**.

11. Adicione um grupo de ação ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

12. Clique **em Fazer** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrica criadas através do portal são criadas no mesmo grupo de recursos que o recurso alvo.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Limiares dinâmicos em Insights de Aplicação HTTP solicitam tempo de execução

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A visualização do Monitor consolida todas as suas definições e dados de monitorização numa única vista.

2. Clique **em Alertas** e clique **em + Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** podendo também criar alertas a partir daí.

3. Clique em **Selecionar o alvo,** no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize **a subscrição** e as reduções **do tipo de recursos 'Application Insights'** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Uma vez selecionado um recurso-alvo, clique na **condição de Adicionar**.

5. Selecione o **'HTTP solicitar o tempo de execução'.**

6. Opcionalmente, refinar a métrica ajustando **o período** e **a agregação**. É desencorajado a utilização do tipo de agregação 'Máximo' para este tipo métrico, uma vez que é menos representativo do comportamento. Para o limiar estático do tipo de agregação 'máximo' talvez mais adequado.

7. Verá um gráfico para a métrica nas últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dynamic'.
    1. **Operador** - Escolha 'Greater Than' para reduzir os alertas disparados sobre a melhoria da duração.
    1. **Frequência** - Considere baixar com base no impacto do negócio do alerta.

8. O gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Clique em **Concluído**.

10. Preencha **detalhes de alerta** como Nome da Regra de **Alerta,** **Descrição** e **Gravidade**.

11. Adicione um grupo de ação ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

12. Clique **em Fazer** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrica criadas através do portal são criadas no mesmo grupo de recursos que o recurso alvo.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretação de gráficos de limiar dinâmicos

Segue-se um gráfico que mostra uma métrica, os seus limites de limiar dinâmicos, e alguns alertas disparados quando o valor estava fora dos limiares permitidos.

![Saiba mais sobre como configurar alertas métricos](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Utilize as seguintes informações para interpretar o gráfico anterior.

- **Linha azul** - A métrica medida real ao longo do tempo.
- **Área sombreada azul** - Mostra o alcance permitido para a métrica. Enquanto os valores métricos permanecerem dentro deste intervalo, não ocorrerá nenhum alerta.
- **Pontos azuis** - Se deixar um clique em parte da tabela e depois pairar sobre a linha azul, vê um ponto azul aparecer sob o seu cursor mostrando um valor métrico agregado individual.
- **Pop-up com ponto azul** - Mostra o valor métrico medido (o ponto azul) e os valores superiores e inferiores da gama permitida.  
- **Ponto vermelho com um círculo preto** - Mostra o primeiro valor métrico fora do alcance permitido. Este é o valor que dispara um alerta métrico e o coloca em estado ativo.
- **Pontos vermelhos**- Indicar valores adicionais medidos fora do intervalo permitido. Não vão disparar alertas métricos adicionais, mas o alerta permanece no ativo.
- **Área vermelha** - Mostra a época em que o valor métrico estava fora do alcance permitido. O alerta permanece no estado ativo enquanto os valores medidos subsequentes estiverem fora do alcance permitido, mas não são disparados novos alertas.
- **Fim da área vermelha** - Quando a linha azul está de volta dentro dos valores permitidos, a área vermelha para e a linha de valor medida torna-se azul. O estado do alerta métrico disparado no momento do ponto vermelho com contorno preto está definido para ser resolvido. 
