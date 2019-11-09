---
title: Gerir iterações das experimentações
titleSuffix: ML Studio (classic) - Azure
description: Como gerenciar iterações de experimento no Azure Machine Learning Studio (clássico). Você pode examinar as execuções anteriores de seus experimentos a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar as suposições anteriores.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 9a69ebaa9b7f6ffffdfc80e09fb2eec9495e2ce2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838829"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Gerenciar iterações de experimento no Azure Machine Learning Studio (clássico)
Desenvolver um modelo de análise preditiva é um processo iterativo-conforme você modifica as várias funções e parâmetros de seu experimento, seus resultados convergem até que você esteja satisfeito com um modelo treinado e eficaz. A chave para esse processo é acompanhar as várias iterações de seus parâmetros de experimento e configurações.



Você pode examinar as execuções anteriores de seus experimentos a qualquer momento para desafiar, revisitar e, por fim, confirmar ou refinar as suposições anteriores. Quando você executa um experimento, Machine Learning Studio (clássico) mantém um histórico da execução, incluindo as conexões de conjunto de módulos (DataSet), módulo e porta e parâmetros. Esse histórico também captura resultados, informações de tempo de execução, como horários de início e de parada, mensagens de log e status de execução. Você pode examinar novamente qualquer uma dessas execuções a qualquer momento para examinar a cronologia do seu experimento e os resultados intermediários. Você pode até mesmo usar uma execução anterior de seu experimento para iniciar em uma nova fase de consulta e descoberta em seu caminho para criar soluções de modelagem simples, complexas ou até mesmo Ensemble.

> [!NOTE]
> Quando você exibe uma execução anterior de um experimento, essa versão do experimento é bloqueada e não pode ser editada. Você pode, no entanto, salvar uma cópia dela clicando em **salvar como** e fornecendo um novo nome para a cópia. Machine Learning Studio (clássico) abre a nova cópia, que você pode editar e executar. Essa cópia do experimento está disponível na lista de **experimentos** , juntamente com todos os outros experimentos.
> 
> 

## <a name="viewing-the-prior-run"></a>Exibindo a execução anterior
Quando você tiver um experimento aberto de que executou pelo menos uma vez, poderá exibir a execução anterior do experimento clicando em **execução anterior** no painel Propriedades.

Por exemplo, suponha que você crie um experimento e execute versões dele em 11:23, 11:42 e 11:55. Se você abrir a última execução do experimento (11:55) e clicar em **execução anterior**, a versão que você executou às 11:42 será aberta.

## <a name="viewing-the-run-history"></a>Exibindo o histórico de execuções
Você pode exibir todas as execuções anteriores de um experimento clicando em **Exibir histórico de execução** em um experimento aberto.

Por exemplo, suponha que você crie um experimento com o módulo [regressão linear][linear-regression] e queira observar o efeito de alterar o valor da **taxa de aprendizagem** nos resultados do experimento. Você executa o experimento várias vezes com valores diferentes para esse parâmetro, da seguinte maneira:

| Valor da taxa de aprendizagem | Hora de início da execução |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 PM |
| 0,2 |9/11/2014 4:24:33 PM |
| 0.4 |9/11/2014 4:28:36 PM |
| 0,5 |9/11/2014 4:33:31 PM |

Se você clicar em **Exibir histórico de execução**, verá uma lista de todas essas execuções:

![Exemplo de histórico de execução](./media/manage-experiment-iterations/viewrunhistory.jpg)

Clique em qualquer uma dessas execuções para exibir um instantâneo do experimento no momento em que você o executou. A configuração, os valores de parâmetro, os comentários e os resultados são todos preservados para fornecer um registro completo dessa execução do experimento.

> [!TIP]
> Para documentar suas iterações do experimento, você pode modificar o título sempre que executá-lo, você pode atualizar o **Resumo** do experimento no painel Propriedades e pode adicionar ou atualizar comentários em módulos individuais para registrar suas alterações. Os comentários de título, resumo e módulo são salvos com cada execução do experimento.
> 
> 

A lista de experimentos na guia **experimentos** em Machine Learning Studio (clássico) sempre exibe a versão mais recente de um experimento. Se você abrir uma execução anterior do experimento (usando **execução anterior** ou **Exibir histórico de execução**), poderá retornar para a versão de rascunho clicando em **Exibir histórico de execução** e selecionando a iteração que tem um **estado** **editável**.

## <a name="iterating-on-a-previous-run"></a>Iterando em uma execução anterior
Ao clicar em **execução anterior** ou **Exibir histórico de execução** e abrir uma execução anterior, você poderá exibir um experimento concluído no modo somente leitura.

Se você quiser iniciar uma iteração de seu experimento a partir da maneira como o configurou para uma execução anterior, poderá fazer isso abrindo a execução e clicando em **salvar como**. Isso cria um novo experimento, com um novo título, um histórico de execução vazio e todos os componentes e valores de parâmetro da execução anterior. Esse novo experimento é listado na guia **experimentos** do Machine Learning Studio (clássico) Home Page e você pode modificá-lo e executá-lo, iniciando um novo histórico de execução para essa iteração de seu experimento. 

Por exemplo, suponha que você tenha o histórico de execução do experimento mostrado na seção anterior. Você deseja observar o que acontece quando você define o parâmetro de **taxa de aprendizagem** como 0,4 e tenta valores diferentes para o parâmetro **número de épocas de treinamento** .

1. Clique em **Exibir histórico de execução** e abra a iteração do experimento que você executou às 4:28:36 PM (em que você definiu o valor do parâmetro como 0,4).
2. Clique em **salvar como**.
3. Insira um novo título e clique na marca de seleção **OK** . Uma nova cópia do experimento é criada.
4. Modifique o parâmetro **número de épocas de treinamento** .
5. Clique em **executar**.

Agora você pode continuar a modificar e executar esta versão do seu experimento, criando um novo histórico de execução para registrar seu trabalho.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
