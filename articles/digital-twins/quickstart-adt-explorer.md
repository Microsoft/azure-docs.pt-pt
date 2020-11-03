---
title: Quickstart - Explore um cenário de amostra
titleSuffix: Azure Digital Twins
description: Quickstart - Utilize a amostra do ADT Explorer para visualizar e explorar um cenário pré-construído.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242284"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Quickstart - Explore uma amostra do cenário Azure Digital Twins usando o ADT Explorer

Com a Azure Digital Twins, pode criar e interagir com modelos ao vivo dos seus ambientes do mundo real. Isto é feito modelando elementos individuais como **gémeos digitais,** ligando-os depois a um **gráfico de** conhecimento que pode responder a eventos ao vivo e ser questionado para obter informações.

Neste quickstart, você explorará um gráfico pré-construído Azure Digital Twins, com a ajuda de uma aplicação de amostra chamada [**Azure Digital Twins (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). O ADT Explorer permite-lhe fazer o upload de uma representação digital de um ambiente, ver imagens visuais dos gémeos e gráficos que são criados para representar o ambiente em Azure Digital Twins, e realizar outras atividades de gestão através de uma experiência visual baseada no navegador.

O quickstart contém os seguintes passos principais:

1. Crie um exemplo de Gémeos Digitais Azure e Explorador ADT
1. Carre faça upload de modelos pré-construídos e dados gráficos para construir o cenário da amostra
1. Explore o gráfico de cenário que é criado
1. Fazer alterações no gráfico

O gráfico de amostra com que vai trabalhar representa um edifício com dois pisos e duas salas. O gráfico será assim:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vista de um gráfico feito de 4 nós circulares ligados por setas. Um círculo com a marca «Piso1» é ligado por uma seta com a etiqueta «contém» a um círculo com a etiqueta «Sala1»; um círculo com a marca 'Floor0' é ligado por uma seta com a etiqueta 'contém' a um círculo com a indicação 'Sala0'. 'Floor1' e 'Floor0' não estão ligados.":::

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma subscrição Azure para completar este arranque rápido. Se ainda não tem um, **[crie um de graça](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** agora.

Também vai precisar de **Node.js** na sua máquina. Pode obter a versão mais recente neste link: [Node.js](https://nodejs.org/).

Por fim, também terá de descarregar a amostra para utilizar durante o arranque rápido: a aplicação de amostra **do ADT Explorer.** Esta amostra contém a aplicação que utiliza no arranque rápido para carregar e explorar um cenário Azure Digital Twins, bem como os ficheiros de cenário de amostra. Para obter a amostra, navegue aqui: [Azure Digital Twins (ADT) explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Aperda no botão *Baixar ZIP* para descarregar um *. Ficheiro ZIP* deste código de amostra para a sua máquina. Isto irá descarregar um . Pasta ZIP para a sua máquina _**comoAzure_Digital_Twins__ADT__explorer.zip**_. Desaperte a pasta e extraa os ficheiros.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurar gémeos digitais Azure e ADT Explorer

O primeiro passo para trabalhar com a Azure Digital Twins é **criar uma instância Azure Digital Twins**. Depois de criar uma instância do serviço e **de configurar as suas credenciais** para autenticar com o ADT Explorer, poderá **ligar-se ao caso no ADT Explorer** e povoá-lo com os dados de exemplo mais tarde no arranque rápido.

O resto desta secção leva-o por estes degraus.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar a instância Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

A aplicação ADT Explorer utiliza [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar os utilizadores com a instância Azure Digital Twins quando a executam na sua máquina local. Para obter mais informações sobre formas diferentes de uma aplicação de clientes poder autenticar com a Azure Digital Twins, consulte [*Como escrever código de autenticação de aplicações*](how-to-authenticate-client.md).

Com este tipo de autenticação, o ADT Explorer procurará credenciais dentro do seu ambiente local, como um login Azure num [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou no Visual Studio/Visual Studio Code. Isto significa que você deve **iniciar sessão no Azure localmente** através de um destes mecanismos para configurar credenciais para a aplicação ADT Explorer.

Se já está a iniciar sessão no Azure através de uma destas formas, pode saltar para a [secção seguinte.](#run-and-configure-adt-explorer)

Caso contrário, pode instalar o **Azure CLI** local com estes passos:
1. Siga o processo [**neste link de instalação**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para concluir a instalação que corresponde ao seu SO.
2. Abra uma janela de consola na sua máquina.
3. Executar `az login` e seguir as instruções de autenticação para iniciar sessão na sua conta Azure.

Depois de o fazer, o ADT Explorer deve recolher automaticamente as suas credenciais Azure quando a executar na secção seguinte.

Pode fechar a janela da consola de autenticação, se quiser, ou mantê-la aberta para ser utilizada no passo seguinte.

### <a name="run-and-configure-adt-explorer"></a>Executar e configurar o Explorador ADT

Em seguida, execute a aplicação ADT Explorer e configuure-a para a sua instância Azure Digital Twins.

Navegue para a pasta _**Azure_Digital_Twins__ADT__explorer**_ descarregada e desapertado. Abra uma janela da consola para a localização da pasta *Azure_Digital_Twins__ADT__explorer/cliente/src*.

Corra `npm install` para descarregar todas as dependências necessárias.

Em seguida, inicie a aplicação executando `npm run start` .

Após alguns segundos, uma janela do navegador será aberta e a aplicação aparecerá no navegador.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Janela do navegador mostrando uma aplicação em execução na localidade:3000. A aplicação chama-se ADT Explorer e contém caixas para um Explorador de Consultas, Visualização de Modelos, Visão de Gráfico e Explorador de Propriedades. Ainda não há dados no ecrã." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Se *premir* o botão Sinal na parte superior da janela (mostrado na imagem abaixo) para configurar o ADT Explorer para trabalhar com a instância que configura. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer destacando o ícone Sign In perto do topo da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Introduza o *URL de exemplo de gémeos digitais Azure* que recolheu anteriormente na secção [Pré-requisitos,](#prerequisites) no formato *https://{instance host name}*.

>[!NOTE]
> Pode revisitar/editar estas informações a qualquer momento selecionando o mesmo ícone para puxar novamente a caixa 'Iniciar' Vai manter os valores que passaste.

> [!TIP]
> Se for mostrada uma `SignalRService.subscribe` mensagem de erro quando ligar, certifique-se de que o URL das gémeas digitais Azure começa com *https://*.

Se vir uma janela pop-up *solicitada pela Microsoft,* conceda o consentimento para esta aplicação e aceite continuar.

## <a name="add-the-sample-data"></a>Adicione os dados da amostra

Em seguida, importará o cenário da amostra e gráfico para o ADT Explorer. O cenário da amostra também está localizado na pasta **Azure_Digital_Twins__ADT__explorer** que descarregou anteriormente.

### <a name="models"></a>Modelos

O primeiro passo numa solução Azure Digital Twins é definir o vocabulário para o seu ambiente. Isto é feito através da criação de [**modelos personalizados**](concepts-models.md), que descrevem os tipos de entidade que existem no seu ambiente. 

Cada modelo é escrito numa língua semelhante a JSON-LD chamada **Linguagem de Definição Digital Twin (DTDL)** e descreve um único tipo de entidade em termos das suas *propriedades,* *telemetria,* *relacionamentos* e *componentes.* Mais tarde, utilizará estes modelos como base para gémeos digitais que representam casos específicos deste tipo.

Normalmente, ao criar um modelo, completará três passos:
1. Escreva a definição do modelo (no arranque rápido, já feito como parte da solução de amostra)
2. Valide-o para garantir que a sintaxe é precisa (no arranque rápido, já feito como parte da solução de amostra)
3. Faça o upload para o seu exemplo Azure Digital Twins
 
Para este arranque rápido, os ficheiros do modelo já foram escritos e validados para si, e estão incluídos na solução que descarregou. Nesta secção, irá enviar dois modelos pré-escritos para o seu caso para definir estes componentes de um ambiente de construção:
* Floor
* Quarto

#### <a name="upload-models"></a>Modelos de upload

Na caixa *MODEL VIEW,* bata no ícone *Upload a Model.*

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Na caixa Model View, destaca-se o ícone do meio. Mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Na caixa de seletor de ficheiros que aparece, navegue para a pasta *Azure_Digital_Twins__ADT__explorer/cliente/exemplos* no repositório descarregado.
2. Selecione *Room.js* e *Floor.js* e bata OK. (Pode fazer o upload de modelos adicionais se quiser, mas não serão utilizados neste arranque rápido.)
3. Siga o diálogo popup pedindo-lhe para assinar na sua conta Azure.

>[!NOTE]
>Se vir a seguinte mensagem de erro: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Um popup a ler 'Error: Error fetching models: ClientAuthError: Error opening popup window. Isto pode acontecer se estiver a usar IE ou se os popups estiverem bloqueados no navegador.' com um botão Close na parte inferior" border="false"::: 
> Tente desativar o seu bloqueador popup ou utilizar um browser diferente.

O ADT Explorer irá agora enviar estes ficheiros de modelo para a sua instância Azure Digital Twins. Devem aparecer na caixa *MODEL VIEW,* exibindo os seus nomes amigáveis e iDs de modelo completo. Pode clicar nas bolhas de informação do *Modelo ver* para ver o código DTDL por trás.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Uma vista da caixa 'Model View' com duas definições de modelo listadas no interior, Piso (dtmi:exemplo:Piso;1) e Quarto (dtmi:exemplo:Quarto;1). Destaca-se o ícone 'Ver modelo' que mostra uma letra 'i' num círculo." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gémeos e o gráfico gémeo

Agora que alguns modelos foram enviados para a sua instância Azure Digital Twins, pode adicionar [**gémeos digitais**](concepts-twins-graph.md) que seguem as definições do modelo. 

Os gémeos digitais representam as entidades reais dentro do seu ambiente de negócios: coisas como sensores numa quinta, luzes num carro, ou - neste arranque rápido - quartos em um piso de construção. Você pode criar muitos gémeos de qualquer tipo de modelo (como vários quartos que todos usam o modelo *Room),* e conectá-los com relacionamentos em um **gráfico gémeo** que representa todo o ambiente.

Nesta secção, você irá carregar gémeos pré-criados que estão ligados a um gráfico pré-criado. O gráfico contém dois pisos e duas salas, conectados no seguinte layout:
* *Piso0*
    - contém *Sala0*
* *Piso1*
    - contém *Sala1*

#### <a name="import-the-graph"></a>Importe o gráfico

Na caixa *GRAPH VIEW,* bata no ícone *Import Graph.*

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Na caixa Graph View, destaca-se um ícone. Mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Na caixa de seletor de ficheiros, navegue na pasta *Azure_Digital_Twins__ADT__explorer/cliente/exemplos* e escolha o ficheiro _**buildingScenario.xlsx**_ folha de cálculo. Este ficheiro contém uma descrição do gráfico da amostra. Bata bem.

Após alguns segundos, o ADT Explorer abrirá uma vista *de Importação* exibindo uma pré-visualização do gráfico que vai ser carregado.

Para confirmar o upload do gráfico, bata no ícone *Guardar* no canto superior direito do *GRAPH VIEW* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Realçando o ícone Guardar no painel de pré-visualização do gráfico" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O ADT Explorer irá agora utilizar o ficheiro carregado para criar os gémeos e relações entre eles. Um diálogo parece mostrar que está terminado. Bater *perto*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Caixa de diálogo indicando o sucesso da importação de gráficos. Diz: &quot;Importar bem sucedido. 4 gémeos importados. 2 relações importadas.»" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O gráfico foi agora enviado para o ADT Explorer. Para ver o gráfico, bata no botão *'Executar Consulta'* na caixa *GRAPH EXPLORER,* perto da parte superior da janela do Explorador ADT. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Um botão de leitura 'Run Consulta' perto do topo da janela é realçado" lightbox="media/quickstart-adt-explorer/run-query.png":::

Isto irá executar a consulta predefinitiva para selecionar e exibir todos os gémeos digitais. O ADT Explorer recuperará todos os gémeos e relações do serviço e desenhará o gráfico definido por eles na caixa *GRAPH VIEW.*

## <a name="explore-the-graph"></a>Explore o gráfico

Agora, pode ver o gráfico carregado do cenário da amostra:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vista da caixa 'Graph View' com um gráfico duplo no interior. Um círculo com a marca «piso1» é ligado por uma seta com a etiqueta «contém» a um círculo com a etiqueta «sala1»; um círculo com a marca «piso 0» é ligado por uma seta com a etiqueta «contém» a um círculo com a indicação de &quot;sala0&quot;.":::

Os círculos (gráficos "nós") representam gémeos digitais, e as linhas representam relacionamentos. Você verá que o twin *Floor0* contém *Quarto0,* e o twin *Floor1* contém *Sala1.*

Se estiver a usar um rato, pode clicar e arrastar pedaços do gráfico para movê-los.

### <a name="view-twin-properties"></a>Ver propriedades gémeas 

Pode selecionar um twin para ver uma lista das suas propriedades e os seus valores na caixa *PROPERTY EXPLORER.* 

Aqui estão as propriedades do *Quarto0:*

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Destaque em torno da caixa 'Property Explorer' mostrando propriedades para o Room0, incluindo (entre outros) um campo de $dtId de 'Room0', um campo de temperatura de 70, e um campo de humidade de 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Note que *o Quarto0* tem uma temperatura de **70**.

Aqui estão as propriedades da *Sala1:*

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Destaque em torno da caixa 'Property Explorer' mostrando propriedades para a Sala1, incluindo (entre outros) um campo de $dtId de 'Room1', um campo de temperatura de 80, e um campo de humidade de 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Note que *a Sala1* tem uma temperatura de **80**.

### <a name="query-the-graph"></a>Consultar o gráfico

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente. 

Uma forma de consultar os gémeos no seu gráfico é pelas suas *propriedades.* A consulta com base em propriedades pode ajudar a responder a uma variedade de questões, incluindo descobrir mais outliers no seu ambiente que possam precisar de atenção.

Nesta secção, você fará uma pergunta para responder à seguinte pergunta: _**Quais são todos os gémeos no meu ambiente com uma temperatura superior a 75?**_

Para ver a resposta, execute a seguinte consulta na caixa *QUERY EXPLORER:*

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Lembre-se de ver as propriedades gémeas anteriormente que *o Quarto0* tem uma temperatura de **70** e *a Sala1* tem uma temperatura de **80**. Como resultado, apenas _**a Sala 1**_ aparece nos resultados aqui.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultados da consulta de propriedade, mostrando apenas o Quarto1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Outros operadores de comparação *<* *>* (, *=* , , , ou *!=* ) também são apoiados na consulta acima. Você pode tentar ligar estes valores diferentes, ou diferentes propriedades gémeas na consulta para experimentar responder às suas próprias perguntas.

## <a name="edit-data-in-the-graph"></a>Editar dados no gráfico

Pode utilizar o ADT Explorer para editar as propriedades dos gémeos representados no seu gráfico. Nesta secção, **_vamos elevar a temperatura da_ Sala 0 para 76**.

Para isso, selecione *Room0,* elevando a sua lista de propriedades na caixa *PROPERTY EXPLORER.*

As propriedades desta lista são editáveis. Selecione o valor de temperatura de **70** para permitir a inserimento de um novo valor. Introduza **76** , e bata no ícone *Guardar* para atualizar a temperatura para **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A caixa 'Property Explorer' mostra propriedades para o Room0. O valor da temperatura é uma caixa editável que mostra 76, e há um destaque em torno do ícone Save." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Após uma poupança bem sucedida, verá uma janela *de Patch Information* exibindo o código de correção que foi usado nos bastidores com as [APIs](how-to-use-apis-sdks.md) de Gémeos Digitais Azure para fazer a atualização. Bater *perto*.

### <a name="query-to-see-the-result"></a>Consulta para ver o resultado

Para verificar se o gráfico registou com sucesso a sua atualização à temperatura da *Sala0,* re-executar a consulta de anteriormente para **obter todos os gémeos no ambiente com uma temperatura superior a 75** :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Agora que a temperatura da *Sala0* foi alterada de **70** para **76,** ambos os gémeos devem aparecer no resultado.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultados da consulta de propriedade, mostrando tanto o Quarto0 como o Quarto1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Rever e contextualizar aprendizagens

Neste quickstart, criou um exemplo de Azure Digital Twins, ligou-o ao ADT Explorer, e povoou-o com um cenário de amostragem. 

Depois, explorou o gráfico, por...
1. Usando uma pergunta para responder a uma pergunta sobre o cenário.
2. Editar uma propriedade num gémeo digital.
3. Executando novamente a consulta para ver como a resposta mudou como resultado da sua atualização.

A intenção deste exercício é demonstrar como pode usar o gráfico Azure Digital Twins para responder a questões sobre o seu ambiente, mesmo que o ambiente continue a mudar. 

Embora neste arranque rápido, tenha feito a atualização de temperatura manualmente, é comum na Azure Digital Twins ligar gémeos digitais a dispositivos IoT reais para que recebam atualizações automaticamente, com base em dados de telemetria. Isto permite-lhe construir um gráfico ao vivo que reflita sempre o estado real do seu ambiente, e usar consultas para obter informações sobre o que está a acontecer no seu ambiente em tempo real.

## <a name="clean-up-resources"></a>Limpar recursos

Para embrulhar o trabalho para este arranque rápido, primeiro termine a aplicação de consola de execução. Isto irá desligar a ligação à aplicação ADT Explorer no navegador, e deixará de ser capaz de ver dados ao vivo no navegador. Pode fechar o separador do navegador.

Se planeia continuar com os tutoriais do Azure Digital Twins, a instância usada neste quickstart pode ser reutilizada para esses artigos, e não precisa removê-lo.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Por fim, elimine a pasta de amostra de projeto que descarregou para a sua máquina local _**(Azure_Digital_Twins__ADT__explorer).**_ Pode ter de eliminar as versões com fecho e desapertado.

## <a name="next-steps"></a>Passos seguintes 

Em seguida, continue para os tutoriais Azure Digital Twins para construir o seu próprio cenário Azure Digital Twins e ferramentas de interação.

> [!div class="nextstepaction"]
> [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)