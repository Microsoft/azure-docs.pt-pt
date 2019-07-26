---
title: 'Início rápido: Executar um bloco de anotações na nuvem'
titleSuffix: Azure Machine Learning service
description: Este tutorial mostra como começar com o serviço de Azure Machine Learning e usar um servidor de notebook gerenciado na nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371049"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início rápido: Use um servidor de bloco de anotações baseado em nuvem para começar a usar o Azure Machine Learning

Este guia de início rápido ensina como começar a usar o serviço de Azure Machine Learning usando um servidor de notebook gerenciado na nuvem. Nenhuma instalação é necessária. Se você quiser instalar o SDK em seu próprio ambiente do Python, consulte [início rápido: Use seu próprio servidor de bloco de anotações para começar](quickstart-run-local-notebook.md)a usar o Azure Machine Learning.

Este guia de início rápido mostra como usar o [Azure Machine Learning espaço de trabalho de serviço](concept-azure-machine-learning-architecture.md) para controlar seus experimentos de ml (aprendizado de máquina). Você faz isso criando uma [máquina virtual de notebook (versão prévia)](how-to-configure-environment.md#notebookvm): uma estação de trabalho do Azure segura e baseada em nuvem que fornece um servidor de notebook Jupyter, JupyterLab e um ambiente de ml totalmente preparado. Em seguida, você executa um notebook do Python nessa VM (máquina virtual) que registra valores no espaço de trabalho.

Para fazer isso, execute as seguintes ações:

* Crie um espaço de trabalho.
* Crie uma VM do bloco de anotações em seu espaço de trabalho.
* Abra a interface da Web do Jupyter.
* Abra um bloco de anotações que contenha código para estimar o PI e registrar erros em cada iteração.
* Execute o bloco de anotações.
* Exiba os valores de erro registrados em seu espaço de trabalho. O exemplo a seguir mostra como o espaço de trabalho ajuda você a manter o controle das informações geradas em um script.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Se você tiver um espaço de trabalho de serviço Azure Machine Learning, pule para a [próxima seção](#create-notebook). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Criar uma VM do notebook

 Em seu espaço de trabalho, crie um recurso de nuvem para começar a usar notebooks Jupyter. Esse recurso é uma plataforma baseada em nuvem que é pré-configurada com tudo o que você precisa para executar o serviço de Azure Machine Learning.

1. Abra seu espaço de trabalho no [portal do Azure](https://portal.azure.com/). Se você não tiver certeza de como localizar seu espaço de trabalho no portal, consulte como [exibir seu espaço de trabalho](how-to-manage-workspace.md#view).

1. Na página do espaço de trabalho, selecione **VMs do notebook** à esquerda.

1. Selecione **+ novo** para criar uma VM de notebook.  

     ![Selecionar nova VM](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Forneça um nome para sua VM. Em seguida, selecione **Criar**.

    > [!NOTE]
    > O nome da VM do bloco de anotações deve ter entre 2 e 16 caracteres. Letras, dígitos e hifens são caracteres válidos. O nome deve ser exclusivo em toda a sua assinatura do Azure.

    ![Criar uma nova VM](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Aguarde aproximadamente 4 a 5 minutos até que o status seja alterado para **em execução**.


## <a name="open-the-jupyter-web-interface"></a>Abrir a interface da Web do Jupyter

Depois que a VM estiver em execução, use a seção de **VMs do notebook** para abrir a interface da Web Jupyter.

1. Selecione **Jupyter** na coluna **URI** para sua VM.  

    ![Iniciar o servidor do Jupyter Notebook](./media/quickstart-run-cloud-notebook/start-server.png)

    O link inicia o servidor do bloco de anotações e abre a página da Web do Jupyter notebook em uma nova guia do navegador.  Esse link funcionará apenas para a pessoa que cria a VM.  Cada usuário do espaço de trabalho deve criar sua própria VM.

1. Na página da Web do Jupyter notebook, o nome da pasta superior é seu nome de usuário. Selecione esta pasta.

    > [!TIP]
    > Essa pasta está localizada no [contêiner de armazenamento](concept-workspace.md#resources) em seu espaço de trabalho em vez de na própria VM do bloco de anotações.  Você pode excluir a VM do notebook e ainda manter todo o seu trabalho.  Quando você criar uma nova VM de bloco de anotações mais tarde, ela carregará essa mesma pasta.  Se você compartilhar seu espaço de trabalho com outras pessoas, ele verá sua pasta e você verá seus. 

1. O nome da pasta de exemplos inclui um número de versão (**Samples-1.0.33.1**, por exemplo). Selecione a pasta de exemplos.

1. Selecione a pasta **início rápido** .

## <a name="run-the-notebook"></a>Executar o bloco de notas

Execute um bloco de anotações que estima PI e registra o erro em seu espaço de trabalho.

1. Selecione **01. Run-experimento. ipynb** para abrir o bloco de anotações.

1. Se você vir um alerta "kernel não encontrado", selecione o kernel **Python 3,6-AzureML** (aproximadamente na metade inferior da lista) e defina o kernel.

1. Selecione a primeira célula de código e, em seguida, selecione **executar**.

    > [!NOTE]
    > As células de código têm colchetes antes delas. Se os colchetes estiverem vazios ( __[]__ ), o código não foi executado. Enquanto o código está em execução, um asterisco é exibido ( __[*]__ ). Depois que o código for concluído, o número **[1]** será exibido.  O número informa a ordem em que as células foram executadas.
    >
    > Use **Shift + Enter** como um atalho para executar uma célula.

    ![Executar a primeira célula de código](media/quickstart-run-cloud-notebook/cell1.png)

1. Execute a segunda célula de código. Se você vir instruções para autenticar, copie o código e siga o link para entrar. Depois de entrar, seu navegador se lembrará dessa configuração.  

    ![Autenticar](media/quickstart-run-cloud-notebook/authenticate.png)

1. Quando a execução da célula de código for bem-sucedida, o número da célula __[2]__ será exibido. Se você precisasse entrar, verá uma mensagem de status de autenticação bem-sucedida.   Se não tiver de entrar, você não verá nenhuma saída para essa célula. Você verá apenas o número, que aparece para mostrar que a célula foi executada com êxito.

    ![Mensagem de êxito](media/quickstart-run-cloud-notebook/success.png)

1. Execute o restante das células de código. À medida que cada célula termina de ser executada, seu número de célula é exibido. Somente a última célula exibe qualquer outra saída.  

    Na maior célula de código, `run.log` aparece em vários lugares. Cada `run.log` um adiciona seu valor ao seu espaço de trabalho.

## <a name="view-logged-values"></a>Ver valores registados

1. A saída da `run` célula contém um link de volta para o portal do Azure, onde você pode exibir os resultados do experimento em seu espaço de trabalho.

    ![Ver experimentações](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Selecione **vincular ao portal do Azure** para exibir informações sobre a execução em seu espaço de trabalho. Esse link abre seu espaço de trabalho no portal do Azure.

1. As plotagens dos valores registrados que você vê foram criadas automaticamente no espaço de trabalho. Sempre que registar vários valores com o mesmo parâmetro de nome, é automaticamente gerado um desenho. Segue-se um exemplo:

   ![Ver histórico](./media/quickstart-run-cloud-notebook/web-results.png)

Como o código para PI aproximado usa valores aleatórios, suas plotagens podem parecer diferentes.  

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="stop-the-notebook-vm"></a>Parar a VM do notebook

Pare a VM do notebook quando você não a estiver usando para reduzir o custo.  

1. Em seu espaço de trabalho, selecione **máquinas virtuais do notebook**.

   ![Parar o servidor de VM](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Na lista, selecione a VM.

1. Selecione **parar**.

1. Quando você estiver pronto para usar o servidor novamente, selecione **Iniciar**.

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Depois de concluir essas tarefas, vá para a página da Web Jupyter Notebook. Na pasta **início rápido** , abra e execute o notebook **02. Deploy-Web-Service. ipynb** para saber como implantar um serviço Web.

Quando você quiser instalar outros pacotes do Python em seu ambiente Jupyter, use este código dentro de um notebook:

```
!source activate py36 && pip install <packagename>
```

Também na página da Web Jupyter Notebook, navegue por outros blocos de anotações na pasta de exemplos para saber mais sobre o serviço Azure Machine Learning.

Para obter uma experiência de fluxo de trabalho aprofundada, siga Machine Learning tutoriais para treinar e implantar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
