---
title: 'Estúdio ML (clássico): Criar um espaço de trabalho - Azure'
description: Para utilizar o Azure Machine Learning Studio (clássico), precisa de ter um espaço de trabalho do Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 3fc7fd1f3f7615c0622b684e3da4020c7da601f5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322865"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Criar e partilhar um espaço de trabalho do Machine Learning Studio (clássico)

**APLICA-SE A:** ![ sim ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) no ![ ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Para utilizar o Azure Machine Learning Studio (clássico), precisa de ter um espaço de trabalho do Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho estúdio (clássico)

Para abrir um espaço de trabalho no Machine Learning Studio (clássico), tem de ser inscrito na Conta Microsoft que utilizou para criar o espaço de trabalho, ou precisa de receber um convite do proprietário para se juntar ao espaço de trabalho. A partir do portal Azure pode gerir o espaço de trabalho, que inclui a capacidade de configurar o acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar um espaço de trabalho studio (clássico), tem de ser um administrador de subscrição da Azure. 
    >
    > 

2. Clique **em +Novo**

3. Na caixa de pesquisa, **escreva Machine Learning Studio (clássico) Workspace** e selecione o item correspondente. Em seguida, selecione clique **Criar** na parte inferior da página.

4. Insira as informações do seu espaço de trabalho:

   - O *nome do espaço de trabalho* pode ter até 260 caracteres, não terminando num espaço. O nome não pode incluir estes caracteres: `< > * % & : \ ? + /`
   - O *plano de serviço web* que você escolhe (ou cria), juntamente com o *nível de preços* associado que seleciona, é usado se implementar serviços web a partir deste espaço de trabalho.

     ![Criar um novo espaço de trabalho studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

   O Machine Learning está atualmente disponível num número limitado de regiões. Se a sua subscrição não incluir uma destas regiões, poderá ver a mensagem de erro: "Não tem subscrições nas regiões permitidas."  Para solicitar que uma região seja adicionada à sua subscrição, crie um novo pedido de suporte da Microsoft a partir do portal Azure, escolha **a Faturação** como o tipo de problema e siga as instruções para submeter o seu pedido.


> [!NOTE]
> O Machine Learning Studio (clássico) baseia-se numa conta de armazenamento Azure que fornece para guardar dados intermediários quando executa o fluxo de trabalho. Após a criação do espaço de trabalho, se a conta de armazenamento for eliminada, ou se as teclas de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todas as experiências nesse espaço de trabalho falharão.
Se eliminar acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento eliminada e ressinc a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Uma vez implantado o espaço de trabalho, pode abri-lo no Machine Learning Studio (clássico).

1. Navegue pelo Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Selecione a sua área de trabalho no canto superior direito.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique **nas minhas experiências.**

    ![Experiências abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre a gestão do seu espaço de trabalho Studio (clássico), consulte Gerir um espaço de [trabalho (clássico) do Azure Machine Learning Studio](manage-workspace.md)( clássico).
Se encontrar um problema na criação do seu espaço de trabalho, consulte [o guia de resolução de problemas: Criar e ligar-se a um espaço de trabalho do Machine Learning Studio (clássico).](index.yml)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Partilhe um espaço de trabalho Azure Machine Learning Studio (clássico)
Uma vez criado um espaço de trabalho do Machine Learning Studio (clássico), pode convidar os utilizadores para o seu espaço de trabalho para partilharem o acesso ao seu espaço de trabalho e a todas as suas experiências, conjuntos de dados, etc. Pode adicionar utilizadores numa de duas funções:

* **Utilizador** - Um utilizador do espaço de trabalho pode criar, abrir, modificar e eliminar experiências, conjuntos de dados, etc. no espaço de trabalho.
* **Proprietário** - Um proprietário pode convidar e remover utilizadores no espaço de trabalho, além do que um utilizador pode fazer.

> [!NOTE]
> A conta de administrador que cria o espaço de trabalho é automaticamente adicionada ao espaço de trabalho como espaço de trabalho Proprietário. No entanto, outros administradores ou utilizadores nessa subscrição não têm acesso automaticamente ao espaço de trabalho - é necessário convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para partilhar um espaço de trabalho (clássico) do Studio

1. Inscreva-se no Machine Learning Studio (clássico) em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **DEFINIÇÕES**

3. Clique no separador **UTILIZADORES**

4. Clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da página

    ![Configurações de estúdio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de e-mail. Os utilizadores precisam de uma conta Microsoft válida ou de uma conta organizacional (a partir do Azure Ative Directory).

6. Selecione se pretende adicionar os utilizadores como Proprietário ou Utilizador.

7. Clique no botão **ok** de marca de verificação.

Cada utilizador que adicionar receberá um e-mail com instruções sobre como iniciar sôm no espaço de trabalho partilhado.

> [!NOTE]
> Para que os utilizadores possam implementar ou gerir serviços web neste espaço de trabalho, devem ser contribuintes ou administradores na subscrição do Azure. 

## <a name="troubleshoot-storage-accounts"></a>Contas de armazenamento de resolução de problemas


O serviço Machine Learning necessita de uma conta de armazenamento para armazenar dados. Pode utilizar uma conta de armazenamento existente, ou pode criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do Machine Learning Studio (clássico) (se tiver quota para criar uma nova conta de armazenamento).

Após a criação do novo espaço de trabalho do Machine Learning Studio (clássico), pode iniciar sessão no Machine Learning Studio (clássico) utilizando a conta microsoft que usou para criar o espaço de trabalho. Se encontrar a mensagem de erro, "Workspace Not Found" (semelhante à seguinte imagem), utilize os seguintes passos para eliminar os cookies do seu navegador.

![Espaço de trabalho não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para eliminar cookies de navegador**

1. Se utilizar o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **as opções de Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. No separador **Geral,** clique em **Eliminar...**

   ![Separador Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo de **Histórico de Navegação delete,** certifique-se de que **os Cookies e os dados do site estão selecionados** e clique em **Eliminar**.

   ![Eliminar cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois de os cookies serem eliminados, reinicie o navegador e, em seguida, vá para a página [do Microsoft Azure Machine Learning Studio (clássico).](https://studio.azureml.net) Quando lhe for solicitado um nome de utilizador e uma palavra-passe, insira a mesma conta Microsoft que usou para criar o espaço de trabalho.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão de um espaço de trabalho, consulte [Gerir um espaço de trabalho (clássico) do Azure Machine Learning Studio (clássico).](manage-workspace.md)