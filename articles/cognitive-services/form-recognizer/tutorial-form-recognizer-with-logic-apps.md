---
title: 'Tutorial: Use reconhecimento de formulário com aplicações lógicas azure para analisar faturas - Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usará O Reconhecimento de Formulário scom Apps Lógicas Azure para criar um fluxo de trabalho que automatiza o processo de formação de um modelo e testá-lo usando dados de amostra.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77118265"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Tutorial: Use reconhecimento de formulário com aplicações lógicas azure para analisar faturas

Neste tutorial, cria-se um fluxo de trabalho em Aplicações Lógicas Azure que utiliza o Form Recogniser, um serviço que faz parte da suite Azure Cognitive Services, para extrair dados de faturas. Primeiro treina um modelo de Reconhecimento de Formulário utilizando um conjunto de dados de amostra, e depois testa o modelo noutro conjunto de dados.

Aqui está o que este tutorial cobre:

> [!div class="checklist"]
> * Solicitar acesso ao Reconhecimento de Formulários
> * Criar um recipiente de blob de armazenamento Azure
> * Enviar dados da amostra para o recipiente de blob Azure
> * Criar uma App Lógica Azure
> * Configure a aplicação lógica para usar um recurso 'Reconhecimento de Formulários'
> * Teste o fluxo de trabalho executando a aplicação lógica

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)

## <a name="understand-the-invoice-to-be-analyzed"></a>Compreender a fatura a analisar

O conjunto de dados da amostra que utilizará para treinar e testar o modelo está disponível como um ficheiro .zip do [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Descarregue e extraio o ficheiro .zip e abra um ficheiro PDF de fatura sob a pasta **/Train.** Note que tem uma tabela com o número da fatura, data da fatura, e assim por diante. 

> [!div class="mx-imgBorder"]
> ![Fatura da amostra](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Neste tutorial, você vai aprender a usar um fluxo de trabalho de Aplicações Azure Logic para extrair a informação de tabelas como estas em formato JSON.

## <a name="create-an-azure-storage-blob-container"></a>Criar um recipiente de blob de armazenamento Azure

Utiliza este recipiente para carregar dados da amostra que são necessários para treinar o modelo.

1. Siga as instruções em [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md) para criar uma conta de armazenamento. Utilize **o formrecostorage** como nome da conta de armazenamento.
1. Siga as instruções em [Criar um recipiente de blob Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) para criar um recipiente dentro da conta de Armazenamento Azure. Utilize **o formrecocontainer** como nome do recipiente. Certifique-se de que define o nível de acesso público ao **Contentor (acesso de leitura anónimo para contentores e bolhas)**.

    > [!div class="mx-imgBorder"]
    > ![Criar contentor de blobs](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Enviar dados da amostra para o recipiente de blob Azure

Faça o download dos dados da amostra disponíveis no [GitHub.](https://go.microsoft.com/fwlink/?linkid=2090451) Extraios os dados para uma pasta local e faça upload do conteúdo da pasta **/Train** para o **formrecocontainer** que criou anteriormente. Siga as instruções no [Upload de uma bolha](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) de bloco para fazer o upload dos dados para um recipiente.

Copie o URL do recipiente. Vai precisar desta URL mais tarde no tutorial. Se criou a conta de armazenamento e o recipiente com os mesmos nomes indicados neste tutorial, o URL será *\/https: /formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

Pode utilizar aplicativos azure logic para automatizar e orquestrar tarefas e fluxos de trabalho. Neste tutorial, cria-se uma aplicação lógica que é desencadeada por receber uma fatura que pretende analisar como um anexo de e-mail. Neste fluxo de trabalho, executa as seguintes tarefas:
* Configure a aplicação lógica para disparar automaticamente quando receber um e-mail com uma fatura anexada.
* Configure a aplicação lógica para utilizar uma operação do Form Recogniser **Train Model** para treinar um modelo utilizando os dados da amostra que carregou para o armazenamento de blob Azure.
* Configure a aplicação lógica para utilizar uma operação de Formulário Reconheço **analisar formulários** para utilizar o modelo que já treinou. Este componente irá analisar a fatura que fornece a esta aplicação lógica com base no modelo que treinou anteriormente.

Siga estes passos para configurar o seu fluxo de trabalho.

1. A partir do menu principal do Azure, selecione Criar uma**Aplicação Lógica**de**Integração** > de **Recursos.** > 

1. Em **Criar aplicação lógica**, forneça detalhes sobre a sua aplicação lógica, conforme aqui apresentado. Depois de terminar, selecione **Criar**.

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*lógica-app-nome*> | O nome da sua aplicação lógica, que pode`-`conter apenas letras, números, hífenes ,`_`sublinha ( ), parênteses (`(`), `)`e períodos (`.`). Este exemplo usa "My-First-Logic-App". |
   | **Subscrição** | <*Nome de assinatura Azure*> | O seu nome de subscrição Azure |
   | **Grupo de recursos** | <*Nome do grupo azure-recursos*> | O nome do grupo de [recursos Azure](./../../azure-resource-manager/management/overview.md) usado para organizar recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Localização** | <*Região azul*> | A região onde armazenar informações de aplicações lógicas. Este exemplo usa "West US". |
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Depois de o Azure implementar a sua aplicação, na barra de ferramentas Azure, selecione **Notificações** > **Para recorrer para** a sua aplicação lógica implementada. Ou, pode encontrar e selecionar a sua aplicação lógica digitando o nome na caixa de pesquisa.

   O Estruturador da Aplicação Lógica é aberto e mostra uma página com um vídeo de introdução e os acionadores habitualmente utilizados. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   > [!div class="mx-imgBorder"]
   > ![Selecione modelo em branco para aplicação lógica](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Configure a aplicação lógica para desencadear o fluxo de trabalho quando um e-mail chegar

Neste tutorial, você despoleta o fluxo de trabalho quando um e-mail é recebido com uma fatura anexada. Este tutorial utiliza o Office 365 como serviço de e-mail, mas pode utilizar qualquer outro fornecedor de e-mail que deseje utilizar.

1. A partir dos separadores, selecione All, selecione **Office 365 Outlook**, e depois sob **Triggers**, selecione **Quando um novo e-mail chegar**.

    ![Trigger aplicativo lógico através de um e-mail de entrada](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Na caixa **do Office 365 Outlook,** clique **em Iniciar sessão**e insira os detalhes para iniciar sessão numa conta do Office 365.

1. Na caixa de diálogo seguinte, execute os seguintes passos.
    1. Selecione a pasta que deve ser monitorizada para qualquer novo e-mail.
    1. Para **ter anexos,** selecione **Sim**. Isto garante que apenas os e-mails com anexos desencadeiam o fluxo de trabalho.
    1. Para **incluir anexos,** selecione **Sim**. Isto garante que o conteúdo do acessório é utilizado no processamento a jusante.

        > [!div class="mx-imgBorder"]
        > ![Configure o gatilho de e-mail da aplicação lógica](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Clique em **Guardar** a partir da barra de ferramentas na parte superior.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Configure a aplicação lógica para utilizar a operação do Modelo de Comboio do Reconhecimento de Formulários

Antes de poder utilizar o serviço 'Reconhecimento de Formulários' para analisar faturas, é necessário formar um modelo, fornecendo-lhe algumas faturas de amostra que o modelo pode analisar e aprender.

1. Selecione **Novo passo**, e sob **o comando Escolha uma ação,** procure um Reconhecimento de **Formulários**. A partir dos resultados que aparecem, selecione Reconhecimento de **Formulários,** e depois sob as ações disponíveis para Reconhecimento de Formulários, selecione **Modelo de Comboio**.

    > [!div class="mx-imgBorder"]
    > ![Treine um modelo de reconhecimento de formulário](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Na caixa de diálogo 'Reconhecimento de Formulários', forneça um nome para a ligação e introduza o URL do ponto final e a chave que recuperou para o recurso 'Reconhecimento de Formulários'.

    > [!div class="mx-imgBorder"]
    > ![Nome de ligação para Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Clique em **Criar**.

1. Na caixa de diálogo **Modelo do Comboio,** para **Fonte,** introduza o URL para o recipiente onde carregou os dados da amostra.

    > [!div class="mx-imgBorder"]
    > ![Recipiente de armazenamento para faturas de amostra](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Clique em **Guardar** a partir da barra de ferramentas na parte superior.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Configure a aplicação lógica para usar a operação Formulário de Formulário de Reconhecimento de Formulários

Nesta secção, adicione a operação **'Forma de Análise'** ao fluxo de trabalho. Esta operação utiliza o modelo já treinado para analisar uma nova fatura que é fornecida à aplicação lógica.

1. Selecione **Novo passo**, e sob **o comando Escolha uma ação,** procure um Reconhecimento de **Formulários**. A partir dos resultados que aparecem, selecione Reconhecimento de **Formulários,** e depois sob as ações disponíveis para Reconhecimento de Formulários, selecione **Formulário de Análise**.

    > [!div class="mx-imgBorder"]
    > ![Analisar um modelo de reconhecimento de formulário](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Na caixa de diálogo **'Formulário de Análise',** faça os seguintes passos:

    1. Clique na caixa de texto **Model ID** e na caixa de diálogo que se abre, sob o separador **Conteúdo Dinâmico,** selecione **modelId**. Ao fazê-lo, fornece a aplicação de fluxo com o modelo ID do modelo que treinou na última secção.

        > [!div class="mx-imgBorder"]
        > ![Utilize o ModelID para Reconhecimento de Formulários](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Clique na caixa de texto **'Documento'** e na caixa de diálogo que se abre, sob o separador **Conteúdo Dinâmico,** selecione Conteúdo de **Anexos**. Isto confunde o fluxo para utilizar o ficheiro de fatura da amostra que está anexado no e-mail que desencadeia o fluxo de trabalho.

        > [!div class="mx-imgBorder"]
        > ![Utilize o anexo de e-mail para analisar faturas](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Clique em **Guardar** a partir da barra de ferramentas na parte superior.

### <a name="extract-the-table-information-from-the-invoice"></a>Extrair as informações da tabela da fatura

Nesta secção, configura a aplicação lógica para extrair a informação da tabela dentro das faturas.

1. Selecione **Adicionar uma ação**, e sob o comando Escolha uma **ação,** procure **compor** e sob as ações disponíveis, selecione **Compor** novamente.
    ![Extrair informações da tabela da fatura](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Na caixa de diálogo **Compor,** clique na caixa de texto **Inputs** e na caixa de diálogo que aparece, selecione **tabelas**.

    > [!div class="mx-imgBorder"]
    > ![Extrair informações da tabela da fatura](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Clique em **Guardar**.

## <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para testar a aplicação lógica, utilize as faturas da amostra na pasta **/Teste** do conjunto de dados da amostra que descarregou do [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Siga estes passos.

1. Do designer de Aplicações Azure Logic para a sua aplicação, selecione **Run** a partir da barra de ferramentas no topo. O fluxo de trabalho está agora ativo e aguarda para receber um e-mail com a fatura anexada.
1. Envie um e-mail com uma fatura de amostra anexada ao endereço de e-mail que forneceu enquanto cria a aplicação lógica. Certifique-se de que o e-mail é entregue na pasta que forneceu enquanto configura a aplicação lógica.
1. Assim que o e-mail é entregue na pasta, o Logic Apps Designer mostra um ecrã com o progresso de cada etapa. Na imagem abaixo, você vê que um e-mail com anexo é recebido e o fluxo de trabalho está em andamento.

    > [!div class="mx-imgBorder"]
    > ![Inicie o fluxo de trabalho enviando um e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Depois de todas as fases do fluxo de trabalho terem terminado de funcionar, o Logic Apps Designer mostra uma caixa de verificação verde contra todas as etapas. Na janela do designer, selecione **Para cada 2**, e, em seguida, selecione **Compor**.

    > [!div class="mx-imgBorder"]
    > ![Fluxo de trabalho concluído](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    A partir da caixa **OUTPUTS,** copie a saída e cole-a a qualquer editor de texto.

1. Compare a saída JSON com a fatura da amostra que enviou como anexo no e-mail. Verifique se os dados da JSON correspondem aos dados do quadro dentro da fatura.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Completou com sucesso este tutorial!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um fluxo de trabalho de Aplicações Lógicas Azure para usar o Reconhecimento de Formulário para treinar um modelo e extrair o conteúdo de uma fatura. Em seguida, aprenda a construir um conjunto de dados de treino para que possa criar um cenário semelhante com as suas próprias formas.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](build-training-data-set.md)