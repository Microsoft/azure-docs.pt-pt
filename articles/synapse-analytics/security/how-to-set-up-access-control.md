---
title: Como configurar o controlo de acesso para o seu espaço de trabalho Synapse
description: Este artigo irá ensiná-lo a controlar o acesso a um espaço de trabalho da Sinapse usando funções Azure, funções de Sinapse, permissões SQL e Git.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 513d23de3d7f823ec934af32e5f3776e876f02d8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369004"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Como configurar o controlo de acesso para o seu espaço de trabalho Synapse 

Este artigo irá ensiná-lo a controlar o acesso a um espaço de trabalho da Sinapse usando funções Azure, funções de Sinapse, permissões SQL e Git.   

Neste guia, você vai configurar um espaço de trabalho e configurar um sistema básico de controlo de acesso adequado para muitos projetos da Sinapse.  Em seguida, descreve opções mais avançadas para um controlo mais fino, caso necessite.  

O controlo de acesso sinapse pode ser simplificado usando grupos de segurança que estão alinhados com os papéis e personalidades da sua organização.  Basta adicionar e remover os utilizadores dos grupos de segurança para gerir o acesso.

Antes de iniciar esta passagem, leia a visão geral do controlo de [acesso da Sinaapse](./synapse-workspace-access-control-overview.md) para se familiarizar com os mecanismos de controlo de acesso utilizados pela Synapse.   

## <a name="access-control-mechanisms"></a>Mecanismos de controlo de acessos

> [!NOTE]
> A abordagem adotada neste guia é criar vários grupos de segurança e, em seguida, atribuir funções a estes grupos. Depois de os grupos serem criados, basta gerir a adesão dentro dos grupos de segurança para controlar o acesso ao espaço de trabalho.

Para garantir um espaço de trabalho da Sinapse, seguirá um padrão de configuração dos seguintes itens:

- **Grupos de** Segurança , para agrupar utilizadores com requisitos de acesso semelhantes.
- **Funções Azure,** para controlar quem pode criar e gerir piscinas SQL, piscinas Apache Spark e tempos de integração, e aceder ao armazenamento da ADLS Gen2.
- **Funções de sinapse**, para controlar o acesso a artefactos de código publicados, utilização de recursos de computação Apache Spark e tempos de integração 
- **Permissões SQL,** para controlar o acesso administrativo e de avião de dados às piscinas SQL. 
- **Permissões de git**, para controlar quem pode aceder a artefactos de código no controlo de fontes se configurar o suporte de Git para o espaço de trabalho 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Passos para garantir um espaço de trabalho da Sinapse

Este documento utiliza nomes padrão para simplificar as instruções. Substitua-os por nomes à sua escolha.

|Definição | Nome padrão | Description |
| :------ | :-------------- | :---------- |
| **Espaço de trabalho sinapse** | `workspace1` |  O nome que o espaço de trabalho da Sinapse terá. |
| **Conta ADLSGEN2** | `storage1` | A conta ADLS para usar com o seu espaço de trabalho. |
| **Container** (Contentor) | `container1` | O recipiente em STG1 que o espaço de trabalho utilizará por defeito. |
| **Inquilino de diretório ativo** | `contoso` | o nome de inquilino de diretório ativo.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSO 1: Criar grupos de segurança

>[!Note] 
>Durante a pré-visualização, foi recomendado criar grupos de segurança mapeados para as funções **de Administrador SQL synapse Synapse** e **Administrador de Faíscas sinapse Apache.**  Com a introdução de novas funções e âmbitos de RBAC da Sinaapse mais finos, recomenda-se agora que utilize estas novas capacidades para controlar o acesso ao seu espaço de trabalho.  Estas novas funções e âmbitos proporcionam mais flexibilidade de configuração e reconhecem que os desenvolvedores usam frequentemente uma mistura de SQL e Spark na criação de aplicações de análise e podem ter de ter acesso a recursos específicos em vez de todo o espaço de trabalho. [Saiba mais](./synapse-workspace-synapse-rbac.md) sobre o SYNAPSE RBAC.

Crie os seguintes grupos de segurança para o seu espaço de trabalho:

- **`workspace1_SynapseAdministrators`**, para os utilizadores que necessitem de controlo total sobre o espaço de trabalho.  Adicione-se a este grupo de segurança, pelo menos inicialmente.
- **`workspace1_SynapseContributors`**, para os desenvolvedores que precisam de desenvolver, depurar e publicar código para o serviço.   
- **`workspace1_SynapseComputeOperators`**, para os utilizadores que necessitem de gerir e monitorizar as piscinas Apache Spark e os tempos de funcionação da Integração.
- **`workspace1_SynapseCredentialUsers`**, para os utilizadores que necessitem de depurar e executar gasodutos de orquestração utilizando a credencial de MSI (identidade de serviço gerido) e cancelar as operações de gasoduto.   

Em breve atribuirá funções de Sinapse a estes grupos no âmbito do espaço de trabalho.  

Crie também este grupo de segurança: 
- **`workspace1_SQLAdmins`**, grupo para utilizadores que necessitem de sql Ative Directory Admin autoridade dentro de piscinas SQL no espaço de trabalho. 

O `workspace1_SQLAdmins` grupo será utilizado quando configurar permissões SQL em piscinas SQL à medida que as cria. 

Para uma configuração básica, estes cinco grupos são suficientes. Mais tarde, pode adicionar grupos de segurança para lidar com utilizadores que necessitem de acesso mais especializado ou para dar aos utilizadores acesso apenas a recursos específicos.

> [!NOTE]
>- Saiba como criar um grupo de segurança [neste artigo.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)
>- Saiba como adicionar um grupo de segurança a outro grupo de segurança [neste artigo](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Os utilizadores individuais da Synapse podem usar o Azure Ative Directory no portal Azure para visualizar os seus membros do grupo para determinar quais as funções que lhes foram concedidas.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>PASSO 2: Prepare a sua conta de armazenamento ADLS Gen2

Um espaço de trabalho da Sinapse utiliza um recipiente de armazenamento predefinido para:
  - Armazenar os ficheiros de dados de suporte para as tabelas Spark
  - Registos de execução para trabalhos de faísca
  - Gestão de bibliotecas que escolhe instalar

Identifique as seguintes informações sobre o seu armazenamento:

- A conta ADLS Gen2 para usar para o seu espaço de trabalho. Este documento `storage1` chama-lhe. `storage1` é considerada a conta de armazenamento "primária" para o seu espaço de trabalho.
- O recipiente dentro `workspace1` do seu espaço de trabalho Synapse utilizará por defeito. Este documento `container1` chama-lhe. 

- Utilizando o portal Azure, atribua as seguintes funções de Azure `container1` aos grupos de segurança 

  - Atribuir a função **de contribuinte de dados blob de armazenamento** para `workspace1_SynapseAdmins` 
  - Atribuir a função **de contribuinte de dados blob de armazenamento** para `workspace1_SynapseContributors`
  - Atribuir a função **de contribuinte de dados blob de armazenamento** para `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSO 3: Criar e configurar o seu espaço de trabalho sinapse

No portal Azure, crie um espaço de trabalho sinapse:

- Selecione a sua subscrição
- Selecione ou crie um grupo de recursos para o qual tem a função **Azure Owner.**
- Nomeie o espaço de trabalho `workspace1`
- Escolha `storage1` a conta de Armazenamento
- Escolha `container1` o recipiente que está a ser utilizado como "sistema de ficheiros".
- Open WS1 no Estúdio Synapse
- Navegue para **gerir**  >  **o controlo de acesso** e atribua funções de Sinapse no âmbito do espaço de *trabalho* aos grupos de segurança da seguinte forma:
  - Atribuir a **função de Administrador de Sinapse** para `workspace1_SynapseAdministrators` 
  - Atribuir o papel **de Contribuinte sinapse** para `workspace1_SynapseContributors` 
  - Atribuir a função **de Operador de Computação De Sinapse** para `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>PASSO 4: Conceder ao espaço de trabalho acesso MSI ao recipiente de armazenamento predefinido

Para executar oleodutos e executar tarefas do sistema, a Synapse requer que a identidade de serviço gerida pelo espaço de trabalho (MSI) tenha acesso `container1` na conta ADLS Gen2 predefinido.

- Abra o portal do Azure
- Localize a conta de `storage1` armazenamento, e depois `container1`
- Utilizando **o Controlo de Acesso (IAM)**, certifique-se de que a função de contribuinte de **dados de armazenamento blob** é atribuída ao ESPAÇO DE TRABALHO MSI
  - Se não for designado, atribua-o.
  - O MSI tem o mesmo nome que o espaço de trabalho. Neste artigo, `workspace1` seria.

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>PASSO 5: Grant Synapse administradores o papel de Colaborador Azure no espaço de trabalho 

Para criar piscinas SQL, piscinas Apache Spark e tempos de execução de integração, os utilizadores devem ter pelo menos acesso Azure Contributor ao espaço de trabalho. O papel do contribuinte também permite que estes utilizadores gerem os recursos, incluindo pausas e dimensionamento.

- Abra o portal do Azure
- Localize o espaço de trabalho, `workspace1`
- Atribua o papel **de Contribuinte Azure** `workspace1` a `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>PASSO 6: Atribuir a função de administrador de diretório ativo SQL

O criador do espaço de trabalho é automaticamente configurado como O SQL Ative Directory Admin para o espaço de trabalho.  Apenas um único utilizador ou grupo pode ser concedido a esta função. Neste passo, atribua o SQL Ative Directory Admin no espaço de trabalho ao `workspace1_SQLAdmins` grupo de segurança.  A atribuição deste papel dá a este grupo acesso administrativo altamente privilegiado a todas as piscinas e bases de dados SQL no espaço de trabalho.   

- Abra o portal do Azure
- Navegue para `workspace1`
- Em **Definições**, selecione **sql ative directy administrador**
- Selecione **Definir administração** e escolha **`workspace1_SQLAdmins`**

>[!Note]
>O passo 6 é opcional.  Pode optar por conceder ao `workspace1_SQLAdmins` grupo um papel menos privilegiado. Para atribuir `db_owner` ou outras funções SQL, tem de executar scripts em cada base de dados SQL. 

## <a name="step-7-grant-access-to-sql-pools"></a>PASSO 7: Conceder acesso a piscinas SQL

Por predefinição, todos os utilizadores designados pela função de Administrador de Sinapse também são atribuídos à função SQL `db_owner` na piscina SQL sem servidor, 'Built-in' e em todas as suas bases de dados.

O acesso a piscinas SQL para outros utilizadores e para o espaço de trabalho MSI é controlado usando permissões SQL.  A atribuição de permissões SQL requer que os scripts SQL sejam executados em cada base de dados SQL após a criação.  Há três casos que exigem que execute estes scripts:
1. Concedendo a outros utilizadores acesso à piscina SQL sem servidor, 'Built-in', e suas bases de dados
2. Conceder acesso de qualquer utilizador a bases de dados dedicadas de piscinas
3. Conceder o acesso do ESPAÇO de trabalho MSI a uma base de dados de piscinas SQL para permitir que os gasodutos que requerem acesso à piscina SQL funcionem com sucesso.

Exemplo, os scripts SQL estão incluídos abaixo.

Para permitir o acesso a uma base de dados de piscinas SQL dedicada, os scripts podem ser executados pelo criador do espaço de trabalho ou por qualquer membro do `workspace1_SQLAdmins` grupo.  

Para permitir o acesso à piscina SQL sem servidor, 'Built-in', os scripts podem ser executados por qualquer membro do `workspace1_SQLAdmins` grupo ou do  `workspace1_SynapseAdministrators` grupo. 

> [!TIP]
> Os passos abaixo precisam de ser executados para **cada** pool SQL para conceder ao utilizador acesso a todas as bases de dados SQL, exceto na secção [Despacência de espaço de trabalho,](#workspace-scoped-permission) onde você pode atribuir a um utilizador uma função sysadmin no nível do espaço de trabalho.

### <a name="step-71-serverless-sql-pool-built-in"></a>PASSO 7.1: Piscina SQL sem servidor, incorporada

Nesta secção, existem exemplos de scripts que mostram como dar permissão a um utilizador para aceder a uma determinada base de dados ou a todas as bases de dados na piscina SQL sem servidor, 'Incorporado'.

> [!NOTE]
> Nos exemplos de scripts, substitua o *pseudónimo* pelo pseudónimo do utilizador ou grupo que tem acesso, e *domínio* com o domínio da empresa que está a utilizar.

#### <a name="database-scoped-permission"></a>Permissão de âmbito de base de dados

Para conceder acesso a um utilizador a uma **única** base de dados SQL sem servidor, siga os passos neste exemplo:

1. Criar LOGIN

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar UTILIZADOR

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Adicionar UTILIZADOR aos membros da função especificada

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Permissão de âmbito de espaço de trabalho

Para conceder acesso total a **todas as** piscinas SQL sem servidor no espaço de trabalho, utilize o script neste exemplo:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>PASSO 7.2: Piscinas SQL dedicadas

Para conceder acesso a uma **única** base de dados de piscinas SQL dedicada, siga estes passos no editor de scripts Synapse SQL:

1. Crie o utilizador na base de dados executando o seguinte comando na base de dados-alvo, selecionada utilizando o *Connect para* desatenção:

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Conceda ao utilizador uma função para aceder à base de dados:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* podem trabalhar para obter permissões de leitura/escrita se não for desejada a concessão *de* db_owner permissão.
> Para que um utilizador da Spark leia e escreva diretamente da Spark dentro ou a partir de uma piscina SQL, é necessária *db_owner* permissão.

Depois de criar os utilizadores, execute consultas para validar que o pool SQL sem servidor pode consultar a conta de armazenamento.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>PASSO 7.3: Controlo de acesso sql para gasoduto synapse corre

### <a name="workspace-managed-identity"></a>Identidade gerida por espaço de trabalho

> [!IMPORTANT]
> Para executar pipelines com sucesso que incluam conjuntos de dados ou atividades que referenciam um pool SQL, a identidade do espaço de trabalho precisa de ter acesso à piscina SQL.

Executar os seguintes comandos em cada piscina SQL para permitir que a identidade do sistema gerido pelo espaço de trabalho funcione para executar gasodutos na base de dados de piscinas SQL:  

>[!note]
>Nos scripts abaixo, para uma base de dados dedicada à piscina SQL, o nome de base de dados é o mesmo que o nome da piscina.  Para uma base de dados na piscina SQL sem servidor 'Built-in', o nome de base de dados é o nome da base de dados.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Esta permissão pode ser removida executando o seguinte script na mesma piscina SQL:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>PASSO 8: Adicionar utilizadores a grupos de segurança

A configuração inicial do seu sistema de controlo de acesso está completa.

Para gerir o acesso, pode adicionar e remover os utilizadores aos grupos de segurança que criou.  Embora possa atribuir manualmente os utilizadores às funções de Sinapse, se o fizer, não configurará as suas permissões de forma consistente. Em vez disso, basta adicionar ou remover os utilizadores aos grupos de segurança.

## <a name="step-9-network-security"></a>PASSO 9: Segurança da rede

Como um passo final para garantir o seu espaço de trabalho, deve garantir o acesso à rede, utilizando:
- [Firewall do espaço de trabalho](./synapse-workspace-ip-firewall.md)
- [Rede virtual gerida](./synapse-workspace-managed-vnet.md) 
- [Pontos finais privados](./synapse-workspace-managed-private-endpoints.md)
- [Ligação Privada](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>PASSO 10: Conclusão

O seu espaço de trabalho está agora totalmente configurado e seguro.

## <a name="supporting-more-advanced-scenarios"></a>Apoiar cenários mais avançados

Este guia focou-se na criação de um sistema básico de controlo de acessos. Você pode apoiar cenários mais avançados criando grupos de segurança adicionais e atribuindo a estes grupos mais papéis granulares em âmbitos mais específicos. Considere os seguintes casos:

**Ativar** o suporte do Git para o espaço de trabalho para cenários de desenvolvimento mais avançados, incluindo CI/CD.  Enquanto no modo Git, as permissões do Git determinarão se um utilizador pode cometer alterações no seu ramo de trabalho.  A publicação ao serviço ocorre apenas a partir da filial de colaboração.  Considere criar um grupo de segurança para desenvolvedores que precisam de desenvolver e depurar atualizações num ramo de trabalho, mas não precisam de publicar alterações no serviço ao vivo.

**Restringir o acesso do desenvolvedor** a recursos específicos.  Criar grupos de segurança adicionais para desenvolvedores que precisam de acesso apenas a recursos específicos.  Atribua a estes grupos funções sinapses apropriadas que são scoped a piscinas de faíscas específicas, tempos de integração ou credenciais.

**Restringir os operadores de acederem a artefactos de código.**  Crie grupos de segurança para os operadores que necessitem de monitorizar o estado operacional da Synapse computação de recursos e ver registos, mas que não precisam de ter acesso ao código ou de publicar atualizações ao serviço. Atribua a estes grupos a função de Operador Compute a piscinas e tempos de execução específicos da Spark.  

## <a name="next-steps"></a>Passos seguintes

Saiba [como gerir atribuições de funções de Synapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md) Crie um espaço de [trabalho de sinapse](../quickstart-create-workspace.md)
