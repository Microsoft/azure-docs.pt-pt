---
title: Configurar a sua experiência de pesquisa personalizada do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Descreve como criar sites e serviços de pesquisa vertical
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 83ae5f923130ecb46b7b94cd8112ee45ae13e3f4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236254"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configurar a sua experiência de pesquisa personalizada do Bing

Uma instância de pesquisa personalizada permite-lhe personalizar a experiência de pesquisa para incluir conteúdo apenas a partir de sites que se preocupam com os seus utilizadores. Em vez de efetuar uma pesquisa de toda a web, Bing, pesquisa apenas os setores da web que lhe interessam. Para criar a sua vista personalizada da Web, utilize a Pesquisa Personalizada do portal [Bing](https://customsearch.ai).

O portal permite-lhe criar uma instância de pesquisa que especifica os setores da web: domínios, subpáginas e páginas da Web, que pretende que o Bing para pesquisar, e aqueles que não quê-lo para pesquisar. O portal também pode sugerir conteúdos que pretende incluir.

Quando definir as partes da web, utilize o seguinte:

| Nome do setor | Descrição                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domínio     | Um setor de domínio inclui todo o conteúdo encontrado dentro de um domínio de internet. Por exemplo, `www.microsoft.com`. A omissão `www.` faz com que o Bing para pesquisar também subdomínios do domínio. Por exemplo, se especificar `microsoft.com`, o Bing devolve também os resultados da `support.microsoft.com` ou `technet.microsoft.com`. |
| Subpáginas    | Um setor de subpáginas inclui todo o conteúdo encontrado na subpáginas e caminhos abaixo dele. Pode especificar um máximo de dois subpáginas no caminho. Por exemplo, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Página Web    | Um setor de página da Web pode incluir apenas essa página da Web numa pesquisa personalizada. Opcionalmente, pode especificar se pretende incluir subpáginas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Todos os domínios, subpáginas e páginas da Web que especificar tem de ser indexados pela Bing e públicas. Se tiver um site público que pretende incluir na pesquisa e, ainda não indexou Bing-lo, consulte o Bing [documentação webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) para obter detalhes sobre como obter o Bing para o indexar. Além disso, consulte a documentação de webmaster para obter detalhes sobre como obter o Bing para atualizar o seu site rastreada se o índice está desatualizado.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Adicionar partes da web à sua instância de pesquisa personalizada

Quando cria a instância de pesquisa personalizada, pode especificar os setores da web: domínios, subpáginas e páginas da Web, que deseja ter incluído ou bloqueado dos resultados de pesquisa. 

Se souber os setores de que pretende incluir na sua instância de pesquisa personalizada, adicioná-los à sua instância **Active Directory** lista. 

Se não tiver a certeza de que setores para incluir, pode enviar consultas de pesquisa para o Bing na **pré-visualização** painel e selecione os setores de que pretende. Para tal: 

1. Selecione "Bing" na lista pendente, no painel de pré-visualização e introduza uma consulta de pesquisa

2. Clique em **Add site** junto ao resultado que pretende incluir. Em seguida, clique em OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalizar sua experiência de pesquisa com o Active Directory e apresenta uma lista de bloqueado 

Pode aceder a lista de setores de Active Directory e bloqueadas ao clicar no **Active Directory** e **bloqueado** separadores na sua instância de pesquisa personalizada. Setores adicionados à lista de Active Directory serão incluídos em sua pesquisa personalizada. Setores bloqueados não ser pesquisadas e não será apresentado nos resultados da pesquisa.

Para especificar os setores da web que pretende que o Bing para pesquisar, clique a **Active Directory** separador e adicione um ou mais URLs. Para editar ou eliminar os URLs, utilize as opções no **controles** coluna. 

Ao adicionar URLs para os **Active Directory** pode adicionar URLs únicas ou várias URLs ao mesmo tempo, carregar um ficheiro de texto com o ícone de carregamento de lista.

![O separador Pesquisa personalizada do Bing ativo](media/file-upload-icon.png)

Para carregar um ficheiro, crie um arquivo de texto e especifique um único domínio, subpáginas ou página Web por linha. O ficheiro será rejeitado se ele não está formatado corretamente.

> [!NOTE]
> * Só pode carregar um ficheiro para o **Active Directory** lista. Não é possível usá-lo para adicionar setores para o **bloqueado** lista.  
> * Se o **bloqueado** lista contém um domínio, subpáginas ou página Web que especificou no ficheiro de carregamento, será removida do **bloqueado** listar e adicionado ao **Active** lista .
> * Entradas duplicadas no seu ficheiro de carregamento serão ignoradas pela pesquisa personalizada do Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Obtenha sugestões de site para a sua experiência de pesquisa

Depois de Adicionar WebSlices para o **Active Directory** listar, a pesquisa personalizada do Bing portal irá gerar sugestões de site e subpáginas na parte inferior do separador. Estes são os setores que pensa de pesquisa personalizada do Bing que pode querer incluir. Clique em **atualizar** para obter sugestões atualizados depois de atualizar as definições da sua instância de pesquisa personalizada. Esta secção só é visível se sugestões estão disponíveis.

## <a name="search-for-images-and-videos"></a>Pesquisa de imagens e vídeos

Pode pesquisar por imagens e vídeos da mesma forma para o conteúdo da web, utilizando o [API de pesquisa de imagens de personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) ou o [API de pesquisa de vídeos de personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Pode exibir esses resultados com o [hospedado da interface do Usuário](hosted-ui.md), ou as APIs. 

Essas APIs são semelhantes às não personalizado [pesquisa de imagens Bing](../Bing-Image-Search/overview.md) e [pesquisa de vídeos do Bing](../Bing-Video-Search/search-the-web.md) APIs, mas pesquisar toda a web e não requerem o `customConfig` parâmetro de consulta. Consulte estes conjuntos de documentação para obter mais informações sobre como trabalhar com imagens e vídeos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testar a sua instância de pesquisa com o painel de visualização

Pode testar a sua instância de pesquisa ao utilizar o painel de visualização no lado direito do portal para enviar consultas de pesquisa e ver os resultados. 

1. Abaixo da caixa de pesquisa, selecione **minha instância**. É possível comparar os resultados de sua experiência de pesquisa para o Bing, selecionando **Bing**. 
2. Selecione um filtro de pesquisa segura e que mercado para pesquisa (consulte [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Introduza uma consulta e prima enter ou clique no ícone de pesquisa para ver os resultados da configuração atual. Pode alterar seu tipo de pesquisa é executar clicando **Web**, **imagem**, ou **vídeo** para obter resultados correspondentes. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Ajustar a classificação de resultados de pesquisa específica

O portal permite-lhe ajustar a classificação de pesquisa de conteúdo a partir de domínios específicos, subpáginas e páginas Web. Depois de enviar uma consulta de pesquisa no painel de pré-visualização, cada resultado de pesquisa contém uma lista de ajustes, que pode fazer para o mesmo:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bloquear      | Move o domínio, subpáginas ou página Web para a lista de bloqueado. Bing irá excluir conteúdo o site selecionado de aparecer nos resultados da pesquisa.                    |
| Aumentar      | Aumenta a conteúdo a partir do domínio ou subpáginas superior nos resultados da pesquisa.                                                                                        |
| Despromover     | Despromove das conteúdo a partir do domínio ou subpáginas inferior nos resultados da pesquisa. Selecione se pretende despromover o conteúdo do domínio ou subpage a que pertence a página Web. |
| Afixar ao início | Move o domínio, subpáginas ou página Web para o **Pinned** lista. Isso força a página Web a aparecer como o resultado da pesquisa superior para uma determinada consulta de pesquisa.                   |

Ajustar a classificação não está disponível para imagem ou vídeo pesquisas.

### <a name="boosting-and-demoting-search-results"></a>Aumentar e despromover os resultados da pesquisa

Pode super aumentar, aumentar, ou despromover qualquer domínio ou subpage no **Active Directory** lista. Por predefinição, todos os setores são adicionados sem ajustes de classificação. Setores da web são muito elevada ou Boosted estão ordenadas superior nos resultados da pesquisa (com a classificação de super boost superior boost). Itens que estão a ser despromovidos são uma classificação mais baixa nos resultados da pesquisa.

Super pode aumentar, aumente ou despromover itens utilizando o **classificação ajustar** controles no **Active Directory** listar, ou ao utilizar o aumento e despromover os controles no painel de pré-visualização. O serviço adiciona o setor à sua lista de Active Directory e ajusta a classificação em conformidade.

> [!NOTE] 
> Aumentar e despromover os domínios e subpáginas são um dos muitos métodos que pesquisa personalizada do Bing utiliza para determinar a ordem dos resultados da pesquisa. Devido a outros fatores influência de classificação de conteúdo web diferente, os efeitos de ajustar a classificação podem variar. Utilize o painel de visualização para testar os efeitos de ajustar a classificação de seus resultados de pesquisa. 

Super aumentar, aumentar e despromover não estão disponíveis para a imagem e pesquisas de vídeo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Setores de PIN na parte superior dos resultados da pesquisa

O portal também permite-lhe afixar os URLs para a parte superior dos resultados da pesquisa para os termos de pesquisa específica, com o **Pinned** separador. Introduza um URL e uma consulta para especificar a página Web que será apresentado como o melhor resultado. Tenha em atenção que pode afixar um máximo de uma página Web por consulta de pesquisa e páginas da Web indexadas apenas serão apresentadas nas pesquisas. Afixar os resultados não está disponível para imagem ou vídeo pesquisas.

Pode afixar uma página Web à parte superior de duas formas:

* Na **Pinned** separador, introduza o URL da página Web para afixar na parte superior e a sua consulta correspondente.

* Na **pré-visualização** painel, introduza uma consulta de pesquisa e clique em Procurar. Encontrar a página Web que pretende afixar na sua consulta e clique em **Pin ao início**. a página da Web e a consulta serão adicionadas para o **Pinned** lista.

### <a name="specify-the-pins-match-condition"></a>Especifique a condição de correspondência do pin

Por predefinição, páginas Web apenas é afixada a parte superior dos resultados da pesquisa quando a cadeia de consulta de um utilizador corresponde exatamente a um listados na **Pinned** lista. Pode alterar este comportamento ao especificar uma das seguintes condições de correspondência:

> [!NOTE]
> Todas as comparações entre a consulta de pesquisa do usuário e consulta de pesquisa do pin diferenciam maiúsculas de minúsculas.

| Value | Descrição                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Começa por | O pin é uma correspondência, se a cadeia de consulta do usuário começa com a cadeia de consulta do pin |
| Termina com   | O pin é uma correspondência, se a cadeia de consulta do utilizador termina com a cadeia de consulta o pin.  |
| contém    | O pin é uma correspondência, se a cadeia de consulta do utilizador contém a cadeia de consulta o pin.   |


Para alterar a condição de correspondência do pin, clique no ícone de edição do pin. Na **condição de correspondência de consulta** coluna, clique na lista pendente e selecione a nova condição a utilizar. Em seguida, clique em salvar ícone para guardar a alteração.

### <a name="change-the-order-of-your-pinned-sites"></a>Alterar a ordem dos seus sites fixos

Para alterar a ordem dos seus pins, pode arrastar e soltar o-los ou editar o seu número de ordem ao clicar no ícone de "Editar" no **controles** coluna da **Pinned** lista.

Se vários pins satisfazem uma condição de correspondência, a pesquisa personalizada do Bing irá utilizar a mais alta na lista.

## <a name="view-statistics"></a>Ver estatísticas

Se subscrever a pesquisa personalizada ao nível apropriado (consulte a [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), uma **estatísticas** separador é adicionado às instâncias de produção. O separador de estatísticas mostra detalhes sobre como os pontos de extremidade de pesquisa personalizada são usados, incluindo o volume de chamadas, principais consultas, distribuição geográfica, códigos de resposta e pesquisa segura. Pode filtrar detalhes usando os controles fornecidos.

## <a name="usage-guidelines"></a>Diretrizes de utilização

- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que pode fazer **Active Directory** e **bloqueado** setores está limitado a 400.
- Adicionar um setor para os separadores do Active Directory ou bloqueado é contabilizado como uma classificação ajuste.
- Aumentar e despromover contagem como dois ajustes de classificação.
- Para cada instância de pesquisa personalizada, o número máximo de pins que pode fazer é limitado a 200.

## <a name="next-steps"></a>Passos Seguintes

- [Chamar a sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a sua experiência de IU alojada](./hosted-ui.md)
- [Utilizar marcadores decorativos para realçar o texto](./hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
