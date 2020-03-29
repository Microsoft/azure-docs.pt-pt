---
title: Teste código de ciência de dados com serviços Azure DevOps - Processo de Ciência de Dados da Equipa
description: Teste do código de ciência de dados em Azure com o conjunto de dados de previsão de rendimento de adultos da UCI com o Processo de Ciência de Dados da Equipa e serviços azure DevOps
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722070"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Teste de código de ciência de dados em Azure com o Processo de Ciência de Dados da Equipa e Serviços Azure DevOps
Este artigo fornece orientações preliminares para o código de teste num fluxo de trabalho de ciência de dados. Estes testes conferem aos cientistas de dados uma forma sistemática e eficiente de verificar a qualidade e o resultado esperado do seu código. Utilizamos um projeto team Data Science Process (TDSP) que utiliza o conjunto de [dados uci adult income](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) que publicámos anteriormente para mostrar como os testes de código podem ser feitos. 

## <a name="introduction-on-code-testing"></a>Introdução de ensaios de código
"Teste de unidade" é uma prática de longa data para o desenvolvimento de software. Mas para a ciência dos dados, muitas vezes não é claro o que significa "teste de unidade" e como você deve testar código para diferentes fases de um ciclo de vida da ciência dos dados, tais como:

* Preparação de dados
* Exame da qualidade dos dados
* Modelação
* Implementação do modelo 

Este artigo substitui o termo "teste de unidade" por "teste de código". Refere-se aos testes como as funções que ajudam a avaliar se o código para um determinado passo de um ciclo de vida da ciência dos dados está a produzir resultados "como esperado". A pessoa que está a escrever o teste define o que é "como esperado", dependendo do resultado da função -- por exemplo, verificação de qualidade de dados ou modelação.

Este artigo fornece referências como recursos úteis.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps para o quadro de testes
Este artigo descreve como realizar e automatizar testes utilizando O Azure DevOps. Pode decidir usar ferramentas alternativas. Também mostramos como configurar uma construção automática usando Azure DevOps e construir agentes. Para os agentes de construção, utilizamos máquinas virtuais azure Data Science (DSVMs).

## <a name="flow-of-code-testing"></a>Fluxo de ensaios de código
O fluxo global de trabalho do código de teste num projeto de ciência de dados é assim: 

![Gráfico de fluxo de testes de código](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Passos detalhados

Utilize os seguintes passos para configurar e executar testes de código e uma construção automatizada utilizando um agente de construção e Azure DevOps:

1. Criar um projeto na aplicação de ambiente de trabalho do Estúdio Visual:

    ![Ecrã "Criar novo projeto" no Estúdio Visual](./media/code-test/create_project.PNG)

   Depois de criar o seu projeto, vai encontrá-lo no Solution Explorer no painel certo:
    
    ![Passos para a criação de um projeto](./media/code-test/create_python_project_in_vs.PNG)

    ![Explorador de Soluções](./media/code-test/solution_explorer_in_vs.PNG)

1. Insera o seu código de projeto no repositório de código do projeto Azure DevOps: 

    ![Repositório de código de projeto](./media/code-test/create_repo.PNG)

1. Suponha que tenha feito alguns trabalhos de preparação de dados, tais como ingestão de dados, engenharia de recursos e criação de colunas de etiquetas. Certifique-se de que o seu código está a gerar os resultados que espera. Aqui está um código que pode usar para testar se o código de processamento de dados está a funcionar corretamente:

    * Verifique se os nomes das colunas estão certos:
    
      ![Código para nomes de colunas correspondentes](./media/code-test/check_column_names.PNG)

    * Verifique se os níveis de resposta estão corretos:

      ![Código para níveis de correspondência](./media/code-test/check_response_levels.PNG)

    * Verifique se a percentagem de resposta é razoável:

      ![Código para percentagem de resposta](./media/code-test/check_response_percentage.PNG)

    * Verifique a taxa de falta de cada coluna nos dados:
    
      ![Código para taxa de falta](./media/code-test/check_missing_rate.PNG)


1. Depois de ter feito o trabalho de processamento de dados e engenharia de funcionalidades, e ter treinado um bom modelo, certifique-se de que o modelo que treinou pode marcar novos conjuntos de dados corretamente. Pode utilizar os dois seguintes testes para verificar os níveis de previsão e distribuição dos valores do rótulo:

    * Verifique os níveis de previsão:
    
      ![Código para verificar os níveis de previsão](./media/code-test/check_prediction_levels.PNG)

    * Verifique a distribuição dos valores de previsão:

      ![Código para verificar valores de previsão](./media/code-test/check_prediction_values.PNG)

1. Junte todas as funções de teste num guião python chamado **test_funcs.py:**

    ![Roteiro python para funções de teste](./media/code-test/create_file_test_func.PNG)


1. Depois de preparados os códigos de teste, pode configurar o ambiente de teste no Estúdio Visual.

   Crie um ficheiro Python chamado **test1.py.** Neste ficheiro, crie uma classe que inclua todos os testes que quiser fazer. O exemplo que se segue mostra seis testes preparados:
    
    ![Ficheiro Python com uma lista de testes numa aula](./media/code-test/create_file_test1_class.PNG)

1. Esses testes podem ser automaticamente descobertos se colocar **o codetest.testCase** após o nome da sua classe. Abra o Explorador de Teste no painel direito e selecione **Run All**. Todos os testes serão executados sequencialmente e dirão se o teste foi bem sucedido ou não.

    ![A fazer os testes](./media/code-test/run_tests.PNG)

1. Verifique o seu código no repositório do projeto utilizando comandos Git. O seu trabalho mais recente será refletido em breve no Azure DevOps.

    ![Git comandos para verificar código](./media/code-test/git_check_in.PNG)

    ![Trabalho mais recente em Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Configurar a construção e o teste automáticos em Azure DevOps:

    a. No repositório do projeto, selecione **Build and Release,** e depois selecione **+New** para criar um novo processo de construção.

    ![Seleções para iniciar um novo processo de construção](./media/code-test/create_new_build.PNG)

    b. Siga as instruções para selecionar a localização do código fonte, nome do projeto, repositório e informações de sucursais.
    
    ![Fonte, nome, repositório e informação sobre sucursais](./media/code-test/fill_in_build_info.PNG)

    c. Selecione um modelo. Porque não há modelo de projeto Python, comece por selecionar o **processo Empty**. 

    ![Lista de modelos e botão "Processo vazio"](./media/code-test/start_empty_process_template.PNG)

    d. Diga o nome da construção e selecione o agente. Pode escolher o padrão aqui se pretender utilizar um DSVM para completar o processo de construção. Para mais informações sobre a definição de [agentes,](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts)consulte a Build and release agents .
    
    ![Seleções de construção e agente](./media/code-test/select_agent.PNG)

    e. Selecione **+** no painel esquerdo, para adicionar uma tarefa para esta fase de construção. Como vamos executar o guião python **test1.py** para completar todos os cheques, esta tarefa é usar um comando PowerShell para executar o código Python.
    
    ![Painel "Adicionar tarefas" com powerShell selecionado](./media/code-test/add_task_powershell.PNG)

    f. Nos detalhes da PowerShell, preencha as informações necessárias, como o nome e a versão do PowerShell. Escolha o **Script Inline** como o tipo. 
    
    Na caixa em **Script Inline,** pode escrever **test1.py python**. Certifique-se de que a variável ambiental está corretamente configurada para python. Se precisar de uma versão ou núcleo diferente de Python, pode especificar explicitamente o caminho como mostrado na figura: 
    
    ![Detalhes da PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Selecione **Guardar & fila** para completar o processo de construção do gasoduto.

    ![Botão "Guardar & fila"](./media/code-test/save_and_queue_build_definition.PNG)

Agora, sempre que um novo compromisso é empurrado para o repositório de código, o processo de construção começará automaticamente. (Aqui usamos o mestre como repositório, mas você pode definir qualquer ramo.) O processo executa o ficheiro **test1.py** na máquina do agente para se certificar de que tudo definido no código funciona corretamente. 

Se os alertas forem configurados corretamente, será notificado por e-mail quando a construção estiver concluída. Também pode verificar o estado de construção em Azure DevOps. Se falhar, pode verificar os detalhes da construção e descobrir qual a peça partida.

![Notificação por e-mail do sucesso da construção](./media/code-test/email_build_succeed.PNG)

![Azure DevOps notificação de sucesso de construção](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Passos seguintes
* Consulte o [repositório](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) de previsão de rendimento da UCI para exemplos concretos de testes unitários para cenários de ciência de dados.
* Siga o contorno anterior e os exemplos do cenário de previsão de rendimento da UCI nos seus próprios projetos de ciência de dados.

## <a name="references"></a>Referências
* [Processo de Ciência de Dados de Equipa](https://aka.ms/tdsp)
* [Ferramentas de teste de estúdio visual](https://www.visualstudio.com/vs/features/testing-tools/)
* [Recursos de teste Azure DevOps](https://www.visualstudio.com/team-services/)
* [Máquinas Virtuais de Ciência de Dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
