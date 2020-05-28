---
title: Criar uma área de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: d8e8e9ec7a0a5c502e3046b0c6ddb3a7da064739
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117281"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Criar e partilhar um estúdio de aprendizagem automática Azure (clássico)

Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho studio (clássico)

Para abrir um espaço de trabalho no Machine Learning Studio (clássico), tem de ser inscrito na Conta Microsoft que usou para criar o espaço de trabalho, ou precisa de receber um convite do proprietário para se juntar ao espaço de trabalho. A partir do portal Azure pode gerir o espaço de trabalho, que inclui a capacidade de configurar o acesso.

1. Inscreva-se no [portal Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar um espaço de trabalho studio (clássico), você precisa ser um administrador de subscrição Azure. 
    >
    > 

2. Clique **+Novo**

3. Na caixa de pesquisa, digite **machine learning studio (clássico) Workspace** e selecione o item correspondente. Em seguida, selecione clique **Criar** na parte inferior da página.

4. Insira as informações do espaço de trabalho:

   - O *nome do espaço de trabalho* pode ser de até 260 caracteres, não terminando num espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
   - O plano de *serviço web* que escolher (ou criar), juntamente com o *nível* de preços associado que selecionar, é utilizado se implementar serviços web a partir deste espaço de trabalho.

     ![Criar um novo espaço de trabalho studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

   O Machine Learning está atualmente disponível num número limitado de regiões. Se a sua subscrição não incluir uma destas regiões, poderá ver a mensagem de erro: "Não tem subscrições nas regiões permitidas"  Para solicitar que uma região seja adicionada à sua subscrição, crie um novo pedido de suporte da Microsoft a partir do portal Azure, escolha **a Faturação** como o tipo de problema e siga as instruções para submeter o seu pedido.


> [!NOTE]
> O Machine Learning Studio (clássico) baseia-se numa conta de armazenamento Azure que fornece para guardar dados intermediários quando executa o fluxo de trabalho. Após a criação do espaço de trabalho, se a conta de armazenamento for eliminada, ou se as teclas de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todas as experiências nesse espaço de trabalho falharão.
Se apagar acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento eliminada e resintifique a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Uma vez que o espaço de trabalho é implantado, você pode abri-lo no Machine Learning Studio (clássico).

1. Navegue no Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Selecione a sua área de trabalho no canto superior direito.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique nas **minhas experiências.**

    ![Experiências abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre a gestão do seu espaço de trabalho studio (clássico), consulte [Manage a Azure Machine Learning Studio (clássico) espaço](manage-workspace.md)de trabalho.
Se encontrar algum problema ao criar o seu espaço de trabalho, consulte o [guia de resolução de problemas: Crie e ligue-se a um espaço de trabalho do Machine Learning Studio (clássico).](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Partilhe um Estúdio de Aprendizagem automática Azure (clássico)
Uma vez criado um espaço de trabalho do Machine Learning Studio (clássico), pode convidar os utilizadores para o seu espaço de trabalho para partilharem acesso ao seu espaço de trabalho e a todas as suas experiências, conjuntos de dados, cadernos, etc. Pode adicionar utilizadores numa de duas funções:

* **Utilizador** - Um utilizador do espaço de trabalho pode criar, abrir, modificar e eliminar experiências, conjuntos de dados, etc. no espaço de trabalho.
* **Proprietário** - Um proprietário pode convidar e remover os utilizadores no espaço de trabalho, além do que um utilizador pode fazer.

> [!NOTE]
> A conta de administrador que cria o espaço de trabalho é automaticamente adicionada ao espaço de trabalho como Proprietário do espaço de trabalho. No entanto, outros administradores ou utilizadores dessa subscrição não têm acesso automaticamente ao espaço de trabalho - é necessário convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para partilhar um espaço de trabalho studio (clássico)

1. Inscreva-se no Machine Learning Studio (clássico) em[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **DEFINIÇÕES**

3. Clique no separador **UTILIZADORES**

4. Clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da página

    ![Configurações do estúdio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de e-mail. Os utilizadores precisam de uma conta Microsoft válida ou de uma conta organizacional (do Azure Ative Directory).

6. Selecione se pretende adicionar os utilizadores como Proprietário ou Utilizador.

7. Clique no botão **OK** checkmark.

Cada utilizador que adicionar receberá um e-mail com instruções sobre como iniciar sessão no espaço de trabalho partilhado.

> [!NOTE]
> Para que os utilizadores possam implementar ou gerir serviços web neste espaço de trabalho, devem ser colaboradores ou administradores na subscrição do Azure. 

## <a name="troubleshoot-storage-accounts"></a>Contas de armazenamento de resolução de problemas


O serviço de Aprendizagem automática precisa de uma conta de armazenamento para armazenar dados. Pode utilizar uma conta de armazenamento existente, ou pode criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do Machine Learning Studio (clássico) (se tiver quota para criar uma nova conta de armazenamento).

Depois de criado o novo espaço de trabalho do Machine Learning Studio (clássico), pode iniciar sessão no Machine Learning Studio (clássico) utilizando a conta microsoft que usou para criar o espaço de trabalho. Se encontrar a mensagem de erro, "Workspace Not Found" (semelhante à seguinte imagem), utilize os seguintes passos para eliminar os cookies do seu navegador.

![Espaço de trabalho não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para eliminar os cookies do navegador**

1. Se utilizar o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **opções**de Internet .  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Sob o separador **Geral,** clique em **Apagar...**

   ![Separador Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo 'Excluir Histórico de **Navegação',** certifique-se de que os **Cookies e os dados do site** são selecionados e clique em **Eliminar**.

   ![Eliminar cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois de eliminados os cookies, reinicie o navegador e depois vá para a página [do Microsoft Azure Machine Learning Studio (clássico).](https://studio.azureml.net) Quando for solicitado um nome de utilizador e uma palavra-passe, introduza a mesma conta Microsoft que usou para criar o espaço de trabalho.


## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a gestão de um espaço de trabalho, consulte [Manage a Azure Machine Learning Studio (clássico) workspace](manage-workspace.md).
