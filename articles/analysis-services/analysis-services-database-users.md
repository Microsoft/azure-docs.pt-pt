---
title: Gerir funções de base de dados e utilizadores em Azure Analysis Services | Microsoft Docs
description: Saiba como gerir as funções de base de dados e os utilizadores num servidor de Serviços de Análise em Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 31910e92ba4d5cbb1f133eaff6880fafb809b772
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054098"
---
# <a name="manage-database-roles-and-users"></a>Gerir funções de base de dados e utilizadores

Ao nível da base de dados modelo, todos os utilizadores devem pertencer a uma função. As funções definem os utilizadores com permissões específicas para a base de dados do modelo. Qualquer utilizador ou grupo de segurança adicionado a uma função deve ter uma conta num inquilino AZure AD na mesma subscrição que o servidor. 

A forma como defines as funções é diferente dependendo da ferramenta que usas, mas o efeito é o mesmo.

As permissões de funções incluem:
*  **Administrador** - Os utilizadores têm permissões completas para a base de dados. As funções de base de dados com permissões de administrador são diferentes dos administradores do servidor.
*  **Processo** - Os utilizadores podem ligar e executar operações de processo na base de dados e analisar dados de bases de dados de modelos.
*  **Ler** - Os utilizadores podem utilizar uma aplicação do cliente para ligar e analisar dados de bases de dados de modelos.

Ao criar um projeto de modelo tabular, cria funções e adiciona utilizadores ou grupos a essas funções utilizando role manager em projetos de Estúdio Visual com Serviços de Análise. Quando implementado num servidor, utilize o SQL Server Management Studio (SSMS), [os serviços de análise PowerShell cmdlets](/analysis-services/powershell/analysis-services-powershell-reference)ou [o Language scripting do modelo tabular](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) para adicionar ou remover funções e membros do utilizador.

Ao adicionar um **grupo de segurança,** utilize `obj:groupid@tenantid` .

Ao adicionar uma utilização **principal de serviço** `app:appid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Adicionar ou gerir papéis e utilizadores em Visual Studio  
  
1.  No **Tabular Model Explorer,** **funções** de clique à direita .  
  
2.  Em **Role Manager,** clique em **New**.  
  
3.  Escreva um nome para o papel.  
  
     Por padrão, o nome da função predefinida é incrementalmente numerado para cada nova função. Recomenda-se que escreva um nome que identifique claramente o tipo de membro, por exemplo, Gestores de Finanças ou Especialistas em Recursos Humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Description|  
    |----------------|-----------------|  
    |**Nenhuma**|Os membros não podem ler ou modificar o esquema do modelo e não podem consultar dados.|  
    |**Ler**|Os membros podem consultar dados (com base em filtros de linha) mas não podem modificar o esquema do modelo.|  
    |**Ler e Processar**|Os membros podem consultar dados (com base em filtros de nível de linha) e executar processo e processo Todas as operações, mas não podem modificar o esquema do modelo.|  
    |**Processo**|Os membros podem executar Processo e Processo Todas as operações. Não é possível ler ou modificar o esquema do modelo e não pode consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema do modelo e consultar todos os dados.|   
  
5.  Se o papel que está a criar tiver permissão de Ler ou Ler e Processar, pode adicionar filtros de linha utilizando uma fórmula DAX. Clique no **separador Filtros de Linha** e, em seguida, selecione uma tabela, depois clique no campo **filtro DAX** e, em seguida, digite uma fórmula DAX.
  
6.  Clique **em Membros**  >  **Adicionar Externos**.  
  
8.  No **Add External Member,** insira os utilizadores ou grupos no seu inquilino Azure AD por endereço de e-mail. Depois de clicar em OK e fechar Role Manager, os papéis e membros de papel aparecem no Tabular Model Explorer. 
 
     ![Funções e utilizadores no Tabular Model Explorer](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implemente o seu servidor Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Adicionar ou gerir funções e utilizadores em SSMS

Para adicionar funções e utilizadores a uma base de dados de modelos implementada, tem de estar ligado ao servidor como administrador do Servidor ou já numa função de base de dados com permissões de administrador.

1. In Object Exporer, **papel** de clique direito  >  **Novo Papel**.

2. In **Create Role**, insira um nome e descrição de funções.

3. Selecione uma permissão.

   |Permissão|Description|  
   |----------------|-----------------|  
   |**Controlo total (Administrador)**|Os membros podem modificar o esquema do modelo, processar e consultar todos os dados.| 
   |**Process database**|Os membros podem executar Processo e Processo Todas as operações. Não é possível modificar o esquema do modelo e não pode consultar dados.|  
   |**Ler**|Os membros podem consultar dados (com base em filtros de linha) mas não podem modificar o esquema do modelo.|  
  
4. Clique **em Adesão** e, em seguida, insira um utilizador ou grupo no seu inquilino Azure AD por endereço de e-mail.

     ![Adicionar utilizador](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se o papel que está a criar tiver permissão de leitura, pode adicionar filtros de linha utilizando uma fórmula DAX. Clique **em Filtros de Linha,** selecione uma tabela e, em seguida, digite uma fórmula DAX no campo **filtro DAX.** 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e utilizadores utilizando um script TMSL

Pode executar um script TMSL na janela XMLA em SSMS ou utilizando o PowerShell. Utilize o comando [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) e o objeto [Roles.](/analysis-services/tmsl/roles-object-tmsl)

**Guião TMSL de amostra**

Nesta amostra, um utilizador externo B2B e um grupo são adicionados à função Analyst com permissões de Leitura para a base de dados SalesBI. Tanto o utilizador externo como o grupo devem estar no mesmo inquilino Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Adicionar funções e utilizadores utilizando o PowerShell

O módulo [SqlServer](/analysis-services/powershell/analysis-services-powershell-reference) fornece cmdlets de gestão de bases de dados específicos de tarefas e o cmdlet de Invoke-ASCmd de uso geral que aceita uma consulta ou script de Script de Script de Modelo Tabular (TMSL). Os cmdlets seguintes são utilizados para gerir funções de base de dados e utilizadores.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro do Add-Role](/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de base de dados.| 
|[Membro de remoção de rolemember](/powershell/module/sqlserver/remove-rolemember)|Remova um membro de uma função de base de dados.|   
|[Invocar-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Os filtros de linha definem quais linhas numa mesa podem ser consultadas por membros de um determinado papel. Os filtros de linha são definidos para cada mesa num modelo utilizando fórmulas DAX.  
  
Os filtros de linha só podem ser definidos para funções com permissões de Leitura e Leitura e Processo. Por predefinição, se um filtro de linha não for definido para uma tabela específica, os membros podem consultar todas as linhas da tabela, a menos que a filtragem cruzada se aplique a partir de outra tabela.
  
 Os filtros de linha requerem uma fórmula DAX, que deve avaliar a um valor VERDADEIRO/FALSO, para definir as linhas que podem ser consultadas por membros desse papel em particular. As linhas não incluídas na fórmula DAX não podem ser questionadas. Por exemplo, a tabela Clientes com a expressão de filtros de linha seguinte, *=Clientes [País] = "EUA",* os membros da função Vendas só podem ver clientes nos EUA.  
  
Os filtros de linha aplicam-se às linhas especificadas e linhas relacionadas. Quando uma tabela tem múltiplas relações, os filtros aplicam segurança à relação que está ativa. Os filtros de linha são interseccionados com outros filetes de linha definidos para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Region|=Região[País]="EUA"|  
|ProductCategory|=ProductCategory[Nome]="Bicicletas"|  
|Transações|=Transações[Ano]=2016|  
  
 O efeito líquido são os membros podem consultar filas de dados onde o cliente está nos EUA, a categoria de produto é bicicletas, e o ano é 2016. Os utilizadores não podem consultar transações fora dos EUA, transações que não sejam bicicletas, ou transações que não sejam em 2016, a menos que sejam membros de outra função que conceda estas permissões.
  
 Pode utilizar o filtro, *=FALSE()*, para negar o acesso a todas as linhas para uma mesa inteira.

## <a name="next-steps"></a>Passos seguintes

  [Gerir administradores de servidores](analysis-services-server-admins.md)   
  [Gerir o Azure Analysis Services com o PowerShell](analysis-services-powershell.md)  
  [Referência da linguagem de script do modelo tabular (TMSL)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)
