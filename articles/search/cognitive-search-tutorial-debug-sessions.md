---
title: 'Tutorial: Use sessões de Debug para diagnosticar, corrigir e comprometer alterações no seu skillset'
titleSuffix: Azure Cognitive Search
description: As sessões de depurg (pré-visualização) fornecem uma interface baseada no portal para avaliar e reparar problemas/erros nas suas habilidades
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8bbd0b1979da69e5d4d18009100a7caee5a3d722
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397407"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Tutorial: Diagnosticar, reparar e comprometer alterações na sua habilidade

Neste artigo, você usará o portal Azure para aceder às sessões de Debug para reparar problemas com o skillset fornecido. O skillset tem alguns erros que precisam de ser resolvidos. Este tutorial irá levá-lo através de uma sessão de depurar para identificar e resolver problemas com entradas e saídas de habilidades.

> [!Important]
> As sessões de depuração são uma funcionalidade de pré-visualização fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ou use a sua subscrição atual
> * Uma instância de serviço de pesquisa cognitiva Azure
> * Uma conta de armazenamento Azure
> * [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este tutorial utiliza serviços de Pesquisa Cognitiva Azure e Armazenamento Azure.

* [Descarregue dados](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) de amostras compostos por 19 ficheiros.

* [Crie uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Escolha a mesma região que a Azure Cognitive Search para evitar cargas de largura de banda.
   
   Escolha o tipo de conta StorageV2 (finalidade geral V2).

* Abra as páginas dos serviços de armazenamento e crie um recipiente. A melhor prática é especificar o nível de acesso "privado". Diga o nome do seu `clinicaltrialdataset` recipiente.

* No recipiente, clique em **Upload** para carregar os ficheiros de amostra que descarregou e desapertou no primeiro passo.

* [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

:::image type="content" source="media/search-get-started-postman/get-url-key.png" alt-text="Obtenha uma chave de acesso http e acesso" border="false":::

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="create-data-source-skillset-index-and-indexer"></a>Criar fonte de dados, skillset, índice e indexador

Nesta secção, o Carteiro e uma recolha fornecida são utilizados para criar a fonte de dados, skillset, índice e indexante do serviço de pesquisa.

1. Se não tiver o Carteiro, pode [descarregar a aplicação de desktop do Carteiro aqui.](https://www.getpostman.com/)
1. [Baixe a coleção Debug Sessions Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Iniciar Carteiro
1. Under **Files**  >  **New**, selecione a coleção para importar.
1. Após a importação da coleção, aumente a lista de ações (...).
1. Clique em **Editar**.
1. Insira o nome do seu serviço de pesquisa (por exemplo, se o ponto final for `https://mydemo.search.windows.net` , então o nome de serviço é " `mydemo` ").
1. Introduza o apiKey com a chave primária ou secundária do seu serviço de pesquisa.
1. Introduza o armazenamentoConnectionString a partir da página de chaves da sua conta de Armazenamento Azure.
1. Introduza o recipienteName para o recipiente que criou na conta de armazenamento.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Obtenha uma chave de acesso http e acesso":::

A coleção contém quatro chamadas REST diferentes que são usadas para completar esta secção.

A primeira chamada cria a fonte de dados. `clinical-trials-ds`. A segunda chamada cria o skillset, `clinical-trials-ss` . A terceira chamada cria o índice, `clinical-trials` . A quarta e última chamada cria o indexante, `clinical-trials-idxr` . Depois de todas as chamadas da coleção terem sido concluídas, feche o Carteiro e regresse ao portal Azure.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="Obtenha uma chave de acesso http e acesso":::

## <a name="check-the-results"></a>Verifique os resultados

O skillset contém alguns erros comuns. Nesta secção, executar uma consulta vazia para devolver todos os documentos apresentará vários erros. Nas etapas seguintes, as questões serão resolvidas através de uma sessão de depurar.

1. Vá ao seu serviço de pesquisa no portal Azure. 
1. Selecione o separador **Índices.** 
1. Selecione o `clinical-trials` índice
1. Clique **em Procurar** para executar uma consulta vazia. 

Após o fim da pesquisa, dois campos sem dados listados; "organizações" e "locais" estão listados na janela. Siga os passos para descobrir todas as questões produzidas pelo skillset.

1. Volte à página geral do serviço de pesquisa.
1. Selecione o **separador Indexadores.** 
1. Clique `clinical-trials-idxr` e selecione a notificação de avisos. 

Existem muitos problemas com mapeamentos de campo de projeção e na página três há avisos porque uma ou mais entradas de habilidade são inválidas.

Volte ao ecrã geral do serviço de pesquisa.

## <a name="start-your-debug-session"></a>Inicie a sua sessão de depurar

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Obtenha uma chave de acesso http e acesso":::

1. Clique no separador Debug (pré-visualização).
1. Selecione +NewDebugSession
1. Dê um nome à sessão. 
1. Ligue a sessão à sua conta de armazenamento. 
1. Forneça o nome do indexante. O indexante tem referências à fonte de dados, ao skillset e ao índice.
1. Aceite a escolha do documento predefinido para o primeiro documento da coleção. 
1. **Guarde** a sessão. A poupança da sessão irá dar início ao oleoduto de enriquecimento de IA, tal como definido pelo skillset.

> [!Important]
> Uma sessão de depurar só funciona com um único documento. Um documento específico no conjunto de dados pode ser > selecionado ou a sessão será padrão para o primeiro documento.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="Obtenha uma chave de acesso http e acesso":::

Quando a sessão de depuração tiver terminado de executar, a sessão predefine no separador DeSecimentos de IA, destacando o Gráfico de Habilidades.

+ O Skill Graph fornece uma hierarquia visual do skillset e sua ordem de execução de cima para baixo. As habilidades que estão lado a lado no gráfico são executadas em paralelo. A codificação de cores de habilidades no gráfico indica os tipos de habilidades que estão sendo executadas no skillset. No exemplo, as habilidades verdes são texto e a habilidade vermelha é a visão. Clicar numa habilidade individual no gráfico mostrará os detalhes dessa instância da habilidade no painel direito da janela de sessão. As definições de habilidade, um editor JSON, detalhes de execução e erros/avisos estão todos disponíveis para revisão e edição.
+ A Estrutura de Dados Enriquecido detalha os nós na árvore de enriquecimento gerada pelas competências do conteúdo do documento de origem.

O separador Erros/Avisos fornecerá uma lista muito menor do que a apresentada anteriormente porque esta lista está apenas a detalhar os erros de um único documento. Tal como a lista apresentada pelo indexante, pode clicar numa mensagem de aviso e ver os detalhes deste aviso.

## <a name="fix-missing-skill-input-value"></a>Corrigir o valor de entrada de habilidade em falta

No separador Erros/Avisos, existe um erro para uma operação rotulada `Enrichment.NerSkillV2.#1` . O detalhe deste erro explica que existe um problema com um valor de entrada de habilidade '/documento/languageCode'. 

1. Volte ao separador AI Enrichments.
1. Clique no **Gráfico de Habilidades.**
1. Clique na habilidade rotulada #1 para mostrar os seus detalhes no painel direito.
1. Localize a entrada para "languageCode".
1. Selecione o **</>** símbolo no início da linha e abra o Avaliador de Expressão.
1. Clique no botão **Avaliar** para confirmar que esta expressão está a resultar num erro. Confirmará que a propriedade "languageCode" não é uma entrada válida.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Obtenha uma chave de acesso http e acesso":::

Há duas formas de investigar este erro na sessão. A primeira é olhar para onde vem a entrada - que habilidade na hierarquia deve produzir este resultado? O separador Execuções no painel de detalhes de habilidade deve mostrar a origem da entrada. Se não houver origem, isto indica um erro de mapeamento de campo.

1. Clique no separador **Execuções.**
1. Olhe para os INPUTS e encontre "languageCode". Não há nenhuma fonte para esta entrada listada. 
1. Mude o painel esquerdo para visualizar a Estrutura de Dados Enriquecida. Não existe um caminho mapeado correspondente ao "languageCode".

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Obtenha uma chave de acesso http e acesso" e "document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Corrigir valores de saída de competência em falta

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso" e "/documento/conteúdo/localizações", respectivamente.

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso" do INPUT.

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso" do OUTPUTS.

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso" a partir de "conteúdo". Quais são os resultados para estas entidades na competência?

1. Selecione o **separador AI Enriquecimentos.**
1. Selecione **Skill Graph** e clique em #1 de habilidade.
1. Navegue nas **Definições de Habilidade** para encontrar "saídas".
1. Abra o Avaliador de Expressão **</>** para a entidade "organizações".

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso" e edite-o para ler "/document/merged_content".
1. Clique em **Guardar** no painel de detalhes de habilidades certos.
1. Clique em **Executar** no menu da janela das sessões. Isto irá dar início a mais uma execução do skillset usando o documento.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Obtenha uma chave de acesso http e acesso":::

Todos os erros foram resolvidos.

## <a name="commit-changes-to-the-skillset"></a>Comprometer alterações no skillset

Quando a sessão de depuração foi iniciada, o serviço de pesquisa criou uma cópia do skillset. Isto foi feito para que as alterações introduzidas não afetassem o sistema de produção. Agora que terminou de depurar o seu skillset, as correções podem ser comprometidas (substituir o skillset original) para o sistema de produção. Se quiser continuar a fazer alterações no skillset sem afetar o sistema de produção, a sessão de depuragem pode ser salva e reaberta mais tarde.

1. Clique em **Comprometer alterações** no menu principal de sessões de Debug.
1. Clique **em OK** para confirmar que deseja atualizar o seu skillset.
1. Feche a sessão de Depurrão e selecione o **separador Indexadores.**
1. Abra o seu "clínico-idxr".
1. Clique **em Reiniciar**.
1. Clique em **Run** (Executar). Clique **em OK** para confirmar.

Quando o indexante terminar de funcionar, deve haver uma marca de verificação verde e a palavra Sucesso ao lado do carimbo de tempo para a última execução no separador histórico de execução. Para garantir a aplicação das alterações:

1. Exit **Indexer** e selecione o **separador Índice.**
1. Abra o índice 'ensaios clínicos' e no separador Explorador de Pesquisa, clique em **Procurar**.
1. A janela de resultados deve mostrar que as entidades de organizações e locais estão agora povoadas com os valores esperados.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre skillsets](./cognitive-search-working-with-skillsets.md) 
>  [Saiba mais sobre enriquecimento incremental e caching](./cognitive-search-incremental-indexing-conceptual.md)