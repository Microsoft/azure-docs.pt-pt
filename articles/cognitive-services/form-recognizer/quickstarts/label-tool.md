---
title: 'Quickstart: Rotular formas, treinar um modelo e analisar um formulário usando a ferramenta de rotulagem de amostra - Reconhecimento de Formulário'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' para rotular manualmente documentos de formulário. Em seguida, você vai treinar um modelo personalizado com os documentos etiquetados e usar o modelo para extrair pares chave/valor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 0cfe58ab0d161019d5f53d9135c65db7beff2bb4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398000"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Treine um modelo de reconhecimento de formulário com etiquetas utilizando a ferramenta de rotulagem da amostra

Neste arranque rápido, utilizará a API do Reconhecimento de Formulários REST com a ferramenta de rotulagem de amostras para treinar um modelo personalizado com dados manualmente rotulados. Consulte o Comboio com a secção de [etiquetas](../overview.md#train-with-labels) da visão geral para saber mais sobre esta funcionalidade.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, deve ter:

- Um conjunto de pelo menos seis formas do mesmo tipo. Usará estes dados para treinar o modelo e testar um formulário. Pode utilizar um conjunto de dados de [amostra](https://go.microsoft.com/fwlink/?linkid=2090451) para este arranque rápido. Faça upload dos ficheiros de treino para a raiz de um recipiente de armazenamento blob numa conta de Armazenamento Azure.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Configurar a ferramenta de rotulagem da amostra

Vais usar o motor Docker para executar a ferramenta de rotulagem de amostras. Siga estes passos para montar o recipiente Docker. Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> A Ferramenta de Rotulagem de Formulários OCR também está disponível como um projeto de código aberto no GitHub. A ferramenta é uma aplicação web construída com react + Redux, e está escrita no TypeScript. Para saber mais ou contribuir, consulte a Ferramenta de Rotulagem de [Formulários OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application).

1. Primeiro, instale o Docker num computador de acolhimento. Este guia irá mostrar-lhe como usar o computador local como hospedeiro. Se quiser utilizar um serviço de hospedagem DoEs tacada em Azure, consulte a [ferramenta de rotulagem](../deploy-label-tool.md) de amostra como orientar. 

   O computador anfitrião deve satisfazer os seguintes requisitos de hardware:

    | Contentor | Mínimo | Recomendado|
    |:--|:--|:--|
    |Ferramenta de rotulagem de amostras|2 núcleo, 4-GB de memória|4 núcleos, 8-GB de memória|

   Instale o Docker na sua máquina seguindo as instruções adequadas para o seu sistema operativo: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Obtenha o recipiente da ferramenta `docker pull` de rotulagem da amostra com o comando.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Agora estápronto para correr o `docker run`contentor com.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Este comando disponibilizará a ferramenta de rotulagem da amostra através de um navegador web. Vai [http://localhost:3000](http://localhost:3000)para.

> [!NOTE]
> Também pode rotular documentos e modelos de comboio utilizando a API REST Do Reconhecimento de Formulários. Para treinar e analisar com a REST API, consulte [O Comboio com etiquetas utilizando a REST API e python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Configurar dados de entrada

Primeiro, certifique-se de que todos os documentos de treino são do mesmo formato. Se tiver formulários em vários formatos, organize-os em subpastas com base no formato comum. Quando treinar, terá de direcionar a API para uma subpasta.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configurar a partilha de recursos de domínio transversal (CORS)

Ative o CORS na sua conta de armazenamento. Selecione a sua conta de armazenamento no portal Azure e clique no separador **CORS** no painel esquerdo. Na linha de fundo, preencha os seguintes valores. Em seguida, clique em **Guardar** na parte superior.

* Origens permitidas = * 
* Métodos \[permitidos = selecionar todos\]
* Cabeçalhos permitidos = *
* Cabeçalhos expostos = * 
* Idade máxima = 200

> [!div class="mx-imgBorder"]
> ![Conjunto CORS no portal Azure](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Ligue-se à ferramenta de rotulagem da amostra

A ferramenta de rotulagem da amostra liga-se a uma fonte (onde estão as suas formas originais) e a um alvo (onde exporta os rótulos criados e os dados de saída).

As ligações podem ser configuradas e partilhadas entre projetos. Utilizam um modelo extensível de fornecedor, para que possa facilmente adicionar novos fornecedores de origem/alvo.

Para criar uma nova ligação, clique no ícone **New Connections** (plug) na barra de navegação esquerda.

Preencha os campos com os seguintes valores:

* Nome do **mostrador** - O nome do display de ligação.
* **Descrição** - Descrição do seu projeto.
* **URL SAS** - O URL de assinatura de acesso partilhado (SAS) do seu recipiente de armazenamento Azure Blob. Para recuperar o URL SAS, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Detete o tempo de validade para algum tempo depois de ter usado o serviço. Certifique-se de que as permissões **de Leitura,** **Escrita,** **Apagar**e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .

![Definições de ligação da ferramenta de rotulagem da amostra](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Criar um novo projeto

Na ferramenta de rotulagem de amostras, os projetos armazenam as suas configurações e configurações. Criar um novo projeto e preencher os campos com os seguintes valores:

* **Nome** do ecrã - o nome do display do projeto
* **Security Token** - Algumas configurações do projeto podem incluir valores sensíveis, tais como chaves API ou outros segredos partilhados. Cada projeto gerará um token de segurança que pode ser usado para encriptar/desencriptar configurações sensíveis do projeto. Pode encontrar fichas de segurança nas Definições de Aplicação clicando no ícone da engrenagem no canto inferior da barra de navegação esquerda.
* **Source Connection** - A ligação de armazenamento De Blob Azure que criou no passo anterior que gostaria de utilizar para este projeto.
* **Caminho da Pasta** - Opcional - Se os formulários de origem estiverem localizados numa pasta no recipiente blob, especifique o nome da pasta aqui
* Serviço de Reconhecimento de **FormulárioS Uri** - Url final do ponto final do reconhecimento de formulários.
* **Chave API** - Chave de subscrição do Reconhecimento de Formulários.
* **Descrição** - Opcional - Descrição do projeto

![Nova página do projeto na ferramenta de rotulagem de amostras](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Rotule os seus formulários

Quando cria ou abre um projeto, abre-se a janela do editor principal. O editor de etiquetas é composto por três partes:

* Um painel de pré-visualização resizável que contém uma lista de formulários permutáveis da ligação de origem.
* O painel principal do editor que lhe permite aplicar etiquetas.
* O painel do editor de tags que permite aos utilizadores modificar, bloquear, reencomendar e apagar etiquetas. 

### <a name="identify-text-elements"></a>Identificar elementos de texto

Clique **em Executar OCR em todos os ficheiros** do painel esquerdo para obter as informações de layout de texto para cada documento. A ferramenta de rotulagem desenhará caixas de delimitação em torno de cada elemento de texto.

### <a name="apply-labels-to-text"></a>Aplicar etiquetas ao texto

Em seguida, irá criar tags (etiquetas) e aplicá-las nos elementos de texto que pretende que o modelo reconheça.

1. Primeiro, use o painel de editor de etiquetas para criar as etiquetas que gostaria de identificar.
  1. Clique **+** para criar uma nova etiqueta.
  1. Insira o nome da etiqueta.
  1. Pressione Entrar para guardar a etiqueta.
1. No editor principal, clique e arraste para selecionar uma ou várias palavras dos elementos de texto destacados.
1. Clique na etiqueta que pretende aplicar ou prima a tecla de teclado correspondente. As teclas de número são atribuídas como chaves de acesso para as primeiras 10 etiquetas. Pode reencomendar as suas etiquetas utilizando os ícones de seta para cima e para baixo no painel do editor de etiquetas.
    > [!Tip]
    > Tenha em mente as seguintes dicas quando estiver a rotular os formulários.
    > * Só é possível aplicar uma etiqueta a cada elemento de texto selecionado.
    > * Cada etiqueta só pode ser aplicada uma vez por página. Se um valor aparecer várias vezes na mesma forma, crie etiquetas diferentes para cada instância. Por exemplo: "fatura nº 1", "fatura# 2" e assim por diante.
    > * As etiquetas não podem estender-se por páginas.
    > * Valores de etiqueta à medida que aparecem no formulário; não tente dividir um valor em duas partes com duas etiquetas diferentes. Por exemplo, um campo de endereços deve ser rotulado com uma única etiqueta, mesmo que se estem por várias linhas.
    > * Não inclua chaves nos seus&mdash;campos marcados apenas os valores.
    > * Os dados da tabela devem ser detetados automaticamente e estarão disponíveis no ficheiro JSON de saída final. No entanto, se o modelo não detetar todos os dados da sua tabela, também pode marcar manualmente estes campos. Marque cada célula na mesa com um rótulo diferente. Se os seus formulários tiverem tabelas com um número variado de linhas, certifique-se de que marca pelo menos um formulário com a maior tabela possível.


Siga os passos acima para rotular cinco dos seus formulários e, em seguida, passe para o passo seguinte.

![Janela principal do editor da ferramenta de rotulagem de amostra](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Clique no ícone do comboio (o vagão de comboio) no painel esquerdo para abrir a página de Treino. Em seguida, clique no botão **Train** para começar a treinar o modelo. Assim que o processo de treino estiver concluído, verá as seguintes informações:

* **Id** modelo - A identificação do modelo que foi criado e treinado. Cada chamada de treino cria um novo modelo com a sua própria identificação. Copiar esta cadeia para um local seguro; Vai precisar se quiser fazer chamadas de previsão através da API REST.
* **Precisão média** - A precisão média do modelo. Pode melhorar a precisão do modelo rotulando novamente formas adicionais e treino para criar um novo modelo. Recomendamos começar por rotular cinco formulários e adicionar mais formulários, se necessário.
* A lista de etiquetas e a precisão estimada por etiqueta.

![visão de formação](../media/label-tool/train-screen.png)

Após o treino terminar, examine o valor de **precisão média.** Se for baixo, deve adicionar mais documentos de entrada e repetir os passos acima. Os documentos que já rotulou permanecerão no índice do projeto.

> [!TIP]
> Você também pode executar o processo de treino com uma chamada REST API. Para aprender a fazer isto, consulte [O Comboio com rótulos usando Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analisar um formulário

Clique no ícone Previsão (retângulos) à esquerda para testar o seu modelo. Faça upload de um documento de formulário que não usou no processo de treino. Em seguida, clique no botão **Previsão** à direita para obter previsões de chave/valor para o formulário. A ferramenta aplicará etiquetas em caixas de delimitação e reportará a confiança de cada etiqueta.

> [!TIP]
> Também pode executar a API de análise com uma chamada REST. Para aprender a fazer isto, consulte [O Comboio com rótulos usando Python](./python-labeled-data.md).

## <a name="improve-results"></a>Melhorar os resultados

Dependendo da precisão reportada, poderá querer fazer mais treino para melhorar o modelo. Depois de fazer uma previsão, examine os valores de confiança de cada uma das etiquetas aplicadas. Se o valor médio de treino de precisão foi elevado, mas as pontuações de confiança são baixas (ou os resultados são imprecisos), deve adicionar o ficheiro utilizado para a previsão no conjunto de treino, rotulá-lo e treinar novamente.

A precisão média reportada, as pontuações de confiança e a precisão real podem ser inconsistentes quando os documentos analisados diferem dos utilizados no treino. Tenha em mente que alguns documentos são semelhantes quando vistos por pessoas, mas podem parecer distintos do modelo de IA. Por exemplo, você pode treinar com um tipo de formulário que tem duas variações, onde o conjunto de formação consiste em variação A de 20% e 80% de variação B. Durante a previsão, as pontuações de confiança para documentos de variação A são suscetíveis de ser mais baixas.

## <a name="save-a-project-and-resume-later"></a>Salve um projeto e retome mais tarde

Para retomar o seu projeto noutra altura ou noutro navegador, precisa de guardar o sinal de segurança do seu projeto e reentrar no mesmo mais tarde. 

### <a name="get-project-credentials"></a>Obtenha credenciais de projeto
Vá à página de definições do seu projeto (ícone deslizante) e tome nota do nome do token de segurança. Em seguida, vá para as definições da sua aplicação (ícone de engrenagem), que mostra todas as fichas de segurança na sua instância atual do navegador. Encontre o símbolo de segurança do seu projeto e copie o seu nome e valor chave para uma localização segura.

### <a name="restore-project-credentials"></a>Restaurar as credenciais do projeto
Quando pretende retomar o seu projeto, primeiro é necessário criar uma ligação ao mesmo recipiente de armazenamento de bolhas. Repita os passos acima para fazer isto. Em seguida, vá à página de definições de aplicação (ícone de engrenagem) e veja se o sinal de segurança do seu projeto está lá. Se não for, adicione um novo símbolo de segurança e copie o seu nome simbólico e a chave do passo anterior. Em seguida, clique em Guardar Definições. 

### <a name="resume-a-project"></a>Retomar um projeto

Por fim, vá à página principal (ícone da casa) e clique no Open Cloud Project. Em seguida, selecione a ligação de armazenamento blob e selecione o ficheiro *.vott* do seu projeto. A aplicação irá carregar todas as configurações do projeto porque tem o símbolo de segurança.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a ferramenta de rotulagem da amostra 'Reconhecimento de Formulários' para treinar um modelo com dados manualmente rotulados. Se quiser integrar a ferramenta de rotulagem na sua própria aplicação, utilize as APIs REST que lidam com a formação de dados etiquetada.

> [!div class="nextstepaction"]
> [Treine com rótulos usando Python](./python-labeled-data.md)
