---
title: 'Tutorial: Como criar um modelo de idioma com o serviço de voz'
titlesuffix: Azure Cognitive Services
description: Saiba como criar um modelo de idioma com o Serviço de Voz. Utilize este modelo de idioma personalizado em conjunto com modelos de voz topo de gama existentes da Microsoft para adicionar a interação de voz à sua aplicação.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8276b86df2dc1bc90fc07da262aa0979f7562619
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548494"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutorial: Criar um modelo de linguagem personalizado

Neste documento, vai criar um modelo de linguagem personalizado. Em seguida, pode utilizar este modelo de linguagem personalizado em conjunto com os modelos de voz de última geração existentes da Microsoft para adicionar interação de voz à sua aplicação.

O documento aborda como:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados de linguagem
> * Criar o modelo de linguagem personalizado

Se não tiver uma conta dos Serviços Cognitivos, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para certificar-se de que a conta dos Serviços Cognitivos está associada a uma subscrição, abra a página [Subscrições de Serviços Cognitivos](https://customspeech.ai/Subscriptions).

Para ligar a uma subscrição do Serviço de Voz que foi criada no portal do Azure, selecione o botão **Ligar subscrição existente**.

Para obter informações sobre como criar uma subscrição dos Serviços de Voz no portal do Azure, veja a página [Introdução](get-started.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para criar um modelo de linguagem personalizado para a sua aplicação, terá de apresentar uma lista de expressões de exemplo ao sistema, por exemplo:

*   "O paciente teve urticária na semana passada."
*   "O paciente teve uma cicatriz de uma herniorrafia que cicatrizou bem."

As frases não têm de ser completas ou gramaticamente corretas, mas devem refletir com precisão a entrada de voz que espera que o sistema deve encontrar na implementação. Estes exemplos devem refletir o estilo e o conteúdo da tarefa que os utilizadores irão executar com a sua aplicação.

Os dados do modelo de linguagem devem ser gravados no UTF-8 BOM. O ficheiro de texto deve conter um exemplo (frase, expressão ou consulta) por linha.

Se desejar que alguns termos tenham uma ponderação mais elevada (importância), pode adicionar várias expressões aos seus dados que incluem esses termos.

Os requisitos principais para os dados de linguagem estão resumidos na tabela seguinte.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM|
| N.º de expressões por linha | 1 |
| Tamanho máximo do ficheiro | 1,5 GB |
| Observações | Evitar repetir os carateres mais de quatro vezes, por exemplo "aaaaa"|
| Observações | Não existem carateres especiais, como «\t» ou qualquer outro caráter UTF-8 acima de U+00A1 na [tabela de carateres Unicode](https://www.utf8-chartable.de/)|
| Observações | os URIs também serão rejeitados porque não existe uma forma única de pronunciar um URI|

Quando o texto é importado, é texto normalizado para que possa ser processado pelo sistema. No entanto, existem algumas normalizações importantes que devem ser feitas pelo utilizador _antes_ de carregar os dados. Veja as [diretrizes de transcrição](prepare-transcription.md) para determinar a linguagem apropriada para utilizar quando preparar os dados de linguagem.

## <a name="language-support"></a>Suporte de idiomas

Veja a lista completa de [linguagens suportadas](language-support.md#text-to-speech) para os modelos de linguagem **Conversão de Voz em Texto** personalizados.



## <a name="import-the-language-data-set"></a>Importar o conjunto de dados de linguagem

Selecione o botão **Importar** na linha **Conjuntos de Dados da Linguagem** e o site apresenta uma página para carregar um novo conjunto de dados.

Quando estiver pronto para importar o seu conjunto de dados de linguagem, inicie sessão no [portal dos Serviços de Voz](https://customspeech.ai). Primeiro, selecione o menu pendente **Voz Personalizada** no friso superior. Em seguida, selecione **Dados de Adaptação**. Quando tentar carregar dados para o Serviço de Voz pela primeira vez, verá uma tabela vazia chamada **Conjuntos de dados**.

Para importar um novo conjunto de dados, selecione o botão **Importar** na linha **Conjuntos de Dados da Linguagem**. Em seguida, o site apresenta uma página para carregar um novo conjunto de dados. Introduza um **Nome** e uma **Descrição** para ajudar a identificar o conjunto de dados no futuro e, em seguida, escolha o local.

Em seguida, utilize o botão **Escolher ficheiro** para localizar o ficheiro de texto dos dados de linguagem. Depois disso, selecione **Importar** e o conjunto de dados será carregado. Dependendo do tamanho do conjunto de dados, a importação pode demorar vários minutos.

![Experimente](media/stt/speech-language-datasets-import.png)

Quando a importação estiver concluída, os dados de linguagem têm uma entrada que corresponde ao seu conjunto de dados de linguagem. Tenha em atenção que foi atribuído um ID exclusivo (GUID) ao conjunto de dados. Os dados também têm um estado que reflete o estado atual dos mesmos. O estado será **A aguardar** enquanto estiverem a ser colocados em fila para processamento, **A processar** enquanto estiverem a ser submetidos a validação e **Concluído** quando os dados estiverem prontos para utilização. A validação de dados realiza uma série de verificações no texto no ficheiro. E também faz alguma normalização de texto dos dados.

Quando o estado for **Concluído**, pode selecionar **Ver Relatório** para ver o relatório de verificação de dados de linguagem. O número de expressões que passaram e falharam na verificação é apresentado, juntamente com os detalhes sobre as expressões que falharam. No exemplo seguinte, dois exemplos falharam a verificação devido a carateres incorretos. (Neste conjunto de dados, a primeira linha tinha dois carateres de tabulação, a segunda tinha vários carateres que não fazem parte do conjunto de carateres imprimível ASCII e a terceira linha estava em branco).

![Experimente](media/stt/speech-language-datasets-report.png)

Quando o estado do conjunto de dados de linguagem for **Concluído**, pode servir para criar um modelo de linguagem personalizado.

![Experimente](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Criar um modelo de linguagem personalizado

Quando os dados de linguagem estiverem prontos, selecione **Modelos de Linguagem** no menu pendente **Menu** para iniciar o processo de criação do modelo de linguagem personalizado. Esta página contém uma tabela chamada **Modelos de Linguagem** com os seus atuais modelos de linguagem personalizados. Se ainda não tiver criado modelos de linguagem personalizados, a tabela estará vazia. A região atual é mostrada na tabela junto à entrada de dados relevantes.

Tem de selecionar a região apropriada antes de realizar qualquer ação. A região atual é indicada no título da tabela em todas as páginas de dados, modelo e implementação. Para alterar a região, selecione o botão **Alterar a Região** que se encontra sob o título da tabela.  Isto leva-o para uma página de confirmação de região. Selecione **OK** para regressar à tabela.

Na página Criar Modelo de Linguagem, introduza um **Nome** e uma **Descrição** para ajudá-lo a manter-se a par de informações pertinentes sobre este modelo, tal como o conjunto de dados utilizado. Em seguida, selecione **Modelo de Linguagem Base** no menu pendente. Este modelo é o ponto de partida para a sua personalização.

Pode escolher entre dois modelos de linguagem base. O modelo Pesquisa e Ditado é adequado para voz direcionada para uma aplicação, como comandos, consultas de pesquisa ou ditado. O modelo Conversação é adequado para reconhecer voz falada num estilo de conversação. Este tipo de voz é, normalmente, direcionado para outra pessoa e ocorre em centros de atendimento telefónico ou reuniões. Um novo modelo chamado "Universal" também está publicamente disponível. O Universal tem como objetivo lidar com todos os cenários e, eventualmente, substituir os modelos de Pesquisa e Ditado e da Conversação.

Conforme mostrado no exemplo abaixo, depois de especificar o modelo de linguagem base, utilize o menu pendente **Dados de Linguagem** para selecionar o conjunto de dados de linguagem que pretende utilizar para a personalização.

![Experimente](media/stt/speech-language-models-create2.png)

Tal como acontece com a criação de modelos acústicos, pode optar por realizar testes offline do novo modelo quando o processamento estiver concluído. As avaliações de modelo precisam de um conjunto de dados acústico.

Para realizar testes offline do seu modelo de linguagem, selecione a caixa de verificação junto a **Testes Offline**. Em seguida, selecione um modelo acústico no menu pendente. Se ainda não tiver criado modelos acústicos personalizados, o modelo acústico base da Microsoft será o único modelo no menu. Se selecionou um modelo base de LM de conversação, terá de utilizar um modelo AM de conversação aqui. Se utiliza um modelo LM de pesquisa e ditado, terá de selecionar um modelo AM de pesquisa e ditado.

Por fim, selecione o conjunto de dados acústicos que pretende utilizar para realizar a avaliação.

Quando estiver pronto para começar a processar, selecione **Create** (Criar). Em seguida, verá a tabela de modelos de linguagem. Haverá uma nova entrada na tabela correspondente a este modelo. O estado reflete o estado do modelo e irá passar por vários estados, incluindo **Waiting** (A aguardar), **Processing** (A processar) e **Complete** (Concluído).

Quando o modelo chegar ao estado **Complete**, pode ser implementado num ponto final. Selecionar **View Result** (Ver Resultado) mostra os resultados dos testes offline, caso sejam realizados.

Se quiser alterar o **Nome** e a **Descrição** do modelo, pode utilizar a hiperligação **Edit** (Editar) na linha adequada da tabela de modelos de linguagem.

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Como reconhecer voz em C#](quickstart-csharp-dotnet-windows.md)
- [Dados de exemplo do Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
