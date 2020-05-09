---
title: Monitor Azure Storage serviços com Monitor Azure para Armazenamento (pré-visualização)] Microsoft Docs
description: Este artigo descreve a funcionalidade Azure Monitor for Storage que fornece aos administradores de armazenamento uma compreensão rápida dos problemas de desempenho e utilização com as suas contas de Armazenamento Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: 497ea5a27a56ed5f8192b694fac1c52c8bf57ca6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691115"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorização do seu serviço de armazenamento com o Monitor Azure para armazenamento (pré-visualização)

O Azure Monitor para armazenamento (pré-visualização) fornece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de Armazenamento Azure. Você pode observar a capacidade de armazenamento, e o desempenho de duas maneiras, ver diretamente a partir de uma conta de armazenamento ou vista do Monitor Azure para ver através de grupos de contas de armazenamento. 

Este artigo irá ajudá-lo a compreender a experiência que o Azure Monitor para armazenamento (pré-visualização) oferece para obter conhecimentos acionáveis sobre a saúde e desempenho das contas de Armazenamento em escala, com capacidade para se concentrar em hotspots e diagnosticar problemas de latência, estrangulamento e disponibilidade.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introdução ao Monitor Azure para Armazenamento (pré-visualização)

Antes de mergulhar na experiência, deve entender como apresenta e visualiza a informação. Quer selecione a funcionalidade de Armazenamento diretamente a partir de uma conta de armazenamento ou do Monitor Azure, o Azure Monitor para armazenamento apresenta uma experiência consistente. 

Combinado que entrega:

* **Numa perspetiva de escala** que mostra uma visão instantânea da sua disponibilidade com base na saúde do serviço de armazenamento ou da operação API, a utilização mostra o número total de pedidos que o serviço de armazenamento recebe, e a latência mostrando o tempo médio que o serviço de armazenamento ou o tipo de operação API está a levar para processar pedidos. Também pode ver a capacidade por blob, arquivo, mesa e fila.

* **Faça análises** de uma determinada conta de armazenamento para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - disponibilidade, desempenho, falhas e capacidade. A seleção de qualquer uma dessas opções proporciona uma visão aprofundada das métricas.  

* **Personalizável** onde pode alterar quais as métricas que pretende ver, modificar ou definir limiares que se alinham com os seus limites, e economizar como o seu próprio livro. Os gráficos do livro podem ser fixados no painel azure.  

Esta funcionalidade não requer que ative ou configure nada, as métricas de armazenamento das suas contas de armazenamento são recolhidas por defeito. Se não estiver familiarizado com as métricas disponíveis no Armazenamento Azure, veja a descrição e definição nas métricas de Armazenamento Azure através da revisão das métricas de [armazenamento do Azure.](../../storage/common/storage-metrics-in-azure-monitor.md)

>[!NOTE]
>Não existe qualquer custo para aceder a esta funcionalidade e só será cobrado para as funcionalidades essenciais do Monitor Azure que configura ou ativa, conforme descrito na página de detalhes de preços do [Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>O Monitor Azure para armazenamento não suporta [contas v1 de uso geral](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Vista do Monitor Azure

Do Azure Monitor, pode visualizar detalhes de transações, latências e capacidade saem de várias contas de armazenamento na sua subscrição, e ajudar a identificar desempenho, problemas de capacidade e falhas.

Para ver a utilização e disponibilidade das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Monitor** a partir do painel da mão esquerda no portal Azure, e sob a secção **Insights,** selecione Contas de **Armazenamento (pré-visualização)**.

    ![Vista de contas de armazenamento múltiplas](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Livro de visão geral

No livro **de** resumo para a subscrição selecionada, a tabela apresenta métricas de armazenamento interativa e estado de disponibilidade de serviço para até 10 contas de armazenamento agruparadas dentro da subscrição. Pode filtrar os resultados com base nas opções selecionadas a partir das seguintes listas de drop-down:

* **Assinaturas** - apenas estão listadas subscrições que tenham contas de armazenamento.  

* **Contas de Armazenamento** - por padrão, 10 contas de armazenamento são pré-selecionadas. Se selecionar todas ou múltiplas contas de armazenamento no seletor de âmbito, serão devolvidas até 200 contas de armazenamento. Por exemplo, se tivesse um total de 573 contas de armazenamento em três subscrições que selecionou, apenas 200 contas seriam apresentadas. 

* **Intervalo** de tempo - por padrão, exibe as últimas 4 horas de informação com base nas seleções correspondentes feitas.

O telha de balcão sob as listas de drop-down rolls-up o número total de contas de armazenamento na subscrição e reflete quantas do total são selecionados. Existe código de cores condicional ou mapas de calor para colunas no livro que reportam métricas de transação ou erros. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. Para as colunas baseadas em erros, o valor está em vermelho e para as colunas métricas, o valor é em azul.

Selecione um valor nas colunas **Disponibilidade**, **Latência E2E,** **Latência do Servidor,** e **o tipo/erro** de transação direciona-o para um relatório adaptado ao tipo específico de métricas de armazenamento que correspondem à coluna selecionada para essa conta de armazenamento. Para obter mais informações sobre os livros de cada categoria, consulte a secção de livros de [armazenamento detalhado](#detailed-storage-workbooks) abaixo. 

>[!NOTE]
>Para mais detalhes sobre quais os erros que podem ser mostrados no relatório, consulte o [esquema do Tipo de Resposta](../../storage/common/monitor-storage-reference.md#metrics-dimensions) e procure tipos de resposta como **ServerOtherError,** **ClientOtherError**, **ClientThrottlingError**. Dependendo das contas de armazenamento selecionadas, se houver mais de três tipos de erros relatados, todos os outros erros são representados na categoria de **Outros**.

O limiar de **disponibilidade** padrão é:

* Aviso - 99%
* Crítico - 90%

Para definir um limiar de disponibilidade com base nos resultados da sua observação ou requisitos, a revisão [modifique o limiar de disponibilidade](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Livro de capacidade

**Selecione Capacidade** no topo da página e abre o livro de trabalho **capacidade.** Mostra-lhe a quantidade de armazenamento total utilizado na conta e capacidade utilizada por cada serviço de dados na conta para ajudar a identificar-se sobre e sob armazenamento utilizado.

![Múltiplas contas de armazenamento Livro de capacidade](./media/storage-insights-overview/storage-account-capacity-02.png) 

Há códigos de cores condicional ou mapas de calor para colunas no livro que reportam métricas de capacidade com um valor azul. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

Quando selecionar um valor sob qualquer uma das colunas do livro, você perfura até o livro de trabalho **da Capacidade** para a conta de armazenamento. Mais detalhes sobre o relatório de perfuração são descritos na secção de livros de [armazenamento detalhado](#detailed-storage-workbooks) abaixo. 

## <a name="view-from-a-storage-account"></a>Vista de uma conta de armazenamento

Para aceder ao Monitor Azure para VMs diretamente de uma conta de armazenamento:

1. No portal Azure, selecione contas de Armazenamento.

2. Na lista, escolha uma conta de armazenamento. Na secção de Monitorização, escolha Insights (pré-visualização).

    ![Página de visão geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-direct-overview-01.png)

No livro **de** resumo da conta de armazenamento, mostra várias métricas de desempenho de armazenamento que o ajudam a avaliar rapidamente:

* Saúde do serviço de armazenamento para ver imediatamente se um problema fora do seu controlo está afetando o serviço de armazenamento na região para o qual está implantado, o que é indicado sob a coluna **Resumo.**

* Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados com a capacidade de armazenamento, disponibilidade, transações e latência.  

* Telhas métricas e de estado destacando a disponibilidade do serviço, contagem total de transações para o serviço de armazenamento, latência E2E e latência do servidor.

Selecionar qualquer um dos botões para **falhas,** **desempenho,** **disponibilidade**e **capacidade** abre o respetivo livro. 

![Página de visão geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Livros de armazenamento detalhados

Quer tenha selecionado um valor nas colunas Disponibilidade , **Latência E2E,** **Latência**do Servidor, e tipo de erro de **transação/Erros** do livro de trabalho da **conta** de armazenamento múltiplo, ou selecionando qualquer um dos botões para **falhas,** **desempenho,** **disponibilidade**e **capacidade** do livro de visão **geral** a partir de uma conta de armazenamento específica, cada um entrega um conjunto de informações interativas relacionadas com armazenamento adaptadas a essa categoria. **Availability**  

* **A disponibilidade** abre o livro **de disponibilidade.** Mostra o estado de saúde atual do serviço de armazenamento Azure, uma tabela que mostra o estado de saúde disponível de cada objeto categorizado pelo serviço de dados definido na conta de armazenamento com uma linha de tendência que representa o intervalo de tempo selecionado, e um gráfico de tendência de disponibilidade para cada serviço de dados na conta.  

    ![Exemplo de relatório de disponibilidade](./media/storage-insights-overview/storage-account-availability-01.png)

* **A Latência e** **a Latência** do Servidor E2E abrem o livro de **performance.** Inclui um azulejo de estado rollup que mostra latência e latência do servidor E2E, um gráfico de desempenho da latência e2E versus servidor, e uma tabela que quebra a latência de chamadas bem sucedidas por API categorizada pelo serviço de dados definido na conta de armazenamento.

    ![Exemplo de relatório de desempenho](./media/storage-insights-overview/storage-account-performance-01.png)

* Selecionando qualquer uma das categorias de erro listadas na grelha abra o livro **de** falhas. O relatório mostra azulejos métricos de todos os outros erros do lado do cliente exceto os descritos e pedidos bem sucedidos, erros de estrangulamento do cliente, um gráfico de desempenho para a métrica de dimensão do Tipo de Resposta de **transação** específica ao atributo ClienteOtherError, e duas tabelas - **Transações por nome API** e **Transações por tipo de Resposta**.

   ![Exemplo de relatório de falha](./media/storage-insights-overview/storage-account-failures-01.png)

* **A capacidade** abre o livro de **capacidade.** Mostra a quantidade total de armazenamento utilizado para cada objeto de dados de armazenamento na conta nos azulejos e no gráfico, e quantos objetos de dados são armazenados na conta.  

    ![Página de capacidade de conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pin e exportação

Pode fixar qualquer uma das secções métricas num Painel De Instrumentos Azure selecionando o ícone do pino na parte superior direita da secção.

![Pino de secção métrica para exemplo do painel de instrumentos](./media/storage-insights-overview/workbook-pin-example.png)

A conta multi-subscrição e armazenamento **De overview** ou **capacidade** de livros suportam a exportação dos resultados em formato Excel, selecionando o ícone de seta para baixo à direita do ícone do pino.

![Exemplo de resultados da grelha do livro de exportação](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Personalize o Monitor Azure para Armazenamento (pré-visualização)

Esta secção destaca cenários comuns para editar o livro de trabalho para personalizar em apoio às suas necessidades de análise de dados:

* Consulte o livro de trabalho para selecionar sempre uma determinada conta de subscrição ou armazenamento
* Alterar métricas na grelha
* Alterar o limiar de disponibilidade
* Mude a renderização de cor

As personalizações são guardadas num livro personalizado para evitar a sobreposição da configuração predefinida no nosso livro publicado. Os livros de reprodução são guardados dentro de um grupo de recursos, quer na secção **My Reports** que seja privada para si ou na secção **Relatórios Partilhados** que esteja acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, precisa de ir à galeria do livro para o lançar.

![Lançar galeria de livro saindo do bar de comando](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Especificação de uma conta de subscrição ou armazenamento

Pode configurar a conta multi-subscrição e armazenamento **De** visão geral ou de trabalho de **capacidade** para uma determinada subscrição ou conta de armazenamento em cada execução, executar os seguintes passos.

1. Selecione **Monitor** a partir do portal e, em seguida, selecione Contas de **Armazenamento (pré-visualização)** do painel da esquerda.

2. No livro **de** resumo, a partir da barra de comando selecione **Editar**.

3. Selecione na lista de **subscrições** de uma ou mais subscrições a que pretende que seja predefinida. Lembre-se, o livro suporta selecionar até um total de 10 subscrições.  

4. Selecione na lista de **depósitos** de Contas de Armazenamento uma ou mais contas às quais deseja que seja predefinida. Lembre-se, o livro suporta selecionar até um total de 200 contas de armazenamento. 

5. Selecione **Guardar a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **editar para** voltar ao modo de leitura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificar métricas e cores no livro

Os livros pré-construídos contêm dados métricos e você tem a capacidade de modificar ou remover qualquer uma das visualizações e personalizar às necessidades específicas da sua equipa.

No nosso exemplo, estamos a trabalhar com o livro de trabalho de capacidade de conta multi-subscrição e armazenamento, para demonstrar como:

* Remover uma métrica
* Alterar a renderização de cor

Pode efetuar as mesmas alterações contra qualquer uma das falhas pré-construídas, **Desempenho,** **Disponibilidade**e Livros **de Capacidade.** **Failures**

1. Selecione **Monitor** a partir do portal e, em seguida, selecione Contas de **Armazenamento (pré-visualização)** do painel da esquerda.

2. Selecione **Capacidade** para mudar para o livro de capacidade e a partir da barra de comando, selecione **Editar** a partir da barra de comando.

    ![Selecione editar para modificar um livro](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Ao lado da secção de métricas, **selecione Editar**.

    ![Selecione Editar para modificar métricas de livro de capacidade](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vamos remover a coluna de **linha temporal de capacidade usada da Conta,** por isso selecione **Definições** de Coluna na grelha de métricas.

    ![Editar definições de colunas](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. No painel de definições da **coluna Editar,** selecione sob a secção **Colunas** **microsoft.storage/storageaccounts-Capacity-UsedAbilityy Timeline$[ [ Conta utilizada capacidade Timeline$**, e sob a lista drop-down **Column renderizador** selecione **Hidden**.

6. Selecione **Guardar e perto** de comprometer a sua mudança.

Agora vamos mudar o tema da cor para as métricas de capacidade no relatório para usar verde em vez de azul.

1. Selecione **Definições de coluna** na grelha de métricas.

2. No painel de definições da **coluna Editar,** selecione sob a secção **Colunas** **microsoft.storage/storageaccounts-Capacity-Capacity$[microsoft.storage/storageaccounts/blobservices-Capacity-Capacity$[microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$[microsoft.storage/storageaccounts/queueservices** Sob a lista de "paleta de **cores"** de down-down, selecione **Verde**.

3. Selecione **Guardar e perto** de comprometer a sua mudança.

4. Selecione **Guardar a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **editar para** voltar ao modo de leitura.  

### <a name="modify-the-availability-threshold"></a>Modificar o limiar de disponibilidade

Neste exemplo, estamos a trabalhar com o livro de trabalho da capacidade da conta de armazenamento e a demonstrar como modificar o limiar de disponibilidade. Por predefinição, a disponibilidade por cento do azulejo e da rede reportando por cento está configurada com um limiar mínimo de 90 e limiar máximo de 99. Vamos alterar para 85% o valor limiar mínimo da **disponibilidade** % na grelha de nome da **API,** o que significa que o Estado de saúde muda para crítico se o limiar for inferior a 85%. 

1. Selecione contas de **Armazenamento** a partir do portal e, em seguida, selecione uma conta de armazenamento da lista.

2. Selecione **Insights (pré-visualização)** a partir do painel da mão esquerda.

3. No livro, selecione **Disponibilidade** para mudar para o livro de trabalho de disponibilidade e, em seguida, selecione **Editar** a partir da barra de comando. 

4. Percorra para baixo da página e no lado esquerdo ao lado esquerdo ao lado da grelha **Disponibilidade por API,** selecione **Editar**.

    ![Editar Disponibilidade por definições de grelha de nome API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecione **as definições da Coluna** e, em seguida, no painel de definições da coluna **Editar,** sob a secção **Colunas** selecione **Disponibilidade (%) (Limiares + Formato)**.

6. Mude o valor para o estado de saúde **crítico** de **90** para **85** e, em seguida, clique em **Guardar e Fechar**.

    ![Modificar o valor limiar de disponibilidade para o estado crítico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecione **Guardar a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **editar para** voltar ao modo de leitura.

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção irá ajudá-lo no diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Monitor Azure para Armazenamento (pré-visualização). Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Resolução de problemas de desempenho, capacidade ou disponibilidade

Para ajudar a resolver problemas com quaisquer problemas relacionados com o armazenamento que identifique com o Azure Monitor para armazenamento (pré-visualização), consulte a orientação de resolução de [problemas](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)do Armazenamento Azure .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Por que só posso ver 200 contas de armazenamento?

O número de contas de armazenamento selecionadas tem um limite de 200, independentemente do número de subscrições selecionadas.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>O que acontece quando clico num azulejo recentemente preso no painel de instrumentos?

* Se clicar em qualquer lugar no azulejo, irá levá-lo ao separador de onde o azulejo foi fixado. Por exemplo, se fixar um gráfico no separador "Visão geral da conta de armazenamento" então quando clicar no azulejo do painel de instrumentos, abrirá essa vista predefinida, no entanto, se fixar um gráfico da sua própria cópia guardada, abrirá a visão da cópia guardada.
* O ícone do filtro na parte superior esquerda do título abre o separador "Configurar as definições de azulejos".
* O ícone da elipse no direito superior irá dar-lhe as opções de "Personalizar dados de título", "personalizar", "refrescar" e "remover do dashboard".

### <a name="what-happens-when-i-save-a-workbook"></a>O que acontece quando guardo um livro?

* Quando guarda um livro, permite criar uma nova cópia do livro com as suas edições e alterar o título. A poupança não substitui o livro de trabalho, o livro atual será sempre a visão padrão.
* Um livro **não guardado** é apenas a visão padrão.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Por que não vejo todas as minhas assinaturas no portal?

O portal mostrará dados apenas para subscrições selecionadas no lançamento do portal. Para alterar o que as subscrições são selecionadas, vá para a direita superior e clique no caderno com um ícone de filtro. Isto mostrará o separador de assinaturas Diretório + +.

![Diretório + subscrição](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Como alterar a coloração e o limiar de disponibilidade?

Consulte a secção de limiar de [disponibilidade](storage-insights-overview.md#modify-the-availability-threshold) Para obter as etapas detalhadas sobre como alterar a coloração e os limiares de disponibilidade.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Como analisar e resolver problemas os dados mostrados no Monitor Azure para Armazenamento?

 Consulte o [monitor, diagnosticar e resolver problemas](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) o artigo do Microsoft Azure Storage para obter detalhes sobre como analisar e resolver os dados de Armazenamento Azure mostrados no Monitor Azure para Armazenamento.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Por que não vejo todos os tipos de erros nas métricas?

Atualmente, até três tipos diferentes de erros são mostrados e o resto dos erros são agrupados num único balde. É controlado usando splitByLimit e pode ser modificado. Para alterar esta propriedade:

1. Clique no livro de edição.
2. Vá às métricas, clique em editar e, em seguida, selecione **Transações, Soma** ou quaisquer métricas que queira editar.

    ![Vá às métricas e clique em editar e depois em "Transações, Sums"](./media/storage-insights-overview/fqa7.png)

1. Em seguida, mude o número de divisões.

    ![Selecione parâmetros métricos"](./media/storage-insights-overview/fqa7-2.png)

Se quiser ver n diferentes tipos de erro do que especificar splitByLimit como n+1, 1 extra para o resto dos erros.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Guardei o meu livro enquanto estava numa conta de armazenamento. Por que não posso encontrá-lo agora?

Cada livro é guardado na conta de armazenamento em que o guardou. Tente encontrar a conta de armazenamento específica na qual o utilizador guardou o livro. Caso contrário, não há forma de encontrar um livro específico sem conhecer o recurso (conta de armazenamento).

### <a name="what-is-time-range"></a>O que é o intervalo de tempo?

O intervalo de tempo mostra-lhe dados de um determinado período de tempo. Por exemplo, se o intervalo de tempo é de 24 horas, então está a mostrar dados das últimas 24 horas.

### <a name="what-is-time-granularity-time-grain"></a>O que é a granularidade do tempo (grão de tempo)?

A granularidade do tempo é a diferença de tempo entre dois pontos de dados. Por exemplo, se o grão de tempo estiver definido para 1 segundo, significa que as métricas são recolhidas a cada segundo.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual é a granularidade do tempo quando colocamos qualquer parte dos livros num painel de instrumentos?

A granularidade do tempo padrão está definida para automática, atualmente não pode ser alterada neste momento.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Como posso alterar a hora/intervalo de tempo do passo do livro no meu painel?

Por predefinição, o intervalo de tempo/tempo no seu painel de instrumentos está definido para 24 horas, para alterar este clique nas elipses superiores direitas, selecione Dados de **azulejos Personalizados,** verifique "sobrepor as definições de tempo do painel de instrumentos no nível do título" e, em seguida, escolha uma hora através do menu de dropdown.  

![Selecione as elipses no canto direito do azulejo e escolha Personalizar estes dados](./media/storage-insights-overview/fqa-data-settings.png)

![Nas definições de azulejos configure, selecione a queda da pá de tempo para alterar a gama de tempo/tempo](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Como posso mudar o título do livro ou um passo de livro que adei a um painel de instrumentos?

O título do livro ou passo do livro que está preso a um painel de instrumentos mantém o mesmo nome que tinha no livro. Para alterar o título, tem de guardar a sua própria cópia do livro. Então poderá nomear o livro antes de premir a save.

![Selecione guardar na parte superior para guardar uma cópia do livro e para alterar o nome do mesmo](./media/storage-insights-overview/fqa-change-workbook-name.png)

Para alterar o nome de um passo no livro guardado, selecione editar sob o degrau e, em seguida, selecione a engrenagem na parte inferior das definições.

![Selecione editar na parte inferior de um](./media/storage-insights-overview/fqa-edit.png)
![passo de livro para abrir as definições Em definições, selecione a engrenagem na parte inferior, para poder alterar o nome do passo](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../platform/alerts-metric.md) e notificações de [saúde](../../service-health/alerts-activity-log-service-notifications.md) de serviço para configurar alertaautomatizado para ajudar na deteção de problemas.

* Saiba os cenários que os livros de trabalho são projetados para apoiar, como autor de novos relatórios existentes e mais através da revisão de [relatórios interativos Create com livros de trabalho do Monitor Azure.](../app/usage-workbooks.md)

* Para um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure, consulte [monitor, diagnóstico e resolução](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)de problemas do Armazenamento Microsoft Azure .
