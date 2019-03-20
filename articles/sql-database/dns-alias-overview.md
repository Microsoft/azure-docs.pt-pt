---
title: Alias de DNS para a base de dados SQL do Azure | Documentos da Microsoft
description: As aplicações podem ligar a um alias para o nome do seu servidor de base de dados do Azure SQL. Enquanto isso, é possível alterar a base de dados de SQL, o alias aponta para em qualquer altura, para facilitar os testes e assim por diante.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,ayolubek, jrasnick
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 9704acee2ca8bad7437ae22ff5041e2253916dce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880437"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias de DNS para a base de dados do Azure SQL

Base de dados SQL do Azure tem um servidor de sistema de nomes de domínio (DNS). PowerShell e REST APIs aceitam [chamadas para criar e gerir aliases de DNS](#anchor-powershell-code-62x) para o nome do servidor de base de dados SQL.

R *alias de DNS* pode ser utilizado em vez do nome do servidor de base de dados do Azure SQL. Programas de cliente podem utilizar o alias em suas cadeias de caracteres de conexão. O DNS alias fornece uma camada de tradução que pode redirecionar seus programas do cliente para servidores diferentes. Essa camada spares as dificuldades de ter de localizar e editar todos os clientes e suas cadeias de caracteres de conexão.

As utilizações comuns para um alias de DNS incluem os seguintes casos:

- Crie uma fácil de memorizar o nome para um servidor de SQL do Azure.
- Durante o desenvolvimento inicial, o seu alias pode se referir a um servidor de base de dados SQL do teste. Quando o aplicativo for executado, pode modificar o alias para fazer referência ao servidor de produção. A transição do teste para produção não requer qualquer modificação para as configurações de vários clientes que se ligam ao servidor de base de dados.
- Suponha que a base de dados única em seu aplicativo é movido para outro servidor de base de dados SQL. Aqui pode modificar o alias sem ter de modificar as configurações de vários clientes.

## <a name="domain-name-system-dns-of-the-internet"></a>Sistema de nomes de domínio (DNS) da Internet

Internet conta com o DNS. O DNS converte seus nomes amigáveis em nome do seu servidor de base de dados do Azure SQL.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um alias de DNS

Suponha que precisa de alternar o seu sistema para um novo servidor de base de dados do Azure SQL. No passado, é necessária para localizar e atualizar cada cadeia de ligação em cada programa de cliente. Mas agora, se as cadeias de ligação utilizam um alias de DNS, tem de ser atualizada apenas uma propriedade de alias.

O recurso de alias DNS do Azure SQL Database pode ajudar a nos seguintes cenários:

### <a name="test-to-production"></a>Testar na produção

Quando começa a desenvolver os programas de cliente, tem de tê-los a utilizar um alias de DNS nas suas cadeias de ligação. Verifique as propriedades do ponto de alias para uma versão de teste do seu servidor de base de dados do Azure SQL.

Mais tarde quando o novo sistema fica em direto na produção, é possível atualizar as propriedades de alias para apontar para o servidor de base de dados SQL de produção. Nenhuma alteração para os programas de cliente é necessária.

### <a name="cross-region-support"></a>Suporte em várias regiões

Uma recuperação após desastre pode mudar o seu servidor de base de dados SQL para uma região geográfica diferente. Para um sistema que estava a utilizar um alias de DNS, a necessidade de localizar e atualizar todas as cadeias de ligação para todos os clientes pode ser evitada. Em vez disso, pode atualizar um alias para fazer referência para o novo servidor de base de dados SQL que aloja agora a sua base de dados.

## <a name="properties-of-a-dns-alias"></a>Propriedades de um alias de DNS

Aplicam as seguintes propriedades para cada alias DNS para o seu servidor de base de dados SQL:

- *Nome exclusivo:* Cada nome de alias que cria é exclusivo em todos os servidores de base de dados do Azure SQL, tal como são os nomes dos servidores.
- *É necessário um servidor:* Um DNS alias não pode ser criado, a menos que ele faz referência exatamente um servidor e o servidor tem de existir. Um alias atualizado sempre tem de referenciar exatamente um servidor existente.
  - Quando remover um servidor de base de dados SQL, o sistema do Azure também remove todos os aliases DNS que se referem ao servidor.
- *Não vinculado a qualquer região:* Aliases de DNS não são vinculados a uma região. Qualquer aliases de DNS podem ser atualizados para fazer referência a um servidor de base de dados do Azure SQL que reside em qualquer região geográfica.
  - No entanto, ao atualizar um alias para fazer referência a outro servidor, ambos os servidores devem existir estar na mesma *subscrição*.
- *Permissões:* Para gerir um alias de DNS, o utilizador tem de ter *contribuinte Server* permissões, ou superior. Para obter mais informações, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Gerir os seus aliases DNS

Cmdlets do PowerShell e REST APIs estão disponíveis para que possa gerir programaticamente os seus aliases DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>APIs REST para gerir os seus aliases DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

A documentação para as APIs REST está disponível perto na seguinte localização para a web:

- [REST API do banco de dados do SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

Além disso, as APIs REST pode ser vistas no GitHub em:

- [Servidor de base de dados SQL do Azure, APIs de REST alias de DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerir os seus aliases DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Cmdlets do PowerShell estão disponíveis que chamar as APIs REST.

Um exemplo de código dos cmdlets do PowerShell que está a ser utilizado para gerir aliases de DNS está documentado em:

- [PowerShell para o Alias de DNS para a base de dados SQL do Azure](dns-alias-powershell.md)

Os cmdlets utilizados no exemplo de código são as seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo alias DNS no sistema de serviço de base de dados do Azure SQL. O alias de suporte de dados refere-se ao servidor de base de dados do Azure SQL 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obter e listar todos os aliases DNS que estão atribuídos ao servidor de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor que o alias está configurado para consultar, do servidor 1 para o servidor de BD SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o alias de DNS de BD do SQL server 2, com o nome de alias.

## <a name="limitations-during-preview"></a>Limitações durante a pré-visualização

Atualmente, um DNS alias tem as seguintes limitações:

- *Atraso de até 2 minutos:* Demora até dois minutos para um alias de DNS ser atualizado ou removido.
  - Independentemente de qualquer atraso breve, o alias interrompe imediatamente a referir-se ligações de cliente para o servidor legado.
- *Pesquisa de DNS:* Por enquanto, a forma apenas autoritativa para verificar qual servidor um determinado DNS alias se refere a está executando uma [pesquisa de DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- *[Não é suportada a auditoria de tabelas](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* Não é possível utilizar um alias de DNS num servidor de base de dados do Azure SQL que tenha *auditoria de tabelas* ativado numa base de dados.
  - Auditoria de tabelas é preterida.
  - Recomendamos que mova [auditoria de BLOBs](sql-database-auditing.md).

## <a name="related-resources"></a>Recursos relacionados

- [Descrição geral da continuidade comercial com a base de dados do Azure SQL](sql-database-business-continuity.md), incluindo a recuperação após desastre.

## <a name="next-steps"></a>Passos Seguintes

- [PowerShell para o Alias de DNS para a base de dados SQL do Azure](dns-alias-powershell.md)
