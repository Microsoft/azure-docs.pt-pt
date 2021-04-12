---
title: Como digitalizar espaços de trabalho da Azure Synapse
description: Saiba como digitalizar um espaço de trabalho da Synapse no seu catálogo de dados Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031447"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Registar e digitalizar espaços de trabalho da Azure Synapse

Este artigo descreve como registar um espaço de trabalho Azure Synapse em Purview e configurar uma verificação nele.

## <a name="supported-capabilities"></a>Capacidades suportadas

As análises ao espaço de trabalho Azure Synapse suportam a captura de metadados e esquemas para bases de dados SQL dedicadas e sem servidor dentro deles. Também classifica os dados automaticamente com base nas regras de classificação do sistema e personalizados.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin
- Configuração da autenticação conforme descrito nas secções abaixo

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Criação de autenticação para enumeração de recursos de base de dados SQL dedicados no âmbito de um espaço de trabalho synapse

1. Navegue para o **grupo de Recursos** ou **Subscrição** em que se encontra o espaço de trabalho Synapse, no portal Azure.  
1. Selecione **Access Control (IAM)**   do menu de navegação à esquerda 
1. Tem de ser proprietário ou administrador de acesso ao utilizador para adicionar uma função no grupo de Recursos ou subscrição. Selecione *+Adicionar* botão. 
1. Desave a Função **leitora** e introduza o nome da sua conta Azure Purview (que representa o seu MSI) na caixa de entrada Select. Clique em *Guardar* para terminar a atribuição de funções.
1. Siga os passos 2 a 4 acima para adicionar também o **leitor de dados blob de armazenamento** Role para o MSI Azure Purview no grupo de recursos ou subscrição em que se encontra o espaço de trabalho Synapse.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Criação de autenticação para enumerar recursos de base de dados SQL sem servidor no âmbito de um espaço de trabalho synapse

> [!NOTE]
> Deve ser administrador **da Synapse** no espaço de trabalho para executar estes comandos. Saiba mais sobre as permissões da Sinapse [aqui.](../synapse-analytics/security/how-to-set-up-access-control.md)

1. Navegue para o seu espaço de trabalho sinapse
1. Navegue para a secção **de Dados** e para uma das suas bases de dados SQL sem servidor
1. Clique no ícone elipses e inicie um novo script SQL
1. Adicione a conta Azure Purview MSI (representada pelo nome da conta) como **sysadmin** nas bases de dados SQL sem servidor, executando o comando abaixo no seu script SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Criação de autenticação para digitalizar recursos no âmbito de um espaço de trabalho da Sinapse

Existem três formas de configurar a autenticação para uma fonte do Azure Synapse:

- Identidade Gerida
- Principal de Serviço
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Utilização de identidade gerida para bases de dados sql dedicadas

1. Navegue para o seu **espaço de trabalho sinapse**
1. Navegue para a secção **de Dados** e para uma das suas bases de dados SQL sem servidor
1. Clique no ícone elipses e inicie um novo script SQL
1. Adicione a conta Azure Purview MSI (representada pelo nome da conta) como **db_owner** na base de dados SQL dedicada, executando o comando abaixo no seu script SQL:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Utilização de identidade gerida para bases de dados SQL sem servidor

1. Navegue para o seu **espaço de trabalho sinapse**
1. Navegue para a secção **de Dados** e para uma das suas bases de dados SQL sem servidor
1. Clique no ícone elipses e inicie um novo script SQL
1. Adicione a conta Azure Purview MSI (representada pelo nome da conta) como **sysadmin** nas bases de dados SQL sem servidor, executando o comando abaixo no seu script SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Utilização do Principal de Serviço para bases de dados SQL dedicadas

> [!NOTE]
> Primeiro, deve configurar uma nova **credencial** de tipo Principal de Serviço seguindo as instruções [aqui.](manage-credentials.md)

1. Navegue para o seu **espaço de trabalho sinapse**
1. Navegue para a secção **de Dados** e para uma das suas bases de dados SQL sem servidor
1. Clique no ícone elipses e inicie um novo script SQL
1. Adicione o **ID principal** de serviço como **db_owner** na base de dados SQL dedicada, executando o comando abaixo no seu script SQL:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Utilização do Principal de Serviço para bases de dados SQL sem servidor

1. Navegue para o seu **espaço de trabalho sinapse**
1. Navegue para a secção **de Dados** e para uma das suas bases de dados SQL sem servidor
1. Clique no ícone elipses e inicie um novo script SQL
1. Adicione a conta Azure Purview MSI (representada pelo nome da conta) como **sysadmin** nas bases de dados SQL sem servidor, executando o comando abaixo no seu script SQL:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Tem de configurar a autenticação em cada base de dados SQL dedicada dentro do seu espaço de trabalho Synapse, que pretende registar e digitalizar. As permissões acima mencionadas para a base de dados SQL sem servidor aplicam-se a todas elas dentro do seu espaço de trabalho, ou seja, terá de executá-la apenas uma vez.
    
## <a name="register-an-azure-synapse-source"></a>Registar uma Fonte de Sinapse Azure

Para registar uma nova Fonte Azure Synapse no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure Synapse Analytics (múltiplos)**
1. Selecione **Continuar**

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Configurar fonte do Azure Synapse":::

No ecrã **'Azure Synapse Analytics',** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. Escolha opcionalmente uma **subscrição** para filtrar para baixo.
1. **Selecione um nome de espaço de trabalho Synapse** a partir do dropdown. Os pontos finais SQL são preenchidos automaticamente com base na sua seleção de espaço de trabalho. 
1. Selecione uma **coleção** ou crie uma nova (Opcional)
1. **Termine** para registar a fonte de dados

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Preencha detalhes para a fonte do Azure Synapse":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:

1. Navegue para a secção **Fontes.**

1. Selecione a fonte de dados que registou.

1. Clique em detalhes de visualização e Selecione **+ Nova digitalização** ou use o ícone de ação rápida de digitalização no azulejo de origem

1. Preencha o *nome* e selecione todos os tipos de recursos que pretende digitalizar dentro desta fonte. **A BASE de Dados SQL** é o único tipo que apoiamos atualmente dentro de um espaço de trabalho da Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Scan de fonte de Sinapse Azure":::

1. Selecione a **credencial** para ligar aos recursos dentro da sua fonte de dados. 
  
1. Dentro de cada tipo pode selecionar para digitalizar todos os recursos ou um subconjunto deles pelo nome.
1.  Clique em **continuar** para continuar. 

1.  Selecione uma regra de **digitalização de** conjuntos de tipo Azure Synapse SQL. Também pode criar conjuntos de regras de digitalização em linha.

1. Escolha o seu gatilho de digitalização. Pode programar para funcionar **semanal/mensalmente** ou **uma vez**

1. Reveja a sua digitalização e selecione Guardar para completar a configuração   

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizar os seus exames e digitalizar corre

1. Ver detalhes da origem clicando nos detalhes da **visualização** no azulejo sob a secção de fontes. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Detalhes da fonte de Azure Synapse"::: 

1. Veja os detalhes da execução da verificação navegando na página de detalhes da **digitalização.**
    1. A *barra de estado* é um breve resumo sobre o estado de funcionamento dos recursos das crianças. Será exibido no nível de espaço de trabalho.
    1. Verde significa sucesso, enquanto vermelho significa falhado. Cinzento significa que a tomografia ainda está em andamento.
    1. Pode clicar em cada digitalização para ver mais detalhes finos

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Detalhes da varredura de Azure Synapse" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Veja um resumo da recente verificação falhada na parte inferior da página de detalhes da origem. Também pode clicar em visualizar mais detalhes granulares relativos a estas corridas.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gerir as suas digitalizações - editar, excluir ou cancelar
Para gerir ou apagar uma verificação, faça o seguinte:

- Navegue para o centro de gestão. Selecione fontes de dados sob as Fontes e a secção de digitalização e, em seguida, selecione na fonte de dados desejada.

- Selecione a digitalização que gostaria de fazer. Pode editar a digitalização selecionando Editar.

- Pode eliminar a sua digitalização selecionando Eliminar.
- Se um exame estiver a decorrer, também pode cancelá-lo.

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)   