---
title: Configurar sua experiência de Pesquisa Personalizada do Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Descreve como criar sites e serviços de pesquisa vertical
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 1827bfdbebaf1ffa17c7c631a94aa8fc6471d13b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854090"
---
# <a name="configure-your-bing-custom-search-experience"></a>Configurar sua experiência de Pesquisa Personalizada do Bing

Uma instância de pesquisa personalizada permite que você personalize a experiência de pesquisa para incluir conteúdo somente de sites dos quais seus usuários se preocupam. Em vez de executar uma pesquisa em toda a Web, o Bing pesquisa apenas as fatias da Web que lhe interessam. Para criar a sua vista personalizada da Web, utilize a Pesquisa Personalizada do portal [Bing](https://customsearch.ai).

O portal permite que você crie uma instância de pesquisa que especifica as fatias da Web: domínios, subpáginas e páginas da Web, que você deseja que o Bing pesquise e aqueles que você não deseja que ele pesquise. O portal também pode sugerir conteúdo que você talvez queira incluir.

Use o seguinte ao definir as fatias da Web:

| Nome da fatia | Descrição                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Uma fatia de domínio inclui todo o conteúdo encontrado em um domínio de Internet. Por exemplo, `www.microsoft.com`. Omitir `www.` faz com que o Bing pesquise também os subdomínios do domínio. Por exemplo, se você especificar `microsoft.com`, o Bing também retorna resultados `support.microsoft.com` de `technet.microsoft.com`ou. |
| Subpáginas    | Uma fatia de subpágina inclui todo o conteúdo encontrado na subpágina e caminhos abaixo dela. Você pode especificar um máximo de duas subpáginas no caminho. Por exemplo, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Página Web    | Uma fatia da página da Web pode incluir somente essa página da Web em uma pesquisa personalizada. Opcionalmente, você pode especificar se deseja incluir subpáginas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Todos os domínios, subpáginas e páginas da Web que você especificar devem ser públicos e indexados pelo Bing. Se você possuir um site público que deseja incluir na pesquisa e o Bing não o tiver indexado, consulte a documentação do Bing [webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) para obter detalhes sobre como obter o Bing para indexá-lo. Além disso, consulte a documentação do webmaster para obter detalhes sobre como obter o Bing para atualizar seu site rastreado se o índice estiver desatualizado.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Adicionar fatias da Web à sua instância de pesquisa personalizada

Quando você cria sua instância de pesquisa personalizada, você pode especificar as fatias da Web: domínios, subpáginas e páginas da webpage, que você deseja que sejam incluídas ou bloqueadas nos resultados da pesquisa. 

Se você souber as fatias que deseja incluir em sua instância de pesquisa personalizada, adicione-as à lista **ativa** da instância. 

Se você não tiver certeza de quais fatias devem ser incluídas, poderá enviar consultas de pesquisa para o Bing no painel de **Visualização** e selecionar as fatias desejadas. Para efetuar este procedimento: 

1. Selecione "Bing" na lista suspensa no painel de visualização e insira uma consulta de pesquisa

2. Clique em **Adicionar site** ao lado do resultado que você deseja incluir. Em seguida, clique em OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Personalize sua experiência de pesquisa com listas ativas e bloqueadas 

Você pode acessar a lista de fatias ativas e bloqueadas clicando nas guias **ativa** e **bloqueada** em sua instância de pesquisa personalizada. As fatias adicionadas à lista ativa serão incluídas na sua pesquisa personalizada. As fatias bloqueadas não serão pesquisadas e não aparecerão nos resultados da pesquisa.

Para especificar as fatias da Web que você deseja que o Bing pesquise, clique na guia **ativa** e adicione uma ou mais URLs. Para editar ou excluir URLs, use as opções na coluna **controles** . 

Ao adicionar URLs à lista **ativa** , você pode adicionar URLs únicas ou várias URLs ao mesmo tempo carregando um arquivo de texto usando o ícone carregar.

![A guia ativa Pesquisa Personalizada do Bing](media/file-upload-icon.png)

Para carregar um arquivo, crie um arquivo de texto e especifique um único domínio, subpágina ou página da Web por linha. O arquivo será rejeitado se não estiver formatado corretamente.

> [!NOTE]
> * Você só pode carregar um arquivo na lista **ativa** . Você não pode usá-lo para adicionar fatias à lista **bloqueada** .  
> * Se a lista de bloqueios contiver um domínio, uma subpágina ou uma página da Web que você especificou no arquivo de upload, ela será removida da lista **bloqueada** e adicionada à lista **ativa** .
> * As entradas duplicadas no arquivo de upload serão ignoradas por Pesquisa Personalizada do Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Obtenha sugestões de site para sua experiência de pesquisa

Depois de adicionar Web Slices à lista **ativa** , o portal de pesquisa personalizada do Bing gerará sugestões de site e subpágina na parte inferior da guia. Essas são fatias que Pesquisa Personalizada do Bing pensa que você talvez queira incluir. Clique em **Atualizar** para obter sugestões atualizadas depois de atualizar as configurações da instância de pesquisa personalizada. Esta seção só será visível se houver sugestões disponíveis.

## <a name="search-for-images-and-videos"></a>Pesquisar imagens e vídeos

Você pode pesquisar imagens e vídeos de forma semelhante ao conteúdo da Web usando a [API de pesquisa de imagem personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) ou a api de [pesquisa de vídeo personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Você pode exibir esses resultados com a [interface do usuário hospedada](hosted-ui.md)ou as APIs. 

Essas APIs são semelhantes às APIs não personalizadas de [pesquisa de imagem do Bing](../Bing-Image-Search/overview.md) e [pesquisa de vídeo do Bing](../Bing-Video-Search/search-the-web.md) , mas pesquisam toda a Web e não exigem o parâmetro de `customConfig` consulta. Consulte estes conjuntos de documentação para obter mais informações sobre como trabalhar com imagens e vídeos. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testar sua instância de pesquisa com o painel de visualização

Você pode testar sua instância de pesquisa usando o painel de visualização no lado direito do portal para enviar consultas de pesquisa e exibir os resultados. 

1. Abaixo da caixa de pesquisa, selecione **minha instância**. Você pode comparar os resultados de sua experiência de pesquisa com o Bing, selecionando **Bing**. 
2. Selecione um filtro de pesquisa seguro e qual mercado Pesquisar (consulte [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Insira uma consulta e pressione Enter ou clique no ícone de pesquisa para exibir os resultados da configuração atual. Você pode alterar o tipo de pesquisa que você executa clicando em **Web**, **imagem**ou **vídeo** para obter os resultados correspondentes. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Ajustar a classificação de resultados de pesquisa específicos

O portal permite que você ajuste a classificação de pesquisa de conteúdo de domínios, subpáginas e páginas da Web específicos. Depois de enviar uma consulta de pesquisa no painel de visualização, cada resultado da pesquisa contém uma lista de ajustes que você pode fazer para ele:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bloquear      | Move o domínio, a subpágina ou a página da Web para a lista de bloqueios. O Bing impedirá que o conteúdo do site selecionado apareça nos resultados da pesquisa.                    |
| Aumentar      | Aumenta o conteúdo do domínio ou da subpágina para ser maior nos resultados da pesquisa.                                                                                        |
| Rebaixar     | Rebaixa o conteúdo do domínio ou da subpágina mais abaixo nos resultados da pesquisa. Você seleciona se deseja rebaixar o conteúdo do domínio ou da subpágina à qual a página da Web pertence. |
| Fixar no início | Move o domínio, a subpágina ou a página da Web para a lista fixada. Isso força a exibição da página da Web como o principal resultado da pesquisa de uma determinada consulta de pesquisa.                   |

O ajuste de classificação não está disponível para pesquisas de imagem ou de vídeo.

### <a name="boosting-and-demoting-search-results"></a>Aumentando e rebaixando os resultados da pesquisa

Você pode melhorar, aumentar ou rebaixar qualquer domínio ou subpágina na lista **ativa** . Por padrão, todas as fatias são adicionadas sem ajustes de classificação. As fatias da Web que são super aumentadas ou aumentadas são classificadas em posição superior nos resultados da pesquisa (com uma classificação super Boost maior do que o aumento). Os itens rebaixados são classificados como mais baixos nos resultados da pesquisa.

Você pode melhorar, aumentar ou rebaixar itens usando os controles de **ajuste de classificação** na lista **ativa** ou usando os controles Boost e rebaixar no painel de visualização. O serviço adiciona a fatia à sua lista ativa e ajusta a classificação de acordo.

> [!NOTE] 
> Aumentar e rebaixar domínios e subpáginas é um dos muitos métodos que Pesquisa Personalizada do Bing usa para determinar a ordem dos resultados da pesquisa. Devido a outros fatores que influenciam a classificação de conteúdo da Web diferente, os efeitos de ajustar a classificação podem variar. Use o painel de visualização para testar os efeitos de ajustar a classificação dos resultados da pesquisa. 

Super Boost, aumento e rebaixamento não estão disponíveis para as pesquisas de imagem e vídeo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Fixar fatias na parte superior dos resultados da pesquisa

O portal também permite que você fixe URLs na parte superior dos resultados da pesquisa para termos de pesquisa específicos, usando a guia **fixado** . Insira uma URL e uma consulta para especificar a página da Web que será exibida como o resultado superior. Observe que você pode fixar um máximo de uma página da Web por consulta de pesquisa e somente as páginas da Web indexadas serão exibidas em pesquisas. Os resultados de fixação não estão disponíveis para pesquisas de imagem ou de vídeo.

Você pode fixar uma página da Web na parte superior de duas maneiras:

* Na guia **fixado** , insira a URL da página da Web a ser fixada na parte superior e sua consulta correspondente.

* No painel de **Visualização** , insira uma consulta de pesquisa e clique em Pesquisar. Localize a página da Web que você deseja fixar na consulta e clique em **fixar na parte superior**. a página da Web e a consulta serão adicionadas à lista fixada.

### <a name="specify-the-pins-match-condition"></a>Especificar a condição de correspondência do PIN

Por padrão, as páginas da Web são fixadas apenas na parte superior dos resultados da pesquisa quando a cadeia de caracteres de consulta de um usuário corresponde exatamente a uma listada na lista fixada. Você pode alterar esse comportamento especificando uma das seguintes condições de correspondência:

> [!NOTE]
> Todas as comparações entre a consulta de pesquisa do usuário e a consulta de pesquisa do PIN não diferenciam maiúsculas de minúsculas.

| Value | Descrição                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Começa com | O PIN é uma correspondência se a cadeia de caracteres de consulta do usuário começa com a cadeia de caracteres de consulta do PIN |
| termina com   | O PIN é uma correspondência se a cadeia de caracteres de consulta do usuário termina com a cadeia de caracteres de consulta do PIN.  |
| Contém    | O PIN será uma correspondência se a cadeia de caracteres de consulta do usuário contiver a cadeia de caracteres de consulta do PIN.   |


Para alterar a condição de correspondência do PIN, clique no ícone de edição do PIN. Na coluna **condição de correspondência de consulta** , clique na lista suspensa e selecione a nova condição a ser usada. Em seguida, clique no ícone salvar para salvar a alteração.

### <a name="change-the-order-of-your-pinned-sites"></a>Alterar a ordem dos sites fixos

Para alterar a ordem dos Pins, você pode arrastá-los e soltá-los ou editar o número do pedido clicando no ícone "Editar" na coluna **controles** da lista fixada .

Se vários Pins atenderem a uma condição de correspondência, Pesquisa Personalizada do Bing usarão o mais alto na lista.

## <a name="view-statistics"></a>Ver estatísticas

Se você assinou a pesquisa personalizada no nível apropriado (consulte as páginas de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), uma guia **estatísticas** é adicionada às suas instâncias de produção. A guia Estatísticas mostra detalhes sobre como os pontos de extremidade de pesquisa personalizados são usados, incluindo o volume de chamada, as principais consultas, a distribuição geográfica, os códigos de resposta e a pesquisa segura. Você pode filtrar detalhes usando os controles fornecidos.

## <a name="usage-guidelines"></a>Diretrizes de uso

- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que você pode fazer em fatias ativas e **bloqueadas** é limitado a 400.
- A adição de uma fatia às guias ativa ou bloqueada conta como um ajuste de classificação.
- Aumentando e rebaixando a contagem como dois ajustes de classificação.
- Para cada instância de pesquisa personalizada, o número máximo de Pins que você pode fazer é limitado a 200.

## <a name="next-steps"></a>Passos Seguintes

- [Chamar a sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a sua experiência de IU alojada](./hosted-ui.md)
- [Utilizar marcadores decorativos para realçar o texto](../bing-web-search/hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
