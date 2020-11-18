---
title: Criar e gerir recursos VS Extensão de Código (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a criar e gerir os recursos de Aprendizagem automática Azure utilizando a extensão do Código do Estúdio Visual Azure Machine Learning.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701420"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Gerir os recursos de aprendizagem automática Azure com a extensão do código VS (pré-visualização)

Saiba como gerir os recursos de Aprendizagem automática Azure com a extensão do Código VS.

![Extensão de código VS de aprendizagem de máquina de Azure](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se não tiver um, inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se não o tiver, [instale-o.](https://code.visualstudio.com/docs/setup/setup-overview)
- VS Código Azure Machine Learning Extension. Siga o [guia de instalação de extensão Azure Machine Learning VS Code](tutorial-setup-vscode-extension.md#install-the-extension) para instalar a extensão.

Todos os processos abaixo assumem que está na vista Azure Machine Learning no Código do Estúdio Visual. Para lançar a extensão, selecione o ícone **Azure** na barra de atividade do Código VS.

## <a name="workspaces"></a>Áreas de trabalho

Para mais informações, consulte [espaços de trabalho.](concept-workspace.md)

### <a name="create-a-workspace"></a>Criar uma área de trabalho

1. Na vista Azure Machine Learning, clique com o botão direito no nó de subscrição e selecione **Criar espaço de trabalho**.
1. No aviso:
    1. Forneça um nome para o seu espaço de trabalho
    1. Escolher a sua subscrição do Azure
    1. Escolha ou crie um novo grupo de recursos para a disponibilização do espaço de trabalho em
    1. Selecione o local onde providenciar o espaço de trabalho.

Os métodos alternativos para criar um espaço de trabalho incluem:

- Abra a paleta de comando **Ver > Paleta de Comando** e introduza no texto prompt **Azure ML: Create Workspace**.
- Clique `+` no ícone no topo da vista Azure Machine Learning.
- Crie um novo espaço de trabalho quando solicitado para selecionar um espaço de trabalho durante o fornecimento de outros recursos.

### <a name="remove-a-workspace"></a>Remover um espaço de trabalho

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Clique com o botão direito no espaço de trabalho que pretende remover.
1. Selecione se deseja remover:
    - *Apenas o espaço de trabalho*: Esta opção elimina **apenas** o recurso Azure do espaço de trabalho. O grupo de recursos, as contas de armazenamento e quaisquer outros recursos a que o espaço de trabalho foi anexado ainda estão em Azure.
    - *Com recursos associados*: Esta opção elimina o espaço de trabalho **e** todos os recursos que lhe estão associados.

## <a name="datastores"></a>Arquivos de dados

A extensão do Código VS suporta atualmente as datas-tores dos seguintes tipos:

- Partilha de Ficheiros do Azure
- Armazenamento de Blobs do Azure

Quando se cria um espaço de trabalho, é criada uma datastore para cada um destes tipos.

Para mais informações, consulte [as lojas de dados.](concept-data.md#datastores)

### <a name="create-a-datastore"></a>Criar uma loja de dados

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho que pretende criar a datastore sob.
1. Clique com o botão direito no nó **datastores** e selecione **Registar datastore**.
1. No aviso:
    1. Forneça um nome para a sua loja de dados.
    1. Escolha o tipo de datastore.
    1. Selecione o seu recurso de armazenamento. Pode escolher um recurso de armazenamento associado ao seu espaço de trabalho ou selecionar a partir de qualquer recurso de armazenamento válido nas suas subscrições Azure.
    1. Escolha o recipiente onde os seus dados estão dentro do recurso de armazenamento previamente selecionado.
1. Um ficheiro de configuração aparece no Código VS. Se estiver satisfeito com o seu ficheiro de configuração, **selecione Save and continue** ou abra a paleta de comando vs Código **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="manage-a-datastore"></a>Gerir uma datastore

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expanda o nó **Datastores** dentro do seu espaço de trabalho.
1. Selecione a loja de dados que deseja:
    - *Definir como padrão*. Sempre que fizer experiências, esta é a datastore que será usada.
    - *Inspecione as definições apenas de leitura*.
    - *Modificar*. Alterar o tipo de autenticação e as credenciais. Os tipos de autenticação suportado incluem a chave de conta e o token SAS.

## <a name="datasets"></a>Conjuntos de dados

A extensão suporta atualmente os seguintes tipos de conjunto de dados:

- *Tabular*: Permite materializar dados num DataFrame (Pandas ou PySpark).
- *Arquivo*: Um ficheiro ou recolha de ficheiros. Permite-lhe descarregar ou montar ficheiros no seu cálculo.

Para mais informações, consulte [conjuntos de dados](concept-data.md#datasets)

### <a name="create-dataset"></a>Criar conjunto de dados

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho que pretende criar a datastore sob.
1. Clique com o botão direito no nó **datasets** e **selecione Criar conjunto de dados**.
1. No aviso:
    1. Escolha o tipo de conjunto de dados
    1. Defina se os dados estão localizados no seu PC ou na web
    1. Forneça a localização dos seus dados. Isto pode ser um único ficheiro ou um diretório contendo os seus ficheiros de dados.
    1. Escolha a loja de dados para a qual pretende fazer o upload dos seus dados.
    1. Forneça um prefixo que ajude a identificar o seu conjunto de dados na datastore.

### <a name="version-datasets"></a>Conjuntos de dados de versão

Ao construir modelos de machine learning, à medida que os dados mudam, poderá querer ver versão do seu conjunto de dados. Para fazê-lo na extensão do Código VS:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expandir o nó **datasets.**
1. Clique com o botão de dados que pretende ver e selecione **Criar Nova Versão**.
1. No aviso:
    1. Selecione o tipo de conjunto de dados
    1. Defina se os dados estão localizados no seu PC ou na web.
    1. Forneça a localização dos seus dados. Isto pode ser um único ficheiro ou um diretório contendo os seus ficheiros de dados.
    1. Escolha a loja de dados para a qual pretende fazer o upload dos seus dados.
    1. Forneça um prefixo que ajude a identificar o seu conjunto de dados na datastore.

### <a name="view-dataset-properties"></a>Ver propriedades do conjunto de dados

Esta opção permite-lhe ver metadados associados a um conjunto de dados específico. Para fazê-lo na extensão do Código VS:

1. Expanda o nó do espaço de trabalho.
1. Expandir o nó **datasets.**
1. Clique com o botão de dados que pretende inspecionar e selecione **Ver Propriedades do Conjunto de Dados**. Isto apresentará um ficheiro de configuração com as propriedades da versão mais recente do conjunto de dados.

> [!NOTE]
> Se tiver várias versões do seu conjunto de dados, pode optar por apenas visualizar as propriedades do conjunto de dados de uma versão específica, expandindo o nó do conjunto de dados e executando os mesmos passos descritos nesta secção na versão de interesse.

### <a name="unregister-datasets"></a>Conjuntos de dados não registados

Para remover um conjunto de dados e toda a versão do mesmo, não regiser o registo. Para fazê-lo na extensão do Código VS:

1. Expanda o nó do espaço de trabalho.
1. Expandir o nó **datasets.**
1. Clique com o botão de dados que pretende não registar e selecione **Conjunto de dados não registador**.

## <a name="environments"></a>Ambientes

Para mais informações, consulte [os ambientes.](concept-environments.md)

### <a name="create-environment"></a>Criar ambiente

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho que pretende criar a datastore sob.
1. Clique com o botão direito no nó **Ambientes** e selecione **Criar Ambiente.**
1. No aviso:
    1. Forneça um nome para o seu ambiente
    1. Defina a configuração do seu ambiente:
        - *Ambientes curados*: Ambientes pré-configurados em Azure Machine Learning. Pode personalizar ainda mais o ambiente modificando a `dependencies` propriedade no ficheiro JSON. Saiba mais sobre [ambientes curados.](resource-curated-environments.md)
        - *Ficheiro de dependências conda*: Para ambientes Anaconda, o ficheiro que contém a definição de ambiente pode ser fornecido.
        - *Ficheiro de requisitos do Pip*: Para ambientes de pip, o ficheiro que contém a definição de ambiente pode ser fornecido.
        - *Ambiente Conda existente*: Esta opção procura os ambientes conda no seu PC local e tenta construir um ambiente a partir do ambiente selecionado.
        - *Costume*: Defina os seus próprios canais e dependências
    1. Um ficheiro de configuração abre no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="view-environment-configurations"></a>Ver configurações de ambiente

Para visualizar as dependências e configurações para um ambiente específico na extensão:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expandir o nó **ambiente.**
1. Clique com o botão direito no ambiente que pretende visualizar e selecione **Ver Ambiente**.

### <a name="edit-environment-configurations"></a>Editar configurações ambientais

Para editar as dependências e configurações para um ambiente específico na extensão:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Ambiente dentro** do seu espaço de trabalho.
1. Clique com o botão direito no ambiente que pretende visualizar e selecione **Editar Ambiente.**
1. Depois de efexar as modificações, se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando vs Código **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

## <a name="experiments"></a>Experimentações

Para mais informações, consulte [as experiências.](concept-azure-machine-learning-architecture.md#experiments)

### <a name="create-experiment"></a>Criar a experimentação

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito no nó **experimentos** no seu espaço de trabalho e selecione **Criar experiência.**
1. No aviso, forneça um nome para a sua experiência.

### <a name="run-experiment"></a>Experiência de execução

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro do seu espaço de trabalho.
1. Clique com a direita na experiência que deseja executar.
1. Selecione o ícone **Run Experiment** na barra de atividade.
1. Selecione se deseja executar a sua experiência localmente ou remotamente. Consulte o [guia de depuragem](how-to-debug-visual-studio-code.md) para obter mais informações sobre experiências de corrida e depuragem localmente.
1. Escolha a sua subscrição.
1. Escolha o espaço de trabalho Azure ML para executar a experiência.
1. Escolha a sua experiência.
1. Escolha ou crie um computacional para executar a experiência.
1. Escolha ou crie uma configuração de execução para a sua experiência.

Em alternativa, pode selecionar o botão **Run Experiment** na parte superior da extensão e configurar a sua experiência executada no pedido.

### <a name="view-experiment"></a>Ver experiência

Para ver a sua experiência no Azure Machine Learning Studio:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro do seu espaço de trabalho.
1. Clique com o botão direito na experiência que pretende visualizar e selecione **Ver Experimento**. 
1. Um pedido aparece pedindo-lhe para abrir o URL de experiência no estúdio Azure Machine Learning. Selecione **Abrir**.

### <a name="track-run-progress"></a>Progresso da corrida de pista

Enquanto estás a fazer a tua experiência, talvez queiras ver o seu progresso. Para acompanhar o progresso de uma corrida no estúdio Azure Machine Learning a partir da extensão:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro do seu espaço de trabalho.
1. Expanda o nó de experiência para o que pretende seguir o progresso.
1. Clique com o botão direito na execução e selecione **Ver Executar no portal Azure**.
1. Um pedido aparece pedindo-lhe para abrir o URL de execução no estúdio Azure Machine Learning. Selecione **Abrir**.

### <a name="download-run-logs--outputs"></a>Descarregue registos de execução & saídas

Uma vez concluída uma execução, poderá querer descarregar os registos e ativos, como o modelo gerado como parte de uma experiência.

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **experimentos** dentro do seu espaço de trabalho.
1. Expanda o nó de experiência para o que pretende seguir o progresso.
1. Clique à direita na corrida:
    - Para descarregar as saídas, selecione **Baixar as saídas**.
    - Para descarregar os registos, **selecione Baixar registos**.

### <a name="view-run-metadata"></a>Ver metadados executados

Na extensão, pode inspecionar metadados como a configuração de execução utilizada para a execução, bem como executar detalhes.

## <a name="compute-instances"></a>Instâncias de computação

Para obter mais informações, consulte [as instâncias computacional](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Criar instância computacional

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho que pretende criar a instância de computação.
1. Clique com o botão direito no nó **de instâncias computacional** e selecione **Criar instância de computação**.
1. No aviso:
    1. Forneça um nome para a sua instância de computação.
    1. Selecione um tamanho VM da lista.
    1. Escolha se deseja ativar o acesso ao SSH.
        1. Se ativar o acesso ao SSH, também terá de fornecer a chave SSH pública ou o ficheiro que contém a chave. Para obter mais informações, consulte o [guia sobre a criação e utilização das teclas SSH no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>Parar ou reiniciar a instância de computação

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de instância compute** dentro do seu espaço de trabalho.
1. Clique com o botão direito na instância de cálculo que pretende parar ou reiniciar e selecione **Para parar a instância do Compute** ou reiniciar a instância de **cálculo,** respectivamente.

### <a name="view-compute-instance-configuration"></a>Ver configuração de instância computacional

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de instância compute** dentro do seu espaço de trabalho.
1. Clique com o botão direito na instância de cálculo que pretende inspecionar e selecione **Ver Casos Compute Propriedades**.

### <a name="delete-compute-instance"></a>Eliminar instância de cálculo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de instância compute** dentro do seu espaço de trabalho.
1. Clique com o botão direito na instância de cálculo que pretende eliminar e selecione **Delete compute instance**.

## <a name="compute-clusters"></a>Clusters computacional

A extensão suporta os seguintes tipos de cálculo:

- Aglomerado de computação Azure Machine Learning
- Azure Kubernetes Service

Para obter mais informações, consulte [os alvos do cálculo.](concept-compute-target.md#train)

### <a name="create-compute"></a>Criar cálculo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho que pretende criar o cluster de computação sob.
1. Clique com o botão direito no nó **de clusters compute** e selecione **Create Compute**.
1. No aviso:
    1. Escolha um tipo de cálculo
    1. Selecionar o tamanho da VM. Saiba mais sobre [os tamanhos de VM.](../virtual-machines/sizes.md)
    1. Forneça um nome para o seu cálculo.

### <a name="view-compute-configuration"></a>Ver configuração computacional

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de clusters Compute** dentro do seu espaço de trabalho.
1. Clique com o botão certo no cálculo que pretende visualizar e selecione **Ver Propriedades compute**.

### <a name="edit-compute-scale-settings"></a>Editar definições de escala de cálculo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de clusters Compute** dentro do seu espaço de trabalho.
1. Clique com o botão certo para editar e selecione **Editar Compute**.
1. Um ficheiro de configuração para o seu cálculo abre no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="delete-compute"></a>Eliminar computação

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de clusters Compute** dentro do seu espaço de trabalho.
1. Clique com o botão direito para eliminar e selecione **Delete Compute**.

### <a name="create-run-configuration"></a>Criar configuração de execução

Para criar uma configuração de execução na extensão:

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **de clusters Compute** dentro do seu espaço de trabalho.
1. Clique com o botão direito no alvo de cálculo que pretende criar a configuração de execução sob e selecione **Criar Configuração de Execução**.
1. No aviso:
    1. Forneça um nome para o seu alvo de computação
    1. Escolha ou crie um novo ambiente.
    1. Digite o nome do script que pretende executar ou **pressione Enter** para navegador para o script no seu computador local.
    1. (Opcional) Escolheu se pretende criar uma referência de dados para a sua formação. Ao fazê-lo, irá solicitar-lhe que defina um conjunto de dados na sua configuração de execução.
        1. Selecione de um dos seus conjuntos de dados registados para ligar à configuração de execução Um ficheiro de configuração para o seu conjunto de dados abre no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.
    1. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="edit-run-configuration"></a>Editar configuração de execução

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do cluster compute no nó de **clusters Compute** do seu espaço de trabalho.
1. Clique com o botão direito na configuração de execução que pretende editar e selecione **Editar Configuração de execução**.
1. Um ficheiro de configuração para a sua configuração de execução abre no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="delete-run-configuration"></a>Eliminar configuração de execução

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Expandir o nó de interesse do cluster compute dentro do nó **de clusters Compute.**
1. Clique com o botão direito na configuração de execução que pretende editar e selecione **Eliminar a Configuração de Execução**.

## <a name="models"></a>Modelos

Para mais informações, consulte [os modelos](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Registar o modelo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito no nó **Modelos** e selecione **Register Model**.
1. No aviso:
    1. Forneça um nome para o seu modelo
    1. Escolha se o seu modelo é um ficheiro ou pasta.
    1. Encontre o modelo no seu PC local.
    1. Um ficheiro de configuração para o seu modelo no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

### <a name="view-model-properties"></a>Ver propriedades do modelo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Modelos** dentro do seu espaço de trabalho.
1. Clique com o botão direito no modelo cujas propriedades pretende ver e selecione **Ver Propriedades do Modelo**. Um ficheiro abre no editor que contém as propriedades do seu modelo.

### <a name="download-model"></a>Modelo de descarregamento

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Modelos** dentro do seu espaço de trabalho.
1. Clique com o botão direito no modelo que pretende descarregar e selecione **Download Model File**.

### <a name="delete-a-model"></a>Eliminar um modelo

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Modelos** dentro do seu espaço de trabalho.
1. Clique com o botão direito no modelo que pretende eliminar e selecione **Remover Modelo**.

## <a name="endpoints"></a>Pontos Finais

A extensão do Código VS suporta os seguintes objetivos de implantação:

- Azure Container Instances
- Azure Kubernetes Service

Para mais informações, consulte [os pontos finais do serviço web.](concept-azure-machine-learning-architecture.md#web-service-endpoint)

### <a name="create-deployments"></a>Criar implementações

> [!NOTE]
> A criação de implantação atualmente só funciona com ambientes Conda.

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito no nó **endpoints** e selecione **o Serviço de Implantação**.
1. No aviso:
    1. Escolha se pretende utilizar um modelo já registado ou um ficheiro de modelo local.
    1. Selecionar o modelo
    1. Escolha o alvo de implantação para o qual pretende implementar o seu modelo.
    1. Forneça um nome para o seu modelo.
    1. Forneça o script para executar ao marcar o modelo.
    1. Forneça um ficheiro de dependências da Conda.
    1. Um ficheiro de configuração para a sua implantação aparece no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.

> [!NOTE]
> Em alternativa, pode clicar com o botão direito num modelo registado no nó *Modelos* e selecionar o **Serviço de Implantação a partir do Modelo Registado.**

### <a name="delete-deployments"></a>Eliminar implementações

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Endpoints** dentro do seu espaço de trabalho.
1. Clique com o botão direito na implementação que pretende remover e selecione **Remover o serviço**.
1. Aparece um aviso confirmando que pretende remover o serviço. Selecione **OK**.

### <a name="manage-deployments"></a>Gerir implementações

Além de criar e eliminar implementações, pode visualizar e editar as definições associadas à implementação.

1. Expanda o nó de subscrição que contém o seu espaço de trabalho.
1. Expanda o nó **Endpoints** dentro do seu espaço de trabalho.
1. Clique com o botão direito na implementação que pretende gerir:
    - Para editar as definições, **selecione o serviço Editar**.
        - Um ficheiro de configuração para a sua implantação aparece no editor. Se estiver satisfeito com a sua configuração, **selecione Save and continue** ou abra a paleta de comando VS Code **(Ver > Paleta de Comando)** e **digite Azure ML: Save and Continue**.
    - Para visualizar as definições de configuração da implementação, selecione **Ver as propriedades do serviço**.

## <a name="next-steps"></a>Passos seguintes

[Treine um modelo de classificação de imagem](tutorial-train-deploy-image-classification-model-vscode.md) com a extensão do Código VS.