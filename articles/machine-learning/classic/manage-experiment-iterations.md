---
title: 'ML Studio (clássico): Ver & refazer experiências - Azure'
description: Gerir a experiência corre no Azure Machine Learning Studio (clássico). Pode rever as execuções anteriores das suas experiências a qualquer momento para desafiar, revisitar e, em última análise, confirmar ou aperfeiçoar pressupostos anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: c958de5e49fbb7519ae71b13e9deba9af0c698de
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312867"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Gerir a experiência corre no Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Desenvolver um modelo de análise preditiva é um processo iterativo - à medida que modifica as várias funções e parâmetros da sua experiência, os seus resultados convergem até que esteja convencido de que tem um modelo treinado e eficaz. A chave para este processo é rastrear as várias iterações dos seus parâmetros e configurações de experiência.

Pode rever as execuções anteriores das suas experiências a qualquer momento para desafiar, revisitar e, em última análise, confirmar ou aperfeiçoar pressupostos anteriores. Quando você faz uma experiência, o Machine Learning Studio (clássico) mantém uma história da execução, incluindo conjunto de dados, módulos e ligações e parâmetros portuários. Este histórico também captura resultados, informações de tempo de execução, tais como tempos de início e paragem, mensagens de registo e estado de execução. Pode olhar para trás em qualquer uma destas corridas a qualquer momento para rever a cronologia da sua experiência e resultados intermédios. Você pode até usar uma execução anterior da sua experiência para lançar para uma nova fase de investigação e descoberta no seu caminho para criar soluções de modelação simples, complexas ou mesmo de conjunto.

> [!NOTE]
> Quando se vê uma série anterior de uma experiência, essa versão da experiência está bloqueada e não pode ser editada. No entanto, pode guardar uma cópia, clicando em **SAVE AS** e fornecendo um novo nome para a cópia. O Machine Learning Studio (clássico) abre a nova cópia, que pode editar e executar. Esta cópia da sua experiência está disponível na lista **EXPERIMENTOS** juntamente com todas as suas outras experiências.
> 
> 

## <a name="view-the-prior-run"></a>Ver a execução anterior
Quando tiver uma experiência aberta que tenha executado pelo menos uma vez, pode ver a execução anterior da experiência clicando em **Prior Run** no painel de propriedades.

Por exemplo, suponha que crie uma experiência e execute versões dela às 11:23, 11:42 e 11:55. Se abrir a última execução da experiência (11:55) e clicar em **Prior Run,** a versão que executou às 11:42 é aberta.

## <a name="view-the-run-history"></a>Ver a história da execução
Pode ver todas as séries anteriores de uma experiência clicando em **View Run History** numa experiência aberta.

Por exemplo, suponha que cria uma experiência com o módulo [de Regressão Linear][linear-regression] e que pretende observar o efeito de alterar o valor da taxa de **Aprendizagem** nos resultados da sua experiência. Execute a experiência várias vezes com valores diferentes para este parâmetro, da seguinte forma:

| Valor da Taxa de Aprendizagem | Tempo de início de execução |
| --- | --- |
| 0.1 |9/11/2014 16:18:58 |
| 0.2 |11/09/2014 16:24:33 |
| 0.4 |11/09/2014 16:28:36 |
| 0,5 |11/09/2014 16:33:31 |

Se clicar em **VER EXECUTAR HISTÓRICO,** veja uma lista de todas estas execuções:

![Exemplo executar história](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer uma destas corridas para ver uma imagem da experiência no momento em que a executou. A configuração, os valores dos parâmetros, os comentários e os resultados estão todos preservados para lhe dar um registo completo dessa execução da sua experiência.

> [!TIP]
> Para documentar as suas iterações da experiência, pode modificar o título sempre que o executou, pode atualizar o **Resumo** da experiência no painel de propriedades, podendo adicionar ou atualizar comentários em módulos individuais para registar as suas alterações. O título, resumo e comentários do módulo são guardados a cada execução da experiência.
> 
> 

A lista de experiências no separador **EXPERIMENTS** no Machine Learning Studio (clássico) apresenta sempre a versão mais recente de uma experiência. Se abrir uma série anterior da experiência (utilizando o **Prior Run** ou VIEW **RUN HISTORY),** pode voltar à versão do projeto clicando em **VER HISTÓRICO E** selecionando a iteração que tem um **ESTADO** de **Editável**.

## <a name="run-a-previous-experiment"></a>Executar uma experiência anterior
Quando clicar em **Prior Run** ou VIEW **RUN HISTORY** e abrir uma execução anterior, pode ver uma experiência terminada no modo apenas de leitura.

Se pretender iniciar uma iteração da sua experiência a começar pela forma como a configura para uma execução anterior, pode fazê-lo abrindo a execução e clicando em **SAVE AS**. Isto cria uma nova experiência, com um novo título, uma história de corrida vazia, e todos os componentes e valores de parâmetros da execução anterior. Esta nova experiência está listada no separador **EXPERIMENTS** na página inicial do Machine Learning Studio (clássico) e pode modificá-la e executá-la, iniciando um novo histórico de execução para esta iteração da sua experiência. 

Por exemplo, suponha que tenha a experiência de fazer a história mostrada na secção anterior. Você quer observar o que acontece quando define o parâmetro **da taxa de Aprendizagem** para 0.4, e tentar diferentes valores para o parâmetro **Número de épocas de treino.**

1. Clique **em VER EXECUTAR HISTÓRICO** e abra a iteração da experiência que executou às 16:28:36 (na qual definiu o valor do parâmetro para 0.4).
2. CLIQUE **EM GUARDAR AS**.
3. Introduza um novo título e clique na marca de verificação **OK.** Uma nova cópia da experiência é criada.
4. Modifique o **parâmetro número de épocas de treino.**
5. Clique **em RUN**.

Pode agora continuar a modificar e executar esta versão da sua experiência, construindo um novo histórico de execução para registar o seu trabalho.

<!-- Module References -->
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression