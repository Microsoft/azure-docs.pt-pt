---
title: Pseudónimo do DNS
description: As suas aplicações podem ligar-se a um pseudónimo para o nome do servidor da Base de Dados Azure SQL. Entretanto, pode alterar a Base de Dados SQL que o pseudónimo aponta para qualquer momento, para facilitar os testes e assim por diante.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 4be3f8c6cd416743c2d1118cf2de01073c3022ff
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790496"
---
# <a name="dns-alias-for-azure-sql-database"></a>Pseudónimo de DNS para Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A base de dados Azure SQL tem um servidor do Sistema de Nome de Domínio (DNS). PowerShell e REST APIs aceitam [chamadas para criar e gerir pseudónimos DNS](#anchor-powershell-code-62x) para o seu nome de servidor [SQL lógico.](logical-servers.md)

Um *pseudónimo DNS* pode ser usado no lugar do nome do servidor. Os programas de clientes podem usar o pseudónimo nas suas cordas de ligação. O pseudónimo DNS fornece uma camada de tradução que pode redirecionar os programas do seu cliente para diferentes servidores. Esta camada poupa-lhe as dificuldades de ter de encontrar e editar todos os clientes e as suas cordas de conexão.

As utilizações comuns para um pseudónimo DNS incluem os seguintes casos:

- Crie um nome fácil de lembrar para um servidor.
- Durante o desenvolvimento inicial, o seu pseudónimo pode consultar um servidor de teste. Quando a aplicação entrar em direto, pode modificar o pseudónimo para se referir ao servidor de produção. A transição do teste para a produção não requer qualquer modificação nas configurações de vários clientes que se ligam ao servidor.
- Suponha que a única base de dados da sua aplicação seja transferida para outro servidor. Pode modificar o pseudónimo sem ter de modificar as configurações de vários clientes.
- Durante uma paragem regional, utiliza-se o geo-restauro para recuperar a sua base de dados num servidor e região diferentes. Pode modificar o seu pseudónimo existente para apontar para o novo servidor para que a aplicação do cliente existente possa voltar a ligar-se ao mesmo.

## <a name="domain-name-system-dns-of-the-internet"></a>Sistema de Nome de Domínio (DNS) da Internet

A Internet depende do DNS. O DNS traduz os seus nomes amigáveis no nome do seu servidor.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um pseudónimo dns

Suponha que precisa mudar o seu sistema para um novo servidor. No passado, precisava de encontrar e atualizar todas as cadeias de ligação em cada programa de clientes. Mas agora, se as cordas de ligação usarem um pseudónimo DNS, apenas uma propriedade de pseudónimo deve ser atualizada.

A funcionalidade de pseudónimo DNS da Base de Dados Azure SQL pode ajudar nos seguintes cenários:

### <a name="test-to-production"></a>Teste à produção

Quando começar a desenvolver os programas de clientes, faça-os usar um pseudónimo DNS nas suas cordas de ligação. As propriedades do pseudónimo apontam para uma versão de teste do seu servidor.

Mais tarde, quando o novo sistema entrar em produção, é possível atualizar as propriedades do pseudónimo para apontar para o servidor de produção. Não é necessária qualquer alteração aos programas de clientes.

### <a name="cross-region-support"></a>Apoio inter-região

Uma recuperação de desastres pode transferir o seu servidor para uma região geográfica diferente. Para um sistema que estava a usar um pseudónimo DNS, a necessidade de encontrar e atualizar todas as cadeias de ligação para todos os clientes pode ser evitada. Em vez disso, pode atualizar um pseudónimo para se referir ao novo servidor que agora acolhe a sua Base de Dados Azure SQL.

## <a name="properties-of-a-dns-alias"></a>Propriedades de um pseudónimo DNS

As seguintes propriedades aplicam-se a cada pseudónimo DNS para o seu servidor:

- *Nome único:* Cada nome pseudónimo que cria é único em todos os servidores, tal como os nomes do servidor são.
- *O servidor é necessário:* Um pseudónimo DNS não pode ser criado a menos que refira exatamente um servidor, e o servidor já deve existir. Um pseudónimo atualizado deve sempre referir exatamente um servidor existente.
  - Quando deixa cair um servidor, o sistema Azure também deixa cair todos os pseudónimos DNS que se referem ao servidor.
- *Não ligado a nenhuma região:* Os pseudónimos do DNS não estão ligados a uma região. Qualquer pseudónimo dns pode ser atualizado para se referir a um servidor que reside em qualquer região geográfica.
  - No entanto, ao atualizar um pseudónimo para se referir a outro servidor, ambos os servidores devem existir na mesma *subscrição* do Azure .
- *Permissões:* Para gerir um pseudónimo DNS, o utilizador deve ter permissões *de Colaborador do Servidor,* ou superiores. Para mais informações, consulte [Começar com Role-Based Controlo de Acesso no portal Azure.](../../role-based-access-control/overview.md)

## <a name="manage-your-dns-aliases"></a>Gerencie os seus pseudónimos DNS

Tanto as APIs rest como as cmdlets PowerShell estão disponíveis para que possa gerir programaticamente os seus pseudónimos DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST APIs para gerir os seus pseudónimos DNS

A documentação para as APIs REST está disponível perto da seguinte localização web:

- [Azure SQL Base de Dados REST API](/rest/api/sql/)

Além disso, as APIs REST podem ser vistas no GitHub em:

- [Azure SQL Database DNS pseudónimo REST APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerir os seus pseudónimos DNS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Estão disponíveis cmdlets PowerShell que ligam para as APIs REST.

Um exemplo de código de cmdlets PowerShell que estão a ser usados para gerir pseudónimos DNS é documentado em:

- [PowerShell para DNS Alias para Azure SQL Database](dns-alias-powershell-create.md)

Os cmdlets utilizados no exemplo do código são os seguintes:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo pseudónimo DNS no sistema de serviço de base de dados Azure SQL. O pseudónimo refere-se ao servidor 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtenha e enumere todos os pseudónimos DNS que são atribuídos ao servidor 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o pseudónimo DNS do servidor 2, utilizando o nome do pseudónimo.

## <a name="limitations-during-preview"></a>Limitações durante a pré-visualização

Atualmente, um pseudónimo DNS tem as seguintes limitações:

- *Atraso de até 2 minutos:* Leva até 2 minutos para que um pseudónimo DNS seja atualizado ou removido.
  - Independentemente de qualquer breve atraso, o pseudónimo para imediatamente de remeter as ligações do cliente para o servidor legado.
- *Procura de DNS:* Por enquanto, a única forma autoritária de verificar a que servidor se refere um dado pseudónimo dns é executando uma [procura de DNS](/windows-server/administration/windows-commands/nslookup).
- _A auditoria de tabela não é suportada:_ Não é possível utilizar um pseudónimo DNS num servidor que tenha *uma auditoria de tabela* ativada numa base de dados.
  - A auditoria de mesa é depreciada.
  - Recomendamos que se mude para a [Blob Auditing.](../../azure-sql/database/auditing-overview.md)

## <a name="related-resources"></a>Recursos relacionados

- [Visão geral da continuidade do negócio com a Base de Dados Azure SQL,](business-continuity-high-availability-disaster-recover-hadr-overview.md)incluindo recuperação de desastres.
- [Referência à API REST do Azure](/rest/api/azure/)
- [Servidor Dns Aliases API](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>Passos seguintes

- [PowerShell para DNS Alias para Azure SQL Database](dns-alias-powershell-create.md)