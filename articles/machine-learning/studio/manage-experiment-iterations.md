---
title: Gerir iterações das experimentações
titleSuffix: Azure Machine Learning Studio
description: Como gerir iterações de experimentação no Azure Machine Learning Studio. Pode rever execuções anteriores de suas experimentações em qualquer altura para desafiar, revisitar e, por fim, confirme ou refinar suposições anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 34a72f2e7b6be90654c0f053d5b8978b0283d56c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890530"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Gerir iterações da experimentação no Azure Machine Learning Studio
Desenvolver um modelo de Análise Preditiva é um processo iterativo - à medida que modifica as várias funções e parâmetros da sua experimentação, os seus resultados convergem até achar que tem um modelo preparado e eficaz. A chave para este processo está acompanhando as várias interações de suas configurações e parâmetros de experimentação.



Pode rever execuções anteriores de suas experimentações em qualquer altura para desafiar, revisitar e, por fim, confirme ou refinar suposições anteriores. Quando executa uma experimentação, o Machine Learning Studio mantém um histórico da execução, incluindo o conjunto de dados, módulo e ligações de porta e parâmetros. Esse histórico também captura os resultados, informações de tempo de execução, tais como iniciar e parar vezes, as mensagens de registo e estado de execução. Pode olhar novamente qualquer um dessas execuções em qualquer altura para rever a cronologia da sua experimentação e os resultados intermediários. Pode até usar uma execução anterior da sua experimentação para uma nova fase de consulta e deteção de iniciar no seu caminho para criar soluções de modelagem de ensemble até mesmo, complexo ou simples.

> [!NOTE]
> Quando vir uma execução anterior de uma experimentação, essa versão da experimentação está bloqueado e não pode ser editado. No entanto, pode, guarde uma cópia do mesmo clicando **guardar como** e fornecendo um novo nome para a cópia. O Machine Learning Studio abre-se a nova cópia, que, em seguida, pode editar e executar. Esta cópia da sua experimentação está disponível na **EXPERIMENTAÇÕES** lista juntamente com todas as suas outras experiências.
> 
> 

## <a name="viewing-the-prior-run"></a>Ver a execução anterior
Quando tiver aberto uma experimentação que executou, pelo menos, uma vez, pode ver a execução anterior da experimentação ao clicar **executar anterior** no painel de propriedades.

Por exemplo, suponha que criar uma experimentação e executam versões do mesmo em 11:23, 11:42 e 11:55. Se abrir a última execução da experimentação (11:55) e clique em **executar anterior**, a versão que foi executada em 11:42 é aberta.

## <a name="viewing-the-run-history"></a>Visualizar o histórico de execuções
Pode ver todas as execuções anteriores de uma experimentação ao clicar **ver histórico de execuções** numa experimentação aberta.

Por exemplo, suponha que crie uma experiência com o [regressão Linear] [ linear-regression] módulo e quiser observar o efeito da alteração do valor de **ritmo de aprendizagem** no seu resultados da experimentação. Executar a experimentação múltiplas vezes com valores diferentes para este parâmetro, da seguinte forma:

| Valor da taxa de aprendizagem | Hora de início de execução |
| --- | --- |
| 0.1 |9/11/2014 4 18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0.4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4 33:31 pm |

Se clicar **ver histórico de EXECUÇÕES**, verá uma lista de todas essas execuções:

![Exemplo de histórico de execuções](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer um dessas execuções para ver um instantâneo da experimentação no momento que o executasse. A configuração, os valores de parâmetros, comentários e os resultados são todos preservados para que tenha um registo completo de que a execução da sua experimentação.

> [!TIP]
> Documentar as iterações da experimentação, pode modificar o título de cada vez que executá-lo, pode atualizar o **resumo** da experimentação nas propriedades do painel e pode adicionar ou atualizar comentários nos módulos individuais para registrar sua alterações. Os comentários de título, resumo e módulo são guardados com cada execução da experimentação.
> 
> 

A lista de experimentações no **EXPERIMENTAÇÕES** separador no Machine Learning Studio apresenta sempre a versão mais recente de uma experimentação. Se abrir uma execução anterior da experimentação (usando **executar anteriores** ou **ver histórico de EXECUÇÕES**), pode retornar para a versão de rascunho, clicando em **ver histórico de EXECUÇÕES** e selecionando o iteração, que tem um **estado** dos **Editable**.

## <a name="iterating-on-a-previous-run"></a>Fazendo a iteração numa execução anterior
Quando clica em **executar anteriores** ou **ver histórico de EXECUÇÕES** e abrir uma execução anterior, pode ver uma experimentação concluída no modo só de leitura.

Se pretender começar uma repetição da experimentação começando com a forma como tiver configurado para uma execução anterior, pode fazê-lo ao abrir a execução e clicar **guardar como**. Esta ação cria uma nova experimentação, com um título novo, vazio histórico, de execução e execute todos os componentes e os valores de parâmetro de anterior. Esta nova experiência está listada na **EXPERIMENTAÇÕES** guia na home page do Machine Learning Studio e pode modificar e executá-lo, iniciar um novo histórico de execuções por essa iteração da sua experimentação. 

Por exemplo, suponha que tem a experimentação mostrado na secção anterior do histórico de execuções. Pretende observar o que acontece quando definir o **taxa de aprendizagem** parâmetro para 0.4 e tente valores diferentes para o **número de epochs de treinamento** parâmetro.

1. Clique em **ver histórico de EXECUÇÕES** e abra a iteração da experimentação que tenha sido executada às: 28:36 16:00 (em que definir o valor do parâmetro para 0.4).
2. Clique em **guardar como**.
3. Introduza um título novo e clique nas **OK** marca de verificação. É criada uma nova cópia da experimentação.
4. Modificar a **número de epochs de treinamento** parâmetro.
5. Clique em **executar**.

Pode continuar modificar e executar esta versão da sua experimentação, criando um histórico de execuções de novo para registrar seu trabalho.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
