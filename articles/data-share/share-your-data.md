---
title: 'Tutorial: Partilhe fora do seu org - Azure Data Share'
description: Tutorial - Partilhar dados com clientes e parceiros usando a Partilha de Dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083060"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: Partilhar dados usando a Partilha de Dados do Azure  

Neste tutorial, você vai aprender a configurar uma nova Partilha de Dados Azure e começar a partilhar os seus dados com clientes e parceiros fora da sua organização Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um Data Share.
> * Adicionar conjuntos de dados ao Data Share.
> * Ative um calendário instantâneo para a sua Partilha de Dados. 
> * Adicione destinatários ao Data Share. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de correio eletrónico do seu destinatário Azure (usando o seu pseudónimo de e-mail não funciona).
* Se a loja de dados Azure de origem estiver numa subscrição Azure diferente daquela que utilizará para criar recurso Data Share, registe o fornecedor de [recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição onde está localizada a loja de dados Azure. 

### <a name="share-from-a-storage-account"></a>Partilhar de uma conta de armazenamento:

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe no papel de Contribuinte.
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorizações/atribuições/escritas/ escrita*. Esta permissão existe no papel de Proprietário. 


### <a name="share-from-a-sql-based-source"></a>Partilhar de uma fonte baseada em SQL:

* Uma Base de Dados Azure SQL ou Azure Synapse Analytics (antiga Azure SQL Data Warehouse) com tabelas e vistas que pretende partilhar.
* Permissão para escrever para as bases de dados no servidor SQL, que está presente em *Microsoft.Sql/servers/bases de dados/write*. Esta permissão existe no papel de Contribuinte.
* Permissão para a partilha de dados para aceder ao armazém de dados. Isto pode ser feito através dos seguintes passos: 
    1. Desemprete-se como o Administrador de Diretório Ativo Azure para o servidor SQL.
    1. Ligue-se à Base de Dados/Armazém de Dados Azure SQL utilizando o Diretório Ativo Azure.
    1. Utilize o Editor de Consulta (pré-visualização) para executar o seguinte script para adicionar o recurso Data Share Managed Identity como um db_datareader. Deve ligar-se utilizando o Diretório Ativo e não a autenticação do Servidor SQL. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso data Share, pode voltar a este pré-requisito mais tarde.  

* Um Utilizador de Base de Dados Azure SQL com acesso 'db_datareader' para navegar e selecionar as tabelas e/ou vistas que deseja partilhar. 

* Acesso ao servidor IP SQL do cliente. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique no alternância **para** permitir o acesso aos Serviços Azure.
    1. Clique em **+Adicionar IP do cliente** e clique em **Guardar**. O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama IP. 

### <a name="share-from-azure-data-explorer"></a>Partilhar do Azure Data Explorer
* Um cluster Azure Data Explorer com bases de dados que pretende partilhar.
* Permissão para escrever para o cluster Azure Data Explorer, que está presente na *Microsoft.Kusto/clusters/write*. Esta permissão existe no papel de Contribuinte.
* Permissão para adicionar atribuição de funções ao cluster Azure Data Explorer, que está presente na *Microsoft.Autorizações/atribuições/escrita.* Esta permissão existe no papel de Proprietário.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma Conta de Partilha de Dados

Crie um recurso Azure Data Share num grupo de recursos Azure.

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal.

1. Pesquisar por *Data Share*.

1. Selecione Data Share e selecione **Criar**.

1. Preencha os detalhes básicos do seu recurso Azure Data Share com as seguintes informações. 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *datashareacount* | Especifique um nome para a sua conta de partilha de dados. |
    | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para a sua conta de partilha de dados.|
    | Grupo de recursos | *grupo de recursos de teste* | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Localização | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
    | | |

1. Selecione **Criar** para fornecer a sua conta de partilha de dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos. 

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-data-share"></a>Criar uma Partilha de Dados

1. Navegue para a sua página de visão geral da partilha de dados.

    ![Partilhe os seus dados](./media/share-receive-data.png "Partilhe os seus dados") 

1. Selecione **Começar a partilhar os seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes para a sua Partilha de Dados. Especifique um nome, tipo de partilha, descrição do conteúdo das ações e termos de utilização (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Insira detalhes da Partilha") 

1. Selecione **Continuar**

1. Para adicionar Conjuntos de Dados à sua Partilha de Dados, **selecione Adicionar Conjuntos**de Dados . 

    ![Conjuntos de dados](./media/datasets.png "Conjuntos de dados")

1. Selecione o tipo de conjunto de dados que gostaria de adicionar. Verá uma lista diferente de tipos de conjuntos de dados dependendo do tipo de partilha (instantâneo ou no lugar) selecionado no passo anterior. Se partilhar a partir de uma Base de Dados Azure SQL ou azure SQL Data Warehouse, você será solicitado para algumas credenciais SQL. Autenticar utilizando o utilizador que criou como parte dos pré-requisitos.

    ![AddDatasets](./media/add-datasets.png "Adicionar Conjuntos de Dados")    

1. Navegue para o objeto que pretende partilhar e selecione 'Adicionar Conjuntos de Dados'. 

    ![SelectDatasets](./media/select-datasets.png "Selecione Conjuntos de Dados")    

1. No separador Destinatário, introduza nos endereços de e-mail do seu Consumidor de Dados selecionando '+ Add Recipient'. 

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**

1. Se tiver selecionado o tipo de partilha de instantâneos, pode configurar o calendário de instantâneos para fornecer atualizações dos seus dados ao seu consumidor de dados. 

    ![EnableSnapshots](./media/enable-snapshots.png "Ativar instantâneos") 

1. Selecione um intervalo de início e recorrência. 

1. Selecione **Continuar**

1. No separador Review + Criar, reveja os conteúdos do pacote, definições, destinatários e definições de sincronização. Selecione **Criar**

A sua Partilha de Dados Azure já foi criada e o destinatário da sua Data Share está agora pronto para aceitar o seu convite. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma Partilha de Dados Azure e a convidar destinatários. Para saber como um Consumidor de Dados pode aceitar e receber uma parte de dados, continue a aceitar e receber o tutorial de [dados.](subscribe-to-data-share.md) 
