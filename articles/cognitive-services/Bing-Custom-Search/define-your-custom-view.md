---
title: Configure a sua experiência de pesquisa personalizada bing / Microsoft Docs
titleSuffix: Azure Cognitive Services
description: O portal permite criar uma instância de pesquisa que especifica as fatias da web; domínios, subpáginas e páginas web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220217"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configure a sua experiência de pesquisa personalizada bing

Uma instância de Pesquisa Personalizada permite-lhe adaptar a experiência de pesquisa para incluir conteúdo apenas a partir de websites com os quais os seus utilizadores se preocupam. Em vez de realizar uma pesquisa em toda a web, Bing pesquisa apenas as fatias da web que lhe interessam. Para criar a sua vista personalizada da Web, utilize a Pesquisa Personalizada do portal [Bing](https://customsearch.ai).

O portal permite criar uma instância de pesquisa que especifica as fatias da web: domínios, subpáginas e páginas web, que quer que bing procure, e aqueles que não quer que ele procure. O portal também pode sugerir conteúdo que você possa querer incluir.

Utilize o seguinte ao definir as suas fatias da web:

| Nome da fatia | Descrição                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Uma fatia de domínio inclui todos os conteúdos encontrados dentro de um domínio de internet. Por exemplo, `www.microsoft.com`. Omitindo `www.` faz com que Bing também procure os subdomínios do domínio. Por exemplo, se `microsoft.com`especificar, bing `support.microsoft.com` também `technet.microsoft.com`devolve resultados de ou . |
| Subpágina    | Uma fatia de subpágina inclui todos os conteúdos encontrados na subpágina e caminhos abaixo dela. Pode especificar um máximo de duas subpáginas no caminho. Por exemplo, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Página Web    | Uma fatia de página web pode incluir apenas essa página web numa pesquisa personalizada. Pode especificar opcionalmente se deve incluir subpáginas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Todos os domínios, subpáginas e páginas web que especifica devem ser públicos e indexados por Bing. Se possui um site público que pretende incluir na pesquisa, e Bing não o indexou, consulte a documentação do [Webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) bing para obter detalhes sobre fazer bing indexá-lo. Além disso, consulte a documentação do webmaster para obter detalhes sobre como fazer bing atualizar o seu site rastejado se o índice estiver desatualizado.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Adicione fatias da web à sua instância de pesquisa personalizada

Quando criar a sua instância de pesquisa personalizada, pode especificar as fatias da web: domínios, subpáginas e páginas web, que pretende ter incluído ou bloqueado dos resultados da sua pesquisa. 

Se você sabe as fatias que deseja incluir na sua instância de pesquisa personalizada, adicione-as à lista **Ativa** da sua instância. 

Se não tiver a certeza de quais fatias incluir, pode enviar consultas de pesquisa para Bing no painel **de pré-visualização** e selecionar as fatias que deseja. Para efetuar este procedimento: 

1. selecione "Bing" da lista de dropdown no painel de pré-visualização, e insira uma consulta de pesquisa

2. Clique em **Adicionar site** ao lado do resultado que pretende incluir. Em seguida, clique em OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalize a sua experiência de pesquisa com listas Ativas e Bloqueadas 

Pode aceder à lista de fatias ativas e bloqueadas clicando nos separadores **Ative** e **Blocked** na sua instância de pesquisa personalizada. As fatias adicionadas à lista ativa serão incluídas na sua pesquisa personalizada. As fatias bloqueadas não serão revistadas e não aparecerão nos resultados da sua pesquisa.

Para especificar as fatias da web, deseja que o Bing procure, clique no separador **Ative** e adicione um ou mais URLs. Para editar ou eliminar URLs, utilize as opções sob a coluna **Controlos.** 

Ao adicionar URLs à lista **Ativa,** pode adicionar URLs únicos ou vários URLs ao mesmo tempo, carregando um ficheiro de texto utilizando o ícone de upload.

![O separador ativo de pesquisa personalizada bing](media/file-upload-icon.png)

Para fazer o upload de um ficheiro, crie um ficheiro de texto e especifique um único domínio, subpágina ou página web por linha. O seu ficheiro será rejeitado se não for formatado corretamente.

> [!NOTE]
> * Só pode enviar um ficheiro para a lista **Ativa.** Não pode usá-lo para adicionar fatias à lista **bloqueada.**  
> * Se a lista **bloqueada** contiver um domínio, subpágina ou página web que especificou no ficheiro de upload, será removida da lista **bloqueada** e adicionada à lista **Ativa.**
> * As entradas duplicadas no seu ficheiro de upload serão ignoradas pela Bing Custom Search. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Obtenha sugestões do site para a sua experiência de pesquisa

Depois de adicionar fatias web à lista **Ativa,** o portal Bing Custom Search gerará sugestões de websites e subpáginas na parte inferior do separador. Estas são fatias que bing Custom Search acha que você pode querer incluir. Clique em **Refresh** para obter sugestões atualizadas depois de atualizar as definições da sua instância de pesquisa personalizada. Esta secção só é visível se houver sugestões disponíveis.

## <a name="search-for-images-and-videos"></a>Pesquisar imagens e vídeos

Pode pesquisar imagens e vídeos similarmente ao conteúdo web utilizando a [API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) de Pesquisa de Imagem Personalizada Bing ou a [API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)de Pesquisa de Vídeo Personalizada bing . Pode apresentar estes resultados com os [UI hospedeiros,](hosted-ui.md)ou as APIs. 

Estas APIs são semelhantes às APIs de Pesquisa de [Imagem bing](../Bing-Image-Search/overview.md) não personalizadas e Bing [Video Search,](../Bing-Video-Search/search-the-web.md) mas procurem toda a web, e não requerem o `customConfig` parâmetro de consulta. Consulte estes conjuntos de documentação para obter mais informações sobre o trabalho com imagens e vídeos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Teste a sua instância de pesquisa com o painel de pré-visualização

Pode testar a sua instância de pesquisa utilizando o painel de pré-visualização do lado direito do portal para apresentar consultas de pesquisa e visualizar os resultados. 

1. Abaixo da caixa de pesquisa, selecione **My Instance**. Pode comparar os resultados da sua experiência de pesquisa com bing, selecionando **Bing**. 
2. Selecione um filtro de pesquisa seguro e qual o mercado a procurar (ver [Parâmetros de Consulta).](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
3. Introduza uma consulta e prima introduza ou clique no ícone de pesquisa para visualizar os resultados da configuração atual. Pode alterar o seu tipo de pesquisa que executa clicando na **Web,** **Na imagem**ou **no vídeo** para obter resultados correspondentes. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Ajuste a classificação de resultados específicos de pesquisa

O portal permite-lhe ajustar o ranking de pesquisa de conteúdos a partir de domínios específicos, subpáginas e páginas web. Depois de enviar uma consulta de pesquisa no painel de pré-visualização, cada resultado de pesquisa contém uma lista de ajustes que pode fazer para o mesmo:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bloquear      | Move o domínio, subpágina ou página web para a lista bloqueada. Bing excluirá o conteúdo do site selecionado de aparecer nos resultados da pesquisa.                    |
| Impulsionar      | Aumenta o conteúdo do domínio ou subpágina para ser mais elevado nos resultados da pesquisa.                                                                                        |
| Despromover     | Despromova o conteúdo do domínio ou subpágina mais baixo nos resultados da pesquisa. Selecione se despromover o conteúdo do domínio ou subpágina a que a página web pertence. |
| Pin para cima | Move o domínio, subpágina ou página web para a lista **pinned.** Isto força a página web a aparecer como o resultado de pesquisa superior para uma determinada consulta de pesquisa.                   |

O ajuste do ranking não está disponível para pesquisas de imagem ou vídeo.

### <a name="boosting-and-demoting-search-results"></a>Impulsionar e despromover resultados de pesquisa

Pode aumentar, impulsionar ou despromover qualquer domínio ou subpágina na lista **Ative.** Por padrão, todas as fatias são adicionadas sem ajustes de classificação. As fatias da web que são Super impulsionadas ou impulsionadas são classificadas mais altas nos resultados de pesquisa (com um ranking de super-boost superior ao boost). Os itens que são despromovidos são classificados mais baixos nos resultados da pesquisa.

Pode aumentar, impulsionar ou despromover itens utilizando os controlos 'Ajuste de **Classificação'** na lista **Ativa,** ou utilizando os controlos Boost e Demote no painel de pré-visualização. O serviço adiciona a fatia à sua lista Ative e ajusta o ranking em conformidade.

> [!NOTE] 
> Aumentar e despromover domínios e subpáginas é um dos muitos métodos que bing Custom Search usa para determinar a ordem dos resultados da pesquisa. Devido a outros fatores que influenciam o ranking de diferentes conteúdos web, os efeitos do ajuste da classificação podem variar. Utilize o painel de pré-visualização para testar os efeitos do ajuste da classificação dos resultados da sua pesquisa. 

Super boost, boost e desmote não estão disponíveis para as pesquisas de imagem e vídeo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Fatias de pinpara o topo dos resultados da pesquisa

O portal também permite fixar URLs no topo dos resultados de pesquisa para termos de pesquisa específicos, utilizando o separador **Pinned.** Introduza um URL e consulte especificando a página web que aparecerá como o resultado superior. Note que pode fixar um máximo de uma página web por consulta de pesquisa, e apenas páginas web indexadas serão exibidas em pesquisas. Os resultados de fixação não estão disponíveis para pesquisas de imagem ou vídeo.

Pode fixar uma página web no topo de duas maneiras:

* No separador **Pinned,** introduza o URL da página web para fixar na parte superior e a sua consulta correspondente.

* No painel **'Pré-visualização',** introduza uma consulta de pesquisa e clique em procurar. Encontre a página web que pretende fixar para a sua consulta e clique **em Pin para cima**. a página web e consulta serão adicionadas à lista **pinned.**

### <a name="specify-the-pins-match-condition"></a>Especifique a condição de correspondência do pino

Por padrão, as páginas web só são fixadas no topo dos resultados de pesquisa quando a cadeia de consulta de um utilizador corresponde exatamente a uma listada na lista **de Pinned.** Pode alterar este comportamento especificando uma das seguintes condições de jogo:

> [!NOTE]
> Todas as comparações entre a consulta de pesquisa do utilizador e a consulta de pesquisa do pino são caso saneados.

| Valor | Descrição                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Starts with | O pino é compatível se a corda de consulta do utilizador começar com a corda de consulta do pino |
| Termina com   | O pino é compatível se a corda de consulta do utilizador terminar com a corda de consulta do pino.  |
| Contains    | O pino é compatível se a corda de consulta do utilizador contiver a corda de consulta do pino.   |


Para alterar a condição de correspondência do pino, clique no ícone de edição do pino. Na coluna condição de **jogo da Consulta,** clique na lista de dropdown e selecione a nova condição para usar. Em seguida, clique no ícone de guardar para salvar a alteração.

### <a name="change-the-order-of-your-pinned-sites"></a>Altere a ordem dos seus sites presos

Para alterar a ordem dos seus pinos, pode arrastar e largar os mesmos, ou editar o seu número de encomenda clicando no ícone "editar" na Coluna de **Controloda** lista **pinned.**

Se vários pinos satisfazerem uma condição de correspondência, o Bing Custom Search utilizará o mais alto da lista.

## <a name="view-statistics"></a>Ver estatísticas

Se subscreveu a Custom Search ao nível apropriado (ver as páginas de [preços),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)é adicionado um separador **Estatísticas** às suas instâncias de produção. O separador de estatísticas mostra detalhes sobre como os seus pontos finais de Pesquisa Personalizada são usados, incluindo volume de chamadas, consultas de topo, distribuição geográfica, códigos de resposta e pesquisa segura. Pode filtrar os detalhes utilizando os controlos fornecidos.

## <a name="usage-guidelines"></a>Diretrizes de utilização

- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que pode fazer às fatias **Ativa** e **Bloqueada** está limitado a 400.
- Adicionar uma fatia aos separadores Ativos ou Bloqueados conta como um ajuste de classificação.
- Aumentar e despromover a contagem como dois ajustes de classificação.
- Para cada instância de pesquisa personalizada, o número máximo de pinos que pode fazer está limitado a 200.

## <a name="next-steps"></a>Passos seguintes

- [Chamar a sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a sua experiência de IU alojada](./hosted-ui.md)
- [Utilizar marcadores decorativos para realçar o texto](../bing-web-search/hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
