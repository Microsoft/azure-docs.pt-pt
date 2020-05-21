---
title: 'Tutorial: Use sessões de Debug para diagnosticar, corrigir e comprometer alterações na sua habilidade'
titleSuffix: Azure Cognitive Search
description: As sessões de depuração (pré-visualização) fornecem uma interface baseada no portal para avaliar e reparar problemas/erros nas suas habilidades
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: b84f98bd383c2b90c3291527b336d798e9b9cae9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666139"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Tutorial: Diagnosticar, reparar e comprometer alterações na sua habilidade

Neste artigo, utilizará o portal Azure para aceder às sessões de Debug para reparar problemas com as competências fornecidas. A habilidade tem alguns erros que precisam de ser resolvidos. Este tutorial irá levá-lo através de uma sessão de depuração para identificar e resolver problemas com inputs de habilidade e saídas.

> [!Important]
> O suporte para sessões de depuração para A Pesquisa Cognitiva Azure está disponível [mediante pedido](https://aka.ms/DebugSessions) como pré-visualização de acesso limitado. As funcionalidades de pré-visualização são fornecidas sem um acordo de nível de serviço, e não são recomendadas para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> Assim que lhe for concedido o acesso à pré-visualização, poderá aceder e utilizar sessões de Debug para o seu serviço utilizando o portal Azure.
>   

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ou use a sua subscrição atual
> * Uma instância de serviço de pesquisa cognitiva Azure
> * Uma conta de armazenamento azure
> * [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Criar serviços e dados de carga

Este tutorial utiliza serviços de Pesquisa Cognitiva Azure e Armazenamento Azure.

* [Descarregue dados](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) da amostra compostos por 19 ficheiros.

* [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) de armazenamento Azure ou [encontre uma conta existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 

   Escolha a mesma região que a Pesquisa Cognitiva Azure para evitar cargas de largura de banda.
   
   Escolha o tipo de conta StorageV2 (finalidade geral V2).

* Abra as páginas dos serviços de armazenamento e crie um recipiente. A melhor prática é especificar o nível de acesso "privado". Nomeie o seu recipiente `clinicaltrialdataset` .

* No recipiente, clique em **Carregar** para carregar os ficheiros de amostra que descarregou e desfechou no primeiro passo.

* [Crie um serviço](search-create-service-portal.md) de Pesquisa Cognitiva Azure ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Settings**  >  **Definições Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="create-data-source-skillset-index-and-indexer"></a>Criar fonte de dados, skillset, indexado e indexante

Nesta secção, o Carteiro e uma recolha fornecida são usados para criar a fonte de dados do serviço de pesquisa, skillset, indexe e indexante.

1. Se não tiver o Carteiro, pode [baixar a aplicação de desktop do Carteiro aqui](https://www.getpostman.com/).
1. [Descarregue a coleção Debug Sessions Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Iniciar carteiro
1. Em **Ficheiros**  >  **Novos,** selecione a coleção para importar.
1. Depois de importada a recolha, expanda a lista de ações (...).
1. Clique em **Editar**.
1. Introduza o nome do seu serviço de pesquisa (por exemplo, se o ponto final for https://mydemo.search.windows.net , então o nome do serviço é "mydemo").
1. Introduza o apiKey com a chave primária ou secundária do seu serviço de pesquisa.
1. Introduza o armazenamentoConnectionString na página de chaves da sua conta De armazenamento Azure.
1. Introduza o nome do recipiente para o recipiente que criou na conta de armazenamento.

> [!div class="mx-imgBorder"]
> ![editar variáveis no Carteiro](media/cognitive-search-debug/postman-enter-variables.png)

A coleção contém quatro chamadas REST diferentes que são usadas para completar esta secção.

A primeira chamada cria a fonte de dados. `clinical-trials-ds`. A segunda chamada cria a habilidade, `clinical-trials-ss` . A terceira chamada cria o `clinical-trials` índice. A quarta e última chamada cria o indexador. `clinical-trials-idxr` Depois de todas as chamadas na coleção terem sido concluídas, feche o Carteiro e regresse ao portal Azure.

> [!div class="mx-imgBorder"]
> ![usando o Carteiro para criar fonte de dados](media/cognitive-search-debug/postman-create-data-source.png)

## <a name="check-the-results"></a>Verifique os resultados

A habilidade contém alguns erros comuns. Nesta secção, a realização de uma consulta vazia para devolver todos os documentos apresentará múltiplos erros. Em etapas posteriores, as questões serão resolvidas através de uma sessão de depuração.

1. Vá ao seu serviço de pesquisa no portal Azure. 
1. Selecione o separador **Indexes.** 
1. Selecione o `clinical-trials` índice
1. Clique em **Procurar** para fazer uma consulta vazia. 

Após a pesquisa ter terminado, dois campos sem dados listados; "organizações" e "locais" estão listados na janela. Siga os passos para descobrir todas as questões produzidas pela habilidade.

1. Volte à página de visualização geral do serviço de pesquisa.
1. Selecione o separador **Indexers.** 
1. Clique `clinical-trials-idxr` e selecione a notificação de avisos. 

Existem muitos problemas com mapeamentos de campo de saída de projeção e na página três há avisos porque uma ou mais inputs de habilidade são inválidos.

Volte ao ecrã de visão geral do serviço de pesquisa.

## <a name="start-your-debug-session"></a>Inicie a sua sessão de depuração

> [!div class="mx-imgBorder"]
> ![iniciar uma nova sessão de depuração](media/cognitive-search-debug/new-debug-session-screen-required.png)

1. Clique no separador Debug (pré-visualização).
1. Selecione +NewDebugSession
1. Dê um nome à sessão. 
1. Ligue a sessão à sua conta de armazenamento. 
1. Forneça o nome do indexador. O indexante tem referências à fonte de dados, à habilidade e ao índice.
1. Aceite a escolha padrão do documento para o primeiro documento da coleção. 
1. **Salve** a sessão. Salvar a sessão iniciará o oleoduto de enriquecimento de IA, tal como definido pela habilidade.

> [!Important]
> Uma sessão de depuração só funciona com um único documento. Um documento específico no conjunto de dados pode ser > selecionado ou a sessão será indefinida para o primeiro documento.

> [!div class="mx-imgBorder"]
> ![Começou nova sessão de depuração](media/cognitive-search-debug/debug-execution-complete1.png)

Quando a sessão de depuração terminar a execução, a sessão falha no separador De enriquecimento de IA, destacando o Gráfico de Habilidades.

+ O Skill Graph fornece uma hierarquia visual do skillset e sua ordem de execução de cima para baixo. As habilidades que estão lado a lado no gráfico são executadas em paralelo. A codificação de cores das habilidades no gráfico indica os tipos de habilidades que estão a ser executadas no skillset. No exemplo, as habilidades verdes são texto e a habilidade vermelha é a visão. Clicar numa habilidade individual no gráfico mostrará os detalhes dessa instância da habilidade no painel direito da janela da sessão. As definições de habilidades, um editor da JSON, detalhes de execução e erros/avisos estão disponíveis para revisão e edição.
+ A Estrutura de Dados Enriquecidos detalha os nódosos na árvore de enriquecimento geradopelas competências a partir do conteúdo do documento de origem.

O separador Errors/Warnings fornecerá uma lista muito menor do que a apresentada anteriormente porque esta lista está apenas detalhando os erros de um único documento. Tal como a lista apresentada pelo indexante, pode clicar numa mensagem de aviso e ver os detalhes deste aviso.

## <a name="fix-missing-skill-input-value"></a>Fixar o valor de entrada de habilidade em falta

No separador Erros/Advertências, existe um erro para uma operação rotulada `Enrichment.NerSkillV2.#1` . O detalhe deste erro explica que existe um problema com um valor de entrada de competência '/documento/idiomaCódigo'. 

1. Volte ao separador de enriquecimento de IA.
1. Clique no **Gráfico de Habilidades**.
1. Clique na habilidade rotulada #1 para mostrar os seus detalhes no painel certo.
1. Localize a entrada para "languageCode".
1. Selecione o **</>** símbolo no início da linha e abra o Avaliador de Expressão.
1. Clique no botão **Avaliar** para confirmar que esta expressão está a resultar num erro. Confirmará que a propriedade "languageCode" não é uma entrada válida.

> [!div class="mx-imgBorder"]
> ![Avaliador de Expressão](media/cognitive-search-debug/expression-evaluator-language.png)

Há duas formas de investigar este erro na sessão. A primeira é olhar para a parte de onde vem o contributo - que habilidade na hierarquia deve produzir este resultado? O separador Execuções no painel de detalhes de habilidade deve apresentar a fonte da entrada. Se não houver fonte, isto indica um erro de mapeamento de campo.

1. Clique no separador **Execuções.**
1. Olhe para os INPUTS e encontre "languageCode". Não há nenhuma fonte para esta entrada listada. 
1. Mude o painel esquerdo para visualizar a Estrutura de Dados Enriquecidos. Não existe um caminho mapeado correspondente ao "LanguageCode".

> [!div class="mx-imgBorder"]
> ![Estrutura de Dados Enriquecidos](media/cognitive-search-debug/enriched-data-structure-language.png)

Há um caminho traçado para a "linguagem". Então, há um erro nas definições de habilidade. Para corrigir esta expressão na #1 competência com a expressão '/documento/linguagem' terá de ser atualizada.

1. Abra o Avaliador de Expressão **</>** para o caminho "linguagem".
1. Copie a expressão. Feche a janela.
1. Aceda às Definições de Habilidade para a habilidade #1 e abra o Avaliador de Expressão **</>** para a entrada "LanguageCode".
1. Colar o novo valor, '/documento/idioma' na caixa de expressão e clicar **em Avaliar**.
1. Deve apresentar a entrada correta "en". Clique Em Aplicar para atualizar a expressão.
1. Clique em **Guardar** no painel certo, detalhes de habilidade.
1. Clique em **Executar** no menu da janela da sessão. Isto vai dar início a outra execução do skillset usando o documento. 

Assim que a execução da sessão de depuração estiver concluída, clique no separador Errors/Warnings e mostrará que o erro marcado "Enriquecimento.NerSkillV2.#1" desapareceu. No entanto, ainda existem dois avisos de que o serviço não poderia mapear campos de saída para organizações e locais para o índice de pesquisa. Faltam valores: «/documento/merged_content/organizações» e «/documento/merged_content/locais».

## <a name="fix-missing-skill-output-values"></a>Fixar valores de saída de habilidades em falta

> [!div class="mx-imgBorder"]
> ![Erros e advertências](media/cognitive-search-debug/warnings-missing-value-locs-orgs.png)

Faltam valores de saída de uma habilidade. Para identificar a habilidade com o erro vá para a Estrutura de Dados Enriquecidos, encontre o nome de valor e veja a sua Origem. No caso dos valores das organizações e locais desaparecidos, são saídas de habilidades #1. A abertura do Avaliador de Expressão </> para cada caminho, apresentará as expressões listadas como '/documento/conteúdo/organizações' e '/documento/conteúdo/localização', respectivamente.

> [!div class="mx-imgBorder"]
> ![Entidade de avaliação de expressão](media/cognitive-search-debug/expression-eval-missing-value-locs-orgs.png)

A produção destas entidades está vazia e não deve estar vazia. Quais são as inputs que produzem este resultado?

1. Vá ao **Skill Graph** e selecione #1 de habilidades.
1. Selecione o separador **Execuções** no painel de detalhes de habilidade seletiva.
1. Abra o Avaliador de Expressão **</>** para o "texto" input.

> [!div class="mx-imgBorder"]
> ![Entrada para habilidade de texto](media/cognitive-search-debug/input-skill-missing-value-locs-orgs.png)

O resultado apresentado para esta entrada não parece uma entrada de texto. Parece uma imagem rodeada de novas linhas. A falta de texto significa que nenhuma entidade pode ser identificada. Olhando para a hierarquia do skillset mostra que o conteúdo é processado primeiro pela habilidade #6 (OCR) e depois passado para a habilidade #5 (Fusão). 

1. Selecione a habilidade #5 (Fusão) no **Gráfico de Habilidades**.
1. Selecione o separador **Execuções** no painel de detalhes de habilidades certos e abra o Avaliador de Expressão **</>** para os OUTPUTS "text merged".

> [!div class="mx-imgBorder"]
> ![Saída para habilidade de fusão](media/cognitive-search-debug/merge-output-detail-missing-value-locs-orgs.png)

Aqui o texto é emparelhado com a imagem. Olhando para a expressão '/documento/merged_content' é visível o erro nos caminhos das "organizações" e "localizações" para a #1 competência. Em vez de utilizar '/documento/conteúdo' deve utilizar '/documento/merged_content' para as entradas de "texto".

1. Copie a expressão para a saída "textmerge" e feche a janela do Avaliador de Expressão.
1. Selecione #1 de habilidade no **Gráfico de Habilidades**.
1. Selecione o separador **Definições** de Habilidade sintetizador a de habilidades corretas.
1. Abra o Avaliador de Expressão **</>** para a entrada de "texto".
1. Cola a nova expressão na caixa. Clique em **Avaliar**.
1. A entrada correta com o texto adicionado deve ser visualizada. Clique **Em Aplicar** para atualizar as Definições de Habilidade.
1. Clique em **Guardar** no painel certo, detalhes de habilidade.
1. Clique em **Executar** no menu da janela de sessões. Isto vai dar início a outra execução do skillset usando o documento.

Depois de o indexante ter terminado de funcionar, os erros ainda estão lá. Volte para a perícia #1 e investigue. O contributo para a habilidade foi corrigido para "merged_content" a partir de "conteúdo". Quais são as saídas para estas entidades na competência?

1. Selecione o separador **De Enriquecimento de IA.**
1. Selecione **Skill Graph** e clique em habilidade #1.
1. Navegue nas **Definições** de Habilidade para encontrar "saídas".
1. Abra o Avaliador de Expressão **</>** para a entidade "organizações".

> [!div class="mx-imgBorder"]
> ![Produção para entidade sibilantes](media/cognitive-search-debug/skill-output-detail-missing-value-locs-orgs.png)

Avaliar o resultado da expressão dá o resultado correto. A habilidade está a trabalhar para identificar o valor correto para a entidade, "organizações". No entanto, o mapeamento de saída no caminho da entidade ainda está a lançar um erro. Ao comparar o percurso de saída na habilidade com o caminho de saída no erro, a habilidade que está a ser parental izada pelas saídas, organizações e localizações sob o nó/documento/conteúdo. Enquanto o mapeamento do campo de saída espera que os resultados sejam parentados sob o nó /documento/merged_content. Na etapa anterior, a entrada passou de «/documento/conteúdo» para '/documento/merged_content». O contexto nas definições de habilidade supor que a saída é gerada com o contexto certo.

1. Selecione o separador **De Enriquecimento de IA.**
1. Selecione **Skill Graph** e clique em habilidade #1.
1. Navegue nas **Definições** de Habilidade para encontrar "contexto".
1. Clique duas vezes na definição para "context" e edite-a para ler '/documento/merged_content'.
1. Clique em **Guardar** no painel certo, detalhes de habilidade.
1. Clique em **Executar** no menu da janela de sessões. Isto vai dar início a outra execução do skillset usando o documento.

> [!div class="mx-imgBorder"]
> ![Correção de contexto na definição de competências](media/cognitive-search-debug/skill-setting-context-correction-missing-value-locs-orgs.png)

Todos os erros foram resolvidos.

## <a name="commit-changes-to-the-skillset"></a>Comprometa alterações no skillset

Quando a sessão de depuração foi iniciada, o serviço de pesquisa criou uma cópia do skillset. Isto foi feito de modo a que as alterações efetuadas não afetassem o sistema produtivo. Agora que terminou de depurar a sua habilidade, as correções podem ser comprometidas (substituir a habilidade original) para o sistema de produção. Se quiser continuar a fazer alterações no skillset sem afetar o sistema de produção, a sessão de depuração pode ser salva e reaberta mais tarde.

1. Clique em alterar as **alterações** no menu principal de sessões de Debug.
1. Clique **em OK** para confirmar que deseja atualizar a sua habilidade.
1. Feche a sessão de Debug e selecione o separador **Indexers.**
1. Abra o seu "ensaio clínico-idxr".
1. Clique em **Redefinir**.
1. Clique em **Executar**. Clique **em OK** para confirmar.

Quando o indexante terminar de funcionar, deve haver uma marca de verificação verde e a palavra Sucesso ao lado do carimbo de tempo para a última execução no separador de história da Execução. Para garantir que as alterações foram aplicadas:

1. Indicador **Indexer** de saída e selecione o separador **Index.**
1. Abra o índice de 'ensaios clínicos' e no separador Explorador de Pesquisa, clique em **Procurar**.
1. A janela de resultados deve mostrar que as entidades de organizações e locais estão agora povoadas com os valores esperados.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba mais sobre habilidades](https://docs.microsoft.com/azure/search/cognitive-search-working-with-skillsets) 
>  [Saiba mais sobre enriquecimento incremental e cache](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)