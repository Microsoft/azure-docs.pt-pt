---
title: Utilize identidades geridas para aceder à Base de Dados Azure SQL ou Azure Synapse Analytics - Azure Stream Analytics
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para Azure SQL Database ou Azure Synapse Analytics output.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 7d624f2dd2c0c9b4c7e99d5628a1d47e4303da7f
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555598"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Utilize identidades geridas para aceder à Base de Dados Azure SQL ou Azure Synapse Analytics a partir de um trabalho de Azure Stream Analytics (Preview)

O Azure Stream Analytics suporta [a autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a Base de Dados Azure SQL e pias de saída Azure Synapse Analytics. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenenciar devido a alterações de senha ou expirações simbólicas do utilizador que ocorrem a cada 90 dias. Quando remove a necessidade de autenticação manual, as suas implementações stream Analytics podem ser totalmente automatizadas.

Uma identidade gerida é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics. A aplicação gerida é utilizada para autenticar um recurso direcionado. Este artigo mostra-lhe como ativar a Identidade Gerida para uma Base de Dados Azure SQL ou uma saída Azure Synapse Analytics de um trabalho stream Analytics através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

#### <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/azure-sql)

São necessários os seguintes para utilizar esta função:

- Um trabalho da Azure Stream Analytics.

- Um recurso de base de dados Azure SQL.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

São necessários os seguintes para utilizar esta função:

- Um trabalho da Azure Stream Analytics.

- Uma piscina Azure Synapse Analytics SQL.

- Uma conta de Armazenamento Azure que está [configurada para o seu trabalho stream Analytics](azure-synapse-analytics-output.md).

---

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

1. Navegue para o seu recurso Azure SQL Database ou Azure Synapse Analytics e selecione o SQL Server em que a base de dados está. Pode encontrar o nome do Servidor SQL ao lado *do nome do Servidor* na página de visão geral do recurso.

1. Selecione **Ative Directory Admin** em **Definições**. Em seguida, selecione **Definir administração**.

   ![Página de administração de diretório ativo](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Na página de administração do Ative Directory, procure um utilizador ou grupo para ser administrador do SqL Server e clique em **Select**.

   ![Adicionar administrador de diretório ativo](./media/sql-db-output-managed-identity/add-admin.png)

   A página de administração do Ative Directory mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores do Azure Ative Directory. Consulte a lista de administradores suportados na secção **Azure Ative Directory Features and Limitations**   of Use [Azure Ative Directory Authentication for authentication with SQL Database ou Azure Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations). O controlo de acesso baseado em funções (Azure RBAC) aplica-se apenas ao portal e não é propagado ao SQL Server. Além disso, o utilizador ou grupo selecionado é o utilizador que poderá criar o **Utilizador de Base de Dados Contido** na secção seguinte.

1. **Selecione Guardar** na página de administração do **Diretório Ativo.** O processo de mudança de administração demora alguns minutos.

   Quando configurar o administrador do Azure Ative Directory, o novo nome de administração (utilizador ou grupo) não pode estar presente na base de dados primária virtual como utilizador de autenticação do SQL Server. Se estiver presente, a configuração de administração do Azure Ative Directory falhará e reverterá a sua criação, indicando que já existe um administrador (nome). Uma vez que o utilizador de autenticação do SQL Server não faz parte do Azure Ative Directory, qualquer esforço para se ligar ao servidor utilizando a autenticação do Azure Ative Directory uma vez que o utilizador falha. 

## <a name="create-a-contained-database-user"></a>Criar um utilizador de base de dados contido

Em seguida, cria um utilizador de base de dados contido na sua base de dados Azure SQL ou Azure Synapse que está mapeada para a identidade do Diretório Ativo Azure. O utilizador da base de dados contido não tem um login para a base de dados primária, mas mapeia para uma identidade no diretório que está associado à base de dados. A identidade do Diretório Ativo Azure pode ser uma conta de utilizador individual ou um grupo. Neste caso, pretende criar um utilizador de base de dados contido para o seu trabalho stream Analytics. 

1. Ligue-se à sua base de dados Azure SQL ou Azure Synapse utilizando o SQL Server Management Studio. **O nome de Utilizador** é um utilizador do Azure Ative Directory com a permissão ALTER ANY **USER.** O administrador que definiu no SQL Server é um exemplo. Utilize **o Diretório Ativo Azure – Universal com** autenticação MFA. 

   ![Ligar ao SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   O nome do servidor `<SQL Server name>.database.windows.net` pode ser diferente em diferentes regiões. Por exemplo, a região chinesa deve `<SQL Server name>.database.chinacloudapi.cn` utilizar.
 
   Pode especificar uma base de dados específica do Azure SQL ou da Azure Synapse indo para **opções > Connection Properties > Ligar à Base de Dados**.  

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

#### <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/azure-sql)

Depois de ter criado um utilizador de base de dados contido e de ter acesso aos serviços Azure no portal, conforme descrito na secção anterior, o seu trabalho stream Analytics tem permissão da Managed Identity para **ligar** ao seu recurso de base de dados Azure SQL através de identidade gerida. Recomendamos que conceda as permissões SELECT e INSERT para o trabalho stream Analytics, uma vez que estas serão necessárias mais tarde no fluxo de trabalho Stream Analytics. A permissão **SELECT** permite que a função teste a sua ligação à tabela na base de dados Azure SQL. A permissão **INSERT** permite testar consultas de stream analytics de ponta a ponta uma vez configurada uma entrada e a saída da base de dados Azure SQL.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Depois de ter criado um utilizador de base de dados contido e de ter acesso aos serviços Azure no portal, conforme descrito na secção anterior, o seu trabalho stream Analytics tem permissão da Identidade Gerida para **ligar** ao seu recurso de base de dados Azure Synapse através de identidade gerida. Recomendamos que conceda ainda as permissões de OPERAÇÕES DE SELECT, INSERT e GESTÃO DE BASE DE DADOS para o trabalho stream Analytics, uma vez que estas serão necessárias mais tarde no fluxo de trabalho Stream Analytics. A permissão **SELECT** permite que a função teste a sua ligação à tabela na base de dados Azure Synapse. As permissões **de OPERAÇÕES** A GRANEL DE **INSERT** e GESTÃO DE DADOS permitem testar consultas de stream analytics de ponta a ponta uma vez configuradas uma entrada e a saída da base de dados Azure Synapse.

Para conceder a permissão de OPERAÇÕES A GRANEL da BASE DE DADOS, terá de conceder todas as permissões que estejam rotuladas como **CONTROL** [sob a permissão implícita por base de dados](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) para o trabalho de Stream Analytics. Necessita desta permissão porque o trabalho stream Analytics executa a declaração COPY, que requer [administrar operações a granel de base de dados e inserir](/sql/t-sql/statements/copy-into-transact-sql).

---

Pode conceder essas permissões ao trabalho stream Analytics utilizando o SQL Server Management Studio. Para obter mais informações, consulte a referência GRANT (Transact-SQL).

Para conceder apenas permissão a uma determinada tabela ou objeto na base de dados, utilize a seguinte sintaxe T-SQL e execute a consulta. 

#### <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/azure-sql)

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT [PERMISSION NAME] OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Em alternativa, pode clicar com o botão direito na base de dados Azure SQL ou Azure Synapse no SQL Server Management Studio e selecionar **Permissões de > propriedades**. A partir do menu de permissões, pode ver o trabalho stream Analytics que adicionou anteriormente, e pode conceder ou negar permissões manualmente como entender.

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Criar uma base de dados Azure SQL ou saída Azure Synapse

#### <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/azure-sql)

Agora que a sua identidade gerida está configurada, está pronto para adicionar uma base de dados Azure SQL ou saída Azure Synapse ao seu trabalho stream Analytics.

Certifique-se de que criou uma tabela na sua Base de Dados SQL com o esquema de saída apropriado. O nome desta tabela é uma das propriedades necessárias que tem de ser preenchida quando adiciona a saída sql Database à função Stream Analytics. Além disso, certifique-se de que o trabalho tem permissões **SELECT** e **INSERT** para testar a ligação e executar consultas stream Analytics. Consulte a secção [de permissões de emprego grant Stream Analytics](#grant-stream-analytics-job-permissions) se ainda não o fez. 

1. Volte para o seu trabalho stream Analytics e navegue para a página **outputs** em **Job Topology**. 

1. **Selecione adicionar > base de dados SQL**. Na janela de propriedades de saída do lavatório de saída SQL Database, selecione **Identidade Gerida** a partir do modo de autenticação.

1. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída de Base de Dados SQL, consulte [Criar uma saída de Base de Dados SQL com Stream Analytics](sql-database-output.md). Quando terminar, **selecione Save**.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Agora que a sua conta de identidade e armazenamento gerida está configurada, está pronto para adicionar uma base de dados Azure SQL ou saída Azure Synapse ao seu trabalho stream Analytics.

Certifique-se de que criou uma tabela na sua base de dados Azure Synapse com o esquema de saída apropriado. O nome desta tabela é uma das propriedades necessárias que tem de ser preenchida quando adiciona a saída Azure Synapse ao trabalho stream Analytics. Além disso, certifique-se de que o trabalho tem permissões **SELECT** e **INSERT** para testar a ligação e executar consultas stream Analytics. Consulte a secção [de permissões de emprego grant Stream Analytics](#grant-stream-analytics-job-permissions) se ainda não o fez.

1. Volte para o seu trabalho stream Analytics e navegue para a página **outputs** em **Job Topology**.

1. **Selecione Adicionar > Azure Synapse Analytics**. Na janela de propriedades de saída do lavatório de saída SQL Database, selecione **Identidade Gerida** a partir do modo de autenticação.

1. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída Azure Synapse, consulte [a saída Azure Synapse Analytics da Azure Stream Analytics](azure-synapse-analytics-output.md). Quando terminar, **selecione Save**.

---

## <a name="remove-managed-identity"></a>Remover Identidade Gerida

A Identidade Gerida criada para um trabalho stream Analytics só é eliminada quando o trabalho é eliminado. Não há como apagar a Identidade Gerida sem apagar o trabalho. Se já não pretender utilizar a Identidade Gerida, pode alterar o método de autenticação para a saída. A Identidade Gerida continuará a existir até que o trabalho seja eliminado, e será utilizado se decidir voltar a utilizar a autenticação identidade gerida.

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Saída Azure Stream Analytics para Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Saída Azure Synapse Analytics da Azure Stream Analytics](azure-synapse-analytics-output.md)
