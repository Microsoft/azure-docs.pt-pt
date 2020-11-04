---
title: Código de ciência de dados de teste com serviços Azure DevOps - Processo de Ciência de Dados de Equipa
description: Testes de código de ciência de dados em Azure com o conjunto de dados de previsão do rendimento dos adultos da UCI com o Processo de Ciência de Dados de Equipa e Serviços Azure DevOps
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
ms.openlocfilehash: bcebc568db4a0d2b4b287045ba0a67c88d9b8c92
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312681"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testes de código de ciência de dados no Azure com o Processo de Ciência de Dados da Equipa e Serviços Azure DevOps
Este artigo fornece orientações preliminares para testar código num fluxo de trabalho de ciência de dados. Estes testes conferem aos cientistas de dados uma forma sistemática e eficiente de verificar a qualidade e o resultado esperado do seu código. Utilizamos um projeto team data science process (TDSP) [que utiliza o conjunto de dados do Rendimento Adulto da UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) que publicámos anteriormente para mostrar como os testes de código podem ser feitos. 

## <a name="introduction-on-code-testing"></a>Introdução em testes de código
"Teste de unidade" é uma prática de longa data para o desenvolvimento de software. Mas para a ciência dos dados, muitas vezes não é claro o que significa "teste unitário" e como deve testar código para diferentes fases de um ciclo de vida da ciência dos dados, tais como:

* Preparação de dados
* Exame de qualidade de dados
* Modelação
* Implementação do modelo 

Este artigo substitui o termo "teste unitário" por "teste de código". Refere-se ao teste como as funções que ajudam a avaliar se o código para um determinado passo de um ciclo de vida da ciência dos dados está a produzir resultados "como esperado". A pessoa que está a escrever o teste define o que é "como esperado", dependendo do resultado da função -- por exemplo, verificação de qualidade de dados ou modelação.

Este artigo fornece referências como recursos úteis.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps para o quadro de testes
Este artigo descreve como realizar e automatizar testes utilizando Azure DevOps. Pode decidir usar ferramentas alternativas. Também mostramos como configurar uma construção automática usando Azure DevOps e construir agentes. Para agentes de construção, utilizamos máquinas virtuais Azure Data Science (DSVMs).

## <a name="flow-of-code-testing"></a>Fluxo de testes de código
O fluxo de trabalho global do código de testes num projeto de ciência de dados é o seguinte: 

![Gráfico de fluxo de testes de código](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Passos detalhados

Utilize os seguintes passos para configurar e executar testes de código e uma construção automatizada utilizando um agente de construção e Azure DevOps:

1. Criar um projeto na aplicação de desktop do Estúdio Visual:

    ![Ecrã "Criar novo projeto" no Visual Studio](./media/code-test/create_project.PNG)

   Depois de criar o seu projeto, irá encontrá-lo no Solution Explorer no painel certo:
    
    ![Passos para a criação de um projeto](./media/code-test/create_python_project_in_vs.PNG)

    ![Explorador de Soluções](./media/code-test/solution_explorer_in_vs.PNG)

1. Introduza o seu código de projeto no repositório do código do projeto Azure DevOps: 

    ![Repositório de código de projeto](./media/code-test/create_repo.PNG)

1. Suponha que tenha feito alguns trabalhos de preparação de dados, tais como ingestão de dados, engenharia de recursos e criação de colunas de etiquetas. Quer ter a certeza de que o seu código está a gerar os resultados que espera. Aqui está um código que pode usar para testar se o código de processamento de dados está a funcionar corretamente:

    * Verifique se os nomes das colunas estão certos:
    
      ![Código para os nomes das colunas correspondentes](./media/code-test/check_column_names.PNG)

    * Verifique se os níveis de resposta estão certos:

      ![Código para níveis de correspondência](./media/code-test/check_response_levels.PNG)

    * Verifique se a percentagem de resposta é razoável:

      ![Código para percentagem de resposta](./media/code-test/check_response_percentage.PNG)

    * Verifique a taxa em falta de cada coluna nos dados:
    
      ![Código para taxa em falta](./media/code-test/check_missing_rate.PNG)


1. Depois de ter feito o processamento de dados e o trabalho de engenharia de recursos, e ter treinado um bom modelo, certifique-se de que o modelo que treinou pode obter corretamente novos conjuntos de dados. Pode utilizar os seguintes dois testes para verificar os níveis de previsão e distribuição dos valores dos rótulos:

    * Verifique os níveis de previsão:
    
      ![Código para verificar os níveis de previsão](./media/code-test/check_prediction_levels.PNG)

    * Verifique a distribuição dos valores de previsão:

      ![Código para verificação dos valores de previsão](./media/code-test/check_prediction_values.PNG)

1. Junte todas as funções de teste num script Python chamado **test_funcs.py:**

    ![Script python para funções de teste](./media/code-test/create_file_test_func.PNG)


1. Depois de preparados os códigos de teste, pode configurar o ambiente de teste no Estúdio Visual.

   Crie um ficheiro Python chamado **test1.py**. Neste ficheiro, crie uma classe que inclua todos os testes que pretende fazer. O exemplo a seguir mostra seis ensaios preparados:
    
    ![Python arquivo com uma lista de testes em uma classe](./media/code-test/create_file_test1_class.PNG)

1. Esses testes podem ser automaticamente descobertos se colocar **codetest.testCase** após o seu nome de classe. Abra o Explorador de Teste no painel direito e selecione **Run All**. Todos os testes serão realizados sequencialmente e dir-lhe-ão se o teste é bem sucedido ou não.

    ![Executar os testes](./media/code-test/run_tests.PNG)

1. Consulte o seu código no repositório do projeto utilizando comandos Git. O seu trabalho mais recente será refletido em breve em Azure DevOps.

    ![Git comanda para verificação em código](./media/code-test/git_check_in.PNG)

    ![Trabalho mais recente em Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Configurar a construção automática e testar em Azure DevOps:

    a. No repositório do projeto, selecione **Build and Release** , e, em seguida, selecione **+Novo** para criar um novo processo de construção.

    ![Seleções para iniciar um novo processo de construção](./media/code-test/create_new_build.PNG)

    b. Siga as instruções para selecionar a localização do código fonte, o nome do projeto, o repositório e a informação do ramo.
    
    ![Fonte, nome, repositório e informação de ramo](./media/code-test/fill_in_build_info.PNG)

    c. Selecione um modelo. Como não há modelo de projeto Python, comece por selecionar o **processo Empty.** 

    ![Lista de modelos e botão "Processo vazio"](./media/code-test/start_empty_process_template.PNG)

    d. Nomeie a construção e selecione o agente. Pode escolher o padrão aqui se quiser utilizar um DSVM para concluir o processo de construção. Para obter mais informações sobre a definição de agentes, consulte [build e release agents](/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Seleções de construção e de agentes](./media/code-test/select_agent.PNG)

    e. Selecione **+** no painel esquerdo, para adicionar uma tarefa para esta fase de construção. Porque vamos executar o script Python **test1.py** para completar todos os cheques, esta tarefa está a usar um comando PowerShell para executar o código Python.
    
    ![Painel "Adicionar tarefas" com PowerShell selecionado](./media/code-test/add_task_powershell.PNG)

    f. Nos detalhes do PowerShell, preencha as informações necessárias, tais como o nome e a versão do PowerShell. Escolha **o Script Inline** como o tipo. 
    
    Na caixa em **Inline Script,** pode escrever **pitão test1.py**. Certifique-se de que a variável ambiente está corretamente configurada para Python. Se precisar de uma versão ou núcleo diferente de Python, pode especificar explicitamente o caminho indicado na figura: 
    
    ![Detalhes da PowerShell](./media/code-test/powershell_scripts.PNG)

    exemplo, **Selecione Guardar & fila** para concluir o processo de construção do gasoduto.

    ![Botão de fila "Guardar &"](./media/code-test/save_and_queue_build_definition.PNG)

Agora, cada vez que um novo compromisso é empurrado para o repositório de código, o processo de construção começará automaticamente. Pode definir qualquer ramo. O processo executa o ficheiro **test1.py** na máquina do agente para se certificar de que tudo o que está definido no código funciona corretamente. 

Se os alertas forem configurados corretamente, será notificado por e-mail quando a construção estiver concluída. Também pode verificar o estado de construção em Azure DevOps. Se falhar, pode verificar os detalhes da construção e descobrir qual a peça partida.

![Notificação de e-mail do sucesso da construção](./media/code-test/email_build_succeed.PNG)

![Azure DevOps notificação de sucesso de construção](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Passos seguintes
* Consulte o [repositório](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) de previsão de rendimento da UCI para exemplos concretos de testes unitários para cenários de ciência de dados.
* Siga o esboço anterior e os exemplos do cenário de previsão de rendimento da UCI nos seus próprios projetos de ciência de dados.

## <a name="references"></a>Referências
* [Processo de Ciência de Dados de Equipa](./index.yml)
* [Ferramentas de teste de estúdio visual](https://www.visualstudio.com/vs/features/testing-tools/)
* [Recursos de teste de Azure DevOps](https://www.visualstudio.com/team-services/)
* [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)