---
title: 'Início rápido: Executar um bloco de notas na cloud'
titleSuffix: Azure Machine Learning service
description: Introdução ao serviço Azure Machine Learning. Utilize um servidor gerido na cloud para experimentar a sua área de trabalho.  A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: f47735a6e41ae14cb439e10803b82c0dbd3e64d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67115020"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início rápido: Utilizar um servidor de bloco de notas com base na cloud para começar a utilizar com o Azure Machine Learning

Não é necessária instalação.  Introdução ao serviço Azure Machine Learning com um servidor gerido na cloud. Se quiser em vez disso, instale-o em seu próprio ambiente de Python, veja [início rápido: Utilizar o seu próprio servidor de bloco de notas para começar a utilizar com o Azure Machine Learning](quickstart-run-local-notebook.md).

Este início rápido mostra como pode utilizar o [Azure Machine Learning a área de trabalho de serviço](concept-azure-machine-learning-architecture.md) para controlar as suas experimentações de machine learning.  Irá criar um [bloco de notas (pré-visualização) de VM](how-to-configure-environment.md#notebookvm), uma segura e baseado na nuvem do Azure estação de trabalho que fornece um servidor de bloco de notas do Jupyter, JupyterLab e um ambiente de ML totalmente preparado. Em seguida, execute um Python notebook nesta VM que valores de registo para a área de trabalho.

Neste início rápido, efetuar as seguintes ações:

* Criar uma área de trabalho
* Crie uma VM do bloco de notas na área de trabalho.
* Inicie a interface de web do Jupyter.
* Abra um bloco de notas que contém o código para calcular o instalador de plataforma e registos de erros em cada iteração.
* Execute o bloco de notas.
* Ver os valores de erro com sessão iniciada na sua área de trabalho. Este exemplo mostra como a área de trabalho pode ajudá-lo a manter o controlo das informações geradas num script.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se tiver uma área de trabalho do serviço do Azure Machine Learning, avance para o [próxima seção](#create-notebook). Caso contrário, crie uma agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Criar uma VM do bloco de notas

 Na área de trabalho, criar um recurso da nuvem para começar a utilizar blocos de notas do Jupyter. Este recurso oferece uma plataforma com base na cloud pré-configuradas com tudo o que precisa para executar o serviço Azure Machine Learning.

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  Se não tem a certeza de como localizar a sua área de trabalho no portal, consulte como [encontrar a sua área de trabalho](how-to-manage-workspace.md#view).

1. Na sua página de área de trabalho no portal do Azure, selecione **VMs de bloco de notas** à esquerda.

1. Selecione **+ novo** para criar uma VM do bloco de notas.

     ![Selecione a nova VM](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Forneça um nome para a sua VM. Em seguida, selecione **Criar**.

    > [!NOTE]
    > O nome da VM de bloco de notas tem de ser entre 2 a 16 carateres. Carateres válidos são letras, dígitos e o - caractere.  O nome tem também de ser exclusivo em toda a sua subscrição do Azure.

    ![Criar uma nova VM](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Aguarde aproximadamente 4 a 5 minutos, até que o estado muda para **em execução**.


## <a name="launch-jupyter-web-interface"></a>Iniciar a interface de web do Jupyter

Depois da VM está em execução, utilize o **VMs de bloco de notas** secção para iniciar a interface de web do Jupyter.

1. Selecione **Jupyter** no **URI** coluna para a sua VM.  

    ![Iniciar o servidor de bloco de notas do Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    A ligação inicia o servidor de bloco de notas e abre a página de Web de bloco de notas do Jupyter num novo separador do browser.  Esse link só funciona para a pessoa que cria a VM.

1. Na página de Web do bloco de notas Jupyter, o foldername superior é o nome de utilizador.  Selecione esta pasta.

1. Exemplos foldername inclui um número de versão, por exemplo **1.0.33.1 exemplos**.  Selecione a pasta de exemplos.

1. Selecione o **guia de introdução** bloco de notas.

## <a name="run-the-notebook"></a>Executar o bloco de notas

Execute um bloco de notas que as estimativas de instalador de plataforma e registra o erro em sua área de trabalho.

1. Selecione **01.run experiment.ipynb** para abrir o bloco de notas.

1. Se vir um alerta de "Kernel não encontrado", selecione o kernel **Python 3.6 - AzureML** (aproximadamente a meio para baixo na lista) e defina o kernel.

1. Clique na primeira célula de código e selecione **executar**.

    > [!NOTE]
    > Células de código têm Retos antes-los. Se os colchetes estão vazios ( __[]__ ), o código não tiver sido executado. Enquanto o código está em execução, verá um asterisco ( __[*]__ ). Após a conclusão de código, um número **[1]** aparece.  O número indica a ordem em que as células foi executado.
    >
    > Uso **Shift-Enter** como um atalho para executar uma célula.

    ![Executa a primeira célula de código](media/quickstart-run-cloud-notebook/cell1.png)

1. Execute a segunda célula do código. Se ver instruções para se autenticar, copie o código e siga a ligação para iniciar sessão. Depois de iniciar sessão, o browser irá se lembrar dessa configuração.  

    ![Autenticar](media/quickstart-run-cloud-notebook/authenticate.png)

1. Quando terminar, o número de células __[2]__ aparece.  Se tiver de iniciar sessão, verá uma mensagem de estado de autenticação com êxito.   Se não tiver de iniciar sessão, não verá quaisquer dados para esta célula, apenas o número é apresentado mostrar que a célula foi executada com êxito.

    ![Mensagem de êxito](media/quickstart-run-cloud-notebook/success.png)

1. Execute o restante das células de código.  Como cada célula termina a execução, verá o número de célula são apresentados. A última célula apresenta quaisquer outros dados.  

    Na maior célula de código, verá `run.log` utilizado em vários locais. Cada `run.log` adiciona o seu valor à sua área de trabalho.

## <a name="view-logged-values"></a>Ver valores registados

1. A saída do `run` célula contém uma ligação para o portal do Azure para ver os resultados da experimentação na sua área de trabalho.

    ![Ver experimentações](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Clique nas **ligação para o portal do Azure** para ver informações sobre a execução na sua área de trabalho.  Esta ligação abre-se a sua área de trabalho no portal do Azure.

1. Os gráficos de valores com sessão iniciada, que verá automaticamente foram criados na área de trabalho. Sempre que registar vários valores com o mesmo parâmetro de nome, é automaticamente gerado um desenho.

   ![Ver histórico](./media/quickstart-run-cloud-notebook/web-results.png)

Uma vez que o código para pi aproximado utiliza valores aleatórios, seus gráficos irão mostrar valores diferentes.  

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="stop-the-notebook-vm"></a>Parar o bloco de notas VM

Pare o bloco de notas VM quando não estiver a utilizá-lo a reduzir os custos.  

1. Na área de trabalho, selecione **VMs de bloco de notas**.

   ![Parar o servidor VM](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Na lista, selecione a VM.

1. Selecione **parar**.

1. Quando estiver pronto para utilizar o servidor novamente, selecione **iniciar**.

### <a name="delete-everything"></a>Eliminar tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Também pode manter o grupo de recursos mas eliminar uma área de trabalho única. Apresentar as propriedades de área de trabalho e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, concluir estas tarefas:

* Criar uma área de trabalho
* Crie uma VM do bloco de notas.
* Inicie a interface de web do Jupyter.
* Abra um bloco de notas que contém o código para calcular o instalador de plataforma e registos de erros em cada iteração.
* Execute o bloco de notas.
* Ver os valores de erro com sessão iniciada na sua área de trabalho.  Este exemplo mostra como a área de trabalho pode ajudá-lo a manter o controlo das informações geradas num script. 

Na página de Web de notas do Jupyter, procure os outros blocos de notas na pasta de exemplos para saber mais sobre o serviço Azure Machine Learning.

Para obter uma experiência de fluxo de trabalho detalhados, siga os tutoriais de Machine Learning para preparar e implementar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: Preparar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
