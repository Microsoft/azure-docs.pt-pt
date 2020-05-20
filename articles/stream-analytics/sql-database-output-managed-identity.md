---
title: Utilize identidades geridas para aceder à Base de Dados Azure SQL - Azure Stream Analytics
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho de Azure Stream Analytics à saída do Azure SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a8699b3942fe3a4b23f1d72036b7364cdab36f8e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651972"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Utilize identidades geridas para aceder à Base de Dados Azure SQL a partir de um trabalho de Azure Stream Analytics (Pré-visualização)

O Azure Stream Analytics suporta a [autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para os sinks de saída da Base de Dados Azure SQL. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenticar devido a alterações de palavra-passe ou expirações de token do utilizador que ocorrem a cada 90 dias. Quando remove a necessidade de autenticar manualmente, as suas implementações stream Analytics podem ser totalmente automatizadas.

Uma identidade gerida é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics. A aplicação gerida é usada para autenticar um recurso direcionado. Este artigo mostra-lhe como ativar a Identidade Gerida para uma(s) saída de base de dados Azure SQL de um trabalho de Stream Analytics através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes recursos para esta funcionalidade:

- Um trabalho de Azure Stream Analytics.

- Um recurso azure SQL Database.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Primeiro, criauma identidade gerida para o seu trabalho de Azure Stream Analytics.

1. No [portal Azure,](https://portal.azure.com)abra o seu trabalho de Azure Stream Analytics.

1. A partir do menu de navegação à esquerda, selecione **Identidade Gerida** localizada em **Configuração**. Em seguida, verifique a caixa ao lado da **Identidade Gerida atribuída** pelo sistema e selecione **Guardar**.

   ![Selecione identidade gerida atribuída pelo sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Um diretor de serviço para a identidade do trabalho do Stream Analytics é criado no Diretório Ativo Azure. O ciclo de vida da identidade recém-criada é gerido por Azure. Quando o trabalho stream analytics é eliminado, a identidade associada (isto é, o diretor de serviço) é automaticamente eliminada pelo Azure. 

1. Quando guardar a configuração, o ID do objeto (OID) do diretor de serviço está listado como id principal, como mostrado abaixo: 

   ![ID do objeto mostrado como ID principal](./media/sql-db-output-managed-identity/principal-id.png)

   O diretor de serviço tem o mesmo nome que o trabalho da Stream Analytics. Por exemplo, se o nome do seu trabalho é *MyASAJob*, o nome do diretor de serviço também é *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selecione um administrador de Diretório Ativo

Depois de criar uma identidade gerida, selecione um administrador de Diretório Ativo.

1. Navegue para o seu recurso de base de dados Azure SQL e selecione o Servidor SQL em que a base de dados está. Pode encontrar o nome Do Servidor SQL ao lado do *nome do Servidor* na página de visão geral do recurso. 

1. Selecione **Adin o Diretório Ativo** em **Definições**. Em seguida, selecione Configurar a **administração**. 

   ![Página de administração de Diretório Ativo](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Na página de administração do Diretório Ativo, procure um utilizador ou grupo para ser administrador do Servidor SQL e clique em **Select**.

   ![Adicionar administrador de Diretório Ativo](./media/sql-db-output-managed-identity/add-admin.png)

   A página de administração do Diretório Ativo mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores da AD Azure. Consulte a lista de administradores suportados na secção de **Funcionalidades e Limitações ad-azure**   da [utilização azure ative de autenticação para autenticação com Base de Dados SQL ou Synapse Azure](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). O controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal e não é propagado ao Servidor SQL. Além disso, o utilizador ou grupo selecionado é o utilizador que poderá criar o Utilizador de **Base de Dados Contido** na próxima secção.

1. Selecione **Guardar** na página de administração do **Diretório Ativo.** O processo de mudança de administrador demora alguns minutos.

   Quando configura o administrador Da D Azure, o novo nome de administrador (utilizador ou grupo) não pode estar presente na base de dados virtual como utilizador de autenticação SQL Server. Se presente, a configuração de administração da AD Azure falhará e reverterá a sua criação, indicando que já existe um administrador (nome). Uma vez que o utilizador de autenticação Do Servidor SQL não faz parte do Azure AD, qualquer esforço para se ligar ao servidor utilizando a autenticação AD Azure, uma vez que esse utilizador falha. 

## <a name="create-a-contained-database-user"></a>Criar um utilizador de base de dados contido

Em seguida, cria um utilizador de base de dados contido na sua Base de Dados SQL que está mapeado para a identidade do Diretório Ativo Azure. O utilizador de base de dados contido não tem um login para a base de dados principal, mas mapeia uma identidade no diretório que está associada à base de dados. A identidade do Diretório Ativo Azure pode ser uma conta de utilizador individual ou um grupo. Neste caso, pretende criar um utilizador de base de dados contido para o seu trabalho no Stream Analytics. 

1. Ligue-se à base de dados SQL utilizando o Estúdio de Gestão de Servidores SQL. O **nome user** é um utilizador do Diretório Ativo Azure com a permissão ALTER ANY **USER.** O administrador que definiu no Servidor SQL é um exemplo. Utilize **o Diretório Ativo Azure – Universal com** autenticação MFA. 

   ![Ligar ao SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   O nome do servidor `<SQL Server name>.database.windows.net` pode ser diferente em diferentes regiões. Por exemplo, a região chinesa deve `<SQL Server name>.database.chinacloudapi.cn` utilizar.
 
   Pode especificar uma base de dados SQL específica indo para Opções > Propriedades de **Ligação > Ligar à Base de Dados**.  

   ![Propriedades de ligação ao Servidor SQL](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Quando se ligar pela primeira vez, poderá encontrar a seguinte janela:

   ![Nova janela de regras de firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Em caso afirmativo, vá ao seu recurso SQL Server no portal Azure. Sob a secção **de Segurança,** abra as Firewalls e a página **de rede virtual.** 
   1. Adicione uma nova regra com qualquer nome de regra.
   1. Utilize o endereço *Ip da* nova janela de regra da **firewall** para o IP *de início*.
   1. Utilize o endereço *To* IP a partir da janela **new firewall rule** para o IP *final*. 
   1. Selecione **Guardar** e tente ligar-se novamente do Estúdio de Gestão de Servidores SQL. 

1. Uma vez ligado, crie o utilizador de base de dados contido. O seguinte comando SQL cria um utilizador de base de dados contido que tem o mesmo nome que o seu trabalho stream analytics. Certifique-se de incluir os suportes à volta do *ASA_JOB_NAME*. Utilize a seguinte sintaxe T-SQL e faça a consulta. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Para que o Diretório Ativo Azure da Microsoft verifique se o trabalho do Stream Analytics tem acesso à Base de Dados SQL, precisamos de dar permissão ao Azure Ative Directory para comunicar com a base de dados. Para isso, aceda novamente à página "Firewalls e rede virtual" no Portal Do Azure e permita que "permita que os serviços e recursos do Azure acedam a este servidor". 

   ![Firewall e rede virtual](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Permissões de emprego de Grant Stream Analytics

Uma vez criado um utilizador de base de dados contido e dado acesso aos serviços Azure no portal, conforme descrito na secção anterior, o seu trabalho stream Analytics tem permissão da Identidade Gerida para **ligar** ao seu recurso de base de dados SQL através de identidade gerida. Recomendamos que conceda as permissões SELECT e INSERT para o trabalho de Stream Analytics, uma vez que estas serão necessárias mais tarde no fluxo de trabalho stream Analytics. A permissão **SELECT** permite que o trabalho teste a sua ligação à tabela na Base de Dados SQL. A permissão **INSERT** permite testar consultas de stream analytics de ponta a ponta uma vez configurado uma entrada e a saída de Base de Dados SQL. Pode conceder essas permissões ao trabalho do Stream Analytics utilizando o Estúdio de Gestão de Servidores SQL. Para mais informações, consulte a referência [GRANT (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15)

Para apenas conceder permissão a uma determinada tabela ou objeto na base de dados, utilize a seguinte sintaxe T-SQL e faça a consulta. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

Em alternativa, pode clicar corretamente na sua base de dados SQL no Estúdio de Gestão de Servidores SQL e selecionar **Propriedades > Permissões**. A partir do menu de permissões, pode ver o trabalho de Stream Analytics que adicionou anteriormente, e pode conceder ou negar manualmente permissões como entender.

## <a name="create-an-azure-sql-database-output"></a>Criar uma saída de base de dados Azure SQL

Agora que a sua identidade gerida está configurada, está pronto para adicionar a Base de Dados Azure SQL como saída ao seu trabalho de Stream Analytics.

Certifique-se de que criou uma tabela na sua Base de Dados SQL com o esquema de saída apropriado. O nome desta tabela é uma das propriedades necessárias que tem de ser preenchida quando adiciona a saída da Base de Dados SQL ao trabalho stream analytics. Além disso, certifique-se de que o trabalho tem permissões **SELECT** e **INSERT** para testar a ligação e executar consultas stream analytics. Consulte a secção de [permissões](#grant-stream-analytics-job-permissions) de trabalho grant Stream Analytics se ainda não o fez. 

1. Volte ao seu trabalho de Stream Analytics e navegue para a página **Outputs** no âmbito da **Job Topology**. 

1. Selecione Adicionar > Base de **Dados SQL**. Na janela de propriedades de saída do saque de saída da Base de Dados SQL, selecione **Identidade Gerida** a partir do modo de autenticação.

1. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída de Base de Dados SQL, consulte Criar uma saída de Base de [Dados SQL com Stream Analytics](stream-analytics-define-outputs.md#sql-database). Quando terminar, selecione **Guardar**. 

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Saída do Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md)
