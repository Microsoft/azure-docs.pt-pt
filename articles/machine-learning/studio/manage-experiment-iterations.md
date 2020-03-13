---
title: Gerir iterações das experimentações
titleSuffix: ML Studio (classic) - Azure
description: Como gerir iterações de experimentação no Azure Machine Learning Studio (clássico). Pode rever execuções anteriores de suas experimentações em qualquer altura para desafiar, revisitar e, por fim, confirme ou refinar suposições anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204312"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Gerir iterações de experimentação no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Desenvolver um modelo de Análise Preditiva é um processo iterativo - à medida que modifica as várias funções e parâmetros da sua experimentação, os seus resultados convergem até achar que tem um modelo preparado e eficaz. A chave para este processo está acompanhando as várias interações de suas configurações e parâmetros de experimentação.



Pode rever execuções anteriores de suas experimentações em qualquer altura para desafiar, revisitar e, por fim, confirme ou refinar suposições anteriores. Quando executa uma experiência, o Machine Learning Studio (clássico) mantém um histórico de execução, incluindo dataset, módulo e ligações e parâmetros de porta. Esse histórico também captura os resultados, informações de tempo de execução, tais como iniciar e parar vezes, as mensagens de registo e estado de execução. Pode olhar novamente qualquer um dessas execuções em qualquer altura para rever a cronologia da sua experimentação e os resultados intermediários. Pode até usar uma execução anterior da sua experimentação para uma nova fase de consulta e deteção de iniciar no seu caminho para criar soluções de modelagem de ensemble até mesmo, complexo ou simples.

> [!NOTE]
> Quando vir uma execução anterior de uma experimentação, essa versão da experimentação está bloqueado e não pode ser editado. Pode, no entanto, guardar uma cópia, clicando em **SAVE AS** e fornecendo um novo nome para a cópia. O Machine Learning Studio (clássico) abre a nova cópia, que pode depois editar e executar. Esta cópia da sua experiência está disponível na lista **EXPERIMENTS** juntamente com todas as suas outras experiências.
> 
> 

## <a name="viewing-the-prior-run"></a>Ver a execução anterior
Quando tiver uma experiência aberta que tenha executado pelo menos uma vez, pode ver a execução anterior da experiência clicando em **Prior Run** no painel de propriedades.

Por exemplo, suponha que criar uma experimentação e executam versões do mesmo em 11:23, 11:42 e 11:55. Se abrir a última execução da experiência (11:55) e clicar em **Prior Run,** a versão que executou às 11:42 está aberta.

## <a name="viewing-the-run-history"></a>Visualizar o histórico de execuções
Pode ver todas as execuções anteriores de uma experiência clicando em **Ver Histórico** numa experiência aberta.

Por exemplo, suponha que crie uma experiência com o módulo [de regressão linear][linear-regression] e queira observar o efeito de alterar o valor da taxa de **Aprendizagem** nos resultados da sua experiência. Executar a experimentação múltiplas vezes com valores diferentes para este parâmetro, da seguinte forma:

| Valor da taxa de aprendizagem | Hora de início de execução |
| --- | --- |
| 0.1 |9/11/2014 4 18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0.4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4 33:31 pm |

Se clicar em **VER VER HISTÓRIA**DE EXECUÇÃO, verá uma lista de todas estas execuções:

![Exemplo de histórico de execuções](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer um dessas execuções para ver um instantâneo da experimentação no momento que o executasse. A configuração, os valores de parâmetros, comentários e os resultados são todos preservados para que tenha um registo completo de que a execução da sua experimentação.

> [!TIP]
> Para documentar as suas iterações da experiência, pode modificar o título sempre que o executar, pode atualizar o **resumo** da experiência no painel de propriedades, e pode adicionar ou atualizar comentários em módulos individuais para registar as suas alterações. Os comentários de título, resumo e módulo são guardados com cada execução da experimentação.
> 
> 

A lista de experiências no separador **EXPERIMENTS** no Machine Learning Studio (clássico) exibe sempre a versão mais recente de uma experiência. Se abrir uma execução anterior da experiência (utilizando **o Prior Run** ou VER RUN **HISTORY),** pode voltar à versão de rascunho clicando no **VER RUN HISTORY** e selecionando a iteração que tem um **ESTADO** de **Editável**.

## <a name="iterating-on-a-previous-run"></a>Fazendo a iteração numa execução anterior
Quando clicar em **Prior Run** ou VIEW **RUN HISTORY** e abrir uma execução anterior, pode ver uma experiência acabada no modo de leitura.

Se quiser iniciar uma iteração da sua experiência começando pela forma como a configurapara uma execução anterior, pode fazê-lo abrindo a execução e clicando EM **SAVE AS**. Esta ação cria uma nova experimentação, com um título novo, vazio histórico, de execução e execute todos os componentes e os valores de parâmetro de anterior. Esta nova experiência está listada no separador **EXPERIMENTS** na página inicial do Estúdio de Aprendizagem automática (clássico), e pode modificá-la e executá-la, adar um novo histórico de execução para esta iteração da sua experiência. 

Por exemplo, suponha que tem a experimentação mostrado na secção anterior do histórico de execuções. Você quer observar o que acontece quando você define o parâmetro da **taxa de aprendizagem** para 0,4, e experimente valores diferentes para o **número de épocas de treino parâmetros.**

1. Clique em **VER HISTÓRIA DE EXECUÇÃO** e abra a iteração da experiência que executou às 16:28:36 (na qual define o valor do parâmetro para 0,4).
2. Clique em **GUARDAR COMO**.
3. Introduza um novo título e clique na marca de verificação **OK.** É criada uma nova cópia da experimentação.
4. Modificar o **número de épocas de treino parâmetros.**
5. Clique em **RUN**.

Pode continuar modificar e executar esta versão da sua experimentação, criando um histórico de execuções de novo para registrar seu trabalho.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
