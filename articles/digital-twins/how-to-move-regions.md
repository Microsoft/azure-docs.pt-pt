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
ms.openlocfilehash: 3c7f9ed9558adc9d129d1df767a05aff1fa4c66c
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047391"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Mover uma instância Azure Digital Twins para uma região de Azure diferente

Se precisa de mover o seu exemplo Azure Digital Twins de uma região para outra, o processo atual é **recriar os seus recursos na nova região**, e depois apagar os recursos originais. No final deste processo, estará a trabalhar com uma nova instância Azure Digital Twins que é idêntica à primeira, com exceção da localização atualizada.

Este artigo fornece orientações sobre como fazer um movimento completo, copiando tudo o que você precisa para fazer a nova instância corresponder ao original.

Este processo inclui os seguintes passos:
1. Prepare: Descarregue os seus modelos originais, gémeos e gráfico.
2. Move: Criar uma nova instância Azure Digital Twins, numa nova região.
3. Move: Repovoar a nova instância Azure Digital Twins.
    - Carremeses originais, gémeos e gráfico.
    - Recrie pontos finais e rotas.
    - Religar recursos ligados.
4. Limpar recursos de origem: Eliminar instância original.

## <a name="prerequisites"></a>Pré-requisitos

Antes de tentar recriar a sua instância Azure Digital Twins, é uma boa ideia releminar os componentes da sua instância original e ter uma ideia clara de todas as peças que terão de ser recriadas.

Aqui estão algumas perguntas que talvez queira considerar:
* Quais são as **modelos enviadas** para o meu caso? Quantos são?
* Quais são os **gémeos** no meu caso? Quantos são?
* Qual é a forma geral do **gráfico** no meu caso? Quantas relações existem?
* Que **pontos finais** tenho no meu caso?
* Que **rotas** tenho no meu caso? Têm filtros?
* Onde é que o meu caso **se liga a outros serviços da Azure?** Alguns pontos comuns de integração incluem...
    - Grelha de eventos, Centro de Eventos ou Ônibus de Serviço
    - Funções do Azure
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Serviço de Fornecimento de Dispositivos (DPS)
* Que outras **aplicações pessoais ou da empresa** tenho que se ligam ao meu caso?

Pode recolher esta informação utilizando o [portal Azure](https://portal.azure.com) [, APIs e SDKs de Gémeos Digitais Azure,](how-to-use-apis-sdks.md) [comandos CLI de Gémeos Digitais Azure ,](how-to-use-cli.md)ou a amostra do Explorador [Azure Digital Twins (ADT).](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

## <a name="prepare"></a>Preparação

Nesta secção, você se preparará para recriar o seu **exemplo, descarregando os seus modelos originais, gémeos e gráfico** da sua instância original. Este artigo faz isto utilizando a amostra [do Azure Digital Twins (ADT) Explorer.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

>[!NOTE]
>Pode já ter ficheiros que contenham os modelos e/ou o gráfico no seu caso. Em caso afirmativo, não precisa de descarregar tudo novamente — apenas as peças que está a perder ou coisas que podem ter mudado desde que fez o upload original destes ficheiros (como gémeos que podem ter sido atualizados com novos dados).

### <a name="limitations-of-adt-explorer"></a>Limitações do Explorador ADT

A [amostra Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) é uma amostra de aplicação do cliente que suporta uma representação visual do seu gráfico e proporciona interação visual com o seu exemplo. Este artigo mostra como usá-lo para descarregar, e mais tarde re-carregar, os seus modelos, gémeos e gráficos.

Por favor, note, no entanto, que esta é uma **amostra** e não uma ferramenta completa. Não foi testado pelo stress e não foi construído para manusear gráficos de grande dimensão. Por conseguinte, tenha em mente as seguintes limitações de amostra fora da caixa:
* A amostra só foi testada em tamanhos de gráfico até 1000 nóns e relações de 2000
* A amostra não suporta a repetição no caso de eventuais falhas intermitentes
* A amostra não notificará necessariamente o utilizador se os dados enviados estiverem incompletos
* A amostra não lida com erros resultantes de gráficos muito grandes que excedem os recursos disponíveis, como a memória

Se a amostra não for capaz de lidar com o tamanho do seu gráfico, pode exportar e importar o gráfico utilizando outras ferramentas de desenvolvimento da Azure Digital Twins:
* [Comandos CLI de Gémeos Digitais Azure](how-to-use-cli.md)
* [APIs e SDKs de gémeos digitais Azure](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Configurar a aplicação ADT Explorer

Para prosseguir com o ADT Explorer, primeiro descarregue o código de aplicação da amostra e confita-o para funcionar na sua máquina. 

Navegue para a amostra aqui: [Azure Digital Twins (ADT) explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Aperda no botão *Baixar ZIP* para descarregar um *. Ficheiro ZIP* deste código de amostra para a sua máquina _** comoADT_Explorer.zip**_. Desaperte o ficheiro.

Em seguida, crie permissões para que o ADT Explorer seja executado na sua máquina. Para isso, siga os passos na secção [*de permissões set ADT Explorer*](quickstart-adt-explorer.md#set-adt-explorer-permissions) do arranque rápido das Gémeas Digitais Azure.

Por fim, corra e configuure o ADT Explorer para ligar à sua instância original do Azure Digital Twins. Siga os passos na [*secção Run e configuure*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) a secção ADT Explorer do arranque rápido.

Agora deve ter a aplicação de amostra do ADT Explorer a funcionar num browser na sua máquina. A amostra deve ser ligada à sua instância original da Azure Digital Twins.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/explorer-blank.png":::

Para verificar a ligação, pode carregar no botão *'Executar'* para executar a consulta predefinida que exibe todos os gémeos e relacionamentos no gráfico na caixa *GRAPH EXPLORER.*

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/run-query.png":::

Pode deixar o ADT Explorer em funcionamento, uma vez que irá usá-lo novamente mais tarde neste artigo para re-carregar estes itens para o seu novo caso na região alvo.

### <a name="download-models-twins-and-graph"></a>Baixar modelos, gémeos e gráfico

Em seguida, baixe os modelos, gémeos e gráfico na sua solução para a sua máquina.

Para descarregar tudo de uma só vez, certifique-se primeiro de que o gráfico completo está a aparecer na caixa *GRAPH VIEW* (pode fazê-lo repetindo a consulta padrão da `SELECT * FROM digitaltwins` caixa *QUERY EXPLORER).*
 
Em seguida, bata no ícone *do gráfico de exportação* na caixa *GRAPH VIEW.*

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/export-graph.png":::

Isto permitirá um link *de descarregamento* no *GRAPH VIEW*. Selecione-o para descarregar uma representação baseada em JSON do resultado da consulta, incluindo os seus modelos, gémeos e relacionamentos. Isto deve descarregar um ficheiro *.json* para a sua máquina.

>[!NOTE]
>Se o ficheiro descarregado parecer ter uma extensão de ficheiro diferente, tente editar a extensão diretamente e mudá-la para *.json*.

## <a name="move"></a>Mover

Em seguida, irá completar o "movimento" do seu caso criando um novo caso na região alvo, e povoando-o com os dados e componentes da sua instância original.

### <a name="create-a-new-instance"></a>Criar um novo exemplo

Em primeiro lugar, **crie uma nova instância de Azure Digital Twins na sua região alvo.** Para isso, siga os passos em [*Como-a: Configurar um caso e autenticação,*](how-to-set-up-instance-portal.md)tendo em conta estes ponteiros:
* Pode manter o mesmo nome para a nova instância **se** estiver num grupo de recursos diferente. Se precisar de utilizar o mesmo grupo de recursos que contém a sua instância original, então a sua nova instância precisará do seu próprio nome distinto.
* Insira a nova região alvo quando solicitado para um local.
* Não **é necessário** recriar o registo da aplicação. A sua nova instância pode reutilizar o mesmo registo de aplicações que já tem.
    - Se utilizar o artigo de configuração [scripted,](how-to-set-up-instance-scripted.md) pode reentrar nos detalhes do seu registo de aplicações existente em vez de introduzir um novo nome quando solicitado.
    - Se utilizar o [portal](how-to-set-up-instance-portal.md) manual ou os artigos de configuração [do CLI,](how-to-set-up-instance-cli.md) pode parar depois da *instância Criar o Azure Digital Twins* e configurar as *permissões de acesso ao utilizador.* Não há necessidade de continuar através *da Configuração de permissões de acesso para aplicações de clientes.*

Uma vez concluído este facto, necessitará do **nome de anfitrião** da sua nova instância para continuar a conushá-lo com os seus dados. Se não tiver dado nota disto durante a configuração, pode seguir [estas instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) para obtê-lo agora a partir do portal Azure.

### <a name="repopulate-old-instance"></a>Repovoar caso antigo

Em seguida, irá configurar o novo caso para que seja uma cópia do original.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Faça upload de modelos originais, gémeos e gráfico usando ODT Explorer

Nesta secção, pode re-carregar os seus modelos, gémeos e gráfico para a nova instância. Se não tiver modelos, gémeos ou gráficos no seu caso original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte](#recreate-endpoints-and-routes).

Caso contrário, para prosseguir, volte à janela do navegador que executa o **ADT Explorer** e siga os passos abaixo.

##### <a name="connect-to-the-new-instance"></a>Ligue-se à nova instância

Atualmente, o ADT Explorer está ligado à sua instância original do Azure Digital Twins. Mude a ligação para apontar para a sua nova instância premindo o botão *Sinal na* parte superior da janela. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/sign-in.png":::

Uma vez que está a reutilizar o registo da aplicação, só precisa de substituir o *URL ADT.* Altere este valor para o que lê *https://{new instance hostname}*.

Hit *Connect*. Pode ser-lhe pedido que faça login novamente com as suas credenciais Azure e/ou conceda este consentimento de pedido para o seu exemplo.

##### <a name="upload-models-twins-and-graph"></a>Upload modelos, gémeos e gráfico

Em seguida, faça o upload dos componentes da solução que descarregou anteriormente para o seu novo exemplo.

Para fazer o upload dos seus **modelos, gémeos e gráfico,** acerte o ícone *Import Graph* na caixa *GRAPH VIEW.* Esta opção irá carregar os três componentes de uma só vez (mesmo modelos que não estão atualmente a ser utilizados no gráfico).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/import-graph.png":::

Na caixa de seletor de ficheiros, navegue para o seu gráfico descarregado. Selecione o ficheiro *gráfico .json* e acerte *em Open*.

Após alguns segundos, o ADT Explorer abrirá uma vista *de Importação* exibindo uma pré-visualização do gráfico que vai ser carregado.

Para confirmar o upload do gráfico, bata no ícone *Guardar* no canto superior direito do *GRAPH VIEW*:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O ADT Explorer irá agora enviar os seus modelos e gráficos (incluindo os gémeos e relacionamentos) para o seu novo exemplo de Azure Digital Twins. Deve ver uma mensagem de sucesso a notar quantos modelos, gémeos e relacionamentos foram carregados:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Para verificar se tudo foi carregado com sucesso, carrememe o botão *'Executar consulta'* na caixa *GRAPH EXPLORER* para executar a consulta predefinida que exibe todos os gémeos e relacionamentos no gráfico. Isto irá também refrescar a lista de modelos no *MODEL VIEW*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/run-query.png":::

Deve ver o seu gráfico com todos os seus gémeos e relacionamentos exibidos na caixa *GRAPH EXPLORER.* Também deverá ver os seus modelos listados na caixa *MODEL VIEW.*

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/how-to-move-regions/post-upload.png":::

Isto confirma que os seus modelos, gémeos e gráficos foram re-carregados para a nova instância na região alvo.

#### <a name="recreate-endpoints-and-routes"></a>Recriar pontos finais e rotas

Se tiver **pontos finais e/ou rotas** no seu caso original, terá de recriá-los no seu novo caso. Se não tiver pontos finais ou rotas no seu caso original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte](#re-link-connected-resources).

Caso contrário, proceda, siga os passos em [*Como-a: Gerir pontos finais e rotas*](how-to-manage-routes-portal.md) utilizando a nova instância, tendo em conta estes ponteiros: 
* Não **precisa de** recriar o recurso Event Grid, Event Hub ou Service Bus que está a utilizar para o ponto final *(secções pré-requisitos* nas instruções do ponto final). Só precisas de recriar o ponto final na instância Azure Digital Twins.
* Pode reutilizar **os nomes**de ponta final e rota, uma vez que são telescópios para um caso diferente.
* Lembre-se de adicionar **os filtros necessários** às rotas que criar.

#### <a name="re-link-connected-resources"></a>Religar recursos conectados

Se tiver outras aplicações ou recursos Azure que estejam ligados à sua instância original do Azure Digital Twins, terá de editar a ligação para que elas cheguem à sua nova instância. Isto pode incluir outros serviços Azure, ou aplicações pessoais ou da empresa que você configura para trabalhar com Azure Digital Twins.

Se não tiver outros recursos ligados à sua instância original ou não quiser movê-los para a nova instância, pode saltar para a [secção seguinte.](#verify)

Caso contrário, para prosseguir, considere os recursos conectados no seu cenário. Não é necessário eliminar e recriar quaisquer recursos ligados; em vez disso, basta editar os pontos em que se ligam a um exemplo de Azure Digital Twins através do seu **nome de anfitrião**, e atualizá-lo para usar o nome de anfitrião do novo exemplo em vez do original.

Os recursos exatos que precisa de editar dependem do seu cenário, mas aqui estão alguns pontos de integração comuns:
* Funções Azure. Se tiver uma função Azure cujo código inclui o nome de anfitrião da instância original, deverá atualizar este valor para o nome de anfitrião da nova instância e voltar a publicar a função.
* Grelha de eventos, centros de eventos ou ônibus de serviço
* Logic Apps
* Time Series Insights
* Azure Maps
* Serviço de Fornecimento de Dispositivos (DPS)
* Aplicativos pessoais ou empresariais fora do Azure, como a **app do cliente** criada em [*Tutorial: Código uma aplicação para clientes,*](tutorial-code.md)que se conectam ao caso e ligam a APIs de Gémeos Digitais Azure

Após completar este passo, a sua nova instância na região alvo deve ser uma cópia do caso original.

## <a name="verify"></a>Verificação

Para verificar se a sua nova instância foi configurada corretamente, pode utilizar as seguintes ferramentas:
* O [**portal Azure**](https://portal.azure.com) (bom para verificar se a sua nova instância existe e está na região alvo correta; também é bom para verificar pontos finais e rotas, e ligações a outros serviços Azure)
* Os [ **comandos CLI** dos Gémeos Digitais Azure (bom](how-to-use-cli.md) para verificar se a sua nova instância existe e está na região alvo correta; também podem ser utilizados para verificar dados de instâncias)
* [**ADT Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (bom para verificar dados de instâncias como modelos, gémeos e gráficos)
* As [APIs e SDKs das Gémeas Digitais Azure](how-to-use-apis-sdks.md) (boas para verificar dados de instâncias como modelos, gémeos e gráficos; também bons para verificar pontos finais e rotas)

Também pode tentar executar quaisquer aplicações personalizadas ou fluxos de ponta a ponta que tenha em execução com a sua instância original, para ajudá-lo a verificar se estão a trabalhar corretamente com a nova instância.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Agora que o seu novo caso é configurado na região alvo com uma cópia dos dados e ligações da instância original, pode **apagar a instância original**.

Pode fazê-lo no [portal Azure,](https://portal.azure.com)com o [CLI,](how-to-use-cli.md)ou com as APIs do [avião de controlo.](how-to-use-apis-sdks.md#overview-control-plane-apis)

Para eliminar o caso utilizando o portal Azure, [abra o portal](https://portal.azure.com) numa janela do navegador e navegue para a sua instância original Azure Digital Twins procurando o seu nome na barra de pesquisa do portal.

Premir o botão *Eliminar* e siga as instruções para terminar a eliminação.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã.":::