---
title: Monitor Azure Storage services with Azure Monitor for Storage [ Monitor Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Storage que fornece aos administradores de armazenamento uma rápida compreensão dos problemas de desempenho e utilização com as suas contas de Armazenamento Azure.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: c030c1548a931bc749ad799a5aa88813c35d19cd
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997029"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Monitorizar o seu serviço de armazenamento com o Azure Monitor para armazenamento

O Azure Monitor for Storage fornece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de armazenamento Azure. Pode observar a capacidade de armazenamento e o desempenho de duas formas, ver diretamente a partir de uma conta de armazenamento ou ver do Azure Monitor para ver através de grupos de contas de armazenamento. 

Este artigo irá ajudá-lo a compreender a experiência que o Azure Monitor for Storage oferece para obter conhecimentos acccímicos sobre a saúde e desempenho das contas de Armazenamento em escala, com a capacidade de se concentrar em hotspots e diagnosticar problemas de latência, estrangulamento e disponibilidade.

## <a name="introduction-to-azure-monitor-for-storage"></a>Introdução ao Monitor Azure para armazenamento

Antes de mergulhar na experiência, deve entender como apresenta e visualiza a informação. Quer selecione a funcionalidade de Armazenamento diretamente a partir de uma conta de armazenamento ou do Azure Monitor, o Azure Monitor para Armazenamento apresenta uma experiência consistente. 

Combinado, entrega:

* **Na perspetiva** da escala mostrando uma visão instantânea da sua disponibilidade com base na saúde do serviço de armazenamento ou da operação API, a utilização mostrando o número total de pedidos que o serviço de armazenamento recebe, e latência mostrando o tempo médio que o serviço de armazenamento ou o tipo de operação API está a levar para processar pedidos. Também pode ver a capacidade por blob, arquivo, mesa e fila.

* **Faça a análise** de uma determinada conta de armazenamento para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - disponibilidade, desempenho, falhas e capacidade. A seleção de qualquer uma dessas opções proporciona uma visão aprofundada das métricas.  

* **Personalizável** onde pode alterar quais as métricas que deseja ver, modificar ou definir limiares que se alinham com os seus limites, e guardar como seu próprio livro de trabalho. Os gráficos do livro podem ser fixados ao painel Azure.  

Esta funcionalidade não requer que você ative ou configure nada, as métricas de armazenamento das suas contas de armazenamento são recolhidas por padrão. Se não estiver familiarizado com as métricas disponíveis no Azure Storage, veja a descrição e definição nas métricas de Armazenamento Azure, revendo [as métricas de armazenamento Azure](../../storage/blobs/monitor-blob-storage.md).

>[!NOTE]
>Não há qualquer custo para aceder a esta funcionalidade e só será cobrado para as funcionalidades essenciais do Azure Monitor que configura ou ativa, conforme descrito na página de detalhes de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>O Azure Monitor for Storage não suporta [contas v1 para fins gerais](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Vista do Monitor Azure

A partir do Azure Monitor, pode visualizar detalhes de transações, latência e capacidade de várias contas de armazenamento na sua subscrição, e ajudar a identificar desempenho, problemas de capacidade e falhas.

Para visualizar a utilização e disponibilidade das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Monitor** a partir do painel esquerdo no portal Azure, e na secção **Insights,** selecione **Contas de Armazenamento**.

    ![Vista de contas de armazenamento múltiplas](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Livro de visão geral

No livro **de resumo** para a subscrição selecionada, a tabela apresenta métricas de armazenamento interativas e estado de disponibilidade de serviço para até 5 contas de armazenamento agrupadas dentro da subscrição. Pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de entrega:

* **Subscrições** - apenas as subscrições que têm contas de armazenamento estão listadas.  

* **Contas de Armazenamento** - por padrão, 5 contas de armazenamento são pré-selecionadas. Se selecionar todas ou múltiplas contas de armazenamento no seletor de âmbito, até 200 contas de armazenamento serão devolvidas. Por exemplo, se tivesse um total de 573 contas de armazenamento em três subscrições selecionadas, apenas 200 contas seriam apresentadas. 

* **Intervalo de tempo** - por padrão, apresenta as últimas 4 horas de informação com base nas respetivas seleções feitas.

O contador de azulejos ao abrigo das listas de down-down revira o número total de contas de armazenamento na subscrição e reflete quantos do total são selecionados. Existe codificação de cores condicional ou massas de calor para colunas no livro que reportam métricas ou erros de transação. A cor mais profunda tem o valor mais elevado e uma cor mais clara baseia-se nos valores mais baixos. Para as colunas baseadas em erro, o valor está em vermelho e para as colunas baseadas em métrica, o valor está em azul.

Selecione um valor nas colunas **Disponibilidade**, **Latência E2E**, **Latência do Servidor**e tipo de erro **de transação/Erros** direciona-o para um relatório adaptado ao tipo específico de métricas de armazenamento que correspondem à coluna selecionada para essa conta de armazenamento. Para obter mais informações sobre os livros de trabalho de cada categoria, consulte a secção [de livros de armazenamento detalhado abaixo.](#detailed-storage-workbooks) 

>[!NOTE]
>Para obter mais informações sobre quais os erros que podem ser mostrados no relatório, consulte o [esquema do Tipo de Resposta](../../storage/blobs/monitor-blob-storage-reference.md#metrics-dimensions) e procure tipos de resposta como **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Dependendo das contas de armazenamento selecionadas, se houver mais de três tipos de erros relatados, todos os outros erros são representados na categoria de **Outros**.

O limiar **de disponibilidade** predefinido é:

* Aviso - 99%
* Crítico - 90%

Para definir um limiar de disponibilidade com base nos resultados da sua observação ou requisitos, [reveja a modificação do limiar de disponibilidade](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Livro de capacidade

Selecione **a capacidade** no topo da página e abre o livro de trabalho **da Capacidade.** Mostra-lhe a quantidade total de armazenamento utilizado na conta e capacidade utilizada por cada serviço de dados na conta para ajudar a identificar o armazenamento sobre e sob a utilização.

![Várias contas de armazenamento Livro de capacidade](./media/storage-insights-overview/storage-account-capacity-02.png) 

Existe codificação de cores condicional ou massas de calor para colunas no livro que reportam métricas de capacidade com um valor azul. A cor mais profunda tem o valor mais elevado e uma cor mais clara baseia-se nos valores mais baixos.

Quando selecionar um valor sob qualquer uma das colunas do livro, perfura-se até ao livro **de trabalhos de Capacidade** para a conta de armazenamento. Mais detalhes sobre o relatório de perfuração são descritos na secção [de livros de armazenamento detalhado abaixo.](#detailed-storage-workbooks) 

## <a name="view-from-a-storage-account"></a>Vista de uma conta de armazenamento

Para aceder ao Azure Monitor para VMs diretamente a partir de uma conta de armazenamento:

1. No portal Azure, selecione contas de Armazenamento.

2. A partir da lista, escolha uma conta de armazenamento. Na secção de Monitorização, escolha Insights.

    ![Página geral de conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-direct-overview-01.png)

No **livro de visão** geral para a conta de armazenamento, mostra várias métricas de desempenho de armazenamento que o ajudam a avaliar rapidamente:

* Saúde do serviço de armazenamento para ver imediatamente se um problema fora do seu controlo está a afetar o serviço de Armazenamento na região a que está implantado, o que é indicado sob a coluna **Resumo.**

* Gráficos de desempenho interativos que mostram os detalhes mais essenciais relacionados com a capacidade de armazenamento, disponibilidade, transações e latência.  

* Azulejos métricos e de estado que destacam a disponibilidade do serviço, a contagem total de transações para o serviço de armazenamento, latência E2E e latência do servidor.

Selecionar qualquer um dos botões para **falhas,** **desempenho,** **disponibilidade**e **capacidade** abre o respetivo livro. 

![Página geral de conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Livros de armazenamento detalhados

Quer tenha selecionado um valor nas colunas Disponibilidade , **Latência** **E2E,** **Latência do Servidor,** e **tipo de erro de transação/Erros** do livro de **visão geral** de **vários armazenamentos,** ou selecionando qualquer um dos botões para Falhas, **Performance,** **Disponibilidade**e **Capacidade** a partir do livro de **visão** geral de uma conta de armazenamento específica, cada um fornece um conjunto de informações interativas relacionadas com armazenamento adaptadas a essa categoria.  

* **A disponibilidade** abre o livro **de disponibilidade.** Mostra o estado atual de saúde do serviço de Armazenamento Azure, uma tabela que mostra o estado de saúde disponível de cada objeto categorizado pelo serviço de dados definido na conta de armazenamento com uma linha de tendência que representa o intervalo de tempo selecionado, e um gráfico de tendência de disponibilidade para cada serviço de dados na conta.  

    ![Exemplo do relatório de disponibilidade](./media/storage-insights-overview/storage-account-availability-01.png)

* **A Latência E2E** e **a Latência do Servidor** abrem o livro de **performance.** Inclui um azulejo de estado rollup que mostra latência E2E e latência do servidor, um gráfico de desempenho de E2E versus latência do servidor, e uma tabela que decompo a latência de chamadas bem sucedidas por API categorizada pelo serviço de dados definido na conta de armazenamento.

    ![Exemplo do relatório de desempenho](./media/storage-insights-overview/storage-account-performance-01.png)

* Selecionando qualquer uma das categorias de erro listadas na grelha, abra o livro **de trabalho 'Falha'.** O relatório mostra azulejos métricos de todos os outros erros do lado do cliente, exceto os descritos e pedidos bem-sucedidos, erros de estrangulamento do cliente, um gráfico de desempenho para a dimensão **do tipo de resposta** de transação específico do atributo ClientOtherError, e duas tabelas - **Transações por nome API** e **Transações por Tipo de Resposta.**

   ![Exemplo do relatório de falhas](./media/storage-insights-overview/storage-account-failures-01.png)

* **A capacidade** abre o livro **de trabalho da Capacidade.** Mostra a quantidade total de armazenamento utilizada para cada objeto de dados de armazenamento na conta nos azulejos e no gráfico, e quantos objetos de dados são armazenados na conta.  

    ![Página de capacidade de conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pino e exportação

Pode fixar qualquer uma das secções métricas a um Painel de Instrumentos Azure selecionando o ícone de pino de pressão no canto superior direito da secção.

![Pino de secção métrica para o exemplo do painel de instrumentos](./media/storage-insights-overview/workbook-pin-example.png)

A **visão geral** da conta multi-subscrição e armazenamento ou os livros de trabalho **de capacidade** suportam a exportação dos resultados no formato Excel selecionando o ícone de seta para baixo à direita do ícone de pino de pressão.

![Exemplo de resultados da grelha de trabalho de exportação](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Personalize o Monitor Azure para armazenamento

Esta secção destaca cenários comuns para a edição do livro para personalizar em apoio às necessidades de análise de dados:

* Scope o livro para selecionar sempre uma determinada conta de subscrição ou armazenamento
* Alterar métricas na grelha
* Alterar o limiar de disponibilidade
* Alterar a renderização de cores

As personalizações são guardadas num livro personalizado para evitar a sobreescrita da configuração padrão no nosso livro publicado. Os livros de trabalho são guardados dentro de um grupo de recursos, quer na secção **My Reports,** que é privada para si, quer na secção **Relatórios Partilhados** que é acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, tem de ir à galeria do livro para o lançar.

![Lançar galeria de livros da barra de comando](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Especificar uma conta de subscrição ou armazenamento

Pode configurar a **conta** geral de multi-subscrição e armazenamento **para** o âmbito de uma determinada subscrição ou conta de armazenamento em cada execução, executar os seguintes passos.

1. Selecione **Monitor** a partir do portal e, em seguida, selecione Contas de **Armazenamento** a partir do painel de esquerda.

2. No **livro de visão** geral, a partir da barra de comando selecione **Edit**.

3. Selecione a partir da lista de **subscrições** de uma ou mais subscrições a que pretende por defeito. Lembre-se, o livro suporta selecionar até um total de 10 subscrições.  

4. Selecione a partir da lista de contas de **armazenamento** uma ou mais contas a que pretende não estar. Lembre-se, o livro suporta selecionar até um total de 200 contas de armazenamento. 

5. **Selecione Guarde a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **Editar Feito** para voltar ao modo de leitura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modifique métricas e cores no livro

Os livros pré-construídos contêm dados métricos e você tem a capacidade de modificar ou remover qualquer uma das visualizações e personalizar às necessidades específicas da sua equipa.

No nosso exemplo, estamos a trabalhar com o livro de capacidade de multi-subscrição e capacidade de armazenamento, para demonstrar como:

* Remover uma métrica
* Alterar a renderização de cores

Pode efetuar as mesmas alterações em qualquer uma das falhas pré-construídas, **Desempenho,** **Disponibilidade**e **Failures** **Capacitação.**

1. Selecione **Monitor** a partir do portal e, em seguida, selecione Contas de **Armazenamento** a partir do painel de esquerda.

2. Selecione **Capacidade** para mudar para o livro de capacidade e a partir da barra de comando, selecione **Editar** a partir da barra de comando.

    ![Selecione editar para modificar um livro](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Junto à secção de métricas, **selecione Editar.**

    ![Selecione Editar para modificar métricas de livro de capacidade](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vamos remover a coluna **de linha de tempo de capacidade utilizada da Conta,** por isso selecione **Definições de Coluna na** grelha de métricas.

    ![Editar definições de colunas](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. No painel de **definições** da coluna Editar, selecione sob a secção **Colunas** **microsoft.storage/storageaccounts-Capacidade-Capacidade-Use-Capacidade Timeline$/ Conta utilizada Timeline$**, e sob a lista de **renderizador de colunas** de retirada selecione **Hidden**.

6. **Selecione Guardar e quase** para cometer a sua alteração.

Agora vamos mudar o tema de cor para as métricas de capacidade no relatório para usar o verde em vez de azul.

1. Selecione **Definições de colunas** na grelha de métricas.

2. No painel de **definições** da coluna Editar, selecione sob a secção **colunas** **microsoft.storage/storageaccounts-Capacity-UsedCapacity$ `|` microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$ `|` microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$ `|` microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$microsoft.storage/storageaccounts/tableacs-capacity-capacity- `|` ** Sob a lista de drop-down **Paleta de cores**, selecione **Verde**.

3. **Selecione Guardar e quase** para cometer a sua alteração.

4. **Selecione Guarde a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **Editar Feito** para voltar ao modo de leitura.  

### <a name="modify-the-availability-threshold"></a>Modificar o limiar de disponibilidade

Neste exemplo, estamos a trabalhar com o livro de capacidade da conta de armazenamento e a demonstrar como modificar o limiar de disponibilidade. Por predefinição, a disponibilidade por cento de relato de azulejos e grelhas são configuradas com um limiar mínimo de 90 e limiar máximo de 99. Vamos alterar o valor limiar mínimo da **disponibilidade %** na rede de **nomes API para** 85%, o que significa que o estado de saúde muda para crítico se o limiar for inferior a 85 por cento. 

1. Selecione **as contas** de Armazenamento do portal e, em seguida, selecione uma conta de armazenamento da lista.

2. Selecione **Insights** a partir do painel da esquerda.

3. No livro de trabalhos, selecione **Disponibilidade** para mudar para o livro de disponibilidade e, em seguida, selecione **Editar** na barra de comando. 

4. Desloque-se até à parte inferior da página e no lado esquerdo ao lado da **grelha Disponibilidade por API,** selecione **Editar**.

    ![Editar disponibilidade por definições de grelha de nome API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecione **as definições de coluna** e, em seguida, no painel de definições da coluna **Editar,** sob a secção **Colunas** selecione **Disponibilidade (%) (Limiares + Formatados)**.

6. Altere o valor para o estado de saúde **critical** de **90** para **85** e, em seguida, clique em **Guardar e Fechar**.

    ![Modifique o valor limiar de disponibilidade para estado crítico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. **Selecione Guarde a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **Editar Feito** para voltar ao modo de leitura.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter orientações gerais sobre a resolução de problemas, consulte o artigo dedicado de resolução de [problemas](troubleshoot-workbooks.md)baseado em livros de trabalho .

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Monitor Azure para armazenamento. Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Resolução de problemas de desempenho, capacidade ou disponibilidade

Para ajudar a resolver problemas relacionados com o armazenamento que identificar com o Azure Monitor para armazenamento, consulte a [orientação de resolução de problemas](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)do Azure Storage .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Por que só posso ver 200 contas de armazenamento?

O número de contas de armazenamento selecionadas tem um limite de 200, independentemente do número de subscrições selecionadas.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Como alterar a coloração e o limiar de disponibilidade?

Consulte a secção [de limiar de disponibilidade](#modify-the-availability-threshold) para os passos detalhados sobre como alterar a coloração e os limiares de disponibilidade.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Como analisar e resolver os dados apresentados no Azure Monitor para armazenamento?

 Consulte o [monitor, o diagnóstico e a resolução de problemas do artigo do Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md) para obter detalhes sobre como analisar e resolver problemas os dados de armazenamento do Azure apresentados no Azure Monitor para armazenamento.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Por que não vejo todos os tipos de erros nas métricas?

Atualmente, são mostrados até três tipos diferentes de erros e os restantes erros são agrupados num único balde. É controlado utilizando splitByLimit e pode ser modificado. Para alterar este imóvel:

1. Clique no livro de edição.
2. Vá a métricas, clique em editar e, em seguida, selecione **Transações, Sum** ou quaisquer métricas que queira editar.

    ![Vá às métricas e clique em editar e depois em "Transações, Somas"](./media/storage-insights-overview/fqa7.png)

3. Em seguida, mude o número de divisões.

    ![Selecione parâmetros métricos"](./media/storage-insights-overview/fqa7-2.png)

Se quiser ver n diferentes tipos de erro do que especificar splitByLimit como n+1, 1 extra para o resto dos erros.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Guardei o meu livro enquanto estava numa conta de armazenamento. Por que não posso encontrá-lo agora?

Cada livro é guardado na conta de armazenamento em que o guardou. Tente encontrar a Conta de Armazenamento específica na qual o utilizador guardou o livro. Caso contrário, não há como encontrar um livro específico sem conhecer o recurso (conta de armazenamento).

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../platform/alerts-metric.md) e [notificações de saúde](../../service-health/alerts-activity-log-service-notifications-portal.md) de serviço para configurar alertas automatizados para ajudar na deteção de problemas.

* Saiba os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)

* Para obter um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure, consulte [Monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).