---
title: Pseudónimo snS
description: As suas aplicações podem ligar-se a um pseudónimo para o nome do seu servidor de base de dados Azure SQL. Entretanto, pode alterar a Base de Dados SQL a que o pseudónimo aponta a qualquer momento, para facilitar os testes e assim por diante.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820618"
---
# <a name="dns-alias-for-azure-sql-database"></a>Pseudónimo dNS para Base de Dados Azure SQL

A Base de Dados Azure SQL tem um servidor do Sistema de Nomede Domínio (DNS). As APIs PowerShell e REST aceitam [chamadas para criar e gerir pseudónimos dNS](#anchor-powershell-code-62x) para o seu nome de servidor de base de dados SQL.

Um *pseudónimo DNS* pode ser usado no lugar do nome do servidor de base de dados Azure SQL. Os programas de clientes podem usar o pseudónimo nas suas cordas de ligação. O pseudónimo DNS fornece uma camada de tradução que pode redirecionar os seus programas de clientes para diferentes servidores. Esta camada poupa-lhe as dificuldades de ter de encontrar e editar todos os clientes e as suas cordas de ligação.

As utilizações comuns para um pseudónimo dNS incluem os seguintes casos:

- Crie um nome fácil de lembrar para um Servidor Azure SQL.
- Durante o desenvolvimento inicial, o seu pseudónimo pode consultar um servidor de base de dados SQL de teste. Quando a aplicação entrar em direto, pode modificar o pseudónimo para se referir ao servidor de produção. A transição do teste para a produção não requer qualquer modificação nas configurações de vários clientes que se ligam ao servidor de base de dados.
- Suponha que a única base de dados da sua aplicação seja transferida para outro servidor de Base de Dados SQL. Aqui pode modificar o pseudónimo sem ter de modificar as configurações de vários clientes.
- Durante uma paragem regional, utilize geo-restauro para recuperar a sua base de dados num servidor e região diferentes. Pode modificar o seu pseudónimo existente para apontar para o novo servidor para que a aplicação do cliente existente possa voltar a ligar-se ao mesmo. 

## <a name="domain-name-system-dns-of-the-internet"></a>Sistema de Nome de Domínio (DNS) da Internet

A Internet depende do DNS. O DNS traduz os seus nomes amigáveis no nome do seu servidor de base de dados Azure SQL.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um pseudónimo dNS

Suponha que precisa de mudar o seu sistema para um novo servidor de base de dados Azure SQL. No passado, precisavas de encontrar e atualizar todas as cordas de ligação em todos os programas de clientes. Mas agora, se as cordas de ligação usam um pseudónimo DNS, apenas uma propriedade de pseudónimo deve ser atualizada.

A funcionalidade de pseudónimo dNS da Base de Dados Azure SQL pode ajudar nos seguintes cenários:

### <a name="test-to-production"></a>Teste à produção

Quando começar a desenvolver os programas de clientes, faça-os usar um pseudónimo DNS nas suas cordas de ligação. As propriedades do pseudónimo apontam para uma versão de teste do seu servidor de base de dados Azure SQL.

Mais tarde, quando o novo sistema entrar em funcionação, pode atualizar as propriedades do pseudónimo para apontar para o servidor de base de dados SQL de produção. Não é necessária qualquer alteração nos programas de clientes.

### <a name="cross-region-support"></a>Apoio transfronteiriço

Uma recuperação de desastres pode transferir o seu servidor de Base de Dados SQL para uma região geográfica diferente. Para um sistema que utilizava um pseudónimo DNS, a necessidade de encontrar e atualizar todas as cordas de ligação para todos os clientes pode ser evitada. Em vez disso, pode atualizar um pseudónimo para se referir ao novo servidor de base de dados SQL que agora acolhe a sua base de dados.

## <a name="properties-of-a-dns-alias"></a>Propriedades de um pseudónimo DNS

As seguintes propriedades aplicam-se a cada pseudónimo DNS para o seu servidor de base de dados SQL:

- *Nome único:* Cada nome pseudónimo que cria é único em todos os servidores de Base de Dados Azure SQL, tal como os nomes dos servidores são.
- *O servidor é necessário:* Um pseudónimo DNS não pode ser criado a menos que refira exatamente um servidor, e o servidor já deve existir. Um pseudónimo atualizado deve sempre fazer referência a um servidor existente.
  - Quando deixa cair um servidor de base de dados SQL, o sistema Azure também deixa cair todos os pseudónimos de DNS que se referem ao servidor.
- *Não vinculado a nenhuma região:* Os pseudónimos do DNS não estão ligados a uma região. Quaisquer pseudónimos de DNS podem ser atualizados para se referir a um servidor de base de dados Azure SQL que reside em qualquer região geográfica.
  - No entanto, ao atualizar um pseudónimo para se referir a outro servidor, ambos os servidores devem existir na mesma *subscrição*do Azure .
- *Permissões:* Para gerir um pseudónimo DNS, o utilizador deve ter permissões do *Colaborador do Servidor,* ou superiores. Para mais informações, consulte [Iniciar o Controlo de Acesso baseado em Funções no portal Azure](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Gerencie os seus pseudónimos dNS

Ambas as APIs rest e powerShell cmdlets estão disponíveis para permitir que você gere programáticamente os seus pseudónimos DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST APIs para gerir os seus pseudónimos DNS

A documentação para as APIs rest está disponível perto da seguinte localização web:

- [Base de Dados Azure SQL REST API](https://docs.microsoft.com/rest/api/sql/)

Além disso, as APIs restantes podem ser vistas no GitHub em:

- [Servidor de base de dados Azure SQL, pseudónimo dNS REST APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerir os seus pseudónimos DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Os cmdlets PowerShell estão disponíveis que liguem para as APIs REST.

Um exemplo de código de cmdlets PowerShell sendo usados para gerir pseudónimos DNS é documentado em:

- [PowerShell para DNS Alias para Base de Dados SQL Azure](dns-alias-powershell.md)

Os cmdlets utilizados no exemplo do código são os seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo pseudónimo DNS no sistema de serviço de base de dados Azure SQL. O pseudónimo refere-se ao servidor de base de dados Azure SQL 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtenha e enumere todos os pseudónimos do DNS que são atribuídos ao servidor SQL DB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor DB SQL 2.
- [Remover-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova os pseudónimos DNS do servidor Db SQL 2, utilizando o nome do pseudónimo.

## <a name="limitations-during-preview"></a>Limitações durante a pré-visualização

Atualmente, um pseudónimo dNS tem as seguintes limitações:

- *Atraso de até 2 minutos:* Leva até 2 minutos para que um pseudónimo DNS seja atualizado ou removido.
  - Independentemente de qualquer breve atraso, o pseudónimo deixa imediatamente de remeter as ligações do cliente para o servidor legado.
- A procura do *DNS:* Por enquanto, a única forma autoritária de verificar a que servidor um dado pseudónimo dNS se refere é executando uma procura de [DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _A auditoria de tabelanão é suportada:_ Não é possível utilizar um pseudónimo DNS num servidor de base de dados Azure SQL que tenha auditoria de *tabela* ativada numa base de dados.
  - A auditoria de mesa é depreciada.
  - Recomendamos que se mude para [a Blob Auditing.](sql-database-auditing.md)

## <a name="related-resources"></a>Recursos relacionados

- Visão geral da continuidade dos negócios com a Base de [Dados Azure SQL](sql-database-business-continuity.md), incluindo a recuperação de desastres.

## <a name="next-steps"></a>Passos seguintes

- [PowerShell para DNS Alias para Base de Dados SQL Azure](dns-alias-powershell.md)
