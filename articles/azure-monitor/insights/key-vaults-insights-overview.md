---
title: Monitor Key Vault com Monitor Azure para Cofre chave (pré-visualização)] Microsoft Docs
description: Este artigo descreve o Monitor Azure para cofres chave.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211306"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitorização do seu serviço de cofre chave com Monitor Azure para cofre de chaves (pré-visualização)
O Monitor Azure para o Cofre de Chaves (pré-visualização) fornece uma monitorização abrangente dos seus cofres chave, fornecendo uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência.
Este artigo irá ajudá-lo a entender como embarcar e personalizar a experiência do Monitor Azure para o Cofre chave (pré-visualização).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introdução ao Monitor Azure para cofre de chaves (pré-visualização)

Antes de entrar na experiência, deve entender como apresenta e visualiza a informação.
-    **Na perspetiva** de escala mostrando uma visão instantânea do desempenho com base nos pedidos, desagregação de falhas e uma visão geral das operações e latência.
-   **Faça análises** de um cofre chave específico para efetuar uma análise detalhada.
-    **Personalizável** onde pode alterar quais as métricas que pretende ver, modificar ou definir limiares que se alinham com os seus limites e guardar o seu próprio livro de trabalho. Os gráficos do livro podem ser fixados nos tabliers Azure.

O Monitor Azure para o Cofre chave combina registos e métricas para fornecer uma solução global de monitorização. Todos os utilizadores podem aceder aos dados de monitorização baseados em métricas, no entanto a inclusão de visualizações baseadas em registos pode exigir que os utilizadores permitam o [registo do seu Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configurar os seus cofres-chave para monitorização

> [!NOTE]
> Ativar registos é um serviço pago que fornece capacidades de monitorização adicionais.

1. O separador Operações & Latência ajuda-o a determinar quantas e quais os cofres chave estão ativados. Para começar a recolher, selecione o botão **Ativa,** que o levará a um livro separado que lista os cofres chave que requerem ativar os registos de diagnóstico.

    ![Screenshot de operações e separador de latência com botão de ativação azul apresentado](./media/key-vaults-insights-overview/enable-logging.png)

2. Para ativar os registos de diagnóstico, clique no link **Enable** por baixo da coluna de ações e crie uma nova definição de diagnóstico que envia registos para um espaço de trabalho de Log Analytics. Recomenda-se enviar todos os registos para o mesmo espaço de trabalho.

3. Uma vez guardadas as definições de diagnóstico, poderá visualizar todos os gráficos e visualizações baseados em registo sem evisores por baixo do Key Vault Insights. Por favor, note que pode levar vários minutos a horas para começar a povoar os troncos.

4. Para obter assistência adicional sobre como ativar os registos de diagnóstico do seu serviço Key Vault, leia o [guia completo](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="view-from-azure-monitor"></a>Vista do Monitor Azure

Do Azure Monitor, pode ver detalhes de pedidos, latências e falhas de vários cofres chave na sua subscrição, e ajudar a identificar problemas de desempenho e cenários de estrangulamento.

Para visualizar a utilização e operações das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos:

1. Assine no [portal Azure](https://portal.azure.com/)

2. Selecione **Monitor** a partir do painel da mão esquerda no portal Azure e, sob a secção Insights, selecione **Key Vaults (pré-visualização)**.

![Screenshot da experiência de visão geral com vários gráficos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Livro de visão geral

No livro de resumo para a subscrição selecionada, a tabela apresenta métricas de cofre seletiva interativa para cofres chave agrupados dentro da subscrição. Pode filtrar os resultados com base nas opções selecionadas a partir das seguintes listas de drop-down:

* Assinaturas – apenas estão listadas subscrições que tenham cofres chave.

* Cofres chave – por defeito, apenas 5 cofres-chave são pré-selecionados. Se selecionar todos ou vários cofres chave no seletor de âmbito, serão devolvidos até 200 cofres-chave. Por exemplo, se tiver um total de 573 cofres chave em três subscrições que selecionou, apenas 200 cofres serão exibidos.

* Intervalo de tempo – por padrão, exibe as últimas 24 horas de informação com base nas seleções correspondentes feitas.

O telha do balcão, sob a lista de drop-down, faz o número total de cofres chave nas subscrições selecionadas e reflete quantos são selecionados. Existem mapas de calor codificados por cores condicionais para as colunas do livro que solicitam, falhas e métricas de latência. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

## <a name="failures-workbook"></a>Livro de falhas

Selecione **Falhas** na parte superior da página e o separador Falhas abre. Mostra-lhe os hits da API, frequência ao longo do tempo, juntamente com a quantidade de certos códigos de resposta.

![Screenshot de livro de falhas](./media/key-vaults-insights-overview/failures.png)

Há códigos de cores condicional ou mapas de calor para colunas no livro que reportam que a API atinge métricas com um valor azul. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

O livro apresenta Sucessos (códigos de estado 2xx), Erros de Autenticação (códigos de estado 401/403), Estrangulamento (códigos de estado 429) e Outras Falhas (códigos de estado 4xx).

Para melhor entender o que cada um dos códigos de estado representam, recomendamos a leitura através da documentação sobre o [estado do Cofre chave Azure e códigos](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses)de resposta .

## <a name="operations--latency-workbook"></a>Operações & livro de latência

Selecione **Operações & Latência** no topo da página e abre o separador **Operações & Latência.** Este separador permite-lhe embarcar nos seus cofres chave para monitorização. Para obter passos mais detalhados, consulte a configuração dos [seus cofres-chave para a secção de monitorização.](#configuring-your-key-vaults-for-monitoring)

Pode ver quantos dos seus cofres estão habilitados para a exploração madeireira. Se pelo menos um cofre tiver sido configurado corretamente, poderá ver tabelas que apresentem as operações e códigos de estado para cada um dos seus cofres chave. Pode clicar na secção de detalhes para obter informações adicionais sobre a operação individual.

![Screenshot de operações e gráficos de latência](./media/key-vaults-insights-overview/logs.png)

Se não estiver a ver quaisquer dados para esta secção, consulte a secção superior sobre como ativar os registos para o Cofre de Chaves Azure ou verifique a secção de resolução de problemas abaixo.

## <a name="view-from-a-key-vault-resource"></a>Vista de um recurso key vault

Para aceder ao Monitor Azure para o Cofre chave diretamente de um cofre chave:

1. No portal Azure, selecione Key Vaults.

2. Da lista, escolha um cofre chave. Na secção de monitorização, escolha Insights (pré-visualização).

Estas vistas também são acessíveis selecionando o nome de recurso de um cofre chave do livro de nível Azure Monitor.

![Screenshot de vista de um recurso chave vault](./media/key-vaults-insights-overview/key-vault-resource-view.png)

No livro de **overview** para o cofre chave, mostra várias métricas de desempenho que o ajudam a avaliar rapidamente:

- Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados com transações chave do cofre, latência e disponibilidade.

- Métricas e azulejos de estado destacando a disponibilidade do serviço, contagem total de transações para o recurso chave do cofre, e latência geral.

A seleção de qualquer um dos outros separadores para **falhas** ou **operações** abre os respetivos livros.

![Screenshot da visão de falhas](./media/key-vaults-insights-overview/resource-failures.png)

O livro de falhas decomvaria os resultados de todos os pedidos chave do cofre no prazo selecionado, e fornece categorização em Sucessos (2xx), Erros de Autenticação (401/403), Estrangulamento (429) e outras falhas.

![Screenshot da vista de operações](./media/key-vaults-insights-overview/operations.png)

O livro de operações permite que os utilizadores mergulhem profundamente em todos os detalhes de todas as transações, que podem ser filtradas pelo Estado do Resultado utilizando os azulejos de nível superior.

![Screenshot da vista de operações](./media/key-vaults-insights-overview/info.png)

Os utilizadores também podem ver vistas baseadas em tipos de transações específicos na tabela superior, que atualiza dinamicamente a tabela inferior, onde os utilizadores podem ver todos os detalhes da operação num painel de contexto pop-up.

>[!NOTE]
> Note que os utilizadores devem ter as definições de diagnóstico habilitadas para visualizar este livro. Para saber mais sobre a definição de diagnóstico, leia mais sobre a [exploração do cofre](https://docs.microsoft.com/azure/key-vault/general/logging)de chaves Azure .

## <a name="pin-and-export"></a>Pin e exportação

Pode fixar qualquer uma das secções métricas num painel de instrumentos Azure selecionando o ícone do pino na parte superior direita da secção.

As abóbadas multi-subscrição e chave suportam a exportação dos resultados em formato Excel, selecionando o ícone de descarregamento para a esquerda do ícone do pino.

![Screenshot do ícone do pino selecionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalize o Monitor Azure para o Cofre de Chaves

Esta secção destaca cenários comuns para editar o livro de trabalho para personalizar em apoio às suas necessidades de análise de dados:
*  Consulte o livro de trabalho para selecionar sempre uma determinada subscrição ou cofre chave
* Alterar métricas na grelha
* Alterar o limiar dos pedidos
* Mude a renderização de cor

Pode iniciar personalizações permitindo o modo de edição, selecionando o botão **Personalizar** a partir da barra de ferramentas superior.

![Screenshot do botão personalizar](./media/key-vaults-insights-overview/customize.png)

As personalizações são guardadas num livro personalizado para evitar a sobreposição da configuração predefinida no nosso livro publicado. Os livros de reprodução são guardados dentro de um grupo de recursos, quer na secção My Reports que seja privada para si ou na secção Relatórios Partilhados que esteja acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, precisa de ir à galeria do livro para o lançar.

![Screenshot da galeria do livro](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificação de uma subscrição ou cofre chave

Pode configurar os livros de visão geral ou de falhas de várias subscrições e cofres chave para uma determinada subscrição ou cofre chave em cada execução, executando os seguintes passos:

1. Selecione **Monitor** a partir do portal e, em seguida, selecione **Key Vaults (pré-visualização)** a partir do painel da mão esquerda.
2. No livro **de** resumo, a partir da barra de comando selecione **Editar**.
3. Selecione na lista de **subscrições** uma ou mais subscrições que deseja utilizar como padrão. Lembre-se, o livro suporta selecionar até um total de 10 subscrições.
4. Selecione na lista de lançamentos de **Cofres Chave** uma ou mais contas que pretende que utilize como padrão. Lembre-se, o livro suporta selecionar até um total de 200 contas de armazenamento.
5. Selecione **Guardar a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **editar para** voltar ao modo de leitura.

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Monitor Azure para o Cofre-Chave (pré-visualização). Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas de desempenho ou falhas

Para ajudar a resolver problemas com qualquer problema relacionado com o cofre que identifique com o Monitor Azure para o Cofre chave (pré-visualização), consulte a documentação do [Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/).

### <a name="why-can-i-only-see-200-key-vaults"></a>Por que só posso ver 200 cofres chave?

Há um limite de 200 cofres chave que podem ser selecionados e vistos. Independentemente do número de subscrições selecionadas, o número de cofres-chave selecionados tem um limite de 200.

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>O que vai acontecer quando um item preso for clicado?

Quando um item preso no painel de instrumentos é clicado, abrirá uma de duas coisas:
* Se os Insights foram salvos – abrirá a instância de insights de que o pino foi salvo.
* Se os insights não forem salvos – abrirá uma nova instância de insights por defeito.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no selecionador de assinaturas?

Apenas mostramos subscrições que contêm cofres chave, escolhidos a partir do filtro de subscrição selecionado, que são selecionados no "Diretório + Subscrição" no cabeçalho do portal Azure.

![Screenshot do filtro de subscrição](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Estou a receber uma mensagem de erro de que a "consulta excede o número máximo de espaços/regiões de trabalho permitidos", o que fazer agora?

Atualmente, existe um limite para 25 regiões e 200 espaços de trabalho, para visualizar os seus dados, você precisará reduzir o número de subscrições e/ou grupos de recursos.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais à Key Vault Insights, como o faço?

Para efazer alterações, selecione o "Modo Editar" para modificar o livro de trabalho, então pode guardar o seu trabalho como um novo livro que está ligado a um grupo de subscrição e recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o grão de tempo quando prendemos alguma parte dos livros de trabalho?

Utilizamos o grão de tempo "Auto", portanto depende da faixa de tempo selecionada.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte do livro está presa?

O intervalo de tempo dependerá das definições do painel de instrumentos.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Por que não vejo dados para o meu Cofre chave sob as secções de operações & latência?

Para visualizar os seus dados baseados em registos, terá de ativar registos para cada um dos cofres-chave que pretende monitorizar. Isto pode ser feito sob as definições de diagnóstico para cada cofre chave. Terá de enviar os seus dados para um espaço de trabalho designado para log analytics.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Já ativei registos para o meu Cofre chave, porque é que ainda não consigo ver os meus dados no âmbito das Operações & Latência?

Atualmente, os registos de diagnóstico não funcionam retroativamente, pelo que os dados só começarão a aparecer depois de terem sido tomadas medidas nos seus cofres chave. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo do quão ativo é o seu cofre chave.

Além disso, se tiver um elevado número de cofres e subscrições selecionados, poderá não ser capaz de ver os seus dados devido a limitações de consulta. Para visualizar os seus dados, poderá ter de reduzir o número de subscrições selecionadas ou cofres-chave. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>E se eu quiser ver outros dados ou fazer as minhas próprias visualizações? Como posso fazer alterações no Key Vault Insights?

Pode editar o livro de trabalho existente, através da utilização do modo de edição, e depois guardar o seu trabalho como um novo livro que terá todas as suas novas alterações.

## <a name="next-steps"></a>Passos seguintes

Saiba os cenários que os livros de trabalho são projetados para apoiar, como autor de novos relatórios existentes e mais através da revisão de [relatórios interativos Create com livros de trabalho do Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)
