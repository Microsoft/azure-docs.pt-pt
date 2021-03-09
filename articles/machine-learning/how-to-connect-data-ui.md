---
title: Conecte-se a dados nos serviços de armazenamento em Azure
titleSuffix: Azure Machine Learning
description: Crie datastores e conjuntos de dados para conectar de forma segura aos dados nos serviços de armazenamento em Azure com o estúdio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 0e2230bdcf2b2f4358db4a0d600506b711f39c45
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508236"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Conecte-se aos dados com o estúdio Azure Machine Learning

Neste artigo, aprenda a aceder aos seus dados com o [estúdio Azure Machine Learning](overview-what-is-machine-learning-studio.md). Conecte-se aos seus dados nos serviços de armazenamento em Azure com [as lojas de dados Azure Machine Learning](how-to-access-data.md), e, em seguida, embale esses dados para tarefas nos seus fluxos de trabalho de ML com [conjuntos de dados de Aprendizagem de Máquinas Azure](how-to-create-register-datasets.md).

A tabela a seguir define e resume os benefícios das datas-tores e conjuntos de dados. 

|Objeto|Descrição| Benefícios|   
|---|---|---|
|Arquivos de dados| Ligue-se de forma segura ao seu serviço de armazenamento no Azure, armazenando as suas informações de conexão, como o seu ID de subscrição e a autorização simbólica no seu [Cofre-Chave](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho | Porque a sua informação está armazenada de forma segura, <br><br> <li> Não &nbsp; coloque &nbsp; em risco credenciais de autenticação &nbsp; ou &nbsp; &nbsp; fontes de &nbsp; dados originais. <li> Já não precisas de os codificar nos teus scripts.
|Conjuntos de dados| Ao criar um conjunto de dados, cria uma referência para a localização da origem de dados, juntamente com uma cópia dos metadados. Com conjuntos de dados pode, <br><br><li> Aceder aos dados durante o treino de modelo.<li> Partilhe dados e colabore com outros utilizadores.<li> Alavancar bibliotecas de código aberto, como pandas, para exploração de dados. | Como os conjuntos de dados são avaliados preguiçosamente, e os dados permanecem na sua localização existente, <br><br><li>Guarde uma única cópia de dados no seu armazenamento.<li> Incorrer sem custos de armazenamento extra <li> Não se arrisque a alterar involuntariamente as suas fontes de dados originais.<li>Melhorar as velocidades de desempenho do fluxo de trabalho ML. 

Para entender onde as datas e conjuntos de dados se encaixam no fluxo de trabalho global de acesso de dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](concept-data.md#data-workflow)

Para uma primeira experiência de código, consulte os seguintes artigos para utilizar o [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) para:
* [Ligue-se aos serviços de armazenamento da Azure com as datastores.](how-to-access-data.md) 
* [Criar conjuntos de dados de aprendizagem automática Azure](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Acesso ao [estúdio Azure Machine Learning](https://ml.azure.com/).

- Uma área de trabalho do Azure Machine Learning. Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md)

    -  Quando cria um espaço de trabalho, um recipiente de blob Azure e uma partilha de ficheiros Azure são automaticamente registados como datastores para o espaço de trabalho. São nomeados `workspaceblobstore` `workspacefilestore` e, respectivamente. Se o armazenamento de bolhas for suficiente para as suas `workspaceblobstore` necessidades, o é definido como a datastore padrão, e já configurado para utilização. Caso contrário, necessita de uma conta de armazenamento no Azure com um [tipo de armazenamento suportado](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Criar datastores

Pode criar datastores a partir [destas soluções de armazenamento Azure](how-to-access-data.md#matrix). **Para soluções de armazenamento não suportadas**, e para poupar o custo da saída de dados durante as experiências de ML, deve [mover os seus dados](how-to-access-data.md#move) para uma solução de armazenamento Azure suportada. [Saiba mais sobre datastores.](how-to-access-data.md) 

Crie uma nova loja de dados em alguns passos com o estúdio Azure Machine Learning.

> [!IMPORTANT]
> Se a sua conta de armazenamento de dados estiver numa rede virtual, são necessários passos de configuração adicionais para garantir que o estúdio tem acesso aos seus dados. Consulte [o isolamento da rede & privacidade](how-to-enable-studio-virtual-network.md) para garantir que são aplicadas as etapas de configuração adequadas.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Datastores** no painel esquerdo em **Manage**.
1. Selecione **+ Nova loja de dados**.
1. Preencha o formulário para criar e registar uma nova datastore. O formulário atualiza-se inteligentemente com base nas suas seleções para o tipo de armazenamento Eszure e tipo de autenticação. Consulte a [secção de acesso ao armazenamento e permissões](#access-validation) para saber onde encontrar as credenciais de autenticação necessárias para preencher este formulário.

O exemplo a seguir demonstra como é a forma quando cria uma loja **de dados azure blob**:

![Formulário para uma nova datastore](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Criar conjuntos de dados

Depois de criar uma datastore, crie um conjunto de dados para interagir com os seus dados. Os conjuntos de dados embalam os seus dados num objeto consumível avaliado preguiçosamente para tarefas de aprendizagem automática, como o treino. [Saiba mais sobre os conjuntos de dados](how-to-create-register-datasets.md).

Existem dois tipos de conjuntos de dados, Dataset de Ficheiros e Dataset Tabular. 
[Os FileDatasets](how-to-create-register-datasets.md#filedataset) criam referências a ficheiros únicos ou múltiplos ou URLs públicos. Enquanto que os [Separadores Tabular](how-to-create-register-datasets.md#tabulardataset) representam os seus dados num formato tabular. 

Os seguintes passos e animação mostram como criar um conjunto de dados no [estúdio Azure Machine Learning](https://ml.azure.com).

> [!Note]
> Os conjuntos de dados criados através do estúdio Azure Machine Learning estão automaticamente registados no espaço de trabalho.

![Criar um conjunto de dados com a UI](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Para criar um conjunto de dados no estúdio:
1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Conjuntos de dados** na secção **Ativos** do painel esquerdo.
1. Selecione **Criar Conjunto de Dados** para escolher a origem do seu conjunto de dados. Esta fonte pode ser ficheiros locais, uma datastore, URLs públicos ou [conjuntos de dados Azure Open](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Selecione **Tabular** ou **Ficheiro** para o tipo de conjunto de dados.
1. Selecione **Seguinte** para abrir o **formulário de seleção de datas e ficheiros.** Neste formulário seleciona onde manter o conjunto de dados após a criação, bem como selecione quais os ficheiros de dados a utilizar para o seu conjunto de dados.
    1. Ativar a validação do skip se os seus dados estiverem numa rede virtual. Saiba mais sobre [o isolamento e privacidade da rede virtual.](how-to-enable-studio-virtual-network.md)
    1. Para conjuntos de dados tabulares, pode especificar um traço de "timeeries" para ativar operações relacionadas com o tempo no seu conjunto de dados. Saiba como [adicionar o traço de temporias ao seu conjunto de dados](how-to-monitor-datasets.md#studio-dataset).
1. Selecione **Seguinte** para preencher as **Definições e pré-visualização** e **formulários De Esquema;** são povoados inteligentemente com base no tipo de ficheiro e pode configurar ainda mais o seu conjunto de dados antes da criação destes formulários. 
1. Selecione **Seguinte** para rever o formulário **de detalhes de Confirmação.** Verifique as suas seleções e crie um perfil de dados opcional para o seu conjunto de dados. Saiba mais sobre [perfis de dados.](#profile)
1. Selecione **Criar** para completar a sua criação de conjunto de dados.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Perfil de dados e pré-visualização

Depois de criar o conjunto de dados, verifique se pode visualizar o perfil e visualizar no estúdio com os seguintes passos. 

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)
1. Selecione **Conjuntos de dados** na secção **Ativos** do painel esquerdo.
1. Selecione o nome do conjunto de dados que pretende visualizar. 
1. Selecione o separador **Explore.** 
1. Selecione o **separador Pré-visualização** ou **Perfil.** 

![Ver perfil de conjunto de dados e visualização](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Pode obter uma grande variedade de estatísticas sumárias através do seu conjunto de dados para verificar se o seu conjunto de dados está pronto para ML. Para colunas não numéricas, incluem apenas estatísticas básicas como min, max e contagem de erros. Para colunas numéricas, também pode rever os seus momentos estatísticos e quânticos estimados. 

Especificamente, o perfil de dados do conjunto de dados de aprendizagem automática Azure inclui:

>[!NOTE]
> As entradas em branco aparecem para funcionalidades com tipos irrelevantes.

|Estatística|Descrição
|------|------
|Funcionalidade| Nome da coluna que está a ser resumida.
|Perfil| Visualização em linha com base no tipo inferido. Por exemplo, cordas, booleanas e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isto permite-lhe obter uma compreensão rápida da distribuição dos dados.
|Distribuição de tipo| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são do seu próprio tipo, pelo que esta visualização é útil para detetar valores ímpares ou em falta.
|Tipo|Tipo inferido da coluna. Os valores possíveis incluem: cordas, booleans, datas e decimais.
|Mín| Valor mínimo da coluna. As entradas em branco aparecem para funcionalidades cujo tipo não tem uma encomenda inerente (como, booleans).
|Máx| Valor máximo da coluna. 
|de palavras| Número total de entradas desaparecidas e não desaparecidas na coluna.
|Contagem não faltando| Número de entradas na coluna que não faltam. Cordas e erros vazios são tratados como valores, para que não contribuam para a "contagem não em falta".
|Quantis| Valores aproximados em cada quântico para fornecer uma sensação de distribuição dos dados.
|Média| Média aritmética ou média da coluna.
|Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
|Variância| Medida da distância que os dados desta coluna estão a partir do seu valor médio. 
|Skewness| Medida de quão diferentes os dados desta coluna são de uma distribuição normal.
|Kurtose| Medida de quão fortemente seguido os dados desta coluna é comparado a uma distribuição normal.

## <a name="storage-access-and-permissions"></a>Acesso ao armazenamento e permissões

Para garantir uma ligação segura ao seu serviço de armazenamento Azure, o Azure Machine Learning requer que tenha permissão para aceder ao armazenamento de dados correspondente. Este acesso depende das credenciais de autenticação utilizadas para registar a datastore.

### <a name="virtual-network"></a>Rede virtual

Se a sua conta de armazenamento de dados estiver numa **rede virtual,** são necessários passos de configuração adicionais para garantir que o Azure Machine Learning tenha acesso aos seus dados. Consulte [o isolamento da rede & privacidade](how-to-enable-studio-virtual-network.md) para garantir que os passos de configuração apropriados são aplicados quando cria e regista a sua datastore.  

### <a name="access-validation"></a>Validação de acesso

**Como parte do processo inicial de criação e registo de datastore, o** Azure Machine Learning valida automaticamente que o serviço de armazenamento subjacente existe e o utilizador fornecido principal (nome de utilizador, principal de serviço ou token SAS) tem acesso ao armazenamento especificado.

**Após a criação da datastore,** esta validação é realizada apenas para métodos que requerem acesso ao recipiente de armazenamento subjacente, **e não** cada vez que os objetos da datas são recuperados. Por exemplo, a validação acontece se pretender descarregar ficheiros da sua datastore; mas se apenas quiser alterar a sua datastore padrão, então a validação não acontece.

Para autenticar o seu acesso ao serviço de armazenamento subjacente, pode fornecer a sua chave de conta, fichas de acesso partilhado (SAS) ou principal de serviço de acordo com o tipo de loja de dados que pretende criar. A [matriz do tipo de armazenamento](how-to-access-data.md#matrix) lista os tipos de autenticação suportados que correspondem a cada tipo de datastore.

Pode encontrar a chave de conta, o token SAS e informações principais do serviço no seu [portal Azure.](https://portal.azure.com)

* Se pretender utilizar uma chave de conta ou um sinal SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar.
  * A **página 'Vista Geral'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros.
      1. Para obter as teclas de conta, aceda às **teclas de acesso** no painel **de definições.**
      1. Para fichas SAS, aceda a **assinaturas de acesso compartilhadas** no painel **de Definições.**

* Se planeia utilizar um [principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md) para autenticação, vá às **inscrições da** sua App e selecione qual a aplicação que pretende utilizar.
    * A sua página **geral** correspondente conterá informações necessárias, como iD do inquilino e identificação do cliente.

> [!IMPORTANT]
> * Se precisar de alterar as suas teclas de acesso para uma conta de Armazenamento Azure (chave de conta ou token SAS), certifique-se de sincronizar as novas credenciais com o seu espaço de trabalho e as lojas de dados ligadas a ela. Saiba como [sincronizar as suas credenciais atualizadas.](how-to-change-storage-access-key.md) <br> <br>
> * Se não registar e re-registar uma loja de dados com o mesmo nome, e falhar, o Cofre da Chave Azure para o seu espaço de trabalho pode não ter a eliminação suave ativada. Por predefinição, a eliminação suave está ativada para a instância do cofre de chaves criada pelo seu espaço de trabalho, mas pode não ser ativada se utilizar um cofre-chave existente ou se tiver um espaço de trabalho criado antes de outubro de 2020. Para obter informações sobre como permitir a eliminação suave, consulte [Ativar o Soft Delete para obter um cofre de chaves existente]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)."

### <a name="permissions"></a>Permissões

Para o recipiente de blob Azure e o armazenamento do Azure Data Lake Gen 2, certifique-se de que as suas credenciais de autenticação têm acesso **ao Leitor de Dados Blob de Armazenamento.** Saiba mais sobre [o Storage Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Uma conta SAS não tem permissões. 
* Para **o acesso à leitura** de dados, as suas credenciais de autenticação devem ter um mínimo de lista e ler permissões para contentores e objetos. 

* Para que os dados **escrevam acesso,** escreva e adicione permissões também são necessárias.

## <a name="train-with-datasets"></a>Preparar com conjuntos de dados

Utilize os seus conjuntos de dados nas suas experiências de aprendizagem automática para treinar modelos ML. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Passos seguintes

* [Um exemplo passo-a-passo de formação com OsDatasets Tabular e a aprendizagem automática de máquinas.](tutorial-first-experiment-automated-ml.md)

* [Treine um modelo.](how-to-set-up-training-targets.md)

* Para obter mais exemplos de formação de conjuntos de dados, consulte os [cadernos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)da amostra .