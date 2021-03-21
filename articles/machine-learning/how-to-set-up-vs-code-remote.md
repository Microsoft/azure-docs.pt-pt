---
title: Conecte-se à instância computecional no Código do Estúdio Visual (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a conectar-se a um computo Azure Machine Learning no Código do Estúdio Visual para executar o Jupyter Notebook interativo e cargas de trabalho de desenvolvimento remoto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070104"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Conecte-se a uma instância computacional de aprendizagem de máquinas Azure no Código do Estúdio Visual (pré-visualização)

Neste artigo, você vai aprender a conectar-se a uma instância de cálculo Azure Machine Learning usando Visual Studio Code.

[A Azure Machine Learning Compute Instance](concept-compute-instance.md) é uma estação de trabalho totalmente gerida baseada na nuvem para cientistas de dados e fornece capacidades de gestão e prontidão empresarial para administradores de TI.

Existem duas formas de se ligar a uma instância computacional do Código do Estúdio Visual:

* Servidor remoto do Bloco de Notas do Jupyter. Esta opção permite-lhe definir uma instância de cálculo como um servidor remoto do Jupyter Notebook.
* [Desenvolvimento remoto do Código do Estúdio Visual](https://code.visualstudio.com/docs/remote/remote-overview). O desenvolvimento remoto do Código do Estúdio Visual permite-lhe utilizar um contentor, uma máquina remota ou o Subsistema Windows para Linux (WSL) como um ambiente de desenvolvimento completo.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurar a instância computacional como servidor de caderno remoto

Para configurar uma instância computacional como um servidor remoto do Jupyter Notebook, você precisará de alguns pré-requisitos:

* Extensão do Código do Estúdio Visual Azure Machine Learning. Para obter mais informações, consulte o [guia de configuração do código do estúdio visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)
* Espaço de trabalho de aprendizagem automática Azure. [Utilize a extensão do Código de Estúdio Visual Azure Machine Learning para criar um novo espaço de trabalho](how-to-manage-resources-vscode.md#create-a-workspace) se ainda não tiver um.

Para ligar a uma instância computacional:

1. Abra um Caderno Jupyter em Código de Estúdio Visual.
1. Quando o portátil integrado experimentar cargas, selecione **Jupyter Server**.

    > [!div class="mx-imgBorder"]
    > ![Lançamento Azure Machine Learning remoto Jupyter Notebook servidor dropdown](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Em alternativa, também utiliza a paleta de comando:

    1. Abra a paleta de comando selecionando **a Paleta de Comando Ver >** da barra de menu.
    1. Introduza na caixa de texto `Azure ML: Connect to Compute instance Jupyter server` .

1. Escolha `Azure ML Compute Instances` entre a lista de opções do servidor Jupyter.
1. Selecione a sua subscrição a partir da lista de subscrições. Se já configurado previamente o seu espaço de trabalho de Aprendizagem automática Azure, este passo é ignorado.
1. Selecione o seu espaço de trabalho.
1. Selecione a sua instância de cálculo da lista. Se não tiver um, **selecione Criar a nova Instância de Computação Azure ML** e siga as instruções para criar uma.
1. Para que as alterações entrem em vigor, tem de recarregar o Código do Estúdio Visual.
1. Abre um Caderno Jupyter e dirige uma cela.

> [!IMPORTANT]
> **Tem de** executar uma célula para estabelecer a ligação.

Neste momento, pode continuar a executar células no seu Caderno Jupyter.

> [!TIP]
> Também pode trabalhar com ficheiros de script Python (.py) que contêm células de código semelhantes a Jupyter. Para mais informações, consulte a [documentação interativa Visual Studio Code Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Configurar o desenvolvimento remoto de instância computacional

Para uma experiência de desenvolvimento remoto em destaque, você precisará de alguns pré-requisitos:

* [Extensão SSH remota do código do estúdio visual](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Instância computacional ativada pelo SSH. Para obter mais informações, [consulte o guia Criar um conjunto de casos de computação.](how-to-create-manage-compute-instance.md)

> [!NOTE]
> Nas plataformas do Windows, tem de [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se ainda não estiver presente. PuTTY não é suportado no Windows, uma vez que o comando ssh deve estar no caminho.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Obtenha a porta IP e SSH para a sua instância computacional

1. Vá ao estúdio Azure Machine Learning em https://ml.azure.com/ .
2. Selecione o seu [espaço de trabalho](concept-workspace.md).
1. Clique no **separador Instâncias computacional.**
1. Na coluna URI de **aplicação,** clique na ligação **SSH** da instância de computação que pretende utilizar como um cálculo remoto. 
1. No diálogo, tome nota do endereço IP e da porta SSH. 
1. Guarde a sua chave privada para o diretório ~/ssh/ no seu computador local; por exemplo, abrir um editor para um novo ficheiro e colar a chave em: 

   **Linux:**

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   A chave privada será um pouco assim:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Altere as permissões no ficheiro para se certificar de que só pode ler o ficheiro.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Adicionar instância como hospedeiro

Abra o ficheiro `~/.ssh/config` (Linux) ou `C:\Users<username>.ssh\config` (Windows) num editor e adicione uma nova entrada semelhante ao conteúdo abaixo:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Aqui alguns detalhes sobre os campos:

|Campo|Descrição|
|----|---------|
|Anfitrião|Use a abreviatura que quiser para a instância computacional |
|Nome do Anfitrião|Este é o endereço IP da instância computacional |
|Porta|Esta é a porta mostrada no diálogo SSH acima |
|User|Isto tem de ser `azureuser` |
|Ficheiro de Identidade|Deve apontar para o ficheiro onde guardou a chave privada |

Agora, você deve ser capaz de ssh para a sua instância de cálculo usando a abreviatura que você usou acima, `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Ligue o Código VS ao caso

1. Clique no ícone Remote-SSH da barra de atividades do Código do Estúdio Visual para mostrar as suas configurações SSH.

1. Clique com o botão direito na configuração do anfitrião SSH que acabou de criar.

1. Selecione **Ligar ao anfitrião na janela atual**. 

A partir de agora, está a trabalhar inteiramente na instância computacional e agora pode editar, depurar, usar git, usar extensões, etc.

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Visual Studio Code Remote, podes usar uma instância computacional como computação remota do Código do Estúdio Visual para [depurar interativamente o teu código.](how-to-debug-visual-studio-code.md)

[Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
