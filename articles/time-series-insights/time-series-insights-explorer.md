---
title: Explorar dados com o Explorador do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como utilizar o Explorador do Azure Time Series Insights no seu browser para ver rapidamente uma vista global dos seus macrodados e validar o seu ambiente de IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2017
ms.custom: seodec18
ms.openlocfilehash: 301326f4ba858c7aef6c8bdec8f2cae3ed926847
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121048"
---
# <a name="azure-time-series-insights-explorer"></a>Explorador do Time Series Insights do Azure
Este artigo explora os vários recursos e as opções disponíveis dentro da aplicação de web de Explorador do Time Series Insights. Utilize o Explorador do Time Series Insights no seu browser para criar visualizações dos dados.
 
O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Ele fornece uma vista global dos seus dados, que lhe permite validar rapidamente a sua solução de IoT e evitar o período de indisponibilidade dispendioso para dispositivos fundamentais. Pode descobrir tendências ocultas, detetar anomalias e realizar análises de causa raiz em tempo real. O Explorador do Time Series Insights está atualmente em pré-visualização pública.

## <a name="video"></a>Vídeo:

Neste vídeo, vamos abordar consultar dados com o Explorador do Time Series Insights. 

Este vídeo baseia-se no vídeo do Time Series Insights:  
<span style="color:blue">Obtendo começa com o Time Series Insights usando um acelerador de solução de IoT do Azure.</span>
</br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o Explorador do Time Series Insights, faça o seguinte:
- Criar um ambiente do Time Series Insights
- Fornecer acesso à sua conta no ambiente
- Adicionar uma origem de eventos para ingerir dados e armazená-lo

## <a name="explore-and-query-data"></a>Explore e consultar dados
Dentro de minutos de ligar-se a origem do evento para o seu ambiente do Time Series Insights, pode explorar e consultar os dados de séries de tempo.

1. Para iniciar, abra a [Explorador do Time Series Insights](https://insights.timeseries.azure.com/) no seu navegador da web e selecione um ambiente no lado esquerdo da janela. Todos os ambientes que tem acesso ao estão listados em ordem alfabética.

2. Depois de selecionar um ambiente, utilizar o **FROM** e **TO** configurações na parte superior, ou clique e arraste ao longo de seu período de tempo pretendido.  Clique na lupa no canto superior direito, ou ao longo do período de tempo selecionado com o botão direito e selecione **pesquisa**.  

3. Também pode atualizar disponibilidade automaticamente a cada minuto, ao selecionar o **automática no** botão.  Tenha em atenção de que o botão 'Auto-On' aplica-se apenas para o gráfico de disponibilidade, não o conteúdo da visualização principal.

4. Observe que o ícone de cloud do Azure leva-o para seu ambiente no portal do Azure.

   ![Ambiente de Informações de Série de Tempo](media/time-series-insights-explorer/explorer1.png)

5. Em seguida, verá um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado.  Aqui tem uma série de controles:

    **Painel do Editor de termos**:  O espaço de termo é onde consultar o seu ambiente.  Que pode ser encontrada no lado esquerdo do ecrã, ativa 
      - **Medida**:  Esta lista pendente mostra todas as colunas numéricas (duplicatas)
      - **Dividir por**: Esta lista pendente mostra colunas categóricas (cadeias de caracteres)
      - Pode ativar a interpolação linear, Mostrar mínimo e máximo e ajustar o eixo y do painel de controlo, em seguida, para medir.  Além disso, pode ajustar se dados mostrados são uma contagem, média ou a soma dos dados.
      - Pode adicionar até cinco termos para ver no mesmo eixo x.  Utilize o **cópia pendente** botão para adicionar um termo adicional ou clique nas **Add** botão para adicionar um novo termo.
     
        ![Painel do Editor de termos](media/time-series-insights-explorer/explorer2.png)

      - **Predicado**:  O predicado permite-lhe filtrar rapidamente os seus eventos usando o conjunto de operandos listados abaixo. Se realizar uma pesquisa ao selecionar/clicar, o predicado serão atualizados automaticamente com base no que a pesquisa.      Tipos de operando suportados incluem:

         |Operação  |Tipos suportados  |Notas  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Período de tempo de Double, DateTime,       |         |
         |=, !=, <>     | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo        |         |
         |IN     | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo        |  Todos os operandos devem ser do mesmo tipo ou ser constante NULL.        |
         |HAS     | String        |  Literais de cadeia de caracteres constante só são permitidas no lado direito. Cadeia de caracteres vazia e NULL não são permitidas.       |

      - **Exemplos de consultas**
      
         ![Consultas de exemplo](media/time-series-insights-explorer/explorer9.png)

6. O **tamanho do intervalo** ferramenta de controlo de deslize permite-lhe ampliar e intervalos de reduzir durante o período de tempo do mesmo.  Esta opção fornece um controle mais preciso de movimento entre grandes frações de tempo que mostram tendências uniformes para baixo para setores tão pequena como milissegundo, permitindo que veja reduções de alta resolução, granulares dos seus dados. Ponto de partida de predefinição do controlo de deslize está definida como a exibição ideal dos dados da sua seleção; balanceamento de resolução, velocidade de consulta e granularidade.

7. O **tempo Pincel** ferramenta torna mais fácil navegar a partir de um intervalo de tempo para outro, colocando intuitiva frente de experiência do Usuário e no Centro de movimento totalmente integrado entre intervalos de tempo.

8. O **guardar** comando permite-lhe guardar a consulta atual e ativá-la para a partilha com outros utilizadores do ambiente. Usando **aberto**, pode ver todas as suas consultas guardadas e quaisquer consultas partilhadas de outros usuários em ambientes que tem acesso. 

   ![Consultas](media/time-series-insights-explorer/explorer3.png)

9. O **vista de ponto de vista** ferramenta fornece uma vista simultânea de até quatro consultas exclusivas. Pode encontrar o botão de vista da perspectiva no canto superior direito do gráfico.  

   ![Vista de perspetiva](media/time-series-insights-explorer/explorer4.png)

10. O **gráfico** permite-lhe explorar visualmente os seus dados. Ferramentas de gráfico incluem:

    - Selecione/clique, que permite uma seleção de um intervalo de tempo específico ou de uma série de dados individual.  
    - Num período de tempo span seleção, pode aplicar zoom ou explorar eventos.  
    - Dentro de uma série de dados, pode dividir as séries por outra coluna, adicionar a série como novo termo, mostrar apenas a série selecionada, excluir a série selecionada, enviar um ping a série ou explorar eventos da série selecionada.
    - Na área de filtro à esquerda do gráfico, pode ver todas as séries de dados exibidos e reordenar por valor ou o nome, ver todas as séries de dados ou séries especificamente afixados ou removidos.  Também pode selecionar uma série de dados individual e dividir as séries por outra coluna, adicionar a série como novo termo, mostrar apenas a série selecionada, excluir a série selecionada, afixar a série ou explorar eventos da série selecionada.
    - Ao visualizar vários termos em simultâneo, pode de pilha, desempilhar, consulte dados adicionais sobre uma série de dados e utilizar o mesmo eixo y entre todos os termos com os botões no canto superior direito do gráfico.
 
    ![Ferramentas do gráfico](media/time-series-insights-explorer/explorer5.png) 

11. O **mapa térmico** pode ser utilizado para detetar rapidamente a série de dados exclusivos ou anómalos numa determinada consulta. Termo de pesquisa apenas um pode ser visualizado como um mapa térmico.    

    ![Mapa térmico](media/time-series-insights-explorer/explorer6.png)

12. **Eventos**:  Ao escolher explorar eventos ao selecionar ou clicando com o botão direito acima, o painel de eventos é disponibilizado.  Aqui, pode ver todos os eventos não processados e exportar seus eventos como JSON ou ficheiros CSV. Tenha em atenção que o Time Series Insights armazena todos os dados não processados.

    ![Eventos](media/time-series-insights-explorer/explorer7.png)

13. Clique nas **estatísticas** separador depois explorar eventos para expor os padrões e as estatísticas de coluna.  

    - **Padrões**: esta funcionalidade analisam proativamente as os padrões mais estatisticamente significativos numa região de dados selecionada. Isso libera da necessidade de examinar a milhares de eventos para compreender quais padrões garantem mais tempo e energia. Além disso, o Time Series Insights permite-lhe passar diretamente para estes padrões estatisticamente significativos para continuar a realizar uma análise. Esta funcionalidade também é útil para investigações de post-mortem a dados históricos. 

    - **Estatísticas de coluna**:  Estatísticas de coluna fornecem a criação de gráficos e tabelas que dividir dados de cada coluna da série de dados selecionadas ao longo do período de tempo selecionado.  
 
      ![ESTATÍSTICAS](media/time-series-insights-explorer/explorer8.png) 

Agora viu os vários recursos e as opções disponíveis dentro da aplicação de web de Explorador do Time Series Insights. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
>[Diagnosticar e resolver problemas no seu ambiente do Time Series Insights](time-series-insights-diagnose-and-solve-problems.md)
