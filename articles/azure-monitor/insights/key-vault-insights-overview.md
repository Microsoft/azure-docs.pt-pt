---
title: Monitor Key Vault com Monitor Azure para Cofre de Chaves (pré-visualização)/ Microsoft Docs
description: Este artigo descreve o Monitor Azure para cofres de chaves.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: c669946ab76fcaeaaa6fd681f521408643c5a63c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531264"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitorizar o seu serviço de cofre de chaves com o Azure Monitor para o Cofre de Chaves (pré-visualização)
O Azure Monitor for Key Vault (pré-visualização) proporciona uma monitorização completa dos seus cofres chave, proporcionando uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência.
Este artigo irá ajudá-lo a entender como embarcar e personalizar a experiência do Azure Monitor para Key Vault (pré-visualização).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introdução ao Monitor Azure para Cofre de Chaves (pré-visualização)

Antes de entrar na experiência, deve entender como apresenta e visualiza a informação.
-    **Na perspetiva da escala** mostrando uma visão instantânea do desempenho com base nos pedidos, desagregação de falhas, e uma visão geral das operações e latência.
-   **Faça uma análise** de um cofre chave para fazer análises detalhadas.
-    **Personalizável** onde pode alterar quais as métricas que deseja ver, modificar ou definir limiares que se alinham com os seus limites, e guardar o seu próprio livro de trabalho. Os gráficos do livro podem ser fixados aos dashboards Azure.

O Azure Monitor para Key Vault combina registos e métricas para fornecer uma solução de monitorização global. Todos os utilizadores podem aceder aos dados de monitorização baseados em métricas, no entanto a inclusão de visualizações baseadas em registos pode exigir que os utilizadores permitam que os utilizadores possam ativar o [registo do seu Cofre de Chaves Azure](../../key-vault/general/logging.md).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configurar os seus cofres-chave para monitorização

> [!NOTE]
> Ativar registos é um serviço pago que fornece capacidades de monitorização adicionais.

1. O separador operações & de latência ajuda-o a determinar quantos e quais cofres-chave estão ativados. Para começar a recolher, selecione o botão **Enable,** que o levará a um livro separado que lista os cofres-chave que requerem registos de diagnóstico que permitem ativar registos de diagnóstico.

    ![Screenshot de operações e separador de latência com botão de ativação azul exibido](./media/key-vaults-insights-overview/enable-logging.png)

2. Para ativar registos de diagnóstico, clique no link **Enable** por baixo da coluna de ações e crie uma nova definição de diagnóstico que envia registos para um espaço de trabalho do Log Analytics. Recomenda-se o envio de todos os registos para o mesmo espaço de trabalho.

3. Uma vez guardadas as definições de diagnóstico, poderá visualizar todos os gráficos e visualizações baseados em registos por baixo dos Insights do Cofre de Chaves. Por favor, note que pode levar vários minutos a horas para começar a povoar os troncos.

4. Para obter assistência adicional sobre como ativar registos de diagnóstico para o seu serviço Key Vault, leia o [guia completo](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Vista do Monitor Azure

A partir do Azure Monitor, pode visualizar detalhes de pedido, latência e falha de vários cofres chave na sua subscrição, e ajudar a identificar problemas de desempenho e cenários de estrangulamento.

Para visualizar a utilização e operações dos seus cofres-chave em todas as suas subscrições, execute os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/)

2. Selecione **Monitor** a partir do painel esquerdo no portal Azure, e sob a secção Insights, selecione **Key Vaults (pré-visualização)**.

![Screenshot da experiência de visão geral com vários gráficos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Livro de visão geral

No livro de resumo para a subscrição selecionada, a tabela apresenta métricas de cofre de chaves interativas para cofres-chave agrupados dentro da subscrição. Pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de entrega:

* Subscrições – apenas subscrições que tenham cofres chave estão listadas.

* Key Vaults – por padrão, apenas até 5 cofres-chave são pré-selecionados. Se selecionar todos ou vários cofres chave no seletor de âmbito, serão devolvidos até 200 cofres-chave. Por exemplo, se tiver um total de 573 cofres-chave em três subscrições que selecionou, apenas 200 cofres serão exibidos.

* Intervalo de tempo – por padrão, apresenta as últimas 24 horas de informação com base nas respetivas seleções es feitas.

O azulejo do contador, na lista de drop-down, aumenta o número total de cofres-chave nas subscrições selecionadas e reflete quantos são selecionados. Existem mapas de calor codificados por cores condicionais para as colunas do livro que reportam pedidos, falhas e métricas de latência. A cor mais profunda tem o valor mais elevado e uma cor mais clara baseia-se nos valores mais baixos.

## <a name="failures-workbook"></a>Livro de falhas

Selecione **Falhas** no topo da página e o separador Falhas abre.s. Mostra-lhe os acessos da API, frequência ao longo do tempo, juntamente com a quantidade de certos códigos de resposta.

![Screenshot do livro de falhas](./media/key-vaults-insights-overview/failures.png)

Existe codificação de cores condicional ou massas de calor para colunas no livro que reportam que a API atinge métricas com um valor azul. A cor mais profunda tem o valor mais elevado e uma cor mais clara baseia-se nos valores mais baixos.

O livro apresenta Sucessos (códigos de estado 2xx), Erros de Autenticação (códigos de estado 401/403), Throttling (429 códigos de estado) e Outras Falhas (códigos de estado 4xx).

Para melhor entender o que cada um dos códigos de estado representam, recomendamos a leitura através da documentação sobre o [estado do Cofre da Chave Azure e os códigos de resposta](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="operations--latency-workbook"></a>Trabalho de & de latência de operações

Selecione **Operações & a Latência** no topo da página e o **separador de & de operação** de latência abre. Este separador permite-lhe embarcar nos cofres das chaves para monitorização. Para passos mais detalhados, consulte a configuração dos [seus cofres-chave para a secção de monitorização.](#configuring-your-key-vaults-for-monitoring)

Pode ver quantos dos seus cofres chave estão habilitados para a exploração madeireira. Se pelo menos um cofre tiver sido configurado corretamente, então poderá ver tabelas que apresentem as operações e os códigos de estado de cada um dos seus cofres-chave. Pode clicar na secção de detalhes para obter informações adicionais sobre a operação individual.

![Screenshot de operações e gráficos de latência](./media/key-vaults-insights-overview/logs.png)

Se não estiver a ver nenhum dado para esta secção, consulte a secção superior sobre como ativar os registos do Cofre da Chave Azure ou consulte a secção de resolução de problemas abaixo.

## <a name="view-from-a-key-vault-resource"></a>Vista a partir de um recurso Key Vault

Para aceder ao Azure Monitor para o Key Vault diretamente a partir de um cofre de chaves:

1. No portal Azure, selecione Key Vaults.

2. Da lista, escolha um cofre chave. Na secção de monitorização, escolha Insights (pré-visualização).

Estas vistas também são acessíveis selecionando o nome de recurso de um cofre chave a partir do livro de trabalho de nível Azure Monitor.

![Screenshot de vista de um recurso de cofre chave](./media/key-vaults-insights-overview/key-vault-resource-view.png)

No **livro de visão** geral para o cofre chave, mostra várias métricas de desempenho que o ajudam a avaliar rapidamente:

- Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados com transações de cofre chave, latência e disponibilidade.

- Métricas e telhas de estado que destacam a disponibilidade do serviço, a contagem total de transações para o recurso do cofre chave e a latência geral.

A seleção de qualquer um dos outros separadores para **Falhas** ou **Operações** abre os respetivos livros de trabalho.

![Screenshot da vista de falhas](./media/key-vaults-insights-overview/resource-failures.png)

O livro de falhas desagreia os resultados de todos os pedidos de cofre chave no prazo selecionado e fornece categorização em Sucessos (2xx), Erros de Autenticação (401/403), Throttling (429) e outras falhas.

![Screenshot da visão de operações](./media/key-vaults-insights-overview/operations.png)

O livro de operações permite que os utilizadores mergulhem profundamente nos detalhes completos de todas as transações, que podem ser filtradas pelo Estado de Resultado usando os azulejos de nível superior.

![Screenshot da visão de operações](./media/key-vaults-insights-overview/info.png)

Os utilizadores também podem estender pontos de vista com base em tipos de transação específicos na tabela superior, que atualiza dinamicamente a tabela inferior, onde os utilizadores podem ver todos os detalhes de funcionamento num painel de contexto pop-up.

>[!NOTE]
> Note que os utilizadores devem ter as definições de diagnóstico ativadas para visualizar este livro. Para saber mais sobre a definição de diagnóstico, leia mais sobre [a Azure Key Vault Logging](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Pino e exportação

Pode fixar qualquer uma das secções métricas a um painel de instrumentos Azure selecionando o ícone de pino de pressão no canto superior direito da secção.

A visão geral de multi-subscrição e de cofres chave ou os livros de trabalho de falhas suportam a exportação dos resultados no formato Excel selecionando o ícone de descarregamento à esquerda do ícone pushpin.

![Screenshot do ícone pin selecionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalize o Monitor Azure para o cofre da chave

Esta secção destaca cenários comuns para a edição do livro para personalizar em apoio às necessidades de análise de dados:
*  Scope o livro para selecionar sempre uma subscrição ou um cofre de chaves específicos
* Alterar métricas na grelha
* Alterar o limiar de pedidos
* Alterar a renderização de cores

Pode iniciar personalizações ativando o modo de edição, selecionando o botão **Personalizar** a partir da barra de ferramentas superior.

![Screenshot do botão de personalização](./media/key-vaults-insights-overview/customize.png)

As personalizações são guardadas num livro personalizado para evitar a sobreescrita da configuração padrão no nosso livro publicado. Os livros de trabalho são guardados dentro de um grupo de recursos, quer na secção My Reports que é privada para si, quer na secção Relatórios Partilhados que é acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, tem de ir à galeria do livro para o lançar.

![Screenshot da galeria de livros](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificando uma subscrição ou cofre de chaves

Pode configurar os livros de visão geral de multi-subscrição e cofre de chaves para o âmbito de uma determinada subscrição ou cofre de chaves em cada execução, realizando os seguintes passos:

1. Selecione **Monitor** a partir do portal e, em seguida, selecione **Key Vaults (pré-visualização)** a partir do painel da esquerda.
2. No **livro de visão** geral, a partir da barra de comando selecione **Edit**.
3. Selecione a partir da lista de **subscrições** desacompindo uma ou mais subscrições que deseja que o yo use como padrão. Lembre-se, o livro suporta selecionar até um total de 10 subscrições.
4. Selecione na lista de drop-down dos **Cofres** de Chaves uma ou mais contas que pretende que utilize como padrão. Lembre-se, o livro suporta selecionar até um total de 200 contas de armazenamento.
5. **Selecione Guarde a** partir da barra de comando para guardar uma cópia do livro com as suas personalizações e, em seguida, clique em **Editar Feito** para voltar ao modo de leitura.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter orientações gerais sobre a resolução de problemas, consulte o artigo dedicado de resolução de [problemas](troubleshoot-workbooks.md)baseado em livros de trabalho .

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Azure Monitor para Key Vault (pré-visualização). Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas de desempenho ou falhas

Para ajudar a resolver problemas relacionados com o cofre que identificar com o Azure Monitor para o Key Vault (pré-visualização), consulte a documentação do [Cofre da Chave Azure](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Por que só posso ver 200 cofres chave

Há um limite de 200 cofres chave que podem ser selecionados e vistos. Independentemente do número de subscrições selecionadas, o número de cofres-chave selecionados tem um limite de 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no selecionador de assinaturas

Só mostramos subscrições que contenham cofres-chave, escolhidos a partir do filtro de subscrição selecionado, que são selecionados no "Diretório + Subscrição" no cabeçalho do portal Azure.

![Screenshot do filtro de subscrição](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Estou a receber uma mensagem de erro de que a "consulta excede o número máximo de espaços de trabalho/regiões permitidas", o que fazer agora

Atualmente, existe um limite para 25 regiões e 200 espaços de trabalho, para visualizar os seus dados, você precisará reduzir o número de subscrições e/ou grupos de recursos.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais a Key Vault Insights, como faço isso

Para escoar alterações, selecione o "Modo de Edição" para modificar o livro, então pode guardar o seu trabalho como um novo livro que está ligado a um grupo de subscrição e recursos designado.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o grão-tempo uma vez que pinemos qualquer parte dos livros de trabalho

Utilizamos o grão de tempo "Auto", pelo que depende do intervalo de tempo selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte do livro está presa

O intervalo de tempo dependerá das definições do painel de instrumentos.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Por que não vejo nenhum dado para o meu Cofre-Chave sob as secções de & de Latência de Operações

Para visualizar os dados baseados em registos, terá de ativar registos para cada um dos cofres-chave que pretende monitorizar. Isto pode ser feito sob as definições de diagnóstico para cada cofre de teclas. Terá de enviar os seus dados para um espaço de trabalho designado log analytics.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Já ativei registos para o meu Cofre-Chave, porque é que ainda não consigo ver os meus dados no âmbito das Operações & Latência.

Atualmente, os registos de diagnóstico não funcionam retroativamente, pelo que os dados só começarão a aparecer depois de terem sido tomadas ações para os seus cofres-chave. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo do quão ativo é o seu cofre chave.

Além disso, se tiver um elevado número de cofres-chave e subscrições selecionadas, poderá não conseguir visualizar os seus dados devido a limitações de consulta. Para visualizar os seus dados, poderá ser necessário reduzir o número de subscrições selecionadas ou cofres-chave. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>E se eu quiser ver outros dados ou fazer as minhas próprias visualizações? Como posso fazer alterações no Key Vault Insights

Pode editar o livro existente, através da utilização do modo de edição, e depois guardar o seu trabalho como um novo livro que terá todas as suas novas alterações.

## <a name="next-steps"></a>Passos seguintes

Saiba os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)
