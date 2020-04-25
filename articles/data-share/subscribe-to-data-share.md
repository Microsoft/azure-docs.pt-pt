---
title: 'Tutorial: Aceitar & receber dados - Partilha de Dados do Azure'
description: Tutorial - Aceitar e receber dados usando a Partilha de Dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4dff48f909cd3febbbb7e92dcf96070020b8f57c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145140"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados usando a Partilha de Dados do Azure  

Neste tutorial, você aprenderá a aceitar um convite de partilha de dados usando a Partilha de Dados Azure. Você aprenderá a receber dados que estão sendo partilhados consigo, bem como como permitir um intervalo de atualização regular para garantir que você sempre tem a imagem mais recente dos dados que estão sendo partilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite azure Data Share
> * Criar uma conta Azure Data Share
> * Especifique um destino para os seus dados
> * Crie uma subscrição da sua quota de dados para aatualização programada

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder aceitar um convite para partilha de dados, deve fornecer uma série de recursos Azure, que estão listados abaixo. 

Certifique-se de que todos os pré-requisitos estão completos antes de aceitar um convite para partilha de dados. 

* Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite da Data Share: Um convite do Microsoft Azure **<yourdataprovider@domain.com>** com um tema intitulado "Convite Azure Data Share de ".
* Registe o fornecedor de [recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição do Azure, onde irá criar um recurso Data Share e a subscrição Azure onde estão localizadas as lojas de dados Azure alvo.

### <a name="receive-data-into-a-storage-account"></a>Receba os dados numa conta de armazenamento: 

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe no papel de Contribuinte. 
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorizações/atribuições/escritas/ escrita*. Esta permissão existe no papel de Proprietário.  

### <a name="receive-data-into-a-sql-based-source"></a>Receba dados numa fonte baseada em SQL:

* Permissão para escrever para bases de dados no servidor SQL, que está presente em *Microsoft.Sql/servers/bases de dados/write*. Esta permissão existe no papel de Contribuinte. 
* Autorização para que a identidade gerida do recurso de partilha de dados aceda à Base de Dados Azure SQL ou ao Armazém de Dados Azure SQL. Isto pode ser feito através dos seguintes passos: 
    1. Desemprete-se como o Administrador de Diretório Ativo Azure para o servidor SQL.
    1. Ligue-se à Base de Dados/Armazém de Dados Azure SQL utilizando o Diretório Ativo Azure.
    1. Utilize o Editor de Consulta (pré-visualização) para executar o seguinte script para adicionar a Identidade Gerida de Partilha de Dados como um 'db_datareader, db_datawriter, db_ddladmin'. Deve ligar-se utilizando o Diretório Ativo e não a autenticação do Servidor SQL. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso data Share, pode voltar a este pré-requisito mais tarde.         

* Acesso ao servidor IP SQL do cliente. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique no alternância **para** permitir o acesso aos Serviços Azure.
    1. Clique em **+Adicionar IP do cliente** e clique em **Guardar**. O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a receber dados num alvo SQL do portal Azure. Também pode adicionar uma gama IP. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Receba dados num cluster do Azure Data Explorer: 

* Um cluster Azure Data Explorer no mesmo centro de dados Azure que o cluster data explorer do fornecedor de dados: Se ainda não tiver um, pode criar um [cluster azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Se não conhece o centro de dados Azure do cluster do fornecedor de dados, pode criar o cluster mais tarde no processo.
* Permissão para escrever para o cluster Azure Data Explorer, que está presente na *Microsoft.Kusto/clusters/write*. Esta permissão existe no papel de Contribuinte. 
* Permissão para adicionar atribuição de funções ao cluster Azure Data Explorer, que está presente na *Microsoft.Autorizações/atribuições/escrita.* Esta permissão existe no papel de Proprietário. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

1. Pode abrir convite sacar-se por e-mail ou diretamente do portal Azure. 

   Para abrir convite a partir de e-mail, consulte a sua caixa de entrada para obter um convite do seu fornecedor de dados. O convite é do Microsoft Azure, intitulado Convite de **Partilha de Dados Do Azure de <yourdataprovider@domain.com> **. Clique no **convite para** ver o seu convite em Azure. 

   Para abrir convite diretamente do portal Azure, procure **convites** de partilha de dados no portal Azure. Isto leva-o à lista de convites da Data Share.

   ![Convites](./media/invitations.png "Lista de convites") 

1. Selecione a parte que gostaria de ver. 

## <a name="accept-invitation"></a>Aceitar convite
1. Certifique-se de que todos os campos são revistos, incluindo os **Termos de Utilização**. Se concordar com os termos de utilização, será obrigado a verificar a caixa para indicar que concorda. 

   ![Termos de Utilização](./media/terms-of-use.png "Termos de utilização") 

1. No âmbito da Conta de Partilha de *Dados Alvo,* selecione o Grupo de Subscrição e Recursos em que irá implementar a sua Partilha de Dados. 

   Para o campo **Data Share Account,** selecione **Criar novo** se não tiver uma conta de Partilha de Dados existente. Caso contrário, selecione uma conta de Partilha de Dados existente em que gostaria de aceitar a sua quota de dados. 

   Para o campo **De nome de partilha recebida,** pode deixar o predefinido especificado pelos dados fornecer, ou especificar um novo nome para a parte recebida. 

   ![Conta de partilha de dados-alvo](./media/target-data-share.png "Conta de partilha de dados-alvo") 

1. Uma vez acordado os termos de utilização e especificado um local para a sua parte, Selecione em *Aceitar e configurar*. Será criada uma subscrição de ações.

   Para partilha baseada em instantâneos, o próximo ecrã irá pedir-lhe para selecionar uma conta de armazenamento de destino para os seus dados serem copiados. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Se preferir aceitar o convite agora, mas configurar a sua loja de dados alvo mais tarde, selecione *Aceitar e configurar mais tarde*. Para continuar a configurar o seu armazenamento mais tarde, consulte a página de mapeamento de conjuntos de dados para obter [passos](how-to-configure-mapping.md) detalhados sobre como retomar a configuração da partilha de dados. 

   Para a partilha no local, consulte a página de mapeamento de conjuntos de [dados configurar](how-to-configure-mapping.md) para ver se há passos detalhados sobre como retomar a configuração da partilha de dados. 

   Se não quiser aceitar o convite, selecione *Rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
1. Em definições de *armazenamento de destino,* selecione a subscrição, grupo de recursos e conta de armazenamento em que gostaria de receber os seus dados. 

   ![Definições de armazenamento de destino](./media/target-storage-settings.png "Armazenamento de destino") 

1. Para receber uma atualização regular dos seus dados, certifique-se de que ativa as definições de instantâneo. Note que só verá um calendário de definição de instantâneo se o seu fornecedor de dados o tiver incluído na partilha de dados. 

   ![Configurações instantâneas](./media/snapshot-settings.png "Configurações instantâneas") 

1. Selecione *Guardar*. 

> [!IMPORTANT]
> Se estiver a receber dados baseados em SQL e quiser receber esses dados numa fonte baseada em SQL, visite configurar um conjunto de [dados mapeando](how-to-configure-mapping.md) como orientar para aprender a configurar um Servidor SQL como destino para o seu conjunto de dados. 

## <a name="trigger-a-snapshot"></a>Desencadear um instantâneo
Estes passos aplicam-se apenas à partilha baseada em instantâneos.

1. Pode ativar uma imagem no separador "Detalhes > > Ações recebidas selecionando o **instantâneo do Gatilho**. Aqui, pode desencadear uma imagem completa ou incremental dos seus dados. Se for a primeira vez que recebe dados do seu fornecedor de dados, selecione cópia completa. 

   ![Instantâneo do gatilho](./media/trigger-snapshot.png "Instantâneo do gatilho") 

1. Quando o último estado de execução for *bem sucedido,* dirija-se ao target data store para ver os dados recebidos. Selecione **Conjuntos**de Dados e clique no link no Caminho-alvo. 

   ![Conjuntos de dados dos consumidores](./media/consumer-datasets.png "Mapeamento de conjunto de dados de consumo") 

## <a name="view-history"></a>Ver histórico
Para ver um histórico das suas fotos, navegue para As Ações Recebidas - história >. Aqui encontrará um histórico de todos os instantâneos que foram gerados nos últimos 60 dias. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a aceitar e a receber uma Partilha de Dados Azure. Para saber mais sobre os conceitos de Partilha de Dados do Azure, continue a [conceitos: Azure Data Share Terminology](terminology.md).