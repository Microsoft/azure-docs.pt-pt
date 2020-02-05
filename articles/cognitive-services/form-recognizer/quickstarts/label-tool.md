---
title: 'Início rápido: rotular formulários, treinar um modelo e analisar um formulário usando a ferramenta de rótulo de exemplo – reconhecedor de formulário'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a ferramenta de rotulagem de exemplo do reconhecedor de formulário para rotular manualmente os documentos de formulário. Em seguida, você treinará um modelo personalizado com os documentos rotulados e usará o modelo para extrair pares de chave/valor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 158faaba1525e162c40c44179f30f7c3cea83b38
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025916"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Treinar um modelo de reconhecimento de formulário com rótulos usando a ferramenta de rotulagem de exemplo

Neste guia de início rápido, você usará a API REST do reconhecedor de formulário com a ferramenta de rotulagem de exemplo para treinar um modelo personalizado com dados rotulados manualmente. Consulte a seção [treinar com rótulos](../overview.md#train-with-labels) da visão geral para saber mais sobre esse recurso.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o [formulário solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess). Você receberá um email com um link para criar um recurso de reconhecimento de formulário.
- Um conjunto de pelo menos seis formas do mesmo tipo. Você usará esses dados para treinar o modelo e testar um formulário. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este guia de início rápido. Carregue os arquivos de treinamento na raiz de um contêiner de armazenamento de BLOBs em uma conta de armazenamento do Azure.

## <a name="set-up-the-sample-labeling-tool"></a>Configurar a ferramenta de rótulo de exemplo

Você usará o mecanismo do Docker para executar a ferramenta de rotulagem de exemplo. Siga estas etapas para configurar o contêiner do Docker. Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).
1. Primeiro, instale o Docker em um computador host. O computador host pode ser seu computador local ([Windows](https://docs.docker.com/docker-for-windows/), [MacOS](https://docs.docker.com/docker-for-mac/)ou [Linux](https://docs.docker.com/install/)). Ou, você pode usar um serviço de hospedagem do Docker no Azure, como o [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/index), as [instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/index)ou um cluster kubernetes [implantado em um Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910). O computador host deve atender aos seguintes requisitos de hardware:

    | Contentor | Mínimo | Recomendado|
    |:--|:--|:--|
    |Ferramenta de rótulo de exemplo|2 núcleos, 4 GB de memória|4 núcleos, 8 GB de memória|
    
1. Obtenha o contêiner de ferramentas de rótulo de exemplo com o comando `docker pull`.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Agora você está pronto para executar o contêiner com `docker run`.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Esse comando disponibilizará a ferramenta de rotulagem de exemplo por meio de um navegador da Web. Vá para [http://localhost:3000](http://localhost:3000).

> [!NOTE]
> Você também pode rotular documentos e treinar modelos usando a API REST do reconhecedor de formulário. Para treinar e analisar com a API REST, consulte [treinar com rótulos usando a API REST e o Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Configurar dados de entrada

Primeiro, verifique se todos os documentos de treinamento têm o mesmo formato. Se você tiver formulários em vários formatos, organize-os em subpastas com base no formato comum. Ao treinar, você precisará direcionar a API para uma subpasta.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configurar o CORS (compartilhamento de recursos entre domínios)

Habilite o CORS em sua conta de armazenamento. Selecione sua conta de armazenamento no portal do Azure e clique na guia **CORS** no painel esquerdo. Na linha inferior, preencha os valores a seguir. Em seguida, clique em **salvar** na parte superior.

* Origens permitidas = * 
* Métodos permitidos = \[selecionar todos os\]
* Cabeçalhos permitidos = *
* Cabeçalhos expostos = * 
* Idade máxima = 200

> [!div class="mx-imgBorder"]
> ![a instalação cors no portal Azure](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Conectar-se à ferramenta de rótulo de exemplo

A ferramenta de rotulagem de exemplo conecta-se a uma fonte (onde os formulários originais são) e um destino (o local onde ele exporta os rótulos criados e os dados de saída).

As conexões podem ser configuradas e compartilhadas entre projetos. Eles usam um modelo de provedor extensível para que você possa adicionar facilmente novos provedores de origem/destino.

Para criar uma nova conexão, clique no ícone **novas conexões** (plug), na barra de navegação à esquerda.

Preencha os campos com os seguintes valores:

* **Nome de exibição** -o nome de exibição da conexão.
* **Descrição** -a descrição do seu projeto.
* **URL SAS** -a URL de SAS (assinatura de acesso compartilhado) do seu contêiner de armazenamento de BLOBs do Azure. Para recuperar a URL SAS, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**. Defina o tempo de expiração para algum tempo depois de ter usado o serviço. Verifique se as permissões de **leitura**, **gravação**, **exclusão**e **lista** estão marcadas e clique em **criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Configurações de conexão da ferramenta de rótulo de exemplo](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Criar um novo projeto

Na ferramenta de rótulo de exemplo, os projetos armazenam suas configurações e configurações. Crie um novo projeto e preencha os campos com os seguintes valores:

* **Nome de exibição** -o nome de exibição do projeto
* **Token de segurança** -algumas configurações de projeto podem incluir valores confidenciais, como chaves de API ou outros segredos compartilhados. Cada projeto irá gerar um token de segurança que pode ser usado para criptografar/descriptografar configurações de projeto confidenciais. Os tokens de segurança podem ser encontrados nas configurações do aplicativo clicando no ícone de engrenagem no canto inferior da barra de navegação à esquerda.
* **Conexão de origem** -a conexão de armazenamento de BLOBs do Azure que você criou na etapa anterior que você deseja usar para este projeto.
* **Caminho da pasta** -opcional – se os formulários de origem estiverem localizados em uma pasta no contêiner de BLOB, especifique o nome da pasta aqui
* **URI do serviço reconhecedor de formulário** -URL do ponto de extremidade do reconhecedor de formulário.
* **Chave de API** -sua chave de assinatura do reconhecedor de formulário.
* **Descrição** -opcional-descrição do projeto

![Página novo projeto na ferramenta de rótulo de exemplo](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Rotular seus formulários

Quando você cria ou abre um projeto, a janela principal do editor de marca é aberta. O editor de marca consiste em três partes:

* Um painel de visualização redimensionável que contém uma lista rolável de formulários da conexão de origem.
* O painel principal do editor que permite aplicar marcas.
* O painel do editor de marcas que permite aos usuários modificar, bloquear, reordenar e excluir marcas. 

### <a name="identify-text-elements"></a>Identificar elementos de texto

Clique em **executar OCR em todos os arquivos** no painel esquerdo para obter as informações de layout de texto de cada documento. A ferramenta de rotulagem desenhará caixas delimitadoras em volta de cada elemento de texto.

### <a name="apply-labels-to-text"></a>Aplicar rótulos ao texto

Em seguida, você criará rótulos e os aplicará aos elementos de texto que deseja que o modelo reconheça.

1. Primeiro, use o painel editor de marcas para criar as marcas (rótulos) que você deseja identificar.
1. No editor principal, clique e arraste para selecionar uma ou várias palavras dos elementos de texto realçados.

    > [!NOTE]
    > No momento, não é possível selecionar texto que se estenda por várias páginas.
1. Clique na marca que você deseja aplicar ou pressione a tecla de teclado correspondente. Você só pode aplicar uma marca a cada elemento de texto selecionado e cada marca só pode ser aplicada uma vez por página.

    > [!TIP]
    > As chaves de número são atribuídas como teclas de pressionamento para as dez primeiras marcas. Você pode reordenar suas marcas usando os ícones de seta para cima e para baixo no painel do editor de marca.

Siga as etapas acima para rotular cinco de seus formulários e, em seguida, passe para a próxima etapa.

![Janela principal do editor da ferramenta de rótulo de exemplo](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Clique no ícone de treinamento (o carro de treinamento) no painel esquerdo para abrir a página de treinamento. Em seguida, clique no botão **treinar** para começar a treinar o modelo. Quando o processo de treinamento for concluído, você verá as seguintes informações:

* **ID do modelo** -a ID do modelo que foi criado e treinado. Cada chamada de treinamento cria um novo modelo com sua própria ID. Copiar esta cadeia de caracteres para um local seguro; Você precisará dela se quiser fazer chamadas de previsão por meio da API REST.
* **Precisão média** -a precisão média do modelo. Você pode melhorar a precisão do modelo ao rotular formulários adicionais e treinar novamente para criar um novo modelo. É recomendável começar rotulando cinco formulários e adicionando mais formulários, conforme necessário.
* A lista de marcas e a precisão estimada por marca.

![exibição de treinamento](../media/label-tool/train-screen.png)

Após a conclusão do treinamento, examine o valor de **precisão médio** . Se ele estiver baixo, você deverá adicionar mais documentos de entrada e repetir as etapas acima. Os documentos que você já rotulado permanecerão no índice do projeto.

> [!TIP]
> Você também pode executar o processo de treinamento com uma chamada à API REST. Para saber como fazer isso, consulte [treinar com rótulos usando o Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analisar um formulário

Clique no ícone prever (retângulos) à esquerda para testar seu modelo. Carregue um documento de formulário que você não usou no processo de treinamento. Em seguida, clique no botão **prever** à direita para obter previsões de chave/valor para o formulário. A ferramenta aplicará marcas nas caixas delimitadoras e relatará a confiança de cada marca.

> [!TIP]
> Você também pode executar a API de análise com uma chamada REST. Para saber como fazer isso, consulte [treinar com rótulos usando o Python](./python-labeled-data.md).

## <a name="improve-results"></a>Melhorar os resultados

Dependendo da precisão relatada, talvez você queira mais treinamento para melhorar o modelo. Depois de fazer uma previsão, examine os valores de confiança para cada uma das marcas aplicadas. Se o valor de treinamento de precisão média era alto, mas as pontuações de confiança são baixas (ou os resultados são imprecisos), você deve adicionar o arquivo usado para previsão no conjunto de treinamento, rotulá-lo e treinar novamente.

A precisão média relatada, as pontuações de confiança e a precisão real podem ser inconsistentes quando os documentos que estão sendo analisados são diferentes daqueles usados no treinamento. Tenha em mente que alguns documentos parecem semelhantes quando exibidos por pessoas, mas podem parecer distintos para o modelo de ia. Por exemplo, você pode treinar com um tipo de formulário que tem duas variações, onde o conjunto de formação consiste em variação A de 20% e 80% de variação B. Durante a previsão, as pontuações de confiança para documentos de variação A são suscetíveis de ser mais baixas.

## <a name="save-a-project-and-resume-later"></a>Salvar um projeto e retomá-lo mais tarde

Para retomar seu projeto em outro momento ou em outro navegador, você precisa salvar o token de segurança do projeto e reinseri-lo mais tarde. 

### <a name="get-project-credentials"></a>Obter credenciais do projeto
Vá para a página de configurações do projeto (ícone do controle deslizante) e anote o nome do token de segurança. Em seguida, vá para as configurações do aplicativo (ícone de engrenagem), que mostra todos os tokens de segurança em sua instância atual do navegador. Localize o token de segurança do seu projeto e copie seu nome e valor de chave para um local seguro.

### <a name="restore-project-credentials"></a>Restaurar credenciais do projeto
Quando desejar retomar seu projeto, primeiro você precisará criar uma conexão com o mesmo contêiner de armazenamento de BLOBs. Siga as etapas acima para fazer isso. Em seguida, vá para a página Configurações do aplicativo (ícone de engrenagem) e veja se o token de segurança do seu projeto está lá. Se não estiver, adicione um novo token de segurança e copie sobre o nome do token e a chave da etapa anterior. Em seguida, clique em salvar configurações. 

### <a name="resume-a-project"></a>Retomar um projeto
Por fim, vá para a página principal (ícone da casa) e clique em Abrir projeto de nuvem. Em seguida, selecione a conexão de armazenamento de BLOBs e selecione o arquivo *. vott* do projeto. O aplicativo carregará todas as configurações do projeto, pois ele tem o token de segurança.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar a ferramenta de rotulação de amostra do reconhecedor de formulário para treinar um modelo com dados rotulados manualmente. Se você quiser integrar a ferramenta de rotulagem em seu próprio aplicativo, use as APIs REST que lidam com o treinamento de dados rotulado.

> [!div class="nextstepaction"]
> [Treine com rótulos usando o Python](./python-labeled-data.md)
