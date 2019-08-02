---
title: Gerenciar usuários e funções de banco de dados no Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar usuários e funções de banco de dados em um servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c38b11ceda010c122e17a7fad3df1684e0a1cf42
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696279"
---
# <a name="manage-database-roles-and-users"></a>Gerenciar usuários e funções de banco de dados

No nível do banco de dados modelo, todos os usuários devem pertencer a uma função. As funções definem os usuários com permissões específicas para o banco de dados modelo. Qualquer usuário ou grupo de segurança adicionado a uma função deve ter uma conta em um locatário do Azure AD na mesma assinatura que o servidor. 

A maneira como você define as funções é diferente dependendo da ferramenta usada, mas o efeito é o mesmo.

As permissões de função incluem:
*  **Administrador** -os usuários têm permissões completas para o banco de dados. As funções de banco de dados com permissões de administrador são diferentes dos administradores de servidor.
*  **Process** -os usuários podem se conectar e executar operações de processo no banco de dados, e analisar o modelo de dado do banco.
*  **Leia** -os usuários podem usar um aplicativo cliente para se conectar e analisar os dados do modelo de banco.

Ao criar um projeto de modelo de tabela, você cria funções e adiciona usuários ou grupos a essas funções usando o Gerenciador de funções no SQL Server Data Tools (SSDT). Quando implantado em um servidor, você usa SQL Server Management Studio (SSMS), [Analysis Services cmdlets do PowerShell](/sql/analysis-services/powershell/analysis-services-powershell-reference)ou TMSL (linguagem de script de [modelo de tabela](https://msdn.microsoft.com/library/mt614797.aspx) ) para adicionar ou remover funções e membros do usuário.

Os **grupos de segurança** devem ser habilitados para `MailEnabled` email com a propriedade definida como. [](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) `True` Ao especificar um grupo por endereço de email `obj:groupid@tenantid`, use.


## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Para adicionar ou gerenciar funções e usuários no SSDT  
  
1.  Em SSDT > **Gerenciador de modelos de tabela**, clique com o botão direito do mouse em **funções**.  
  
2.  No **Gestor de Funções**, clique em **Nova**.  
  
3.  Digite um nome para a função.  
  
     Por padrão, o nome da função padrão é numerado incrementalmente para cada nova função. É recomendável que você digite um nome que identifique claramente o tipo de membro, por exemplo, gerentes de finanças ou especialistas de recursos humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhum**|Os membros não podem modificar o esquema do modelo e não podem consultar dados.|  
    |**Leitura**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema do modelo.|  
    |**Leitura e processo**|Os membros podem consultar dados (com base em filtros em nível de linha) e executar processar e processar todas as operações, mas não podem modificar o esquema do modelo.|  
    |**Process**|Os membros podem executar processar e processar todas as operações. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
    |**Administradores**|Os membros podem modificar o esquema do modelo e consultar todos os dados.|   
  
5.  Se a função que você está criando tiver a permissão ler ou ler e processar, você poderá adicionar filtros de linha usando uma fórmula DAX. Clique na guia **filtros de linha** , selecione uma tabela, clique no campo **filtro Dax** e digite uma fórmula DAX.
  
6.  Clique em **Membros** > **Adicionar externo**.  
  
8.  Em **Adicionar membro externo**, insira usuários ou grupos no seu locatário do Azure ad por endereço de email. Depois de clicar em OK e fechar o Gerenciador de funções, as funções e os membros da função aparecerão no Gerenciador de modelos de tabela. 
 
     ![Funções e usuários no Gerenciador de modelos de tabela](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implante em seu servidor de Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerenciar funções e usuários no SSMS

Para adicionar funções e usuários a um banco de dados modelo implantado, você deve estar conectado ao servidor como um administrador de servidor ou já em uma função de banco de dados com permissões de administrador.

1. Em objeto explorador, clique com o botão direito do mouse em **funções** > **nova função**.

2. Em **criar função**, insira um nome de função e uma descrição.

3. Selecione uma permissão.

   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controle total (administrador)**|Os membros podem modificar o esquema do modelo, processar e consultar todos os dados.| 
   |**Processar banco de dados**|Os membros podem executar processar e processar todas as operações. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
   |**Leitura**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema do modelo.|  
  
4. Clique em **Associação**, em seguida, insira um usuário ou grupo no seu locatário do Azure ad por endereço de email.

     ![Adicionar utilizador](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se a função que você está criando tiver permissão de leitura, você poderá adicionar filtros de linha usando uma fórmula DAX. Clique em **filtros de linha**, selecione uma tabela e, em seguida, digite uma fórmula DAX no campo **filtro Dax** . 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e usuários usando um script TMSL

Você pode executar um script TMSL na janela XMLA no SSMS ou usando o PowerShell. Use o comando [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) e o objeto [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) .

**Exemplo de script TMSL**

Neste exemplo, um usuário externo B2B e um grupo são adicionados à função de analista com permissões de leitura para o banco de dados SalesBI. O usuário externo e o grupo devem estar no mesmo locatário do Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e usuários usando o PowerShell

O módulo [SqlServer](/sql/analysis-services/powershell/analysis-services-powershell-reference) fornece cmdlets de gerenciamento de banco de dados específicos de tarefas e o cmdlet Invoke-ASCmd de uso geral que aceita uma consulta ou script de TMSL (linguagem de script de modelo de tabela). Os cmdlets a seguir são usados para gerenciar usuários e funções de banco de dados.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Adicione um membro a uma função de banco de dados.| 
|[Remove-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Execute um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Os filtros de linha definem quais linhas em uma tabela podem ser consultadas por membros de uma função específica. Os filtros de linha são definidos para cada tabela em um modelo usando fórmulas DAX.  
  
Os filtros de linha podem ser definidos somente para funções com permissões de leitura e leitura e processo. Por padrão, se um filtro de linha não estiver definido para uma tabela específica, os membros poderão consultar todas as linhas na tabela, a menos que a filtragem cruzada se aplique de outra tabela.
  
 Os filtros de linha exigem uma fórmula DAX, que deve ser avaliada como um valor TRUE/FALSE, para definir as linhas que podem ser consultadas por membros dessa função específica. Linhas não incluídas na fórmula DAX não podem ser consultadas. Por exemplo, a tabela Customers com a seguinte expressão de filtros de linha, *= Customers [Country] = "usa"* , os membros da função Sales só podem ver os clientes nos EUA.  
  
Os filtros de linha se aplicam às linhas especificadas e linhas relacionadas. Quando uma tabela tem várias relações, os filtros aplicam segurança para a relação que está ativa. Os filtros de linha são interseccionados com outros Filers de linha definidos para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|= Region [país] = "EUA"|  
|ProductCategory|= ProductCategory [name] = "bicicletas"|  
|Transações|= Transações [ano] = 2016|  
  
 O efeito líquido é que os membros podem consultar linhas de dados em que o cliente está nos EUA, a categoria do produto é bicicletas e o ano é 2016. Os usuários não podem consultar transações fora dos EUA, transações que não são bicicletas ou transações que não estão em 2016, a menos que sejam membros de outra função que concede essas permissões.
  
 Você pode usar o filtro, *= false ()* , para negar o acesso a todas as linhas de uma tabela inteira.

## <a name="next-steps"></a>Passos seguintes

  [Gerenciar administradores de servidor](analysis-services-server-admins.md)   
  [Gerenciar Azure Analysis Services com o PowerShell](analysis-services-powershell.md)  
  [Referência de TMSL (linguagem de script de modelo de tabela)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

