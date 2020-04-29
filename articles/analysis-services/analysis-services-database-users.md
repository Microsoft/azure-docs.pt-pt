---
title: Gerir funções de base de dados e utilizadores em Serviços de Análise Azure [ Microsoft Docs
description: Saiba como gerir funções de base de dados e utilizadores num servidor de Serviços de Análise em Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81454208"
---
# <a name="manage-database-roles-and-users"></a>Gerir funções de base de dados e utilizadores

Ao nível da base de dados do modelo, todos os utilizadores devem pertencer a um papel. As funções definem os utilizadores com permissões específicas para a base de dados do modelo. Qualquer utilizador ou grupo de segurança adicionado a uma função deve ter uma conta num inquilino DaD Azure na mesma subscrição que o servidor. 

A forma como define funções é diferente dependendo da ferramenta que utiliza, mas o efeito é o mesmo.

As permissões de funções incluem:
*  **Administrador** - Os utilizadores têm permissões completas para a base de dados. As funções de base de dados com permissões do Administrador são diferentes dos administradores do servidor.
*  **Processo** - Os utilizadores podem ligar e executar operações de processo na base de dados e analisar dados de base de dados de modelos.
*  **Ler** - Os utilizadores podem usar uma aplicação do cliente para conectar e analisar dados de base de dados de modelos.

Ao criar um projeto de modelo tabular, cria funções e adiciona utilizadores ou grupos a essas funções utilizando o Role Manager em Visual Studio com projetos de Serviços de Análise. Quando implantado num servidor, utilize o SQL Server Management Studio (SSMS), os serviços de [análise PowerShell cmdlets,](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)ou o Idioma de [Scripts de Modelo Tabular](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) para adicionar ou remover funções e membros do utilizador.

Ao adicionar um grupo `obj:groupid@tenantid`de **segurança,** utilize .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Para adicionar ou gerir papéis e utilizadores no Estúdio Visual  
  
1.  No **Tabular Model Explorer,** clique direito **em Papéis**.  
  
2.  No **Role Manager,** clique em **New**.  
  
3.  Escreva um nome para o papel.  
  
     Por predefinição, o nome da função padrão é incrementalmente numerado para cada nova função. Recomenda-se que escreva um nome que identifique claramente o tipo de membro, por exemplo, Gestores de Finanças ou Especialistas em Recursos Humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhum**|Os membros não podem ler ou modificar o esquema do modelo e não podem consultar os dados.|  
    |**Leitura**|Os membros podem consultar dados (com base em filtros de linha) mas não podem modificar o esquema do modelo.|  
    |**Ler e Processar**|Os membros podem consultar dados (com base em filtros ao nível da linha) e executar o Processo e o Processo Todas as operações, mas não podem modificar o esquema do modelo.|  
    |**Processo**|Os membros podem executar processando e processar todas as operações. Não é possível ler ou modificar o esquema do modelo e não pode consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema do modelo e consultar todos os dados.|   
  
5.  Se o papel que está a criar tiver permissão de Leitura ou Leitura e Processo, pode adicionar filtros de linha utilizando uma fórmula DAX. Clique no separador **Filtros de Linha,** depois selecione uma tabela, clique no campo **de filtro DAX** e, em seguida, digite uma fórmula DAX.
  
6.  Clique em **Membros** > **Adicionar Externo**.  
  
8.  Em **Adicionar Membro Externo,** insira utilizadores ou grupos no seu inquilino Azure AD por endereço de e-mail. Depois de clicar em OK e fechar role Manager, as funções e membros de papéis aparecem no Tabular Model Explorer. 
 
     ![Papéis e utilizadores no Tabular Model Explorer](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implemente para o seu servidor De serviços de análise Azure.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerir funções e utilizadores em SSMS

Para adicionar funções e utilizadores a uma base de dados de modelos implementado, deve estar ligado ao servidor como administrador do Servidor ou já numa função de base de dados com permissões de administrador.

1. Em Object Exporer, clique direito **funções** > **novo papel**.

2. In **Create Role,** insira um nome e descrição de papel.

3. Selecione uma permissão.

   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controlo total (Administrador)**|Os membros podem modificar o esquema do modelo, processar e consultar todos os dados.| 
   |**Process database**|Os membros podem executar processando e processar todas as operações. Não pode modificar o esquema do modelo e não pode consultar dados.|  
   |**Leitura**|Os membros podem consultar dados (com base em filtros de linha) mas não podem modificar o esquema do modelo.|  
  
4. Clique em **Adesão**e, em seguida, insira um utilizador ou grupo no seu inquilino Azure AD por endereço de e-mail.

     ![Adicionar utilizador](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se o papel que está a criar tiver permissão de leitura, pode adicionar filtros de linha utilizando uma fórmula DAX. Clique em **Filtros de Linha,** selecione uma tabela e, em seguida, digite uma fórmula DAX no campo **filtro DAX.** 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e utilizadores utilizando um script TMSL

Pode executar um script TMSL na janela XMLA no SSMS ou utilizando o PowerShell. Utilize o comando [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) e o objeto [Funções.](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl)

**Script TMSL da amostra**

Nesta amostra, um utilizador externo B2B e um grupo são adicionados ao papel de Analista com permissões de Leitura para a base de dados SalesBI. Tanto o utilizador externo como o grupo devem estar no mesmo inquilino Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e utilizadores utilizando o PowerShell

O módulo [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) fornece cmdlets de gestão de base de dados específicos de tarefas e o cmdlet invoca-ASCmd de propósito geral que aceita uma consulta ou script de script de script de modelo tabular (TMSL). Os seguintes cmdlets são utilizados para gerir funções de base de dados e utilizadores.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro adum](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de base de dados.| 
|[Remover-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remova um membro de uma função de base de dados.|   
|[Invocar-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Os filtros de linha definem quais as linhas de uma mesa que podem ser consultadas por membros de um papel particular. Os filtros de linha são definidos para cada tabela num modelo utilizando fórmulas DAX.  
  
Os filtros de linha só podem ser definidos para funções com permissões de Leitura e Leitura e Processo. Por predefinição, se um filtro de linha não for definido para uma determinada tabela, os membros podem consultar todas as linhas da tabela, a menos que a filtragem cruzada se aplique a partir de outra tabela.
  
 Os filtros de linha requerem uma fórmula DAX, que deve avaliar para um valor VERDADEIRO/FALSO, para definir as linhas que podem ser consultadas por membros desse papel específico. As filas não incluídas na fórmula DAX não podem ser consultadas. Por exemplo, a tabela Clientes com a seguinte expressão de filtros de linha, *=Clientes [País] = "EUA",* os membros da função De venda só podem ver clientes nos EUA.  
  
Os filtros de remo aplicam-se às linhas especificadas e às linhas relacionadas. Quando uma tabela tem múltiplas relações, os filtros aplicam segurança à relação que está ativa. Os filtros de linha são interseccionados com outros filetes de linha definidos para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|=Região[País]="EUA"|  
|ProductCategory|=Categoria de Produto[Nome]="Bicicletas"|  
|Transações|=Transações[Ano]=2016|  
  
 O efeito líquido é que os membros podem consultar filas de dados onde o cliente está nos EUA, a categoria do produto é bicicletas, e o ano é 2016. Os utilizadores não podem consultar transações fora dos EUA, transações que não são bicicletas, ou transações não em 2016, a menos que sejam membros de outra função que conceda essas permissões.
  
 Pode utilizar o filtro, *=FALSE()* para negar o acesso a todas as linhas para uma mesa inteira.

## <a name="next-steps"></a>Passos seguintes

  [Gerir administradores de servidores](analysis-services-server-admins.md)   
  [Gerir o Azure Analysis Services com o PowerShell](analysis-services-powershell.md)  
  [Referência tabular modelo scripting (TMSL)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

