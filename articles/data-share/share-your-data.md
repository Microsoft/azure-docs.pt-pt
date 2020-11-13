---
title: 'Tutorial: Partilhe fora do seu org - Azure Data Share'
description: Tutorial - Partilhar dados com clientes e parceiros usando a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 27d48ef8961aa0b7fde4a92195ea92a1ec20c3f0
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594203"
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

### <a name="share-from-a-storage-account"></a>Partilhar a partir de uma conta de armazenamento

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](../storage/common/storage-account-create.md)
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe na função de **Contribuidor**.
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorização/atribuições/escrita de funções.* Esta permissão existe na função de **Proprietário**. 


### <a name="share-from-a-sql-based-source"></a>Partilhar a partir de uma fonte baseada em SQL
Abaixo está a lista de pré-requisitos para a partilha de dados de fonte SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Pré-requisitos para a partilha da Azure SQL Database ou da Azure Synapse Analytics (anteriormente Azure SQL DW)
Pode seguir a [demonstração passo](https://youtu.be/hIE-TjJD8Dc) a passo para configurar os pré-requisitos.

* Uma Base de Dados Azure SQL ou Azure Synapse Analytics (anteriormente Azure SQL DW) com tabelas e vistas que pretende partilhar.
* Permissão para escrever nas bases de dados do servidor SQL, que está presente no *Microsoft.Sql/servers/databases/write*. Esta permissão existe na função de **Contribuidor**.
* Permissão para a identidade gerida do recurso Data Share para aceder à base de dados. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o servidor SQL e coloque-se como O **Administrador Ativo Azure.**
    1. Conecte-se à Base de Dados/Armazém de Dados Azure SQL utilizando [o Editor de Consulta](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) ou o SQL Server Management Studio com a autenticação do Azure Ative Directory. 
    1. Execute o seguinte script para adicionar o recurso Data Share Identidade Gerida como um db_datareader. Tem de se ligar utilizando o Ative Directory e não a autenticação do SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.  

* Um Utilizador da Base de Dados Azure SQL com acesso **"db_datareader"** para navegar e selecione as tabelas e/ou vistas que deseja partilhar. 

* Acesso sql Server Firewall. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o servidor SQL. Selecione *Firewalls e redes virtuais* a partir da navegação à esquerda.
    1. Clique **em Sim** para permitir que os *serviços e recursos do Azure acedam a este servidor.*
    1. Clique **em +Adicionar IP ao cliente.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama de IP.
    1. Clique em **Guardar**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Pré-requisitos para a partilha da piscina SQL da Azure Synapse Analytics (espaço de trabalho)

* * Uma piscina DE SQL dedicada Azure Synapse (espaço de trabalho) com mesas que pretende partilhar. A partilha de pontos de vista não é atualmente suportada. A partilha da piscina SQL sem servidor não é suportada atualmente.
* Permissão para escrever para a piscina SQL no espaço de trabalho synapse, que está presente no *Microsoft.Synapse/workspaces/sqlPools/write*. Esta permissão existe na função de **Contribuidor**.
* Permissão para a identidade gerida do recurso Data Share para aceder à piscina SQL do espaço de trabalho Synapse. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o espaço de trabalho da Sinapse. Selecione o administrador do SqL Ative Directory da navegação esquerda e coloque-se como administrador do **Azure Ative Directory**.
    1. Abra o Estúdio Synapse, *selecione Gerir* a partir da navegação à esquerda. Selecione *o controlo de acesso* sob Segurança. Atribua-se o papel **de administrador SQL** ou **workspace.**
    1. No Synapse Studio, *selecione Desenvolver* a partir da navegação à esquerda. Execute o seguinte script no pool SQL para adicionar o recurso Data Share Identidade Gerida como db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.  

* Acesso ao espaço de trabalho Synapse Firewall. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o espaço de trabalho da Sinapse. Selecione *Firewalls* da navegação à esquerda.
    1. Clique **em ON** para permitir que os *serviços e recursos da Azure acedam a este espaço de trabalho.*
    1. Clique **em +Adicionar IP ao cliente.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama de IP.
    1. Clique em **Guardar**. 


### <a name="share-from-azure-data-explorer"></a>Partilhar a partir do Azure Data Explorer
* Um cluster Azure Data Explorer com bases de dados que pretende partilhar.
* Permissão para escrever ao cluster Azure Data Explorer, que está presente no *Microsoft.Kusto/clusters/write*. Esta permissão existe na função de **Contribuidor**.
* Permissão para adicionar atribuição de funções ao cluster Azure Data Explorer, que está presente na *Microsoft.Authorization/role assignments/write*. Esta permissão existe na função de **Proprietário**.

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

1. Selecione **Rever + criar** e, em seguida, **criar** para obter a sua conta de partilha de dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos. 

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

1. Selecione o tipo de conjunto de dados que gostaria de adicionar. Verá uma lista diferente de tipos de conjuntos de dados dependendo do tipo de partilha (instantâneo ou no local) que selecionou no passo anterior. Se partilhar de uma Base de Dados Azure SQL ou Azure Synapse Analytics (anteriormente Azure SQL DW), você será solicitado para credenciais SQL para listar tabelas.

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando o recurso já não for necessário, vá à página de Visão Geral do **Conjunto de Partilha de Dados** e selecione **Eliminar** para o remover.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma Partilha de Dados Azure e a convidar os destinatários. Para saber como um Consumidor de Dados pode aceitar e receber uma partilha de dados, continue a aceitar e receber o tutorial de dados.

> [!div class="nextstepaction"]
> [Tutorial: Aceitar e receber dados com o Azure Data Share](subscribe-to-data-share.md)
