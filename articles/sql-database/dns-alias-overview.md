---
title: Alias de DNS para o banco de dados SQL do Azure | Microsoft Docs
description: Seus aplicativos podem se conectar a um alias para o nome do seu servidor de banco de dados SQL do Azure. Enquanto isso, você pode alterar o banco de dados SQL que o alias aponta para qualquer momento, para facilitar o teste e assim por diante.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, ayolubek, jrasnick
ms.date: 06/26/2019
ms.openlocfilehash: 3d0a4b5890ed5758f4045459815fb4ebbffe75c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550667"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias de DNS para o banco de dados SQL do Azure

O banco de dados SQL do Azure tem um servidor DNS (sistema de nomes de domínio). As APIs REST e do PowerShell aceitam [chamadas para criar e gerenciar aliases DNS](#anchor-powershell-code-62x) para o nome do servidor do banco de dados SQL.

Um *alias DNS* pode ser usado no lugar do nome do servidor do banco de dados SQL do Azure. Os programas cliente podem usar o alias em suas cadeias de conexão. O alias DNS fornece uma camada de conversão que pode redirecionar seus programas cliente para servidores diferentes. Essa camada poupa as dificuldades de ter que localizar e editar todos os clientes e suas cadeias de conexão.

Os usos comuns para um alias DNS incluem os seguintes casos:

- Crie um nome fácil de lembrar para uma SQL Server do Azure.
- Durante o desenvolvimento inicial, seu alias pode se referir a um servidor de banco de dados SQL de teste. Quando o aplicativo é colocado em tempo real, você pode modificar o alias para fazer referência ao servidor de produção. A transição do teste para a produção não requer nenhuma modificação nas configurações de vários clientes que se conectam ao servidor de banco de dados.
- Suponha que o único banco de dados em seu aplicativo seja movido para outro servidor de banco de dados SQL. Aqui você pode modificar o alias sem precisar modificar as configurações de vários clientes.
- Durante uma interrupção regional, você usa a restauração geográfica para recuperar o banco de dados em um servidor e região diferentes. Você pode modificar seu alias existente para apontar para o novo servidor para que o aplicativo cliente existente possa se conectar novamente a ele. 

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (sistema de nomes de domínio) da Internet

A Internet depende do DNS. O DNS traduz seus nomes amigáveis para o nome do seu servidor de banco de dados SQL do Azure.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um alias DNS

Suponha que você precise mudar seu sistema para um novo servidor de banco de dados SQL do Azure. No passado, você precisava encontrar e atualizar cada cadeia de conexão em cada programa cliente. Mas agora, se as cadeias de conexão usarem um alias DNS, somente uma propriedade alias deverá ser atualizada.

O recurso alias DNS do banco de dados SQL do Azure pode ajudar nos seguintes cenários:

### <a name="test-to-production"></a>Teste para produção

Quando você começar a desenvolver os programas cliente, faça com que eles usem um alias DNS em suas cadeias de conexão. Você faz as propriedades do alias apontarem para uma versão de teste do seu servidor de banco de dados SQL do Azure.

Posteriormente, quando o novo sistema ficar ativo em produção, você poderá atualizar as propriedades do alias para apontar para o servidor de banco de dados SQL de produção. Nenhuma alteração nos programas cliente é necessária.

### <a name="cross-region-support"></a>Suporte entre regiões

Uma recuperação de desastre pode mudar o servidor do banco de dados SQL para uma região geográfica diferente. Para um sistema que estava usando um alias DNS, a necessidade de encontrar e atualizar todas as cadeias de conexão para todos os clientes pode ser evitada. Em vez disso, você pode atualizar um alias para se referir ao novo servidor de banco de dados SQL que agora hospeda seu banco de dados.

## <a name="properties-of-a-dns-alias"></a>Propriedades de um alias DNS

As propriedades a seguir se aplicam a cada alias DNS para o servidor do banco de dados SQL:

- *Nome exclusivo:* Cada nome de alias que você cria é exclusivo em todos os servidores de banco de dados SQL do Azure, assim como os nomes de servidor.
- *O servidor é necessário:* Um alias DNS não pode ser criado, a menos que ele referencie exatamente um servidor, e o servidor já deve existir. Um alias atualizado sempre deve fazer referência a exatamente um servidor existente.
  - Quando você remove um servidor de banco de dados SQL, o sistema do Azure também descarta todos os aliases DNS que se referem ao servidor.
- *Não associado a nenhuma região:* Os aliases DNS não estão associados a uma região. Todos os aliases de DNS podem ser atualizados para se referir a um servidor de banco de dados SQL do Azure que reside em qualquer região geográfica.
  - No entanto, ao atualizar um alias para fazer referência a outro servidor, ambos os servidores devem existir na mesma *assinatura*do Azure.
- *Permissões* Para gerenciar um alias DNS, o usuário deve ter permissões de *colaborador do servidor* ou superior. Para obter mais informações, consulte Introdução [ao controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Gerenciar seus aliases de DNS

As APIs REST e os cmdlets do PowerShell estão disponíveis para permitir que você gerencie programaticamente seus aliases DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>APIs REST para gerenciar seus aliases de DNS

A documentação para as APIs REST está disponível perto do seguinte local da Web:

- [API REST do banco de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

Além disso, as APIs REST podem ser vistas no GitHub em:

- [Servidor de banco de dados SQL do Azure, APIs REST de alias DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerenciar seus aliases de DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Há cmdlets do PowerShell disponíveis que chamam as APIs REST.

Um exemplo de código dos cmdlets do PowerShell que está sendo usado para gerenciar aliases de DNS está documentado em:

- [PowerShell para alias DNS para o banco de dados SQL do Azure](dns-alias-powershell.md)

Os cmdlets usados no exemplo de código são os seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias refere-se ao servidor de banco de dados SQL do Azure 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtenha e liste todos os aliases DNS atribuídos ao servidor de banco de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 ao servidor de banco de BD SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o alias DNS do servidor de banco de BD SQL 2, usando o nome do alias.

## <a name="limitations-during-preview"></a>Limitações durante a visualização

Atualmente, um alias DNS tem as seguintes limitações:

- *Atraso de até 2 minutos:* Leva até 2 minutos para que um alias de DNS seja atualizado ou removido.
  - Independentemente de qualquer breve atraso, o alias imediatamente interrompe as conexões de cliente para o servidor herdado.
- *Pesquisa de DNS:* Por enquanto, a única maneira autoritativa de verificar a qual servidor um determinado alias DNS se refere é executando uma [pesquisa de DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Não há suporte para a auditoria de tabela:_ Você não pode usar um alias DNS em um servidor de banco de dados SQL do Azure que tenha a *auditoria de tabela* habilitada em um banco de dados.
  - A auditoria de tabela foi preterida.
  - Recomendamos que você mova para a [auditoria de blob](sql-database-auditing.md).

## <a name="related-resources"></a>Recursos relacionados

- [Visão geral da continuidade dos negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md), incluindo recuperação de desastre.

## <a name="next-steps"></a>Passos Seguintes

- [PowerShell para alias DNS para o banco de dados SQL do Azure](dns-alias-powershell.md)
