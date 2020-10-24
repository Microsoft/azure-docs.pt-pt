---
title: Utilize identidades geridas para aceder à Base de Dados Azure SQL - Azure Stream Analytics
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para a saída DB Azure SQL.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 8b5c106c1464ec6d77305b1985cc8dbd51e2b4db
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519482"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Utilize identidades geridas para aceder à Base de Dados Azure SQL a partir de um trabalho de Azure Stream Analytics (Preview)

O Azure Stream Analytics suporta [a autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para lavatórios de saída da Base de Dados Azure SQL. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenenciar devido a alterações de senha ou expirações simbólicas do utilizador que ocorrem a cada 90 dias. Quando remove a necessidade de autenticação manual, as suas implementações stream Analytics podem ser totalmente automatizadas.

Uma identidade gerida é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics. A aplicação gerida é utilizada para autenticar um recurso direcionado. Este artigo mostra-lhe como ativar a Identidade Gerida para uma saída(s) de Base de Dados SQL Azure de um trabalho stream Analytics através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes recursos para esta função:

- Um trabalho da Azure Stream Analytics.

- Um recurso de base de dados Azure SQL.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Primeiro, cria-se uma identidade gerida para o seu trabalho Azure Stream Analytics.

1. No [portal Azure,](https://portal.azure.com)abra o seu trabalho Azure Stream Analytics.

1. A partir do menu de navegação à esquerda, **selecione Identidade Gerida** localizada em **Configuração.** Em seguida, verifique a caixa ao lado **da Identidade Gerida atribuída ao Sistema** e selecione **Save**.

   ![Selecione identidade gerida atribuída pelo sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Um diretor de serviço para a identidade do trabalho stream Analytics é criado no Azure Ative Directory. O ciclo de vida da identidade recém-criada é gerido por Azure. Quando o trabalho stream Analytics é eliminado, a identidade associada (isto é, o principal de serviço) é automaticamente eliminada pela Azure. 

1. Quando guarda a configuração, o ID do Objeto (OID) do principal de serviço está listado como iD principal como mostrado abaixo: 

   ![ID do objeto mostrado como ID principal](./media/sql-db-output-managed-identity/principal-id.png)

   O diretor de serviço tem o mesmo nome que o trabalho da Stream Analytics. Por exemplo, se o nome do seu trabalho for *MyASAJob,* o nome do diretor de serviço também é *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selecione um administrador de diretório ativo

Depois de criar uma identidade gerida, selecione um administrador do Ative Directory.

1. Navegue para o seu recurso Azure SQL Database e selecione o SQL Server em que a base de dados está. Pode encontrar o nome do Servidor SQL ao lado *do nome do Servidor* na página de visão geral do recurso. 

1. Selecione **Ative Directory Admin** em **Definições**. Em seguida, selecione **Definir administração**. 

   ![Página de administração de diretório ativo](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Na página de administração do Ative Directory, procure um utilizador ou grupo para ser administrador do SqL Server e clique em **Select**.

   ![Adicionar administrador de diretório ativo](./media/sql-db-output-managed-identity/add-admin.png)

   A página de administração do Ative Directory mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores do Azure Ative Directory. Consulte a lista de administradores suportados na secção **Azure Ative Directory Features and Limitations**   of Use [Azure Ative Directory Authentication for authentication with SQL Database ou Azure Synapse](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). O controlo de acesso baseado em funções (Azure RBAC) aplica-se apenas ao portal e não é propagado ao SQL Server. Além disso, o utilizador ou grupo selecionado é o utilizador que poderá criar o **Utilizador de Base de Dados Contido** na secção seguinte.

1. **Selecione Guardar** na página de administração do **Diretório Ativo.** O processo de mudança de administração demora alguns minutos.

   Quando configurar o administrador do Azure Ative Directory, o novo nome de administração (utilizador ou grupo) não pode estar presente na base de dados primária virtual como utilizador de autenticação do SQL Server. Se estiver presente, a configuração de administração do Azure Ative Directory falhará e reverterá a sua criação, indicando que já existe um administrador (nome). Uma vez que o utilizador de autenticação do SQL Server não faz parte do Azure Ative Directory, qualquer esforço para se ligar ao servidor utilizando a autenticação do Azure Ative Directory uma vez que o utilizador falha. 

## <a name="create-a-contained-database-user"></a>Criar um utilizador de base de dados contido

Em seguida, cria um utilizador de base de dados contido na sua Base de Dados SQL que está mapeada para a identidade do Diretório Ativo Azure. O utilizador da base de dados contido não tem um login para a base de dados primária, mas mapeia para uma identidade no diretório que está associado à base de dados. A identidade do Diretório Ativo Azure pode ser uma conta de utilizador individual ou um grupo. Neste caso, pretende criar um utilizador de base de dados contido para o seu trabalho stream Analytics. 

1. Ligue-se à Base de Dados SQL utilizando o SQL Server Management Studio. **O nome de Utilizador** é um utilizador do Azure Ative Directory com a permissão ALTER ANY **USER.** O administrador que definiu no SQL Server é um exemplo. Utilize **o Diretório Ativo Azure – Universal com** autenticação MFA. 

   ![Ligar ao SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   O nome do servidor `<SQL Server name>.database.windows.net` pode ser diferente em diferentes regiões. Por exemplo, a região chinesa deve `<SQL Server name>.database.chinacloudapi.cn` utilizar.
 
   Pode especificar uma base de dados SQL específica indo para **opções > propriedades de conexão > ligar à base de dados.**  

   ![Propriedades de conexão do Servidor SQL](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Quando se ligar pela primeira vez, poderá encontrar a seguinte janela:

   ![Nova janela de regra de firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Em caso afirmativo, aceda ao seu recurso SQL Server no portal Azure. Sob a secção **de Segurança,** abra as Firewalls e a página **de rede virtual.** 
   1. Adicione uma nova regra com qualquer nome de regra.
   1. Utilize o endereço *IP a* partir da janela New **Firewall Rule** para o IP *inicial*.
   1. Utilize o endereço *Para* IP a partir da janela **New Firewall Rule** para O IP *final*. 
   1. **Selecione Save** e tente ligar-se do SQL Server Management Studio novamente. 

1. Uma vez ligado, crie o utilizador da base de dados contido. O seguinte comando SQL cria um utilizador de base de dados contido que tem o mesmo nome que o seu trabalho stream Analytics. Certifique-se de que inclui os suportes à volta do *ASA_JOB_NAME*. Utilize a seguinte sintaxe T-SQL e faça a consulta. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Para que o Azure Ative Directory da Microsoft verifique se o trabalho do Stream Analytics tem acesso à Base de Dados SQL, precisamos de dar permissão ao Azure Ative Directory para comunicar com a base de dados. Para isso, vá novamente à página "Firewalls e rede virtual" no portal Azure e permita "Permitir que os serviços e recursos do Azure acedam a este servidor". 

   ![Firewall e rede virtual](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Permissões de emprego grant Stream Analytics

Depois de ter criado um utilizador de base de dados contido e de ter acesso aos serviços Azure no portal, conforme descrito na secção anterior, o seu trabalho stream Analytics tem permissão da Managed Identity para **ligar** ao seu recurso SQL Database através de identidade gerida. Recomendamos que conceda as permissões SELECT e INSERT para o trabalho stream Analytics, uma vez que estas serão necessárias mais tarde no fluxo de trabalho Stream Analytics. A permissão **SELECT** permite que a função teste a sua ligação à tabela na Base de Dados SQL. A permissão **INSERT** permite testar consultas de stream analytics de ponta a ponta uma vez configurada uma entrada e a saída sql Database. Pode conceder essas permissões ao trabalho stream Analytics utilizando o SQL Server Management Studio. Para mais informações, consulte a referência GRANT (Transact-SQL).

Para conceder apenas permissão a uma determinada tabela ou objeto na base de dados, utilize a seguinte sintaxe T-SQL e execute a consulta. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

Em alternativa, pode clicar com o botão direito na sua base de dados SQL no SQL Server Management Studio e selecionar **Permissões > propriedades**. A partir do menu de permissões, pode ver o trabalho stream Analytics que adicionou anteriormente, e pode conceder ou negar permissões manualmente como entender.

## <a name="create-an-azure-sql-database-output"></a>Criar uma saída de Base de Dados Azure SQL

Agora que a sua identidade gerida está configurada, está pronto para adicionar a Base de Dados Azure SQL como saída para o seu trabalho stream Analytics.

Certifique-se de que criou uma tabela na sua Base de Dados SQL com o esquema de saída apropriado. O nome desta tabela é uma das propriedades necessárias que tem de ser preenchida quando adiciona a saída sql Database à função Stream Analytics. Além disso, certifique-se de que o trabalho tem permissões **SELECT** e **INSERT** para testar a ligação e executar consultas stream Analytics. Consulte a secção [de permissões de emprego grant Stream Analytics](#grant-stream-analytics-job-permissions) se ainda não o fez. 

1. Volte para o seu trabalho stream Analytics e navegue para a página **outputs** em **Job Topology**. 

1. **Selecione adicionar > base de dados SQL**. Na janela de propriedades de saída do lavatório de saída SQL Database, selecione **Identidade Gerida** a partir do modo de autenticação.

1. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída de Base de Dados SQL, consulte [Criar uma saída de Base de Dados SQL com Stream Analytics](sql-database-output.md). Quando terminar, **selecione Save**. 

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Saída Azure Stream Analytics para Azure SQL Database](stream-analytics-sql-output-perf.md)
