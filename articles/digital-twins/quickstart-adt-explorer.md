---
title: Quickstart - Explore um cenário de amostra
titleSuffix: Azure Digital Twins
description: Quickstart - Use a amostra Azure Digital Twins Explorer para visualizar e explorar um cenário pré-construído.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 8c6e584a4bdb1953d7ceac4e3d612b329738f646
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203985"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Quickstart - Explore uma amostra do cenário Azure Digital Twins usando o Azure Digital Twins Explorer

Com a Azure Digital Twins, pode criar e interagir com modelos ao vivo dos seus ambientes do mundo real. Primeiro, modela elementos individuais como *gémeos digitais.* Em seguida, conectá-los a um *gráfico de* conhecimento que pode responder a eventos ao vivo e ser questionado para obter informações.

Neste quickstart, você explorará um gráfico pré-construído Azure Digital Twins, com a ajuda de uma aplicação de amostra chamada [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Usa o Azure Digital Twins Explorer para:

- Faça upload de uma representação digital de um ambiente.
- Veja imagens visuais dos gémeos e gráfico que são criados para representar o ambiente em Azure Digital Twins.
- Realizar outras atividades de gestão através de uma experiência visual baseada no navegador.

O quickstart contém os seguintes passos principais:

1. Crie um exemplo de Azure Digital Twins e Azure Digital Twins Explorer.
1. Faça upload de modelos pré-construídos e dados gráficos para construir o cenário da amostra.
1. Explore o gráfico de cenário que foi criado.
1. Faça alterações no gráfico.

O gráfico de amostra com que vai trabalhar representa um edifício com dois andares e dois quartos. O gráfico será parecido com esta imagem:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vista de um gráfico feito de quatro nós circulares ligados por setas. Um círculo com a etiqueta 'Floor1' é ligado por uma seta com a etiqueta 'contém' a um círculo com a indicação 'Sala1'. Um círculo com a etiqueta 'Floor0' é ligado por uma seta com a etiqueta 'contém' a um círculo com a indicação 'Room0'. 'Floor1' e 'Floor0' não estão ligados.":::

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma subscrição Azure para completar este arranque rápido. Se ainda não tem um, [crie um de graça](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

Também vai precisar de **Node.js** na sua máquina. Para obter a versão mais recente, consulte [Node.js](https://nodejs.org/).

Por fim, também terá de descarregar a amostra para ser utilizada durante o arranque rápido. A aplicação da amostra é **Azure Digital Twins Explorer.** Esta amostra contém a aplicação que utiliza no arranque rápido para carregar e explorar um cenário Azure Digital Twins. Também contém os ficheiros do cenário da amostra. Para obter a amostra, vá ao [Azure Digital Twins Explorer.](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) Selecione o botão **Baixar ZIP** para descarregar um ficheiro .zip deste código de amostra para a sua máquina. Desaperte a pasta **Azure_Digital_Twins__ADT__explorer.zip** e extraa os ficheiros.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Configurar Azure Digital Twins e Azure Digital Twins Explorer

O primeiro passo para trabalhar com a Azure Digital Twins é criar um exemplo de Azure Digital Twins. Depois de criar uma instância do serviço e configurar as suas credenciais para autenticar com o Azure Digital Twins Explorer, pode ligar-se ao caso no Azure Digital Twins Explorer e povoá-lo com os dados do exemplo mais tarde no arranque rápido.

O resto desta secção leva-o por estes degraus.

### <a name="set-up-an-azure-digital-twins-instance"></a>Configurar um exemplo de Gémeos Digitais Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

A aplicação Azure Digital Twins Explorer utiliza [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Para obter mais informações sobre diferentes formas de uma aplicação do cliente poder autenticar-se com a Azure Digital Twins, consulte [o código de autenticação da aplicação Write](how-to-authenticate-client.md).

Com este tipo de autenticação, o Azure Digital Twins Explorer procurará credenciais dentro do seu ambiente local, como um azure in em um [Azure CLI](/cli/azure/install-azure-cli) local ou em Visual Studio ou Visual Studio Code. Por esta razão, deverá *iniciar seduca no Azure localmente* através de um destes mecanismos para configurar credenciais para a aplicação Azure Digital Twins Explorer.

Se já se inscreveu no Azure através de uma destas formas, pode saltar para a [secção seguinte.](#run-and-configure-azure-digital-twins-explorer)

Caso contrário, pode instalar o Azure CLI local com estes passos:

1. Acompanhe o processo [neste link de instalação](/cli/azure/install-azure-cli) para concluir a instalação que corresponde ao seu SO.
1. Abra uma janela de consola na sua máquina.
1. Executar `az login` , e siga as instruções de autenticação para iniciar súm na sua conta Azure.
1. Possível último passo: Se utilizar várias subscrições Azure nesta conta, deslo aproveite o contexto de autenticação para a subscrição Azure que contém a sua instância Azure Digital Twins executando `az account set --subscription "<your-subscription-name-or-ID>"` (o nome ou o valor de ID da subscrição funcionarão).

Depois de iniciar sação, o Azure Digital Twins Explorer deve recolher automaticamente as suas credenciais Azure quando a executar na secção seguinte.

Pode fechar a janela da consola de autenticação, se quiser. Ou pode mantê-lo aberto para ser usado no próximo passo.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Executar e configurar Azure Digital Twins Explorer

Em seguida, execute a aplicação Azure Digital Twins Explorer e configuure-a para a sua instância Azure Digital Twins.

1. Vá para a pasta **Azure_Digital_Twins__ADT__explorer** descarregada e desapertado.
Abra uma janela da consola para a localização da pasta **Azure_Digital_Twins__ADT__explorer/cliente/src**.

1. Corra `npm install` para descarregar todas as dependências necessárias.

1. Inicie a aplicação `npm run start` executando.

   Após alguns segundos, uma janela do navegador abre e a aplicação aparece no navegador.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se Azure Digital Twins Explorer e contém caixas para Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Selecione o botão **Sign In** no canto superior direito da janela, como mostra a imagem seguinte, para configurar o Azure Digital Twins Explorer para trabalhar com a instância que configuraste.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer destacando o ícone Sign In perto do topo da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Introduza o URL de exemplo de gémeos digitais Azure que recolheu anteriormente na secção Configurar uma secção [de exemplos Azure Digital Twins,](#set-up-an-azure-digital-twins-instance) no formato *https://{instance host name}*.

>[!NOTE]
> Pode revisitar ou editar estas informações a qualquer momento selecionando o mesmo ícone para abrir novamente a caixa **Sign In.** Vai manter os valores que passaste.

> [!TIP]
> Se aparecer uma `SignalRService.subscribe` mensagem de erro quando ligar, certifique-se de que o URL das gémeas digitais Azure começa com *https://*.

Se vir uma janela pop-up **solicitada pela Microsoft,** conceda o consentimento para esta aplicação e aceite continuar.

## <a name="add-the-sample-data"></a>Adicione os dados da amostra

Em seguida, você importará o cenário da amostra e gráfico para Azure Digital Twins Explorer. O cenário da amostra também está localizado na pasta **Azure_Digital_Twins__ADT__explorer** que descarregou anteriormente.

### <a name="models"></a>Modelos

O primeiro passo numa solução Azure Digital Twins é definir o vocabulário para o seu ambiente. Irá criar [modelos personalizados](concepts-models.md) que descrevem os tipos de entidade que existem no seu ambiente.

Cada modelo é escrito numa língua como JSON-LD chamada Linguagem de Definição Digital Twin (DTDL). Cada modelo descreve um único tipo de entidade em termos das suas *propriedades,* *telemetria,* *relacionamentos* e *componentes.* Mais tarde, utilizará estes modelos como base para gémeos digitais que representam casos específicos deste tipo.

Normalmente, quando se cria um modelo, completa-se três passos:

1. Escreva a definição de modelo. No arranque rápido, este passo já é feito como parte da solução da amostra.
1. Valide-o para se certificar de que a sintaxe está correta. No arranque rápido, este passo já é feito como parte da solução da amostra.
1. Faça o upload para a sua instância Azure Digital Twins.
 
Para este arranque rápido, os ficheiros do modelo já estão escritos e validados para si. Estão incluídos na solução que descarregaste. Nesta secção, você irá enviar dois modelos pré-escritos para o seu caso para definir estes componentes de um ambiente de construção:

* Floor
* Quarto

#### <a name="upload-models"></a>Modelos de upload

Siga estes passos para carregar os modelos.

1. Na caixa **MODEL VIEW,** selecione o ícone **Upload a Model.**

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Na caixa Model View, destaca-se o ícone do meio. Mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Na caixa de seletor de ficheiros que aparece, aceda à pasta **Azure_Digital_Twins__ADT__explorer/cliente/exemplos** no repositório descarregado.
1. Selecione **Room.jse** **Floor.jse** selecione **OK**. Pode fazer upload de modelos adicionais se quiser, mas não serão utilizados neste arranque rápido.
1. Siga a caixa de diálogo pop-up que lhe pede para iniciar seduca na sua conta Azure.

>[!NOTE]
>Se vir a seguinte mensagem de erro: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Uma caixa pop-up que lê 'Error: Error fetching models: ClientAuthError: Error opening popup window. Isto pode acontecer se estiver a utilizar o IE ou se os popups estiverem bloqueados no navegador.' com um botão Close na parte inferior." border="false"::: 
> Tente desativar o seu bloqueador pop-up ou utilizar um browser diferente.

O Azure Digital Twins Explorer agora envia estes ficheiros de modelos para a sua instância Azure Digital Twins. Devem aparecer na caixa **MODEL VIEW** e apresentar os seus nomes amigáveis e iDs de modelo completo. Pode selecionar os ícones de informação **do Modelo ver** para ver o código DTDL por trás deles.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Uma vista da caixa Model View com duas definições de modelo listadas no interior, Piso (dtmi:exemplo:Piso;1) e Quarto (dtmi:exemplo:Quarto;1). Destaca-se o ícone de informação do Modelo de Visualização que mostra uma letra 'i' num círculo." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gémeos e o gráfico gémeo

Agora que alguns modelos foram enviados para a sua instância Azure Digital Twins, pode adicionar [gémeos digitais](concepts-twins-graph.md) que seguem as definições do modelo.

Os gémeos digitais representam as entidades reais dentro do seu ambiente de negócios. Podem ser coisas como sensores numa fazenda, luzes num carro, ou - neste arranque rápido - quartos em um piso de construção. Você pode criar muitos gémeos de qualquer tipo de modelo dado, como vários quartos que todos usam o modelo *Quarto.* Liga-se-lhes relacionamentos a um *gráfico gémeo* que representa todo o ambiente.

Nesta secção, você carregará gémeos pré-criados que estão ligados a um gráfico pré-criado. O gráfico contém dois pisos e duas salas, conectados no seguinte layout:

* Piso0
    - Contém quarto0
* Piso1
    - Contém sala1

#### <a name="import-the-graph"></a>Importe o gráfico

Siga estes passos para importar o gráfico.

1. Na caixa **GRAPH VIEW,** selecione o ícone **'Gráfico de Importação'.**

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Na caixa Graph View, destaca-se um ícone. Mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. Na caixa de seletor de ficheiros, vá à pasta **Azure_Digital_Twins__ADT__explorer/cliente/exemplos** e selecione o ficheiro **buildingScenario.xlsx** folha de cálculo. Este ficheiro contém uma descrição do gráfico da amostra. Selecione **OK**.

   Após alguns segundos, o Azure Digital Twins Explorer abre uma vista **importância** que mostra uma pré-visualização do gráfico a ser carregado.

3. Para confirmar o upload do gráfico, selecione o ícone **Guardar** no canto superior direito da caixa **GRAPH VIEW.**

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Realçando o ícone 'Guardar' no painel de pré-visualização do gráfico." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. O Azure Digital Twins Explorer usa agora o ficheiro carregado para criar os gémeos e relações entre eles. Uma caixa de diálogo aparece quando está terminada. Selecione **Fechar**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Uma caixa de diálogo que indica o sucesso da importação de gráficos. Diz: &quot;Importar bem sucedido. 4 gémeos importados. 2 relações importadas.»" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. O gráfico foi agora enviado para o Azure Digital Twins Explorer. Para ver o gráfico, selecione o botão **'Fazer',** na caixa **GRAPH EXPLORER,** perto da parte superior da janela Azure Digital Twins Explorer.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Destaca-se o botão 'Fazer-do-lado direito' no canto superior direito da janela." lightbox="media/quickstart-adt-explorer/run-query.png":::

Esta ação executa a consulta predefinitiva para selecionar e exibir todos os gémeos digitais. O Azure Digital Twins Explorer recupera todos os gémeos e relacionamentos do serviço. Desenha o gráfico definido por eles na caixa **GRAPH VIEW.**

## <a name="explore-the-graph"></a>Explore o gráfico

Agora pode ver o gráfico carregado do cenário da amostra.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vista da caixa Graph View com um gráfico duplo no interior. Um círculo com a etiqueta 'floor1' é ligado por uma seta com a etiqueta 'contém' a um círculo marcado como &quot;sala1&quot;. Um círculo com a etiqueta 'floor0' é ligado por uma seta com a etiqueta 'contém' a um círculo marcado como &quot;quarto0&quot;.":::

Os círculos (gráficos "nós") representam gémeos digitais. As linhas representam relacionamentos. O **twin Floor0** contém **o Quarto0,** e o twin **Floor1** contém **a Sala1.**

Se estiver a usar um rato, pode arrastar pedaços do gráfico para movê-los.

### <a name="view-twin-properties"></a>Ver propriedades gémeas

Pode selecionar um twin para ver uma lista das suas propriedades e os seus valores na caixa **PROPERTY EXPLORER.**

Aqui estão as propriedades do Quarto0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Destaque em torno da caixa do Property Explorer mostrando propriedades para o Room0, que incluem (entre outros) um campo de $dtId da Sala0, um campo de temperatura de 70, e um campo de humidade de 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O quarto 0 tem uma temperatura de 70.

Aqui estão as propriedades da Sala1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Destaque em torno da caixa do Property Explorer mostrando propriedades para a Sala1, que incluem (entre outras) um campo de $dtId da Sala1, um campo de temperatura de 80, e um campo de humidade de 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O quarto1 tem uma temperatura de 80.

### <a name="query-the-graph"></a>Consultar o gráfico

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente.

Uma forma de consultar os gémeos no seu gráfico é pelas suas *propriedades.* A consulta com base em propriedades pode ajudar a responder a uma variedade de perguntas. Por exemplo, você pode encontrar mais distantes no seu ambiente que podem precisar de atenção.

Nesta secção, você fará uma pergunta para responder à questão de quantos gémeos no seu ambiente têm uma temperatura superior a 75.

Para ver a resposta, execute a seguinte consulta na caixa **QUERY EXPLORER.**

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Lembre-se de ver as propriedades gémeas mais cedo que o Quarto0 tem uma temperatura de 70, e a Sala1 tem uma temperatura de 80. Por esta razão, apenas a Sala 1 aparece nos resultados aqui.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultados da consulta de propriedade mostrando apenas a Sala1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Outros operadores de comparação (<>, =ou !=) também são apoiados na consulta anterior. Pode tentar ligar estes operadores, valores diferentes ou propriedades gémeas diferentes na consulta para experimentar responder às suas próprias perguntas.

## <a name="edit-data-in-the-graph"></a>Editar dados no gráfico

Pode utilizar o Azure Digital Twins Explorer para editar as propriedades dos gémeos representados no seu gráfico. Nesta secção, vamos aumentar a temperatura da Sala 0 para 76.

Para começar, selecione **Room0** para apresentar a sua lista de propriedades na caixa **PROPERTY EXPLORER.**

As propriedades desta lista são editáveis. Selecione o valor de temperatura de **70** para permitir a inserimento de um novo valor. Introduza **76** e selecione o ícone **Guardar** para atualizar a temperatura para **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A caixa do Explorador de Propriedades mostra propriedades para o Room0. O valor da temperatura é uma caixa editável que mostra 76, e há um destaque em torno do ícone Save." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Agora, você verá uma janela **de Informação de Patch** onde aparece o código de patch que foi usado nos bastidores com as [APIs](how-to-use-apis-sdks.md) Azure Digital Twins para fazer a atualização. Selecione **Fechar**.

### <a name="query-to-see-the-result"></a>Consulta para ver o resultado

Para verificar se o gráfico registou com sucesso a sua atualização à temperatura da Sala 0, reexame a consulta de antes para obter todos os gémeos no ambiente com uma temperatura superior a 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Agora que a temperatura da Sala 0 foi alterada de 70 para 76, ambos os gémeos devem aparecer no resultado.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultados da consulta de propriedade mostrando tanto o Quarto0 como o Quarto1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Rever e contextualizar aprendizagens

Neste quickstart, criou um exemplo de Azure Digital Twins, ligou-o ao Azure Digital Twins Explorer, e povoou-o com um cenário de amostragem.

Em seguida, explorou o gráfico, por:

* Usando uma pergunta para responder a uma pergunta sobre o cenário.
* Editar uma propriedade num gémeo digital.
* Executando novamente a consulta para ver como a resposta mudou como resultado da sua atualização.

A intenção deste exercício é demonstrar como pode usar o gráfico Azure Digital Twins para responder a questões sobre o seu ambiente, mesmo que o ambiente continue a mudar.

Neste arranque rápido, fez a atualização da temperatura manualmente. É comum no Azure Digital Twins ligar gémeos digitais a dispositivos IoT reais para que recebam atualizações automaticamente, com base em dados de telemetria. Desta forma, pode construir um gráfico ao vivo que reflita sempre o estado real do seu ambiente. Você pode usar consultas para obter informações sobre o que está acontecendo no seu ambiente em tempo real.

## <a name="clean-up-resources"></a>Limpar os recursos

Para embrulhar o trabalho para este arranque rápido, primeiro termine a aplicação de consola de execução. Esta ação desliga a ligação à aplicação Azure Digital Twins Explorer no navegador. Não poderá mais ver dados ao vivo no navegador. Pode fechar o separador do navegador.

Depois, pode escolher quais os recursos que gostaria de remover, dependendo do que quiser fazer a seguir.

* **Se planeia continuar com os tutoriais do Azure Digital Twins,** pode reutilizar o caso neste quickstart para esses artigos, e não precisa removê-lo.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Também pode querer eliminar a pasta do projeto da sua máquina local.

## <a name="next-steps"></a>Passos seguintes

Em seguida, continue para os tutoriais Azure Digital Twins para construir o seu próprio cenário Azure Digital Twins e ferramentas de interação.

> [!div class="nextstepaction"]
> [Tutorial: Código de uma aplicação de cliente](tutorial-code.md)