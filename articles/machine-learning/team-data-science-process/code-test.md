---
title: Testar o código de ciência de dados com os serviços de DevOps do Azure - Team Data Science Process
description: Código de ciência de dados de teste no Azure com o conjunto de dados da predição de rendimento para adultos UCI com o processo de ciência de dados de equipa e os serviços do Azure DevOps
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722070"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Código de ciência de dados de teste no Azure com o processo de ciência de dados de equipa e os serviços do Azure DevOps
Este artigo fornece diretrizes preliminares para testar o código num fluxo de trabalho de ciência de dados. Esses testes fornece os cientistas de dados uma forma sistemática e eficiente para verificar a qualidade e o resultado esperado do seu código. Utilizamos um projeto team Data Science Process (TDSP) que utiliza o conjunto de [dados uci adult income](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) que publicámos anteriormente para mostrar como os testes de código podem ser feitos. 

## <a name="introduction-on-code-testing"></a>Introdução no código de teste
"Testes de unidade" é uma prática de longa data para o desenvolvimento de software. Mas para a ciência dos dados, muitas vezes não é claro o que significa "teste de unidade" e como você deve testar código para diferentes fases de um ciclo de vida da ciência dos dados, tais como:

* Preparação de dados
* Análise de qualidade de dados
* Modelação
* Implementação do modelo 

Este artigo substitui o termo "testes de unidade" com "código de teste." Refere-se para testar como as funções que ajudam a avaliar se o código de um determinado passo de um ciclo de vida de ciência de dados é produzir resultados "conforme o esperado." A pessoa que está a escrever que o teste define o que é "tal como esperado," consoante o resultado da função – por exemplo, a verificação de qualidade de dados ou modelagem.

Este artigo fornece referências, como recursos úteis.

## <a name="azure-devops-for-the-testing-framework"></a>DevOps do Azure para a estrutura de testes
Este artigo descreve como realizar e automatizar os testes com o Azure DevOps. Pode optar por utilizar as ferramentas alternativas. Também vamos mostrar como configurar uma compilação automática através da utilização do Azure DevOps e agentes de compilação. Agentes de compilação, vamos utilizar máquinas de virtuais de ciência de dados de Azure (DSVMs).

## <a name="flow-of-code-testing"></a>Fluxo de código de teste
O fluxo de trabalho geral de código de teste num projeto de ciência de dados tem esta aparência: 

![Fluxograma de teste de código](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Passos detalhados

Utilize os seguintes passos para configurar e executar testes de código e uma compilação automatizada com um agente de compilação e DevOps do Azure:

1. Crie um projeto no aplicativo de desktop do Visual Studio:

    ![Ecrã "Criar novo projeto de" no Visual Studio](./media/code-test/create_project.PNG)

   Depois de criar seu projeto, vai encontrá-lo no Explorador de soluções, no painel da direita:
    
    ![Passos para criar um projeto](./media/code-test/create_python_project_in_vs.PNG)

    ![Explorador de Soluções](./media/code-test/solution_explorer_in_vs.PNG)

1. Feed seu código de projeto para o repositório de código do projeto de DevOps do Azure: 

    ![Repositório de código do projeto](./media/code-test/create_repo.PNG)

1. Suponha que fez um trabalho de preparação de dados, como a ingestão de dados, engenharia de funcionalidades e criação de colunas de etiqueta. Pretende certificar-se de que seu código está a gerar os resultados esperados. Este é o código que pode usar para testar se o código de processamento de dados está a funcionar corretamente:

    * Verifique se os nomes de coluna são certos:
    
      ![Código para efetuar a correspondência de nomes de colunas](./media/code-test/check_column_names.PNG)

    * Verifique que os níveis de resposta estão certos:

      ![Código para efetuar a correspondência de níveis](./media/code-test/check_response_levels.PNG)

    * Certifique-se de que a percentagem de resposta está razoável:

      ![Código de percentagem de resposta](./media/code-test/check_response_percentage.PNG)

    * Verificar a taxa de falta de cada coluna nos dados:
    
      ![Código para obter uma taxa em falta](./media/code-test/check_missing_rate.PNG)


1. Depois de fazer o processamento de dados e o trabalho de engenharia de funcionalidades e Treinou um bom modelo, certifique-se de que o modelo treinado por pode classificar novos conjuntos de dados corretamente. Pode utilizar os seguintes dois testes para verificar os níveis de previsão e distribuição de valores de etiqueta:

    * Verifique os níveis de predição:
    
      ![Código para verificar os níveis de predição](./media/code-test/check_prediction_levels.PNG)

    * Verifique a distribuição dos valores de predição:

      ![Código para verificar os valores de previsão](./media/code-test/check_prediction_values.PNG)

1. Junte todas as funções de teste num guião python chamado **test_funcs.py:**

    ![Script de Python para funções de teste](./media/code-test/create_file_test_func.PNG)


1. Depois dos códigos de teste estão preparados, pode configurar o ambiente de teste no Visual Studio.

   Crie um ficheiro Python chamado **test1.py.** Este ficheiro, crie uma classe que inclui todos os testes que deseja fazer. O exemplo seguinte mostra seis testes preparados:
    
    ![Ficheiro de Python com uma lista de testes numa classe](./media/code-test/create_file_test1_class.PNG)

1. Esses testes podem ser automaticamente descobertos se colocar **o codetest.testCase** após o nome da sua classe. Abra o Explorador de Teste no painel direito e selecione **Run All**. Todos os testes serão executados sequencialmente e informará se o teste é efetuada com êxito ou não.

    ![Executar os testes](./media/code-test/run_tests.PNG)

1. Verifique no seu código para o repositório do projeto ao utilizar comandos do Git. Seu trabalho mais recente será refletido daqui a pouco no Azure DevOps.

    ![Comandos do Git para a verificação no código](./media/code-test/git_check_in.PNG)

    ![Trabalho mais recente em DevOps do Azure](./media/code-test/git_check_in_most_recent_work.PNG)

1. Configurar a compilação automática e teste no Azure DevOps:

    a. No repositório do projeto, selecione **Build and Release,** e depois selecione **+New** para criar um novo processo de construção.

    ![Seleções para iniciar um novo processo de construção](./media/code-test/create_new_build.PNG)

    b. Siga as instruções para selecionar a localização do código de origem, nome do projeto, repositório e informações do ramo.
    
    ![Fonte, nome, repositório e informação sobre sucursais](./media/code-test/fill_in_build_info.PNG)

    c. Selecione um modelo. Porque não há modelo de projeto Python, comece por selecionar o **processo Empty**. 

    ![Lista de modelos e botão "Processo vazio"](./media/code-test/start_empty_process_template.PNG)

    d. Nomeie a compilação e selecione o agente. Pode escolher o padrão aqui se pretender utilizar um DSVM para completar o processo de construção. Para mais informações sobre a definição de [agentes,](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts)consulte a Build and release agents .
    
    ![Seleções de construção e agente](./media/code-test/select_agent.PNG)

    e. Selecione **+** no painel esquerdo, para adicionar uma tarefa para esta fase de construção. Como vamos executar o guião python **test1.py** para completar todos os cheques, esta tarefa é usar um comando PowerShell para executar o código Python.
    
    ![Painel "Adicionar tarefas" com powerShell selecionado](./media/code-test/add_task_powershell.PNG)

    f. Nos detalhes do PowerShell, preencha as informações necessárias, como o nome e versão do PowerShell. Escolha o **Script Inline** como o tipo. 
    
    Na caixa em **Script Inline,** pode escrever **test1.py python**. Certifique-se de que a variável ambiental está corretamente configurada para python. Se precisar de uma versão ou núcleo diferente de Python, pode especificar explicitamente o caminho como mostrado na figura: 
    
    ![Detalhes da PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Selecione **Guardar e fazer fila** para completar o processo de construção do gasoduto.

    ![Botão "Guardar e fazer fila"](./media/code-test/save_and_queue_build_definition.PNG)

Agora sempre que uma consolidação novo é emitida para o repositório de código, o processo de compilação será iniciada automaticamente. (Aqui usamos o mestre como repositório, mas você pode definir qualquer ramo.) O processo executa o ficheiro **test1.py** na máquina do agente para se certificar de que tudo definido no código funciona corretamente. 

Se os alertas estão configurados corretamente, será notificado por e-mail quando a compilação estiver concluída. Também pode verificar o estado da compilação do Azure DevOps. Se falhar, pode verificar os detalhes da compilação e descobrir qual parte foi interrompida.

![Notificação de e-mail de êxito de compilação](./media/code-test/email_build_succeed.PNG)

![Notificação de DevOps do Azure de sucesso de compilação](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Passos Seguintes
* Consulte o [repositório](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) de previsão de rendimento da UCI para exemplos concretos de testes unitários para cenários de ciência de dados.
* Siga a estrutura de tópicos e exemplos de cenário de predição de renda UCI em seus próprios projetos de ciência de dados anterior.

## <a name="references"></a>Referências
* [Processo de Ciência de Dados de Equipa](https://aka.ms/tdsp)
* [Ferramentas de teste de estúdio visual](https://www.visualstudio.com/vs/features/testing-tools/)
* [Recursos de teste Azure DevOps](https://www.visualstudio.com/team-services/)
* [Máquinas virtuais da ciência dos dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
