---
title: Gerir funções de base de dados e os utilizadores no Azure Analysis Services | Documentos da Microsoft
description: Saiba como gerir funções de base de dados e os usuários num servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d7ba922d66bf97dbd8173b0d5466a7e55a41f6b4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993179"
---
# <a name="manage-database-roles-and-users"></a>Gerir funções de base de dados e utilizadores

No nível de base de dados do modelo, todos os utilizadores têm de pertencer a uma função. Funções definem os utilizadores com permissões específicas para a base de dados do modelo. Qualquer utilizador ou grupo de segurança de adicionada a uma função, tem de ter uma conta no inquilino do Azure AD na mesma subscrição que o servidor. 

Como definir funções é diferente, dependendo da ferramenta utilizada, mas o efeito é o mesmo.

Permissões de função incluem:
*  **Administrador** -os utilizadores têm permissões completas para a base de dados. Funções de base de dados com permissões de administrador são diferentes dos administradores de servidor.
*  **Processo** -os utilizadores podem ligar a e executar operações de processo na base de dados e analisar dados de base de dados do modelo.
*  **Leitura** -os utilizadores podem utilizar uma aplicação de cliente para ligar a e analisar dados de base de dados do modelo.

Ao criar um projeto de modelo em tabela, pode criar funções e adicionar utilizadores ou grupos a essas funções utilizando o Gestor de funções no SSDT. Quando implantado num servidor, utilize o SSMS, [cmdlets do PowerShell do Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx), ou [linguagem de script de modelo em tabela](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) para adicionar ou remover funções e membros de utilizador.

> [!NOTE]
> Grupos de segurança tem de ter o `MailEnabled` definida como `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Para adicionar ou gerir funções e os utilizadores no SSDT  
  
1.  No SSDT > **Explorador de modelos tabulares**, clique com botão direito **funções**.  
  
2.  No **Gestor de Funções**, clique em **Nova**.  
  
3.  Escreva um nome para a função.  
  
     Por predefinição, o nome da função predefinida de forma incremental é numerado para cada função nova. Recomenda-se que digitar um nome que identifique claramente o tipo de membro, por exemplo, Finance Managers ou especialistas de recursos humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhum**|Os membros não é possível modificar o esquema do modelo e não é possível consultar dados.|  
    |**Leitura**|Membros podem consultar os dados (com base nos filtros de linha), mas não é possível modificar o esquema do modelo.|  
    |**Ler e processar**|Os membros podem consultar dados (com base nos filtros de nível de linha) e executadas operações de processo e processar tudo, mas não é possível modificar o esquema do modelo.|  
    |**Processo**|Os membros podem executar operações de processo e processar tudo. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema do modelo e consultar todos os dados.|   
  
5.  Se a função estiver a criar tem de leitura ou permissão de leitura e processo, pode adicionar filtros de linha, utilizando uma fórmula DAX. Clique nas **filtros de linha** separador, em seguida, selecione uma tabela, em seguida, clique nas **filtro DAX** campo e, em seguida, escreva uma fórmula DAX.
  
6.  Clique em **membros** > **adicionar externo**.  
  
8.  Na **Adicionar membro externo**, introduza utilizadores ou grupos no seu inquilino do Azure AD por endereço de e-mail. Depois de clicar em OK e feche o Gestor de funções, funções e membros da função é apresentada no Explorador de modelos em tabela. 
 
     ![Funções e os utilizadores no Explorador de modelos em tabela](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implemente no seu servidor Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerir funções e os utilizadores no SSMS

Para adicionar funções e os utilizadores a uma base de dados do modelo implementado, tem de estar ligado ao servidor como um administrador do servidor, ou já numa função de base de dados com permissões de administrador.

1. No objeto Exporer, com o botão direito **funções** > **nova função**.

2. Na **criar função**, introduza um nome de função e uma descrição.

3. Selecione uma permissão.

   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controlo total (administrador)**|Os membros podem modificar o esquema do modelo, processar e pode consultar todos os dados.| 
   |**Base de dados do processo**|Os membros podem executar operações de processo e processar tudo. Não é possível modificar o esquema do modelo e não é possível consultar dados.|  
   |**Leitura**|Membros podem consultar os dados (com base nos filtros de linha), mas não é possível modificar o esquema do modelo.|  
  
4. Clique em **associação**, em seguida, introduza um utilizador ou grupo no seu inquilino do Azure AD por endereço de e-mail.

     ![Adicionar utilizador](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se a função que está a criar tem permissão de leitura, pode adicionar filtros de linha, utilizando uma fórmula DAX. Clique em **filtros de linha**, selecione uma tabela e, em seguida, escreva uma fórmula DAX na **filtro DAX** campo. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e os utilizadores ao utilizar um script TMSL

Pode executar um script TMSL na janela de XMLA no SSMS ou ao utilizar o PowerShell. Utilize o [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) comando e o [funções](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objeto.

**Exemplo de script TMSL**

Neste exemplo, um utilizador externo de B2B e um grupo são adicionados à função de analista com permissões de leitura para a base de dados SalesBI. O utilizador externo e o grupo tem de estar no mesmo inquilino do Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e os utilizadores com o PowerShell

O [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) módulo fornece cmdlets de gestão de base de dados de tarefa específica e o cmdlet Invoke-Scmd para fins gerais que aceita uma consulta de linguagem de scripts de modelo em tabela (TMSL) ou um script. Os cmdlets seguintes são utilizados para gerir funções de base de dados e utilizadores.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Adicione um membro a uma função de base de dados.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remova um membro de uma função de base de dados.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Execute um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Filtros de linha definem que linhas numa tabela podem ser consultadas por membros de uma função específica. Filtros de linha são definidos para cada tabela num modelo ao utilizar as fórmulas DAX.  
  
Filtros de linha podem ser definidos apenas para funções com a leitura e de leitura e as permissões de processo. Por predefinição, se um filtro de linha não está definido para uma tabela específica, os membros podem consultar todas as linhas na tabela, a menos que a filtragem cruzada aplica-se de outra tabela.
  
 Filtros de linha necessitam de uma fórmula DAX, que deve ser avaliado como um valor de verdadeiro/falso, para definir as linhas que podem ser consultadas por membros da função em particular. Não não possível consultar as linhas não incluídas na fórmula DAX. Por exemplo, a tabela de clientes com a linha seguinte filtra expressão, *= clientes [País] = "USA"*, membros da função de vendas possam ver apenas os clientes nos EUA.  
  
Filtros de linha aplicam-se para as linhas especificadas e as linhas relacionadas. Quando uma tabela tiver várias relações, filtros aplicam-se em segurança para a relação que está ativa. Filtros de linha são cruzados com outros se filtram de linha definido para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|=Region[Country]="USA"|  
|ProductCategory|= ProductCategory [nome] = "Bicicletas"|  
|Transações|=Transactions[Year]=2016|  
  
 O efeito líquido é que os membros podem consultar linhas de dados em que o cliente estiver nos EUA, a categoria de produto é de bicicletas e é o ano 2016. Os utilizadores não podem consultar transações fora do EUA, transações que não são bicicletas ou transações não em 2016, a menos que um membro de outra função que concede essas permissões.
  
 Pode utilizar o filtro *=FALSE()*, para negar o acesso a todas as linhas de uma tabela inteira.

## <a name="next-steps"></a>Passos Seguintes

  [Gerir administradores de servidor](analysis-services-server-admins.md)   
  [Gerir o Azure Analysis Services com o PowerShell](analysis-services-powershell.md)  
  [Referência de linguagem (TMSL) de script de modelo em tabela](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

