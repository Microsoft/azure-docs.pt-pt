---
title: 'Tutorial: Use Sessões de Debug para corrigir habilidades'
titleSuffix: Azure Cognitive Search
description: As sessões de depuração (pré-visualização) são uma ferramenta do portal Azure usada para encontrar, diagnosticar e reparar problemas num skillset.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509154"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Tutorial: Debug um skillset usando Debug Sessions

Os Skillsets coordenam uma série de ações que analisam ou transformam conteúdo, onde a saída de uma habilidade se torna a entrada de outra. Quando as entradas dependem de saídas, erros nas definições de skillset e associações de campo podem resultar em operações e dados perdidos.

**As sessões de depuração** no portal Azure proporcionam uma visualização holística de um skillset. Utilizando esta ferramenta, pode perfurar até passos específicos para ver facilmente onde uma ação pode estar a cair.

Neste artigo, você usará **sessões de Debug** para encontrar e corrigir 1) uma entrada em falta e 2) mapeamentos de campo de saída. O tutorial é tudo incluído. Fornece dados para indexar (dados de ensaios clínicos), uma coleção de carteiros que cria objetos, e instruções para usar **sessões de Debug** para encontrar e corrigir problemas no skillset.

> [!Important]
> As sessões de depuração são uma funcionalidade de pré-visualização fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha os seguintes pré-requisitos no lugar:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ Uma conta de Armazenamento Azure com [armazenamento Blob,](../storage/blobs/index.yml)utilizada para hospedar dados de amostra, e para a persistência de dados temporários criados durante uma sessão de depuração.

+ [Aplicativo de desktop do Carteiro](https://www.getpostman.com/) e uma [coleção do Carteiro](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) para criar objetos usando as APIs REST.

+ [Dados da amostra (ensaios clínicos)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Este quickstart também usa [serviços cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para processamento gratuito até 20 transações. Isto significa que você pode completar este exercício sem ter que criar um recurso adicional de Serviços Cognitivos.

## <a name="set-up-your-data"></a>Configurar os seus dados

Esta secção cria o conjunto de dados de amostra no armazenamento de blob Azure para que o indexante e skillset tenham conteúdo para trabalhar.

1. [Descarregue os dados da amostra (ensaios clínicos-pdf-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), compostos por 19 ficheiros.

1. [Crie uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Escolha a mesma região que a Azure Cognitive Search para evitar cargas de largura de banda.

   + Escolha o tipo de conta StorageV2 (finalidade geral V2).

1. Navegue nas páginas dos serviços de armazenamento Azure no portal e crie um recipiente Blob. A melhor prática é especificar o nível de acesso "privado". Diga o nome do seu `clinicaltrialdataset` recipiente.

1. No recipiente, clique em **Upload** para carregar os ficheiros de amostra que descarregou e desapertou no primeiro passo.

1. Enquanto está no portal, obtenha e guarde a cadeia de ligação para o Azure Storage. Vai precisar para o REST API ligar para os dados do índice. Pode obter a cadeia de ligação a partir de  >  **Definições de Teclas de acesso** no portal.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obtenha uma chave de acesso http e acesso" border="false":::

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="create-data-source-skillset-index-and-indexer"></a>Criar fonte de dados, skillset, índice e indexador

Nesta secção, o Carteiro e uma recolha fornecida são usados para criar a fonte de dados de Pesquisa Cognitiva, skillset, índice e indexante. Se não está familiarizado com o Carteiro, veja [este quickstart.](search-get-started-rest.md)

Você precisará da [coleção do Carteiro](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) criada para este tutorial para completar esta tarefa. 

1. Inicie o Carteiro e importe a coleção. Under **Files**  >  **New**, selecione a coleção para importar.
1. Após a importação da coleção, aumente a lista de ações (...).
1. Clique em **Editar**.
1. Em Valor Corrente, insira o nome do seu `searchService` (por exemplo, se o ponto final for `https://mydemo.search.windows.net` , então o nome de serviço é " `mydemo` ").
1. Introduza a `apiKey` chave primária ou secundária do seu serviço de pesquisa.
1. Introduza a `storageConnectionString` página de chaves da sua conta de Armazenamento Azure.
1. Introduza o `containerName` recipiente para o recipiente que criou na conta de armazenamento e, em seguida, clique em **Update**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="editar variáveis no Carteiro":::

A coleção contém quatro chamadas REST diferentes que são usadas para criar objetos usados neste tutorial.

A primeira chamada cria a fonte de dados. `clinical-trials-ds`. A segunda chamada cria o skillset, `clinical-trials-ss` . A terceira chamada cria o índice, `clinical-trials` . A quarta e última chamada cria o indexante, `clinical-trials-idxr` .

+ Abra cada pedido por sua vez e clique em **Enviar** cada pedido para o serviço de pesquisa. 

Depois de todas as chamadas da coleção terem sido concluídas, feche o Carteiro e regresse ao portal Azure.

## <a name="check-results-in-the-portal"></a>Verifique os resultados no portal

O código de amostra cria intencionalmente um índice de buggy como consequência de problemas ocorridos durante a execução do skillset. O problema é a falta de dados. 

1. No portal Azure, na página de Visão Geral do serviço de pesquisa, selecione o separador **Índices.** 
1. Selecione o `clinical-trials` índice.
1. Introduza esta cadeia de consulta: `$select=metadata_storage_path, organizations, locations&$count=true` para devolver campos para documentos específicos (identificados pelo campo `metadata_storage_path` único).
1. Clique em **Procurar** para executar a consulta, devolvendo todos os 19 documentos, mostrando valores vazios para "organizações" e "localizações".

Estes campos deveriam ter sido povoados através da habilidade de Reconhecimento de [Entidades](cognitive-search-skill-entity-recognition.md)do Skillset, usados para encontrar organizações e locais em qualquer lugar dentro do conteúdo da bolha. No próximo exercício, vais usar a sessão do Debug para determinar o que correu mal.

Outra forma de investigar erros e avisos é através do portal Azure.

1. Abra o **separador Indexadores** e selecione `clinical-trials-idxr` .
1. Note-se que, embora o trabalho de indexante tenha sido bem sucedido no geral, houve 57 avisos.
1. Clique **em Sucesso** para ver os avisos (se houvesse na sua maioria erros, o link de detalhe seria **Falhado).** Verá uma longa lista de todos os avisos emitidos pelo indexante.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="ver avisos":::

## <a name="start-your-debug-session"></a>Inicie a sua sessão de depurar

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="iniciar uma nova sessão de depurar":::

1. Na página de visão geral da pesquisa, clique no separador **Sessões Debug.**
1. Selecione **+ Nova Sessão de Depurg**.
1. Dê um nome à sessão. 
1. Ligue a sessão à sua conta de armazenamento. 
1. No modelo indexante, forneça o nome do indexante. O indexante tem referências à fonte de dados, ao skillset e ao índice.
1. Aceite a escolha do documento predefinido para o primeiro documento da coleção. 
1. **Guarde** a sessão. A poupança da sessão irá dar início ao oleoduto de enriquecimento de IA, tal como definido pelo skillset.

> [!Important]
> Uma sessão de depurar só funciona com um único documento. Pode escolher qual o documento a depurar ou apenas usar o primeiro.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Quando a sessão de depuração tiver terminado a inicialização, a sessão predefine no **separador DeSecimentos de IA,** destacando o **Gráfico de Habilidades**. O Gráfico de Habilidades fornece uma hierarquia visual do skillset e sua ordem de execução sequencialmente e em paralelo.

## <a name="find-issues-with-the-skillset"></a>Encontrar problemas com o skillset

Quaisquer problemas relatados pelo indexante podem ser encontrados no **separador Erros/Avisos adjacentes.** 

Note que o separador **Erros/Avisos** fornecerá uma lista muito menor do que a apresentada anteriormente porque esta lista está apenas detalhando os erros de um único documento. Tal como a lista apresentada pelo indexante, pode clicar numa mensagem de aviso e ver os detalhes deste aviso.

Selecione **Erros/Avisos** para rever as notificações. Devia ver três:

   + "Não foi possível mapear as 'localizações' do campo de saída para o índice de pesquisa. Verifique a propriedade 'outputFieldMappings' do seu indexante.
Valor em falta '/documento/merged_content/localizações'."

   + "Não foi possível mapear as 'organizações' do campo de saída para o índice de pesquisa. Verifique a propriedade 'outputFieldMappings' do seu indexante.
Valor em falta '/documento/merged_content/organizações'."

   + "Habilidade executada, mas pode ter resultados inesperados porque uma ou mais entradas de habilidades eram inválidas.
Falta uma entrada de habilidade opcional. Nome: 'languageCode', Fonte: '/document/languageCode'. Questões de análise da linguagem de expressão: Valor em falta '/documento/languageCode'."

   Muitas habilidades têm um parâmetro 'languageCode'. Ao inspecionar a operação, pode ver que esta entrada de código de idioma está em falta a partir da `Enrichment.NerSkillV2.#1` , que é a mesma habilidade de Reconhecimento de Entidade que está a ter problemas com a produção de 'localizações' e 'organizações'. 

Como as três notificações são sobre esta habilidade, o seu próximo passo é depurar esta habilidade. Se possível, comece por resolver problemas de entrada primeiro antes de passar para os problemas de outputFieldMapping.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Nova sessão de depurar começou":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Corrigir o valor de entrada de habilidade em falta

No **separador Erros/Avisos,** existe um erro para uma operação rotulada `Enrichment.NerSkillV2.#1` . O detalhe deste erro explica que existe um problema com um valor de entrada de habilidade '/documento/languageCode'. 

1. Volte ao separador **AI Enrichments.**
1. Clique no **Gráfico de Habilidades.**
1. Clique na habilidade rotulada **#1** para mostrar os seus detalhes no painel direito.
1. Localize a entrada para "languageCode".
1. Selecione o **</>** símbolo no início da linha e abra o Avaliador de Expressão.
1. Clique no botão **Avaliar** para confirmar que esta expressão está a resultar num erro. Confirmará que a propriedade "languageCode" não é uma entrada válida.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Avaliador de Expressão":::

Há duas formas de investigar este erro na sessão. A primeira é olhar para onde vem a entrada - que habilidade na hierarquia deve produzir este resultado? O separador Execuções no painel de detalhes de habilidade deve mostrar a origem da entrada. Se não houver origem, isto indica um erro de mapeamento de campo.

1. Clique no separador **Execuções.**
1. Olhe para os INPUTS e encontre "languageCode". Não há nenhuma fonte para esta entrada listada. 
1. Mude o painel esquerdo para visualizar a Estrutura de Dados Enriquecida. Não existe um caminho mapeado correspondente ao "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Estrutura de Dados Enriquecida":::

Há um caminho traçado para "linguagem". Então, há um erro nas definições de habilidade. Para corrigir esta expressão no #1 habilidade com a expressão "/documento/linguagem" terá de ser atualizada.

1. Abra o avaliador de expressão **</>** para o caminho "linguagem".
1. Copie a expressão. Feche a janela.
1. Vá às Definições de Habilidade para a habilidade #1 e abra o Avaliador de Expressão **</>** para a entrada "LanguageCode".
1. Cole o novo valor, '/documento/idioma' na caixa de Expressão e clique em **Avaliar.**
1. Deve mostrar a entrada correta "en". Clique em Aplicar para atualizar a expressão.
1. Clique em **Guardar** no painel de detalhes de habilidades certos.
1. Clique em **Executar** no menu da janela da sessão. Isto irá dar início a mais uma execução do skillset usando o documento. 

Assim que a execução da sessão de depuração estiver concluída, clique no separador Erros/Avisos e mostrará que o erro com a etiqueta "Enriquecimento.NerSkillV2.#1" desapareceu. No entanto, existem ainda dois avisos de que o serviço não conseguiu mapear campos de saída para organizações e locais para o índice de pesquisa. Faltam valores: "/document/merged_content/organizations" e "document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Corrigir valores de saída de competência em falta

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Erros e avisos":::

Faltam valores de saída de uma habilidade. Para identificar a habilidade com o erro aceda à Estrutura de Dados Enriquecidos, encontre o nome de valor e veja a sua Fonte Originária. No caso das organizações e dos valores de locais em falta, são saídas de #1 de competências. A abertura do avaliador de expressão </> para cada caminho, apresentará as expressões listadas como "/documento/conteúdo/organizações" e "/documento/conteúdo/localizações", respectivamente.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entidade de avaliadores de expressão":::

A produção para estas entidades está vazia e não deve estar vazia. Quais são as entradas que produzem este resultado?

1. Vá ao **Skill Graph** e selecione habilidade #1.
1. Selecione O separador **execuções** no painel de detalhes de habilidades certos.
1. Abra o avaliador de expressão **</>** para o "texto" do INPUT.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Entrada para habilidade de texto":::

O resultado apresentado para esta entrada não parece uma entrada de texto. Parece uma imagem rodeada de novas linhas. A falta de texto significa que nenhuma entidade pode ser identificada. Olhando para a hierarquia do skillset exibe o conteúdo é processado primeiro pela habilidade #6 (OCR) e depois passado para a habilidade #5 (Merge). 

1. Selecione a habilidade #5 (Merge) no **Gráfico de Habilidades**.
1. Selecione o separador **Execuções** no painel de detalhes de habilidades certos e abra o Avaliador de Expressão **</>** para o "Texto fundido" do OUTPUTS.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Saída para a capacidade de fusão":::

Aqui o texto é emparelhado com a imagem. Olhando para a expressão "/document/merged_content" é visível o erro nos caminhos das "organizações" e "localizações" para a #1 habilidade. Em vez de utilizar "/documento/conteúdo" deve utilizar "/document/merged_content" para as entradas de "texto".

1. Copie a expressão para a saída "Texto fundido" e feche a janela do avaliador de expressão.
1. Selecione habilidade #1 no **Gráfico de Habilidades**.
1. Selecione o separador **Definições de Habilidade** no painel de detalhes de habilidades certos.
1. Abra o avaliador de expressão **</>** para a entrada "texto".
1. Cole a nova expressão na caixa. Clique **em Avaliar**.
1. Deve ser apresentada a entrada correta com o texto adicionado. Clique **em Aplicar** para atualizar as Definições de Habilidade.
1. Clique em **Guardar** no painel de detalhes de habilidades certos.
1. Clique em **Executar** no menu da janela das sessões. Isto irá dar início a mais uma execução do skillset usando o documento.

Depois que o indexante terminou de funcionar, os erros ainda estão lá. Volte para a perícia #1 e investigue. A entrada para a habilidade foi corrigida para "merged_content" a partir de "conteúdo". Quais são os resultados para estas entidades na competência?

1. Selecione o **separador AI Enriquecimentos.**
1. Selecione **Skill Graph** e clique em #1 de habilidade.
1. Navegue nas **Definições de Habilidade** para encontrar "saídas".
1. Abra o Avaliador de Expressão **</>** para a entidade "organizações".

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Produção para entidades de organizações":::

Avaliar o resultado da expressão dá o resultado correto. A habilidade está a trabalhar para identificar o valor correto para a entidade, "organizações". No entanto, o mapeamento de saída no caminho da entidade ainda está a lançar um erro. Ao comparar a trajetória de saída na habilidade com a trajetória de saída no erro, a habilidade que está a ser a parentalidade das saídas, organizações e localizações sob o nó /documento/conteúdo. Enquanto o mapeamento do campo de saída espera que os resultados sejam parentados no nó /documento/merged_content. No passo anterior, a entrada passou de '/documento/conteúdo' para '/documento/merged_content'. O contexto nas definições de habilidades tem de ser alterado de modo a garantir que a saída é gerada com o contexto certo.

1. Selecione o **separador AI Enriquecimentos.**
1. Selecione **Skill Graph** e clique em #1 de habilidade.
1. Navegue nas **Definições de Habilidade** para encontrar "contexto".
1. Clique duas vezes na definição para "contexto" e edite-o para ler "/document/merged_content".
1. Clique em **Guardar** no painel de detalhes de habilidades certos.
1. Clique em **Executar** no menu da janela das sessões. Isto irá dar início a mais uma execução do skillset usando o documento.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correção de contexto na definição de habilidade":::

Todos os erros foram resolvidos.

## <a name="commit-changes-to-the-skillset"></a>Comprometer alterações no skillset

Quando a sessão de depuração foi iniciada, o serviço de pesquisa criou uma cópia do skillset. Isto foi feito para proteger o skillset original no seu serviço de pesquisa. Agora que terminou de depurar o seu skillset, as correções podem ser comprometidas (substituir o skillset original). 

Em alternativa, se não estiver pronto para cometer alterações, pode guardar a sessão de depurar e reabri-la mais tarde.

1. Clique em **Comprometer alterações** no menu principal de sessões de Debug.
1. Clique **em OK** para confirmar que deseja atualizar o seu skillset.
1. Feche a sessão de Depurrão e selecione o **separador Indexadores.**
1. Abra o seu "clínico-idxr".
1. Clique **em Reiniciar**.
1. Clique em **Run** (Executar). Clique **em OK** para confirmar.

Quando o indexante terminar de funcionar, deve haver uma marca de verificação verde e a palavra Sucesso ao lado do carimbo de tempo para a última execução no separador **histórico de execução.** Para garantir a aplicação das alterações:

1. Na página 'Vista Geral' de pesquisa, selecione o **separador 'Índice'.**
1. Abra o índice de "ensaios clínicos" e no separador Explorador de Pesquisa, introduza esta cadeia de consulta: `$select=metadata_storage_path, organizations, locations&$count=true` para devolver campos para documentos específicos (identificados pelo campo `metadata_storage_path` único).
1. Clique em **Procurar**.

Os resultados devem mostrar que as organizações e os locais estão agora povoados com os valores esperados.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Este tutorial abordou vários aspetos da definição e processamento de skillset. Para saber mais sobre conceitos e fluxos de trabalho, consulte os seguintes artigos:

+ [Como mapear campos de saída de skillset para campos em um índice de pesquisa](cognitive-search-output-field-mapping.md)

+ [Skillsets em Pesquisa Cognitiva Azure](cognitive-search-working-with-skillsets.md)

+ [Como configurar caching para enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)