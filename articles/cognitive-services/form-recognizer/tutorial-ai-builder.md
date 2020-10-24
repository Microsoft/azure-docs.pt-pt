---
title: 'Tutorial: Criar uma aplicação de processamento de formulários com AI Builder - Form Recogniser'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você usará o AI Builder para criar e treinar uma aplicação de processamento de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: 0261c495ad08000f9041390658e0f19954b71669
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517026"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Tutorial: Criar uma app de processamento de formulários com OI Builder

[O AI Builder](https://docs.microsoft.com/ai-builder/overview) é uma capacidade da Power Platform que permite automatizar processos e prever resultados para melhorar o desempenho do negócio. Pode utilizar o processamento de formulários AI Builder para criar modelos de IA que identifiquem e extraam pares de valores-chave e dados de tabela a partir de documentos de formulário.

> [!NOTE]
> Este projeto também está disponível como [módulo Microsoft Learn.](https://docs.microsoft.com/learn/modules/get-started-with-form-processing/)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de IA de processamento de formulários
> * Preparar o seu modelo
> * Publique o seu modelo para usar em Azure Power Apps ou Power Automate

## <a name="prerequisites"></a>Pré-requisitos

* Um conjunto de, pelo menos, cinco formas do mesmo tipo a utilizar para os dados de treino/teste. Consulte [Construir um conjunto de dados de treino](./build-training-data-set.md) para obter dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2128080)da amostra .
* Uma licença power apps ou power automate - consulte o [Guia de Licenciamento](https://go.microsoft.com/fwlink/?linkid=2085130). A licença deve incluir [o Serviço Comum de Dados.](https://powerplatform.microsoft.com/en-us/common-data-service/)
* Um [addon ou teste](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)do AI Builder .


## <a name="create-a-form-processing-project"></a>Criar um projeto de processamento de formulários

1. Vá a [Power Apps](https://make.powerapps.com/) ou [Power Automamate](https://flow.microsoft.com/signin), e inscreva-se na sua conta da organização.
1. No painel esquerdo, selecione **AI Builder**  >  **Build**.
1. Selecione o cartão **de processamento de** formulários.
1. Escreva um nome para o seu modelo.
1. Selecione **Criar**.

## <a name="upload-and-analyze-documents"></a>Carregar e analisar documentos

Na página **de documentos Adicionar,** precisa de fornecer documentos de amostra para treinar o seu modelo para o tipo de formulário de onde pretende extrair informações. Depois de fazer o upload dos seus documentos, o AI Builder analisa-os para verificar se são suficientes para treinar um modelo.

> [!NOTE]
> Atualmente, o AI Builder não suporta os seguintes tipos de dados de entrada de processamento de formulários:
>
> - Tabelas complexas (tabelas aninhadas, cabeçalhos ou células unidas, etc.)
> - Caixas de verificação ou botões de opção
> - Documentos PDF com mais de 50 páginas
> - PDFs preenchíveis
>
> Para obter mais informações sobre os requisitos para os documentos de entrada, consulte [os requisitos de entrada](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Carregar os documentos

1. Selecione **Adicionar documentos**, selecione um mínimo de cinco documentos e, em seguida, selecione **Carregar**.
1. Depois de concluído o upload, selecione **Close**.
1. Em seguida, **selecione Analisar**.

> [!NOTE] 
> Depois de carregar estes documentos, ainda pode remover alguns documentos ou carregar documentos adicionais.

> [!div class="mx-imgBorder"]
> ![adicionar página de documentos](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analisar os documentos

Durante o passo de análise, o AI Builder examina os documentos que carregou e deteta os campos e tabelas. O tempo que leva para concluir esta operação depende do número de documentos fornecidos, mas na maioria dos casos deve levar apenas alguns minutos.

Quando a análise terminar, selecione a miniatura para abrir a experiência de seleção de campo.

> [!IMPORTANT]
> Se a análise falhou, é provável que porque o Construtor de IA não conseguiu detetar texto estruturado nos seus documentos. Verifique se os documentos atualizados seguem os [requisitos de entrada](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Selecionar os campos de formulário

Na página de seleção de campo, você escolhe os campos que lhe interessam:

1. Para selecionar um campo, clique num retângulo que indique um campo detetado no documento ou selecione vários campos clicando e arrastando. Também pode selecionar campos a partir do painel do lado direito.
1. Clique no nome do campo selecionado se quiser renomeá-lo para alinhar com as suas necessidades ou normalizar as etiquetas extraídas.

    Quando clica num campo detetado, aparecem as seguintes informações:

    - **Nome do**campo : O nome da etiqueta para o campo detetado.
    - **Valor do**campo : O valor para o campo detetado.

> [!div class="mx-imgBorder"]
> ![página de marcação](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Rotular campos não detetados

Se o campo que pretende rotular não tiver sido detetado automaticamente pelo modelo, pode desenhar um retângulo em torno do seu valor e escrever um nome de etiqueta no diálogo que aparece.

## <a name="train-your-model"></a>Preparar o seu modelo

1. Selecione **Seguinte** para verificar os campos de formulário selecionados. Se estiver tudo correto, selecione **Preparar** para preparar o modelo.

    > [!div class="mx-imgBorder"]
    > ![página de formação](./media/tutorial-ai-builder/summary-train-page.png)
1. Quando a preparação estiver concluída, selecione **Ir para a página Detalhes** no ecrã **Preparação concluída**.
## <a name="quick-test-your-model"></a>Testar rapidamente o modelo

A página Detalhes permite-lhe testar o modelo antes de o publicar ou utilizar:

1. Na página Detalhes, selecione **Teste rápido**.
2. Pode arrastar e largar um documento ou selecionar **upload do meu dispositivo** para carregar o seu ficheiro de teste. O teste rápido só deverá demorar alguns segundos a apresentar os resultados.
3. Pode selecionar **Recomeçar** para executar outro teste ou **fechar** se estiver acabado.

### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Se estiver a obter maus resultados ou pontuações de baixa confiança para determinados campos, experimente as seguintes dicas:

- Reforçár a utilização de formulários com valores diferentes em cada campo.
- Retreine usando um conjunto maior de documentos de treino. Quanto mais documentos marcar, mais construtor de IA aprenderá a reconhecer melhor os campos.
- Pode otimizar ficheiros PDF selecionando apenas determinadas páginas para treinar. Utilize **Print**a  >  opção Imprimir em**PDF para** selecionar determinadas páginas dentro do seu documento.

## <a name="publish-your-model"></a>Publicar o modelo

Se está satisfeito com o seu modelo, **selecione Publicar**  para publicá-lo. Quando a publicação estiver concluída, o modelo passa a **Publicado** e estará pronto para ser utilizado.

> [!div class="mx-imgBorder"]
> ![publicar página de modelo](./media/tutorial-ai-builder/model-page.png)

Depois de publicar o seu modelo de processamento de formulários, pode usá-lo numa [aplicação](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps) de tela power apps ou em [Power Automamate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Passos seguintes

Siga a documentação do AI Builder para utilizar um modelo de processamento de formulários.

* [Utilize o componente do processador de formulários em Power Apps](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)
* [Utilize um modelo de processamento de formulários em Power Automamate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)