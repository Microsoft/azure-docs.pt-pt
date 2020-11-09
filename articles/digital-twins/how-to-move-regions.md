---
title: Mover instância para uma região de Azure diferente
titleSuffix: Azure Digital Twins
description: Veja como mover uma instância Azure Digital Twins de uma região de Azure para outra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 6393b0b8d794345fded95718a2581ae9b929ad49
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381155"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Mover uma instância Azure Digital Twins para uma região de Azure diferente

Se precisar de mover o seu exemplo Azure Digital Twins de uma região para outra, o processo atual é recriar os seus recursos na nova região e, em seguida, eliminar os recursos originais. No final deste processo, estará a trabalhar com uma nova instância Azure Digital Twins que é idêntica à primeira, com exceção da localização atualizada.

Este artigo fornece orientações sobre como fazer um movimento completo e copiar tudo o que você precisa para fazer a nova instância corresponder ao original.

Este processo inclui os seguintes passos:

1. Prepare: Descarregue os seus modelos originais, gémeos e gráfico.
1. Move: Criar um novo exemplo de Gémeos Digitais Azure numa nova região.
1. Move: Repovoar a nova instância Azure Digital Twins.
    - Carremese os modelos originais, gémeos e gráfico.
    - Re-crie pontos finais e rotas.
    - Religar recursos conectados.
1. Limpar os recursos de origem: Eliminar a instância original.

## <a name="prerequisites"></a>Pré-requisitos

Antes de tentar recriar o seu exemplo Azure Digital Twins, reenvia os componentes da sua instância original para ter uma ideia clara de todas as peças que terão de ser recriadas.

Eis algumas das questões a considerar:

* Quais são as *modelos enviadas* para o meu caso? Quantos são?
* Quais são os *gémeos* no meu caso? Quantos são?
* Qual é a forma geral do *gráfico* no meu caso? Quantas relações existem?
* Que *pontos finais* tenho no meu caso?
* Que *rotas* tenho no meu caso? Têm filtros?
* Onde é que o meu caso *se liga a outros serviços da Azure?* Alguns pontos comuns de integração incluem:

    - Azure Event Grid, Azure Event Hubs ou Azure Service Bus
    - Funções do Azure
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure
* Que outras *aplicações pessoais ou da empresa* tenho que se ligam ao meu caso?

Pode recolher esta informação utilizando o [portal Azure](https://portal.azure.com) [, APIs e SDKs,](how-to-use-apis-sdks.md) [Azure Digital Twins CLI](how-to-use-cli.md)ou a amostra [do Azure Digital Twins (ADT).](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

## <a name="prepare"></a>Preparação

Nesta secção, você se preparará para recriar o seu exemplo, descarregando os seus modelos originais, gémeos e gráficos da sua instância original. Este artigo utiliza a amostra [do ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) para esta tarefa.

>[!NOTE]
>Pode já ter ficheiros que contenham os modelos ou o gráfico no seu caso. Em caso afirmativo, não precisa de descarregar tudo de novo — apenas as peças que está a perder ou coisas que podem ter mudado desde que fez o upload original destes ficheiros. Por exemplo, pode ter gémeos que foram atualizados com novos dados.

### <a name="limitations-of-adt-explorer"></a>Limitações do Explorador ADT

A [amostra do ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) é uma amostra de aplicação do cliente que suporta uma representação visual do seu gráfico e proporciona interação visual com o seu exemplo. Este artigo mostra como usá-lo para descarregar, e mais tarde recarregar, os seus modelos, gémeos e gráficos.

Esta amostra não é uma ferramenta completa. Não foi testado pelo stress e não foi construído para lidar com gráficos de grande dimensão. Por conseguinte, tenha em mente as seguintes limitações de amostra fora da caixa:

* A amostra só foi testada em tamanhos de gráfico até 1.000 nós e 2.000 relacionamentos.
* A amostra não suporta a repetição no caso de falhas intermitentes.
* A amostra não notificará necessariamente o utilizador se os dados enviados estiverem incompletos.
* A amostra não lida com erros resultantes de gráficos muito grandes que excedem os recursos disponíveis, como a memória.

Se a amostra não for capaz de lidar com o tamanho do seu gráfico, pode exportar e importar o gráfico utilizando outras ferramentas de desenvolvimento da Azure Digital Twins:

* [Comandos CLI de Gémeos Digitais Azure](how-to-use-cli.md)
* [APIs e SDKs de gémeos digitais Azure](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Configurar a aplicação ADT Explorer

Para prosseguir com o ADT Explorer, primeiro descarregue o código de aplicação da amostra e confita-o para funcionar na sua máquina.

Para obter a amostra, consulte o [ADT Explorer.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) Selecione o botão **Download ZIP** para transferir um ficheiro .zip deste código de amostra para a sua máquina à medida **queAzure_Digital_Twins__ADT__explorer.zip**. Desaperte o ficheiro.

Em seguida, configurar e configurar permissões para o ADT Explorer. Siga as instruções na secção [Configurar Gémeos Digitais Azure e ADT Explorer](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) do arranque rápido das Gémeas Digitais Azure. Esta secção acompanha-o através dos seguintes passos:

1. Crie uma instância Azure Digital Twins. Podes saltar esta parte porque já tens um caso.
1. Crie credenciais locais do Azure para fornecer acesso ao seu caso.
1. Executar o ADT Explorer e configurá-lo para ligar ao seu caso. Vais usar o nome de *anfitrião* da tua instância original dos Gémeos Digitais Azure que estás a mover.

Agora deve ter a aplicação de amostra do ADT Explorer a funcionar num browser na sua máquina. A amostra deve ser ligada à sua instância original da Azure Digital Twins.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/explorer-blank.png":::

Para verificar a ligação, selecione o botão **''Fazer',** para executar a consulta predefinitiva que exibe todos os gémeos e relações no gráfico na caixa **GRAPH EXPLORER.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Um botão de leitura Executar Consulta no canto superior direito da janela é realçado." lightbox="media/how-to-move-regions/run-query.png":::

Pode deixar o ADT Explorer em funcionamento porque voltará a usá-lo mais tarde neste artigo para recarregar estes itens para o seu novo caso na região alvo.

### <a name="download-models-twins-and-graph"></a>Baixar modelos, gémeos e gráfico

Em seguida, baixe os modelos, gémeos e gráfico na sua solução para a sua máquina.

Para descarregar todos estes itens de uma só vez, certifique-se primeiro de que o gráfico completo aparece na caixa **GRAPH VIEW.** Se o gráfico completo ainda não aparecer, repercuta a consulta predefinida `SELECT * FROM digitaltwins` na caixa **QUERY EXPLORER.**
 
Em seguida, selecione o ícone **'Gráfico de Exportação'** na caixa **GRAPH VIEW.**

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Na caixa Graph View, destaca-se um ícone. Mostra uma seta apontando para baixo de uma nuvem." lightbox="media/how-to-move-regions/export-graph.png":::

Esta ação permite um link **de descarregamento** na caixa **GRAPH VIEW.** Selecione-o para descarregar uma representação baseada em JSON do resultado da consulta, que inclui os seus modelos, gémeos e relacionamentos. Esta ação deve descarregar um ficheiro .json para a sua máquina.

>[!NOTE]
>Se o ficheiro descarregado parecer ter uma extensão de ficheiro diferente, tente editar a extensão diretamente e mudá-la para .json.

## <a name="move"></a>Mover

Em seguida, você completará o "movimento" do seu caso criando um novo caso na região alvo. Em seguida, irá povoá-lo com os dados e componentes da sua instância original.

### <a name="create-a-new-instance"></a>Criar um novo exemplo

Primeiro, crie uma nova instância de Azure Digital Twins na sua região alvo. Siga os passos em [Como-a: Configurar uma instância e autenticação](how-to-set-up-instance-portal.md). Tenha estes ponteiros em mente:

* Pode manter o mesmo nome para o novo caso *se* estiver num grupo de recursos diferente. Se precisar de utilizar o mesmo grupo de recursos que contém a sua instância original, a sua nova instância precisará do seu próprio nome distinto.
* Entre na nova região alvo quando solicitado para um local.

Depois deste passo estar completo, vai precisar do nome de anfitrião da sua nova instância para continuar a conussá-lo com os seus dados. Se não tiver dado nota do nome do anfitrião durante a configuração, siga [estas instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) para obtê-lo agora do portal Azure.

### <a name="repopulate-the-old-instance"></a>Repovoar o antigo exemplo

Em seguida, vai configurar o novo caso para que seja uma cópia do original.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Faça upload dos modelos, gémeos e gráficos originais usando o ADT Explorer

Nesta secção, pode recarregar os seus modelos, gémeos e gráfico para a nova instância. Se não tiver modelos, gémeos ou gráficos no seu caso original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte](#re-create-endpoints-and-routes).

Caso contrário, volte à janela do navegador executando o ADT Explorer e siga estes passos.

##### <a name="connect-to-the-new-instance"></a>Ligue-se à nova instância

Atualmente, o ADT Explorer está ligado à sua instância original do Azure Digital Twins. Mude a ligação para apontar para a sua nova instância selecionando o botão **Sign In** no canto superior direito da janela.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="ADT Explorer destacando o ícone Sign In no canto superior direito da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/how-to-move-regions/sign-in.png":::

Substitua o **URL ADT** para refletir a sua nova instância. Altere este valor de modo a ler *https://{novo nome de anfitrião de instância}*.

Selecione **Connect** (Ligar). Pode ser-lhe pedido que volte a assinar com as suas credenciais Azure ou conceder este consentimento de pedido para o seu exemplo.

##### <a name="upload-models-twins-and-graph"></a>Upload modelos, gémeos e gráfico

Em seguida, faça o upload dos componentes da solução que descarregou anteriormente para o seu novo exemplo.

Para fazer o upload dos seus modelos, gémeos e gráfico, selecione o ícone **Import Graph** na caixa **GRAPH VIEW.** Esta opção carrega os três componentes de uma só vez. Até carrega modelos que não estão a ser usados no gráfico.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Na caixa Graph View, destaca-se um ícone. Mostra uma seta apontando para uma nuvem." lightbox="media/how-to-move-regions/import-graph.png":::

Na caixa de seletor de ficheiros, vá ao seu gráfico descarregado. Selecione o ficheiro **gráfico .json** e selecione **Abrir**.

Após alguns segundos, o ADT Explorer abre uma vista **importância** que mostra uma pré-visualização do gráfico a ser carregado.

Para confirmar o upload do gráfico, selecione o ícone **Guardar** no canto superior direito da caixa **GRAPH VIEW.**

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Realçando o ícone 'Guardar' no painel de pré-visualização do gráfico." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O ADT Explorer agora envia os seus modelos e gráficos (incluindo os gémeos e relacionamentos) para o seu novo exemplo de Azure Digital Twins. Devias ver uma mensagem de sucesso a notar quantos modelos, gémeos e relacionamentos foram carregados.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Caixa de diálogo indicando o sucesso da importação de gráficos. Diz: &quot;Importar bem sucedido. 2 modelos importados. 4 gémeos importados. 2 relações importadas.»" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Para verificar se tudo foi carregado com sucesso, selecione o botão **''Fazer',** na caixa **GRAPH EXPLORER,** para executar a consulta predefinida que exibe todos os gémeos e relacionamentos no gráfico. Esta ação também atualiza a lista de modelos na caixa **MODEL VIEW.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Destaque em torno do botão 'Executar consulta' no canto superior direito da janela." lightbox="media/how-to-move-regions/run-query.png":::

Deve ver o seu gráfico com todos os seus gémeos e relacionamentos exibidos na caixa **GRAPH EXPLORER.** Também deverá ver os seus modelos listados na caixa **MODEL VIEW.**

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Uma vista do ADT Explorer mostrando dois modelos destacados na caixa Model View e um gráfico destacado na caixa do Graph Explorer." lightbox="media/how-to-move-regions/post-upload.png":::

Estas opiniões confirmam que os seus modelos, gémeos e gráficos foram re-carregados para a nova instância na região alvo.

#### <a name="re-create-endpoints-and-routes"></a>Recriar pontos finais e rotas

Se tiver pontos finais ou rotas no seu caso original, terá de os recriar no seu novo caso. Se não tiver pontos finais ou rotas no seu caso original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte](#relink-connected-resources).

Caso contrário, siga os passos em [Como-a:Gerir pontos finais e rotas](how-to-manage-routes-portal.md) utilizando a nova instância. Tenha estes ponteiros em mente:

* *Não* precisa de recriar o recurso Event Grid, Event Hubs ou Service Bus que está a utilizar para o ponto final. Para obter mais informações, consulte a secção "Pré-requisitos" nas instruções do ponto final. Só precisas de recriar o ponto final na instância Azure Digital Twins.
* Pode reutilizar o ponto final e encaminhar nomes porque são traçados para um caso diferente.
* Lembre-se de adicionar os filtros necessários às rotas que criar.

#### <a name="relink-connected-resources"></a>Religar recursos conectados

Se tiver outras aplicações ou recursos Azure que estejam ligados à sua instância original do Azure Digital Twins, terá de editar a ligação para que elas cheguem à sua nova instância. Estes recursos podem incluir outros serviços Azure ou aplicações pessoais ou da empresa que você criou para trabalhar com a Azure Digital Twins.

Se não tiver outros recursos ligados à sua instância original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte.](#verify)

Caso contrário, considere os recursos conectados no seu cenário. Não é necessário eliminar e recriar quaisquer recursos ligados. Em vez disso, basta editar os pontos onde se ligam a uma instância Azure Digital Twins através do seu nome de anfitrião. Em seguida, atualiza estes pontos para usar o nome de anfitrião da nova instância em vez do original.

Os recursos exatos que precisa de editar dependem do seu cenário, mas aqui estão alguns pontos de integração comuns:

* Funções Azure. Se tiver uma função Azure cujo código inclui o nome de anfitrião da instância original, deverá atualizar este valor para o nome de anfitrião da nova instância e republicar a função.
* Grelha de eventos, Centros de Eventos ou Autocarro de Serviço.
* Aplicativos lógicos.
* Insights da Série De Tempo.
* Mapas Azure.
* Serviço de Provisionamento de Dispositivos IoT Hub.
* Aplicativos pessoais ou empresariais fora do Azure, como a app do cliente criada em [Tutorial: Código uma aplicação](tutorial-code.md)para clientes, que se conectam ao caso e ligam para APIs de Gémeos Digitais Azure.
* Os registos de aplicações AD *AZure não* precisam de ser recriados. Se estiver a utilizar um [registo de aplicações](how-to-create-app-registration.md) para ligar às APIs das Gémeas Digitais Azure, pode reutilizar o mesmo registo de aplicações com a sua nova instância.

Depois de terminar este passo, o seu novo caso na região alvo deve ser uma cópia do exemplo original.

## <a name="verify"></a>Verificação

Para verificar se a sua nova instância foi configurada corretamente, utilize as seguintes ferramentas:

* [Portal do Azure](https://portal.azure.com). O portal é bom para verificar se o seu novo exemplo existe e está na região alvo correta. Também é bom para verificar pontos finais e rotas e ligações a outros serviços Azure.
* [Comandos CLI de Gémeos Digitais Azure](how-to-use-cli.md). Estes comandos são bons para verificar se o seu novo exemplo existe e está na região alvo correta. Também podem ser usados para verificar dados de instância.
* [ADT Explorer.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) O ADT Explorer é bom para verificar dados de exemplos como modelos, gémeos e gráficos.
* [APIs e SDKs de gémeos digitais Azure](how-to-use-apis-sdks.md). Estes recursos são bons para verificar dados de exemplos como modelos, gémeos e gráficos. Também são bons para verificar pontos finais e rotas.

Também pode tentar executar quaisquer aplicações personalizadas ou fluxos de ponta a ponta que tenha em execução com a sua instância original para ajudá-lo a verificar se estão a trabalhar corretamente com a nova instância.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Agora que o seu novo caso é configurado na região alvo com uma cópia dos dados e ligações da instância original, pode apagar a instância original.

Pode utilizar o [portal Azure](https://portal.azure.com), o [Azure CLI,](how-to-use-cli.md)ou as [APIs](how-to-use-apis-sdks.md#overview-control-plane-apis)do avião de controlo .

Para eliminar o caso utilizando o portal Azure, [abra o portal](https://portal.azure.com) numa janela do navegador e vá à sua instância original Azure Digital Twins procurando o nome na barra de pesquisa do portal.

Selecione o botão **Eliminar** e siga as instruções para terminar a eliminação.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Veja os detalhes da instância Azure Digital Twins no portal Azure, no separador Visão Geral. Destaca-se o botão Eliminar.":::