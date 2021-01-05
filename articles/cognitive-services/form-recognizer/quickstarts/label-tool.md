---
title: 'Quickstart: Label forms, train a model, and analyze forming using the sample labeling tool - Form Recogniser'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a ferramenta de rotulagem da amostra do Reconhecimento de Formulários para rotular manualmente documentos de formulário. Em seguida, irá treinar um modelo de processamento de documentos personalizado com os documentos rotulados e usar o modelo para extrair pares de chaves/valor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: processamento de documentos
ms.openlocfilehash: a1cf919e17e22cb6280dce27faceb7cd034a6962
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845549"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Treine um modelo de reconhecimento de formulários com etiquetas usando a ferramenta de rotulagem da amostra

Neste arranque rápido, utilizará a API do Coitalista de Formulários com a ferramenta de rotulagem da amostra para treinar um modelo de processamento de documentos personalizado com dados etiquetados manualmente. Consulte a secção [Train com etiquetas](../overview.md#train-with-labels) da visão geral para saber mais sobre a aprendizagem supervisionada com o Form Recogniser.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Um conjunto de pelo menos seis formas do mesmo tipo. Usará estes dados para treinar o modelo e testar um formulário. Pode utilizar um [conjunto de dados de amostra](https://go.microsoft.com/fwlink/?linkid=2090451) (descarregar e extrair *sample_data.zip)* para este arranque rápido. Faça o upload dos ficheiros de treino para a raiz de um recipiente de armazenamento de bolhas numa conta de armazenamento Azure de nível de desempenho padrão.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Experimente

Para experimentar online a Ferramenta de Rotulagem da Amostra do Reconhecimento de Formulários, aceda ao site da [FOTT](https://fott-preview.azurewebsites.net/).

# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Experimente modelos pré-construídos](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Experimente modelos pré-construídos](https://fott-preview.azurewebsites.net/)

---

Você precisará de uma subscrição Azure[(crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto final de [recurso do Reconhecimento de Formulários](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e chave para experimentar o serviço Deseclarador de Formulários. 


## <a name="set-up-the-sample-labeling-tool"></a>Configurar a ferramenta de rotulagem da amostra

Vai usar o motor Docker para executar a ferramenta de rotulagem da amostra. Siga estes passos para configurar o contentor Docker. Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> A Ferramenta de Rotulagem do Formulário OCR também está disponível como um projeto de código aberto no GitHub. A ferramenta é uma aplicação web TypeScript construída utilizando o React + Redux. Para saber mais ou contribuir, consulte o repo da Ferramenta de Rotulagem do [Formulário OCR.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) Para experimentar a ferramenta online, aceda ao site da [FOTT.](https://fott.azurewebsites.net/)   

1. Primeiro, instala o Docker num computador anfitrião. Este guia irá mostrar-lhe como usar o computador local como hospedeiro. Se pretender utilizar um serviço de hospedagem Docker em Azure, consulte a ferramenta de [rotulagem da amostra](../deploy-label-tool.md) como orientar. 

   O computador anfitrião deve satisfazer os seguintes requisitos de hardware:

    | Contentor | Mínimo | Recomendado|
    |:--|:--|:--|
    |Ferramenta de rotulagem de amostra|2 núcleo, memória de 4-GB|4 núcleo, memória de 8 GB|

   Instale o Docker na sua máquina seguindo as instruções apropriadas para o seu sistema operativo: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)



1. Obtenha o recipiente da ferramenta de rotulagem da amostra com o `docker pull` comando.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
    # <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
    ```

    ---

1. Agora está pronto para executar o contentor `docker run` com.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```
    # <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept    
    ```

    --- 

   Este comando disponibilizará a ferramenta de rotulagem da amostra através de um navegador web. Aceda a `http://localhost:3000`.

> [!NOTE]
> Também pode rotular documentos e modelos de comboio usando a API do Reconhecimento de Formulários REST. Para treinar e analisar com a API REST, consulte [Train com etiquetas utilizando a API REST e a Python.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

## <a name="set-up-input-data"></a>Configurar dados de entrada

Primeiro, certifique-se de que todos os documentos de treino são do mesmo formato. Se tiver formulários em vários formatos, organize-os em subpastas com base no formato comum. Ao preparar, precisará de direcionar a API para uma subpasta.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configure a partilha de recursos de domínio cruzado (CORS)

Ativar o CORS na sua conta de armazenamento. Selecione a sua conta de armazenamento no portal Azure e clique no **separador CORS** no painel esquerdo. Na linha de fundo, preencha os seguintes valores. Em seguida, clique em **Guardar** na parte superior.

* Origens permitidas = * 
* Métodos permitidos = \[ selecione todos os\]
* Cabeçalhos permitidos = *
* Cabeçalhos expostos = * 
* Idade máxima = 200

> [!div class="mx-imgBorder"]
> ![Instalação do CORS no portal Azure](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Ligue-se à ferramenta de rotulagem da amostra

A ferramenta de rotulagem da amostra liga-se a uma fonte (onde estão os seus formulários originais) e a um alvo (onde exporta os rótulos e dados de saída criados).

As ligações podem ser configurada e partilhadas entre projetos. Eles usam um modelo de fornecedor extensível, para que você possa facilmente adicionar novos fornecedores de origem/alvo.

Para criar uma nova ligação, clique no ícone **Novas Ligações** (ficha) na barra de navegação esquerda.

Preencha os campos com os seguintes valores:

* **Nome do visor** - O nome do visor da ligação.
* **Descrição** - A descrição do seu projeto.
* **URL SAS** - O URL de assinatura de acesso partilhado (SAS) do seu recipiente de armazenamento Azure Blob. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Recuperação de URL SAS":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Regulações de ligação da ferramenta de rotulagem da amostra.":::


## <a name="create-a-new-project"></a>Criar um novo projeto

Na ferramenta de rotulagem da amostra, os projetos armazenam as suas configurações e configurações. Criar um novo projeto e preencher os campos com os seguintes valores:

* **Display Name** - o nome do display do projeto
* **Token de segurança** - Algumas definições de projeto podem incluir valores sensíveis, tais como chaves API ou outros segredos partilhados. Cada projeto gerará um símbolo de segurança que pode ser usado para encriptar/desencriptar configurações sensíveis do projeto. Pode encontrar fichas de segurança nas Definições de Aplicação clicando no ícone de engrenagem na parte inferior da barra de navegação esquerda.
* **Ligação Origem** - A ligação Azure Blob Storage que criou no passo anterior que gostaria de utilizar para este projeto.
* **Caminho da pasta** - Opcional - Se os formulários de origem estiverem localizados numa pasta no recipiente blob, especifique o nome da pasta aqui
* **Formulário Reconhecedor Serviço Uri** - O seu URL de ponto final do Reconhecimento de Formulário.
* **Chave API** - A tecla de subscrição do Reconhecimento de Formulários.
* **Descrição** - Opcional - Descrição do projeto

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Nova página do projeto na ferramenta de rotulagem de amostras.":::

## <a name="label-your-forms"></a>Rotular os seus formulários

Quando cria ou abre um projeto, abre-se a janela principal do editor de etiquetas. O editor de etiquetas é composto por três partes:

* Um painel de pré-visualização resizável que contém uma lista de formulários scrollable da ligação de origem.
* O painel de editor principal que lhe permite aplicar etiquetas.
* O painel de editores de etiquetas que permite aos utilizadores modificar, bloquear, reencomendar e eliminar tags. 

### <a name="identify-text-elements"></a>Identificar elementos de texto

Clique **em OCR em todos os ficheiros** do painel esquerdo para obter as informações de layout de texto para cada documento. A ferramenta de rotulagem irá desenhar caixas de delimitação em torno de cada elemento de texto.

Também mostrará quais as tabelas que foram automaticamente extraídas. Clique no ícone de tabela/grelha na mão esquerda do documento para ver a tabela extraída. Neste arranque rápido, uma vez que o conteúdo da tabela é extraído automaticamente, não vamos rotular o conteúdo da tabela, mas sim confiar na extração automatizada.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Visualização da tabela na ferramenta de rotulagem da amostra.":::

### <a name="apply-labels-to-text"></a>Aplicar rótulos no texto

Em seguida, irá criar tags (etiquetas) e aplicá-las aos elementos de texto que pretende que o modelo reconheça.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
1. Primeiro, use o painel de editores de etiquetas para criar as etiquetas que gostaria de identificar.
   1. Clique **+** para criar uma nova etiqueta.
   1. Insira o nome da etiqueta.
   1. Pressione Insira para guardar a etiqueta.
1. No editor principal, clique para selecionar palavras dos elementos de texto realçados.
1. Clique na etiqueta que pretende aplicar ou prima a tecla de teclado correspondente. As teclas numeradas são atribuídas como hotkeys para as primeiras 10 tags. Pode reencomendar as suas etiquetas utilizando os ícones de seta para cima e para baixo no painel de editores de etiquetas.
    > [!Tip]
    > Tenha em mente as seguintes dicas quando estiver a rotular os formulários.
    > * Só pode aplicar uma etiqueta a cada elemento de texto selecionado.
    > * Cada tag só pode ser aplicada uma vez por página. Se um valor aparecer várias vezes na mesma forma, crie etiquetas diferentes para cada instância. Por exemplo: "fatura nº 1", "fatura nº 2" e assim por diante.
    > * As etiquetas não podem abranger páginas.
    > * Valores de etiquetagem tal como aparecem no formulário; não tente dividir um valor em duas partes com duas etiquetas diferentes. Por exemplo, um campo de endereços deve ser rotulado com uma única etiqueta, mesmo que se enveredeça por várias linhas.
    > * Não inclua chaves nos seus campos marcados &mdash; apenas os valores.
    > * Os dados do quadro devem ser detetados automaticamente e estarão disponíveis no ficheiro JSON de saída final. No entanto, se o modelo não detetar todos os dados da sua tabela, também pode marcar manualmente estes campos. Marque cada célula na mesa com uma etiqueta diferente. Se os seus formulários tiverem tabelas com um número variado de linhas, certifique-se de que marca pelo menos um formulário com a maior tabela possível.
    > * Utilize os botões à direita da **+** pesquisa, renomeação, reencomenda e exclusão das suas etiquetas.
    > * Para remover uma etiqueta aplicada sem eliminar a etiqueta em si, selecione o retângulo marcado na vista do documento e prima a tecla de eliminação.


# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1) 
1. Primeiro, use o painel de editores de etiquetas para criar as etiquetas que gostaria de identificar.
   1. Clique **+** para criar uma nova etiqueta.
   1. Insira o nome da etiqueta.
   1. Pressione Insira para guardar a etiqueta.
1. No editor principal, clique para selecionar palavras dos elementos de texto realçados. Na _pré-visualização v2.1.2_ também pode clicar para selecionar _Marcas de Seleção_ como botões de rádio e caixas de verificação como pares de valor chave. O Reconhecimento de Formulários identificará se a marca de seleção é "selecionada" ou "não selecionada" como o valor.
1. Clique na etiqueta que pretende aplicar ou prima a tecla de teclado correspondente. As teclas numeradas são atribuídas como hotkeys para as primeiras 10 tags. Pode reencomendar as suas etiquetas utilizando os ícones de seta para cima e para baixo no painel de editores de etiquetas.
    > [!Tip]
    > Tenha em mente as seguintes dicas quando estiver a rotular os formulários.
    > * Só pode aplicar uma etiqueta a cada elemento de texto selecionado.
    > * Cada tag só pode ser aplicada uma vez por página. Se um valor aparecer várias vezes na mesma forma, crie etiquetas diferentes para cada instância. Por exemplo: "fatura nº 1", "fatura nº 2" e assim por diante.
    > * As etiquetas não podem abranger páginas.
    > * Valores de etiquetagem tal como aparecem no formulário; não tente dividir um valor em duas partes com duas etiquetas diferentes. Por exemplo, um campo de endereços deve ser rotulado com uma única etiqueta, mesmo que se enveredeça por várias linhas.
    > * Não inclua chaves nos seus campos marcados &mdash; apenas os valores.
    > * Os dados do quadro devem ser detetados automaticamente e estarão disponíveis no ficheiro JSON de saída final. No entanto, se o modelo não detetar todos os dados da sua tabela, também pode marcar manualmente estes campos. Marque cada célula na mesa com uma etiqueta diferente. Se os seus formulários tiverem tabelas com um número variado de linhas, certifique-se de que marca pelo menos um formulário com a maior tabela possível.
    > * Utilize os botões à direita da **+** pesquisa, renomeação, reencomenda e exclusão das suas etiquetas.
    > * Para remover uma etiqueta aplicada sem eliminar a etiqueta em si, selecione o retângulo marcado na vista do documento e prima a tecla de eliminação.


---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Janela principal do editor da ferramenta de rotulagem de amostra.":::


Siga os passos acima para rotular pelo menos cinco dos seus formulários.

### <a name="specify-tag-value-types"></a>Especifique tipos de valor de etiqueta

Opcionalmente, pode definir o tipo de dados esperado para cada tag. Abra o menu de contexto à direita de uma etiqueta e selecione um tipo no menu. Esta funcionalidade permite ao algoritmo de deteção fazer certos pressupostos que melhorarão a precisão da deteção de texto. Também garante que os valores detetados serão devolvidos num formato padronizado na saída final do JSON. As informações do tipo de valor são guardadas no *fields.jsficheiro* no mesmo caminho que os ficheiros da sua etiqueta.

> [!div class="mx-imgBorder"]
> ![Seleção do tipo de valor com ferramenta de rotulagem de amostra](../media/whats-new/formre-value-type.png)

Os seguintes tipos de valor e variações são atualmente suportados:
* `string`
    * padrão, `no-whitespaces` , `alphanumeric`
* `number`
    * padrão, `currency`
* `date` 
    * padrão, `dmy` `mdy` , `ymd`
* `time`
* `integer`
* `selectionMark` – _Novo em v2.1-pré-visualização.1!_

> [!NOTE]
> Consulte estas regras para formatação de data:
> 
> Tem de especificar um formato `dmy` (, `mdy` , , ) para `ymd` a formatação da data para o trabalho.
>
> Os seguintes caracteres podem ser usados como delimiters de data: `, - / . \` . O espaço branco não pode ser usado como um delimiter. Por exemplo:
> * 01,01,2020
> * 01-01-2020
> * 01/01/2020
>
> O dia e o mês podem ser escritos como um ou dois dígitos, e o ano pode ser de dois ou quatro dígitos:
> * 1-1-2020
> * 1-01-20
>
> Se uma cadeia de data tem oito dígitos, o delimiter é opcional:
> * 01012020
> * 01 01 2020
>
> O mês também pode ser escrito como o seu nome completo ou curto. Se o nome for utilizado, os caracteres delimiter são opcionais. No entanto, este formato pode ser reconhecido com menos precisão do que outros.
> * 01/Jan/2020
> * 01Jan2020
> * 01 Jan 2020

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Clique no ícone Train no painel esquerdo para abrir a página De Treino. Em seguida, clique no botão **Train** para começar a treinar o modelo. Assim que o processo de treino estiver concluído, verá as seguintes informações:

* **ID modelo** - O ID do modelo que foi criado e treinado. Cada chamada de treino cria um novo modelo com a sua própria identificação. Copie esta cadeia para um local seguro; você vai precisar se você quiser fazer chamadas de previsão através da [API REST](./client-library.md?pivots=programming-language-rest-api) ou [biblioteca de clientes](./client-library.md).
* **Precisão média** - Precisão média do modelo. Pode melhorar a precisão do modelo rotulando formas adicionais e treinando novamente para criar um novo modelo. Recomendamos começar por rotular cinco formas e adicionar mais formas conforme necessário.
* A lista de etiquetas e a precisão estimada por etiqueta.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Vista de treino.":::

Após o final do treino, examine o valor **de precisão média.** Se for baixo, deve adicionar mais documentos de entrada e repetir os passos acima. Os documentos que já rotulou permanecerão no índice do projeto.

> [!TIP]
> Também pode executar o processo de treino com uma chamada de API REST. Para aprender a fazê-lo, consulte [Train com rótulos usando Python.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

## <a name="compose-trained-models"></a>Compor modelos treinados

# <a name="v20"></a>[v2.0](#tab/v2-0)  

Esta funcionalidade encontra-se atualmente disponível em v2.1. previsualizar. 

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1) 

Com a Composição de Modelo, pode compor até 100 modelos para um único ID de modelo. Quando chama Analisar com este ID de modelo composto, o Reconhecedor de Formato vai, em primeiro lugar, classificar o formulário que submeteu, fazendo-o corresponder ao modelo com a melhor correspondência e, em seguida, devolver os resultados para esse modelo. Tal é útil quando os formulários recebidos possam pertencer a um de vários modelos.

Para compor os modelos na ferramenta de rotulagem da amostra, clique no ícone Model Compose (seta de fusão) à esquerda. À esquerda, selecione os modelos que pretende compor em conjunto. Os modelos com o ícone das setas já são modelos compostos. Clique no botão "Compor". No pop-up, nomeie o seu novo modelo composto e clique em "Compor". Quando a operação estiver concluída, o seu novo modelo composto deverá aparecer na lista. 

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Modelo componha a vista UX.":::

---

## <a name="analyze-a-form"></a>Analisar um formulário 

Clique no ícone Predict (lâmpada) à esquerda para testar o seu modelo. Faça upload de um documento de formulário que não usou no processo de treino. Em seguida, clique no botão **Prever** à direita para obter previsões de chave/valor para o formulário. A ferramenta aplicará etiquetas em caixas de delimitação e reportará a confiança de cada etiqueta.

> [!TIP]
> Também pode executar a API de Análise com uma chamada REST. Para aprender a fazê-lo, consulte [Train com rótulos usando Python.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

## <a name="improve-results"></a>Melhorar os resultados

Dependendo da precisão reportada, pode querer fazer mais treino para melhorar o modelo. Depois de fazer uma previsão, examine os valores de confiança de cada uma das etiquetas aplicadas. Se o valor médio de treino de precisão for elevado, mas as notas de confiança forem baixas (ou os resultados forem imprecisos), deve adicionar o ficheiro utilizado para a previsão no conjunto de treino, rotulá-lo e treinar novamente.

A precisão média reportada, as pontuações de confiança e a precisão real podem ser inconsistentes quando os documentos analisados diferem dos utilizados no treino. Tenha em mente que alguns documentos são semelhantes quando vistos por pessoas, mas podem parecer distintos ao modelo de IA. Por exemplo, pode treinar com um tipo de formulário que tem duas variações, onde o conjunto de formação consiste em variação de 20% A e 80% de variação B. Durante a previsão, as pontuações de confiança para documentos de variação A são suscetíveis de ser menores.

## <a name="save-a-project-and-resume-later"></a>Guarde um projeto e retome mais tarde

Para retomar o seu projeto em outra hora ou em outro navegador, você precisa guardar o token de segurança do seu projeto e reentrá-lo mais tarde. 

### <a name="get-project-credentials"></a>Obtenha credenciais de projeto
Vá à página de definições do projeto (ícone de slider) e tome nota do nome do símbolo de segurança. Em seguida, vá para as definições da sua aplicação (ícone de engrenagem), que mostra todos os tokens de segurança na sua instância atual do navegador. Encontre o símbolo de segurança do seu projeto e copie o seu nome e valor chave para um local seguro.

### <a name="restore-project-credentials"></a>Restaurar credenciais de projeto
Quando pretende retomar o seu projeto, primeiro tem de criar uma ligação ao mesmo recipiente de armazenamento de bolhas. Repita os passos acima para fazer isto. Em seguida, vá à página de definições de aplicação (ícone de engrenagem) e veja se o sinal de segurança do seu projeto está lá. Se não for, adicione um novo token de segurança e copie o nome e a chave do seu token do passo anterior. Em seguida, clique em Guardar Definições. 

### <a name="resume-a-project"></a>Retomar um projeto

Por fim, vá à página principal (ícone da casa) e clique no Open Cloud Project. Em seguida, selecione a ligação de armazenamento de bolhas e selecione o ficheiro *.fott* do seu projeto. A aplicação irá carregar todas as configurações do projeto porque tem o token de segurança.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a ferramenta de rotulagem da amostra do Form Recogniser para treinar um modelo com dados etiquetados manualmente. Se quiser construir a sua própria utilidade para rotular dados de treino, use as APIs REST que lidam com a formação de dados rotulados.

> [!div class="nextstepaction"]
> [Treine com rótulos usando Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [O que é o Reconhecedor de Formato?](../overview.md)
* [Forma Reconhecimentor quickstart](client-library.md)
