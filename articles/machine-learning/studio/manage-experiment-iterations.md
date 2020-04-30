---
title: Ver experiências de recorrer &
titleSuffix: ML Studio (classic) - Azure
description: Gerir as experiências corre no Azure Machine Learning Studio (clássico). Pode rever as execuções anteriores das suas experiências a qualquer momento para desafiar, revisitar e, em última análise, confirmar ou refinar pressupostos anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: d2ace48ff1b2027a95aef2100540edc10aebb4cc
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208419"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Gerir experiências corre no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Desenvolver um modelo de análise preditiva é um processo iterativo - à medida que modifica as várias funções e parâmetros da sua experiência, os seus resultados convergem até ficar satisfeito por ter um modelo treinado e eficaz. A chave para este processo é rastrear as várias iterações dos parâmetros e configurações da sua experiência.

Pode rever as execuções anteriores das suas experiências a qualquer momento para desafiar, revisitar e, em última análise, confirmar ou refinar pressupostos anteriores. Quando executa uma experiência, o Machine Learning Studio (clássico) mantém um histórico de execução, incluindo dataset, módulo e ligações e parâmetros de porta. Este histórico também captura resultados, informações de tempo de execução como tempos de início e paragem, mensagens de registo e estado de execução. Você pode olhar para trás em qualquer uma destas corridas a qualquer momento para rever a cronologia da sua experiência e resultados intermédios. Você pode até usar uma execução anterior da sua experiência para lançar em uma nova fase de investigação e descoberta no seu caminho para criar soluções de modelação simples, complexas ou até mesmo conjunto.

> [!NOTE]
> Quando se vê uma execução anterior de uma experiência, essa versão da experiência está bloqueada e não pode ser editada. Pode, no entanto, guardar uma cópia, clicando em **SAVE AS** e fornecendo um novo nome para a cópia. O Machine Learning Studio (clássico) abre a nova cópia, que pode depois editar e executar. Esta cópia da sua experiência está disponível na lista **EXPERIMENTS** juntamente com todas as suas outras experiências.
> 
> 

## <a name="view-the-prior-run"></a>Ver a corrida anterior
Quando tiver uma experiência aberta que tenha executado pelo menos uma vez, pode ver a execução anterior da experiência clicando em **Prior Run** no painel de propriedades.

Por exemplo, suponha que crie uma experiência e execute versões dela às 11:23, 11:42 e 11:55. Se abrir a última execução da experiência (11:55) e clicar em **Prior Run,** a versão que executou às 11:42 está aberta.

## <a name="view-the-run-history"></a>Ver a história da corrida
Pode ver todas as execuções anteriores de uma experiência clicando em **Ver Histórico** numa experiência aberta.

Por exemplo, suponha que crie uma experiência com o módulo [de regressão linear][linear-regression] e queira observar o efeito de alterar o valor da taxa de **Aprendizagem** nos resultados da sua experiência. Executa a experiência várias vezes com valores diferentes para este parâmetro, da seguinte forma:

| Valor da Taxa de Aprendizagem | Executar a hora de início |
| --- | --- |
| 0.1 |9/11/2014 16:18:58 |
| 0,2 |9/11/2014 16:24:33 |
| 0.4 |9/11/2014 16:28:36 |
| 0,5 |9/11/2014 16:33:31 |

Se clicar em **VER VER HISTÓRIA**DE EXECUÇÃO, verá uma lista de todas estas execuções:

![Exemplo de história de execução](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer uma destas corridas para ver uma foto da experiência no momento em que a executou. A configuração, os valores dos parâmetros, os comentários e os resultados são preservados para lhe dar um registo completo dessa execução da sua experiência.

> [!TIP]
> Para documentar as suas iterações da experiência, pode modificar o título sempre que o executar, pode atualizar o **resumo** da experiência no painel de propriedades, e pode adicionar ou atualizar comentários em módulos individuais para registar as suas alterações. Os comentários do título, resumo e módulo são guardados a cada execução da experiência.
> 
> 

A lista de experiências no separador **EXPERIMENTS** no Machine Learning Studio (clássico) exibe sempre a versão mais recente de uma experiência. Se abrir uma execução anterior da experiência (utilizando **o Prior Run** ou VER RUN **HISTORY),** pode voltar à versão de rascunho clicando no **VER RUN HISTORY** e selecionando a iteração que tem um **ESTADO** de **Editável**.

## <a name="run-a-previous-experiment"></a>Executar uma experiência anterior
Quando clicar em **Prior Run** ou VIEW **RUN HISTORY** e abrir uma execução anterior, pode ver uma experiência acabada no modo de leitura.

Se quiser iniciar uma iteração da sua experiência começando pela forma como a configurapara uma execução anterior, pode fazê-lo abrindo a execução e clicando EM **SAVE AS**. Isto cria uma nova experiência, com um novo título, uma história de execução vazia, e todos os componentes e valores de parâmetros da corrida anterior. Esta nova experiência está listada no separador **EXPERIMENTS** na página inicial do Estúdio de Aprendizagem automática (clássico), e pode modificá-la e executá-la, adar um novo histórico de execução para esta iteração da sua experiência. 

Por exemplo, suponha que tenha o histórico de experiências mostrado na secção anterior. Você quer observar o que acontece quando você define o parâmetro da **taxa de aprendizagem** para 0,4, e experimente valores diferentes para o **número de épocas de treino parâmetros.**

1. Clique em **VER HISTÓRIA DE EXECUÇÃO** e abra a iteração da experiência que executou às 16:28:36 (na qual define o valor do parâmetro para 0,4).
2. Clique em **GUARDAR COMO**.
3. Introduza um novo título e clique na marca de verificação **OK.** Uma nova cópia da experiência é criada.
4. Modificar o **número de épocas de treino parâmetros.**
5. Clique em **RUN**.

Agora pode continuar a modificar e executar esta versão da sua experiência, construindo um novo histórico de execução para gravar o seu trabalho.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
