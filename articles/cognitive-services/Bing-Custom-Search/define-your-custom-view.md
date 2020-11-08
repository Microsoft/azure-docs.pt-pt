---
title: Configure a sua experiência de Pesquisa Personalizada Bing / Microsoft Docs
titleSuffix: Azure Cognitive Services
description: O portal permite criar um caso de pesquisa que especifica as fatias da web; domínios, sub-páginas e páginas web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 52819caeb833eb475c8ea2c476ef5ba27681a50c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367561"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configure a sua experiência de Pesquisa Personalizada Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Uma instância de Pesquisa Personalizada permite-lhe adaptar a experiência de pesquisa para incluir conteúdo apenas a partir de websites que os seus utilizadores se preocupam. Em vez de realizar uma pesquisa em toda a web, Bing procura apenas as fatias da web que lhe interessam. Para criar a sua vista personalizada da Web, utilize a Pesquisa Personalizada do portal [Bing](https://customsearch.ai).

O portal permite criar um caso de pesquisa que especifica as fatias da web: domínios, sub-páginas e páginas web, que pretende que bing procure, e aqueles que não quer que ele procure. O portal também pode sugerir conteúdo que possa querer incluir.

Utilize o seguinte ao definir as suas fatias da web:

| Nome da fatia | Description                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domínio     | Uma fatia de domínio inclui todos os conteúdos encontrados dentro de um domínio de internet. Por exemplo, `www.microsoft.com`. Omitir `www.` faz com que Bing também procure os subdomínios do domínio. Por exemplo, se `microsoft.com` especificar, Bing também devolve resultados de `support.microsoft.com` ou `technet.microsoft.com` . |
| Sub-página    | Uma fatia de sub-página inclui todo o conteúdo encontrado na sub-página e caminhos abaixo dela. Pode especificar um máximo de duas sub-páginas no caminho. Por exemplo, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Página web    | Uma fatia de página web pode incluir apenas essa página web numa pesquisa personalizada. Pode especificar opcionalmente se deve incluir sub-páginas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Todos os domínios, sub-páginas e páginas web que especifique devem ser públicos e indexados por Bing. Se possuir um site público que pretende incluir na pesquisa, e bing não o indexou, consulte a documentação do [webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing para obter detalhes sobre como fazer bing indexá-lo. Além disso, consulte a documentação do webmaster para obter detalhes sobre como obter Bing para atualizar o seu site rastejado se o índice estiver desatualizado.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Adicione fatias da web à sua instância de pesquisa personalizada

Quando criar o seu caso de pesquisa personalizado, pode especificar as fatias da web: domínios, sub-páginas e páginas web, que pretende ter incluído ou bloqueado dos resultados da sua pesquisa. 

Se conhecer as fatias que pretende incluir no seu caso de pesquisa personalizado, adicione-as à lista **Ative** do seu caso. 

Se não tiver a certeza de quais as fatias a incluir, pode enviar consultas de pesquisa para Bing no painel **de pré-visualização** e selecionar as fatias que deseja. Para efetuar este procedimento: 

1. selecione "Bing" da lista de dropdown no painel de pré-visualização e introduza uma consulta de pesquisa

2. Clique **em Adicionar o site** ao lado do resultado que pretende incluir. Em seguida, clique em OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalize a sua experiência de pesquisa com listas Ativas e Bloqueadas 

Pode aceder à lista de fatias ativas e bloqueadas clicando nos separadores **Ative** e **Bloqueados** no seu caso de pesquisa personalizado. As fatias adicionadas à lista ativa serão incluídas na sua pesquisa personalizada. As fatias bloqueadas não serão revistadas e não aparecerão nos resultados da sua pesquisa.

Para especificar as fatias da web que pretende que bing procure, clique no separador **Ative** e adicione um ou mais URLs. Para editar ou eliminar URLs, utilize as opções por baixo da coluna **Controlos.** 

Ao adicionar URLs à lista **Ative,** pode adicionar URLs únicos ou múltiplos URLs de uma só vez, carregando um ficheiro de texto utilizando o ícone de upload.

![O separador Bing Custom Search Ative](media/file-upload-icon.png)

Para fazer o upload de um ficheiro, crie um ficheiro de texto e especifique um único domínio, sub-página ou página web por linha. O seu ficheiro será rejeitado se não for formatado corretamente.

> [!NOTE]
> * Só é possível enviar um ficheiro para a lista **Ative.** Não é possível usá-lo para adicionar fatias à lista **bloqueada.**  
> * Se a lista **bloqueada** contiver um domínio, sub-página ou página web que especificou no ficheiro de upload, será removido da lista **bloqueada** e adicionado à lista **Ative.**
> * As entradas duplicadas no seu ficheiro de upload serão ignoradas pela Bing Custom Search. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Obtenha sugestões de site para a sua experiência de pesquisa

Depois de adicionar fatias web à lista **Ative,** o portal Bing Custom Search gerará sugestões de website e sub-página na parte inferior do separador. Estas são fatias que bing Custom Search acha que você pode querer incluir. Clique **em Refresh** para obter sugestões atualizadas depois de atualizar as definições da sua instância de pesquisa personalizada. Esta secção só é visível se houver sugestões.

## <a name="search-for-images-and-videos"></a>Pesquisa de imagens e vídeos

Pode pesquisar imagens e vídeos de forma semelhante ao conteúdo web utilizando a [API de Pesquisa de Imagem Personalizada Bing](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) ou a [API de Pesquisa de Vídeo Personalizada Bing.](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) Pode apresentar estes resultados com a [UI hospedada,](hosted-ui.md)ou com as APIs. 

Estas APIs são semelhantes às APIs de pesquisa de [imagem de Bing](../Bing-Image-Search/overview.md) e [Bing Video Search,](../bing-video-search/overview.md) mas procuram em toda a web e não requerem o `customConfig` parâmetro de consulta. Consulte estes conjuntos de documentação para obter mais informações sobre o trabalho com imagens e vídeos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Teste o seu caso de pesquisa com o painel de pré-visualização

Pode testar a sua instância de pesquisa utilizando o painel de pré-visualização no lado direito do portal para submeter consultas de pesquisa e ver os resultados. 

1. Abaixo da caixa de pesquisa, selecione **My Instance**. Pode comparar os resultados da sua experiência de pesquisa com bing, selecionando **Bing**. 
2. Selecione um filtro de pesquisa seguro e qual o mercado a procurar (ver [Parâmetros de Consulta).](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
3. Introduza uma consulta e prima insira ou clique no ícone de pesquisa para ver os resultados da configuração atual. Pode alterar o seu tipo de pesquisa que executa clicando **em Web,** **Image** ou **Video** para obter resultados correspondentes. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Ajuste o nível de resultados específicos da pesquisa

O portal permite-lhe ajustar o ranking de pesquisa de conteúdos a partir de domínios específicos, sub-páginas e páginas web. Depois de enviar uma consulta de pesquisa no painel de pré-visualização, cada resultado de pesquisa contém uma lista de ajustes que pode fazer para ele:  

| Ajustamento | Description |
|------------|-------------|
| Bloquear      | Move o domínio, sub-página ou página web para a lista bloqueada. Bing excluirá o conteúdo do site selecionado de aparecer nos resultados da pesquisa.                    |
| Impulsionar      | Aumenta o conteúdo do domínio ou sub-página para ser mais elevado nos resultados da pesquisa.                                                                                        |
| Despromover     | Despromote o conteúdo do domínio ou sub-página mais baixo nos resultados da pesquisa. Selecione se despromia o conteúdo do domínio ou sub-página a que a página web pertence. |
| Pino para cima | Move o domínio, sub-página ou página web para a lista **Pinned.** Isto força a página web a aparecer como o resultado de pesquisa principal para uma consulta de pesquisa dada.                   |

O ranking de ajuste não está disponível para pesquisas de imagem ou vídeo.

### <a name="boosting-and-demoting-search-results"></a>Impulsionar e despromundo os resultados da pesquisa

Pode aumentar, aumentar ou despromugar qualquer domínio ou sub-página na lista **Ative.** Por predefinição, todas as fatias são adicionadas sem ajustes de classificação. As fatias da web que são super impulsionadas ou aumentadas são classificadas mais altas nos resultados de pesquisa (com o ranking de super boost mais alto que o boost). Os itens que são despromovidos são classificados mais baixos nos resultados da pesquisa.

Pode aumentar, aumentar ou despromu-lo utilizando os controlos de Ajuste de **Classificação** na lista **Ative,** ou utilizando os controlos Boost and Demote no painel de pré-visualização. O serviço adiciona a fatia à sua lista Ative e ajusta o ranking em conformidade.

> [!NOTE] 
> Impulsionar e despromondo domínios e subpages é um dos muitos métodos que a Bing Custom Search usa para determinar a ordem dos resultados da pesquisa. Devido a outros fatores que influenciam o ranking de diferentes conteúdos web, os efeitos do ajuste do rank podem variar. Utilize o painel de pré-visualização para testar os efeitos de ajustar o nível dos resultados da sua pesquisa. 

Super boost, boost e demote não estão disponíveis para as pesquisas de imagem e vídeo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Pin fatias para o topo dos resultados da pesquisa

O portal também permite fixar URLs no topo dos resultados de pesquisa para termos de pesquisa específicos, utilizando o **separador Pinned.** Introduza um URL e uma consulta para especificar a página web que aparecerá como o resultado superior. Note que pode fixar um máximo de uma página web por consulta de pesquisa, e apenas páginas web indexadas serão exibidas em pesquisas. Os resultados da fixação não estão disponíveis para pesquisas de imagem ou vídeo.

Pode fixar uma página web no topo de duas maneiras:

* No **separador Pinned,** introduza o URL da página web para fixar na parte superior e a sua consulta correspondente.

* No painel **de pré-visualização,** introduza uma consulta de pesquisa e clique em procurar. Encontre a página web que deseja fixar para a sua consulta e clique em **Pin para cima**. a página web e a consulta serão adicionadas à lista **Pinned.**

### <a name="specify-the-pins-match-condition"></a>Especifique a condição de correspondência do pino

Por predefinição, as páginas web são fixadas apenas ao topo dos resultados de pesquisa quando a cadeia de consulta de um utilizador corresponde exatamente a uma listada na lista **Pinned.** Pode alterar este comportamento especificando uma das seguintes condições de jogo:

> [!NOTE]
> Todas as comparações entre a consulta de pesquisa do utilizador e a consulta de pesquisa do pino são insensíveis a caso.

| Valor | Descrição                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Starts with | O pino é uma correspondência se a cadeia de consulta do utilizador começar com a cadeia de consulta do pino |
| Termina com   | O pino é uma correspondência se a corda de consulta do utilizador terminar com a corda de consulta do pino.  |
| Contains    | O pino é uma correspondência se a cadeia de consulta do utilizador contiver a corda de consulta do pino.   |


Para alterar a condição de correspondência do pino, clique no ícone de edição do pino. Na coluna **de condição de correspondência de consulta,** clique na lista de dropdown e selecione a nova condição a utilizar. Em seguida, clique no ícone de salvamento para guardar a alteração.

### <a name="change-the-order-of-your-pinned-sites"></a>Altere a ordem dos seus sites presos

Para alterar a ordem dos seus pinos, pode arrastar e largar os mesmos ou editar o número de encomenda clicando no ícone "editar" na Coluna de **Controlos** da lista **Pinned.**

Se vários pinos satisfizerem uma condição de correspondência, a Bing Custom Search utilizará a mais alta da lista.

## <a name="view-statistics"></a>Ver estatísticas

Se subscrever a Pesquisa Personalizada ao nível apropriado (ver as [páginas de preços),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)é adicionado um separador **De Estatística** às suas instâncias de produção. O separador de estatísticas mostra detalhes sobre como os seus pontos finais de pesquisa personalizada são usados, incluindo volume de chamadas, consultas de topo, distribuição geográfica, códigos de resposta e pesquisa segura. Pode filtrar detalhes utilizando os controlos fornecidos.

## <a name="usage-guidelines"></a>Diretrizes de utilização

- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que pode fazer para fatias **Ativas** e **Bloqueadas** está limitado a 400.
- A adição de uma fatia às abas Ativa ou Bloqueada conta como um ajuste de classificação.
- Aumentar e despromoção conta como dois ajustes de classificação.
- Para cada instância de pesquisa personalizada, o número máximo de pinos que pode fazer está limitado a 200.

## <a name="next-steps"></a>Passos seguintes

- [Chamar a sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a sua experiência de IU alojada](./hosted-ui.md)
- [Utilizar marcadores decorativos para realçar o texto](../bing-web-search/hit-highlighting.md)
- [Paginação de páginas Web](../bing-web-search/paging-search-results.md)