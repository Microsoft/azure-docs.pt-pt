---
title: 'Tutorial: Partilhe fora do seu org - Azure Data Share'
description: Tutorial - Partilhar dados com clientes e parceiros usando a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/28/2020
ms.openlocfilehash: adc3b4542b6eba0cc0df7d93ac2cabbf7c1c144a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216594"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: Partilhar dados com o Azure Data Share  

Neste tutorial, você vai aprender a configurar uma nova Azure Data Share e começar a partilhar os seus dados com clientes e parceiros fora da sua organização Azure. 

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Criar um Data Share.
> * Adicionar conjuntos de dados ao Data Share.
> * Ativar um horário instantâneo para a sua Partilha de Dados. 
> * Adicione destinatários ao Data Share. 

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de e-mail Azure do seu destinatário (usando o seu pseudónimo de e-mail não funciona).
* Se a loja de dados Azure de origem estiver numa subscrição Azure diferente da que utilizará para criar o recurso Data Share, registe o [fornecedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição onde está localizada a loja de dados Azure. 

### <a name="share-from-a-storage-account"></a>Partilhar a partir de uma conta de armazenamento:

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](../storage/common/storage-account-create.md)
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe na função de Contribuidor.
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorização/atribuições/escrita de funções.* Esta permissão existe na função de Proprietário. 


### <a name="share-from-a-sql-based-source"></a>Partilhar a partir de uma fonte baseada em SQL:

* Uma Base de Dados Azure SQL ou Azure Synapse Analytics (anteriormente SQL Data Warehouse) com tabelas e vistas que pretende partilhar.
* Permissão para escrever nas bases de dados do servidor SQL, que está presente no *Microsoft.Sql/servers/databases/write*. Esta permissão existe na função de Contribuidor.
* Permissão para que a partilha de dados aceda ao armazém de dados. Isto pode ser feito através dos seguintes passos: 
    1. Coloque-se como o Azure Ative Directory Admin para o servidor SQL.
    1. Ligue-se à Base de Dados/Armazém de Dados Azure SQL utilizando o Azure Ative Directory.
    1. Utilize o Editor de Consulta (pré-visualização) para executar o seguinte script para adicionar o recurso Data Share Managed Identity como db_datareader. Tem de se ligar utilizando o Ative Directory e não a autenticação do SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.  

* Um Utilizador da Base de Dados Azure SQL com acesso "db_datareader" para navegar e selecione as tabelas e/ou vistas que deseja partilhar. 

* Acesso ao Servidor IP SQL do cliente. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique **no** alternador para permitir o acesso aos Serviços Azure.
    1. Clique **em +Adicionar IP ao cliente** e clique em **Guardar.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama de IP. 

### <a name="share-from-azure-data-explorer"></a>Partilhar a partir do Azure Data Explorer
* Um cluster Azure Data Explorer com bases de dados que pretende partilhar.
* Permissão para escrever ao cluster Azure Data Explorer, que está presente no *Microsoft.Kusto/clusters/write*. Esta permissão existe na função de Contribuidor.
* Permissão para adicionar atribuição de funções ao cluster Azure Data Explorer, que está presente na *Microsoft.Authorization/role assignments/write*. Esta permissão existe na função de Proprietário.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta de partilha de dados

Crie um recurso Azure Data Share num grupo de recursos Azure.

1. Selecione o botão de menu no canto superior esquerdo do portal e, em seguida, selecione **Criar um recurso** (+).

1. Procurar *por Partilha de Dados.*

1. Selecione partilha de dados e **selecione Criar**.

1. Preencha os detalhes básicos do seu recurso Azure Data Share com as seguintes informações. 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para a sua conta de partilha de dados.|
    | Grupo de recursos | *grupo de recursos de teste* | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Localização | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
    | Name | *conta de datashare* | Especifique um nome para a sua conta de partilha de dados. |
    | | |

1. Selecione **Rever + criar**e, em seguida, **criar** para obter a sua conta de partilha de dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos. 

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-share"></a>Criar uma parte

1. Navegue para a sua página de visão geral da partilha de dados.

    ![Partilhar os seus dados](./media/share-receive-data.png "Partilhar os seus dados") 

1. Selecione **Comece a partilhar os seus dados.**

1. Selecione **Criar**.   

1. Preencha os detalhes da sua parte. Especifique um nome, tipo de partilha, descrição do conteúdo da partilha e termos de utilização (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Insira detalhes de partilha") 

1. Selecione **Continuar**.

1. Para adicionar Conjuntos de Dados à sua parte, selecione **Adicionar Conjuntos de Dados**. 

    ![Adicione conjuntos de dados à sua parte](./media/datasets.png "Conjuntos de dados")

1. Selecione o tipo de conjunto de dados que gostaria de adicionar. Verá uma lista diferente de tipos de conjuntos de dados dependendo do tipo de partilha (instantâneo ou no local) que selecionou no passo anterior. Se partilhar de uma Base de Dados Azure SQL ou Azure Synapse Analytics, você será solicitado para algumas credenciais SQL. Autenticar utilizando o utilizador que criou como parte dos pré-requisitos.

    ![AdicionarDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Navegue para o objeto que pretende partilhar e selecione 'Adicionar conjuntos de dados'. 

    ![SelecioneDatasets](./media/select-datasets.png "Selecione conjuntos de dados")    

1. No separador Destinatários, insira nos endereços de e-mail do seu Consumidor de Dados selecionando '+ Adicionar Destinatário'. 

    ![AdicionarRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se tiver selecionado o tipo de partilha snapshot, pode configurar o calendário de instantâneos para fornecer atualizações dos seus dados ao seu consumidor de dados. 

    ![Ativar Fotos](./media/enable-snapshots.png "Ativar instantâneos") 

1. Selecione um intervalo de tempo de início e recorrência. 

1. Selecione **Continuar**.

1. No separador 'Rever + Criar', rever os conteúdos, definições, destinatários e definições de sincronização do pacote. Selecione **Criar**.

A sua Azure Data Share foi agora criada e o destinatário da sua Partilha de Dados está agora pronto para aceitar o seu convite. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma Partilha de Dados Azure e a convidar os destinatários. Para saber como um Consumidor de Dados pode aceitar e receber uma partilha de dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)