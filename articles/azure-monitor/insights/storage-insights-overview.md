---
title: Monitorar os serviços de armazenamento do Azure com o Azure Monitor para armazenamento (visualização) | Microsoft Docs
description: Este artigo descreve o Azure Monitor para o recurso de armazenamento que fornece aos administradores de armazenamento uma compreensão rápida dos problemas de desempenho e utilização com suas contas de armazenamento do Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/15/2019
ms.openlocfilehash: e5738b9f7cca03898d3bb5c593004bb316aa0b23
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553892"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorando seu serviço de armazenamento com o Azure Monitor para armazenamento (versão prévia)

Azure Monitor para armazenamento (visualização) fornece um monitoramento abrangente de suas contas de armazenamento do Azure, fornecendo uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços de armazenamento do Azure. Você pode observar a capacidade de armazenamento e o desempenho de duas maneiras, exibir diretamente de uma conta de armazenamento ou exibição de Azure Monitor para ver em grupos de contas de armazenamento. 

Este artigo o ajudará a entender a experiência Azure Monitor para armazenamento (versão prévia) que o fornece para obter um conhecimento acionável sobre a integridade e o desempenho das contas de armazenamento em escala, com uma capacidade de se concentrar em pontos de uso e diagnosticar latência, limitação, e problemas de disponibilidade.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introdução ao Azure Monitor para armazenamento (visualização)

Antes de mergulhar na experiência, você deve entender como ele apresenta e visualiza informações. Se você selecionar o recurso de armazenamento diretamente de uma conta de armazenamento ou de Azure Monitor, Azure Monitor para armazenamento apresentará uma experiência consistente. 

Ele oferece:

* **Em perspectiva de escala** mostrando uma exibição de instantâneo de sua disponibilidade com base na integridade do serviço de armazenamento ou na operação de API, a utilização mostrando o número total de solicitações que o serviço de armazenamento recebe e a latência mostrando o tempo médio que o o serviço de armazenamento ou o tipo de operação da API está demorando para processar solicitações. Você também pode exibir a capacidade por blob, arquivo, tabela e fila.

* **Análise de busca detalhada** de uma determinada conta de armazenamento para ajudar a diagnosticar problemas ou executar uma análise detalhada por categoria, desempenho, falhas e capacidade. A seleção de qualquer uma dessas opções fornece uma visão detalhada das métricas.  

* **Personalizável** , onde você pode alterar quais métricas deseja ver, modificar ou definir limites que se alinham com seus limites e salvar como sua própria pasta de trabalho. Os gráficos na pasta de trabalho podem ser fixados no painel do Azure.  

Esse recurso não exige que você habilite ou configure nada, as métricas de armazenamento de suas contas de armazenamento são coletadas por padrão. Se você não estiver familiarizado com as métricas disponíveis no armazenamento do Azure, exiba a descrição e a definição em métricas de armazenamento do Azure examinando as [métricas de armazenamento do Azure](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você só será cobrado pelos recursos essenciais Azure Monitor que você configurar ou habilitar, conforme descrito na página de [detalhes de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

>[!NOTE]
>Azure Monitor para armazenamento não oferece suporte [a contas v1 de uso geral](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Exibir de Azure Monitor

Em Azure Monitor, você pode exibir detalhes de transação, latência e capacidade de várias contas de armazenamento em sua assinatura e ajudar a identificar desempenho, problemas de capacidade e falhas.

Para exibir a utilização e a disponibilidade de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Monitor** no painel esquerdo na portal do Azure e, na seção **insights** , selecione **contas de armazenamento (versão prévia)** .

    ![Exibição de várias contas de armazenamento](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Pasta de trabalho de visão geral

Na pasta de trabalho **visão geral** da assinatura selecionada, a tabela exibe as métricas de armazenamento interativo e o estado de disponibilidade do serviço para até 10 contas de armazenamento agrupadas na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas** -somente assinaturas que têm contas de armazenamento são listadas.  

* **Contas de armazenamento** -por padrão, 10 contas de armazenamento são previamente selecionadas. Se você selecionar todas ou várias contas de armazenamento no seletor de escopo, até 200 contas de armazenamento serão retornadas. Por exemplo, se você tivesse um total de 573 contas de armazenamento em três assinaturas que você selecionou, somente as contas 200 serão exibidas. 

* **Intervalo de tempo** – por padrão, exibe as últimas 4 horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de contas de armazenamento na assinatura e reflete quantos dos totais são selecionados. Há codificação de cor condicional ou calor para colunas na pasta de trabalho que relatam erros ou métricas de transação. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. Para as colunas baseadas em erro, o valor está em vermelho e para as colunas baseadas em métrica, o valor está em azul.

Selecione um valor nas colunas **disponibilidade**, **latência E2E**, **latência do servidor**e **erros/tipo de erro de transação** direciona você a um relatório personalizado para o tipo específico de métricas de armazenamento que correspondem à coluna selecionada para esse conta de armazenamento. Para obter mais informações sobre as pastas de trabalho para cada categoria, consulte a seção [pastas de trabalho de armazenamento detalhadas](#detailed-storage-workbooks) abaixo. 

>[!NOTE]
>Para obter detalhes sobre quais erros podem ser mostrados no relatório, consulte [esquema de tipo de resposta](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) e procure tipos de resposta, como **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Dependendo das contas de armazenamento selecionadas, se houver mais de três tipos de erros relatados, todos os outros erros serão representados sob a categoria de **outros**.

O limite de **disponibilidade** padrão é:

* Aviso-99%
* Crítico-90%

Para definir um limite de disponibilidade com base nos resultados de sua observação ou requisitos, examine [Modificar o limite de disponibilidade](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Pasta de trabalho de capacidade

Selecione **capacidade** na parte superior da página e a pasta de trabalho de **capacidade** é aberta. Ele mostra a quantidade de armazenamento total usada na conta e a capacidade usada por cada serviço de dados na conta para ajudar a identificar o armazenamento subutilizado e o menor.

![Pasta de trabalho de capacidade de várias contas de armazenamento](./media/storage-insights-overview/storage-account-capacity-02.png) 

Há codificação de cor condicional ou calor para colunas na pasta de trabalho que relatam métricas de capacidade com um valor azul. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

Quando você seleciona um valor em qualquer uma das colunas na pasta de trabalho, faz uma busca detalhada na pasta de trabalho de **capacidade** da conta de armazenamento. Mais detalhes sobre o relatório de busca detalhada são descritos na seção [pastas de trabalho de armazenamento detalhadas](#detailed-storage-workbooks) abaixo. 

## <a name="view-from-a-storage-account"></a>Exibir de uma conta de armazenamento

Para acessar Azure Monitor para VMs diretamente de uma conta de armazenamento:

1. No portal do Azure, selecione contas de armazenamento.

2. Na lista, escolha uma conta de armazenamento. Na seção monitoramento, escolha insights (versão prévia).

    ![Página Visão geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Na pasta de trabalho **visão geral** da conta de armazenamento, ele mostra várias métricas de desempenho de armazenamento que ajudam você a avaliar rapidamente:

* Integridade do serviço de armazenamento para ver imediatamente se um problema fora do seu controle está afetando o serviço de armazenamento na região em que ele está implantado, que é indicado na coluna de **Resumo** .

* Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados à capacidade, disponibilidade, transações e latência de armazenamento.  

* Os blocos métrica e status realçando a disponibilidade do serviço, a contagem total de transações para o serviço de armazenamento, a latência de E2E e a latência do servidor.

A seleção de qualquer um dos botões para **falhas**, **desempenho**, **disponibilidade**e **capacidade** abre a respectiva pasta de trabalho. 

![Página Visão geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Pastas de trabalho de armazenamento detalhadas

Se você selecionou um valor nas colunas **disponibilidade**, **latência E2E**, **latência do servidor**e **tipo/erros de transação** da pasta de trabalho de **visão geral** da conta de várias armazenamento ou selecionando qualquer um dos botões para **Falhas**, **desempenho**, **disponibilidade**e **capacidade** da pasta de trabalho de **visão geral** de uma conta de armazenamento específica, cada uma delas fornecem um conjunto de informações relacionadas ao armazenamento interativo adaptadas a essa categoria.  

* **Disponibilidade** abre a pasta de trabalho de **disponibilidade** . Ele mostra o estado de integridade atual do serviço de armazenamento do Azure, uma tabela que mostra o estado de integridade disponível de cada objeto Categorizado pelo serviço de dados definido na conta de armazenamento com uma linha de tendência que representa o intervalo de tempo selecionado e um gráfico de tendência de disponibilidade para cada serviço de dados na conta.  

    ![Exemplo de relatório de disponibilidade](./media/storage-insights-overview/storage-account-availability-01.png)

* **Latência de E2E** e latência de **servidor** abre a pasta de trabalho de **desempenho** . Ele inclui um bloco de status de rollup mostrando latência E2E e latência de servidor, um gráfico de desempenho de E2E versus latência de servidor e uma tabela que reduz a latência de chamadas bem-sucedidas por API categorizadas pelo serviço de dados definido na conta de armazenamento.

    ![Exemplo de relatório de desempenho](./media/storage-insights-overview/storage-account-performance-01.png)

* Selecionando qualquer uma das categorias de erro listadas na grade, abra a pasta de trabalho **falha** . O relatório mostra os blocos de métrica de todos os outros erros do lado do cliente, exceto os descritos e as solicitações bem-sucedidas, erros de limitação do cliente, um gráfico de desempenho para a métrica de dimensão do **tipo de resposta** da transação específica ao atributo ClientOtherError, e duas tabelas – **transações por nome da API** e **transações por tipo de resposta**.

   ![Exemplo de relatório de falha](./media/storage-insights-overview/storage-account-failures-01.png)

* A **capacidade** abre a pasta de trabalho **capacidade** . Ele mostra a quantidade total de armazenamento usado para cada objeto de dados de armazenamento na conta nos blocos e o gráfico e quantos objetos de dados são armazenados na conta.  

    ![Página de capacidade da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Fixar e exportar

Você pode fixar qualquer uma das seções de métrica em um painel do Azure selecionando o ícone de pino na parte superior direita da seção.

![Exemplo de PIN da seção de métrica para painel](./media/storage-insights-overview/workbook-pin-example.png)

A **visão geral** da conta de armazenamento e de várias assinaturas ou as pastas de trabalho de **capacidade** dão suporte à exportação dos resultados no formato do Excel selecionando o ícone de seta para baixo à direita do ícone de pino.

![Exportar exemplo de resultados de grade da pasta de trabalho](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Personalizar Azure Monitor para armazenamento (visualização)

Esta seção destaca os cenários comuns para editar a pasta de trabalho para personalizar o suporte às suas necessidades de análise de dados:

* Fazer o escopo da pasta de trabalho para sempre selecionar uma determinada assinatura ou conta (s) de armazenamento
* Alterar as métricas na grade
* Alterar o limite de disponibilidade
* Alterar a renderização de cor

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão em nossa pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, na seção **meus relatórios** , que é particular para você ou na seção **relatórios compartilhados** , que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a Galeria de pastas de trabalho para iniciá-la.

![Iniciar Galeria de pastas de trabalho na barra de comandos](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Especificando uma assinatura ou conta de armazenamento

Você pode configurar a **visão geral** da conta de armazenamento e de várias assinaturas ou as pastas de trabalho de **capacidade** para definir o escopo para uma ou mais assinaturas ou contas de armazenamento em cada execução, execute as etapas a seguir.

1. Selecione **Monitor** no portal e, em seguida, selecione **contas de armazenamento (versão prévia)** no painel esquerdo.

2. Na pasta de trabalho **visão geral** , na barra de comandos, selecione **Editar**.

3. Selecione na lista suspensa **assinaturas** uma ou mais assinaturas para as quais você deseja que ele seja padronizado. Lembre-se de que a pasta de trabalho dá suporte à seleção de até um total de 10 assinaturas.  

4. Selecione na lista suspensa **contas de armazenamento** uma ou mais contas para as quais você deseja que o padrão seja. Lembre-se de que a pasta de trabalho dá suporte à seleção de até um total de 200 contas de armazenamento. 

5. Selecione **salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com suas personalizações e clique em **edição concluída** para retornar ao modo de leitura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificar métricas e cores na pasta de trabalho

As pastas de trabalho predefinidas contêm dados de métrica e você tem a capacidade de modificar ou remover qualquer uma das visualizações e personalizá-las para as necessidades específicas de sua equipe. 

Em nosso exemplo, estamos trabalhando com a pasta de trabalho de várias assinaturas e capacidade de conta de armazenamento, para demonstrar como:

* Remover uma métrica
* Alterar renderização de cor

Você pode executar as mesmas alterações em qualquer uma das pastas de trabalho de **falhas**, **desempenho**, **disponibilidade**e **capacidade** predefinidas.

1. Selecione **Monitor** no portal e, em seguida, selecione **contas de armazenamento (versão prévia)** no painel esquerdo.

2. Selecione a **capacidade** para alternar para a pasta de trabalho de capacidade e, na barra de comandos, selecione **Editar** na barra de comandos.

    ![Selecione Editar para modificar uma pasta de trabalho](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Ao lado da seção métricas, selecione **Editar**. 

    ![Selecione Editar para modificar as métricas de pasta de trabalho de capacidade](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vamos remover a coluna da **linha do tempo de capacidade usada da conta** , portanto, selecione **configurações de coluna** na grade métricas.

    ![Editar configurações de coluna](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. No painel **Editar configurações de coluna** , selecione na seção **colunas** **Microsoft. Storage/Storageaccounts-Capacity-UsedCapacity linha do tempo $ | A capacidade usada da conta linha do tempo $** e, no **renderizador da coluna** da lista suspensa, selecione **oculto**. 

6. Selecione **salvar e fechar** para confirmar sua alteração.

Agora, vamos alterar o tema de cores para que as métricas de capacidade no relatório usem verde em vez de azul.

1. Selecione **configurações de coluna** na grade métricas.

2. No painel **Editar configurações de coluna** , selecione na seção **colunas** **Microsoft. Storage/storageaccounts-Capacity-UsedCapacity $ | Microsoft. Storage/Storageaccounts/blobservices-Capacity-capacidade $ | Microsoft. Storage/ storageaccounts/fileservices-capacidade-filecapacity $ | Microsoft. Storage/storageaccounts/queueservices-Capacity-QueueCapacity $ | Microsoft. Storage/storageaccounts/tabelaservices – Capacity-TableCapacity $** . Na **paleta de cores**da lista suspensa, selecione **verde**.

3. Selecione **salvar e fechar** para confirmar sua alteração.

4. Selecione **salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com suas personalizações e clique em **edição concluída** para retornar ao modo de leitura.  

### <a name="modify-the-availability-threshold"></a>Modificar o limite de disponibilidade

Neste exemplo, estamos trabalhando com a pasta de trabalho de capacidade da conta de armazenamento e demonstrando como modificar o limite de disponibilidade. Por padrão, a porcentagem de disponibilidade do bloco e do relatório de grade é configurada com um limite mínimo de 90 e o limite máximo de 99. Vamos alterar o valor do limite mínimo do **percentual de disponibilidade** na grade **disponibilidade por nome da API** para 85%, o que significa que o estado de integridade mudará para crítico se o limite for menor que 85%. 

1. Selecione **contas de armazenamento** no portal e, em seguida, selecione uma conta de armazenamento na lista.

2. Selecione **insights (visualização)** no painel esquerdo.

3. Na pasta de trabalho, selecione **disponibilidade** para alternar para a pasta de trabalho de disponibilidade e, em seguida, selecione **Editar** na barra de comandos. 

4. Role para baixo até a parte inferior da página e no lado esquerdo ao lado da grade de **disponibilidade por API** , selecione **Editar**.

    ![Editar disponibilidade por nome da API configurações de grade](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecione **configurações de coluna** e, em seguida, no painel **Editar configurações de coluna** , na seção **colunas** , selecione **disponibilidade (%) (Limites + formatado)** .

6. Altere o valor do estado de integridade **crítico** de **90** para **85** e, em seguida, clique em **salvar e fechar**.

    ![Modificar o valor do limite de disponibilidade para o estado crítico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecione **salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com suas personalizações e clique em **edição concluída** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Resolução de problemas

Esta seção o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar o Azure Monitor para armazenamento (versão prévia). Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Resolvendo problemas de desempenho, capacidade ou disponibilidade

Para ajudar a solucionar problemas relacionados ao armazenamento que você identifica com Azure Monitor para armazenamento (versão prévia), consulte as [diretrizes de solução de problemas](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)do armazenamento do Azure.  

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados para auxiliar na detecção de problemas.

* Conheça os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar novos e personalizar relatórios existentes e muito mais examinando [criar relatórios interativos com pastas de trabalho do Azure monitor](../app/usage-workbooks.md).

* Para obter um guia detalhado sobre como usar Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure, consulte [monitorar, diagnosticar e solucionar problemas armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
