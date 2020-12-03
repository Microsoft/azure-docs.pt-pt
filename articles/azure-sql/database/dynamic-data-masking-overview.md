---
title: Máscara de dados dinâmicos
description: A mascaração dinâmica de dados limita a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados para a Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: f8d352dac98f953f7f6d8033d0d9e1376c4da313
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532249"
---
# <a name="dynamic-data-masking"></a>Máscara de dados dinâmicos 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics suportam máscara dinâmica de dados. A máscara de dados dinâmicos limita a exposição a dados confidenciais ao mascará-los para utilizadores sem privilégios. 

A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

Por exemplo, um representante de serviço num call center pode identificar chamadas por vários dígitos do seu número de cartão de crédito, mas esses itens de dados não devem ser totalmente expostos ao representante do serviço. Uma regra de mascaramento pode ser definida que mascara todos menos os últimos quatro dígitos de qualquer número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados adequada pode ser definida para proteger os dados pessoais, de modo que um desenvolvedor pode consultar ambientes de produção para fins de resolução de problemas sem violar as normas de conformidade.

## <a name="dynamic-data-masking-basics"></a>Básicos dinâmicos de mascaramento de dados

Configura uma política dinâmica de mascaramento de dados no portal Azure selecionando a lâmina **de mascaramento de dados dinâmicos** sob **segurança** no painel de configuração da base de dados SQL. Esta função não pode ser definida utilizando o portal para a sql Managed Instance (use PowerShell ou REST API). Para obter mais informações, consulte [a Máscara de Dados Dinâmicos.](/sql/relational-databases/security/dynamic-data-masking)

### <a name="dynamic-data-masking-permissions"></a>Permissões dinâmicas de mascaramento de dados

A mascaragem dinâmica de dados pode ser configurada pelas funções de administrador de base de dados Azure SQL, administrador de servidores ou [gestor de segurança SQL.](../../role-based-access-control/built-in-roles.md#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Política dinâmica de mascaramento de dados

* **Utilizadores SQL excluídos da máscara** - Um conjunto de utilizadores SQL ou identidades AD AZure que obtêm dados desmascarados nos resultados da consulta SQL. Os utilizadores com privilégios de administrador estão sempre excluídos da máscara e vêem os dados originais sem qualquer máscara.
* **Regras de mascaramento** - Um conjunto de regras que definem os campos designados para serem mascarados e a função de máscara que é usada. Os campos designados podem ser definidos usando um nome de esquema de base de dados, nome de tabela e nome da coluna.
* **Funções de mascaramento** - Um conjunto de métodos que controlam a exposição de dados para diferentes cenários.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Predefinição** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Utilize XXXX ou menos Xs se o tamanho do campo for inferior a 4 caracteres para tipos de dados de cordas (nchar, ntext, nvarchar).<br/>• Utilize um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numérico, pequeno, pequeno dinheiro, minúsculo, flutuante, real).<br/>• Utilize 01-01-1900 para tipos de dados de data/hora (data, data2, data, data, data, tempo de data, hora de data, hora certa).<br/>• Para a variante SQL, é utilizado o valor predefinido do tipo atual.<br/>• Para XML o documento \<masked/> é utilizado.<br/>• Utilize um valor vazio para tipos especiais de dados (tabela de relógios, hierarquia, GUID, binário, imagem, tipos espaciais varbinários). |
| **Cartão de crédito** |**Método de mascaramento, que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Método de mascaramento, que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo de corda constante sob a forma de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento, que gera um número aleatório** de acordo com os limites selecionados e tipos de dados reais. Se os limites designados forem iguais, então a função de máscara é um número constante.<br/><br/>![Screenshot que mostra o método de mascaramento para gerar um número aleatório.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento, que expõe os primeiros e últimos caracteres** e adiciona uma corda de enchimento personalizada no meio. Se a corda original for mais curta do que o prefixo e sufixo expostos, apenas é utilizada a corda de enchimento. <br/>prefixo[enchimento]sufixo]sufixo<br/><br/>![Painel de navegação](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O motor de recomendações do DDM sinaliza certos campos da sua base de dados como campos potencialmente sensíveis, que podem ser bons candidatos para mascarar. Na lâmina de mascaramento de dados dinâmicos no portal, irá ver as colunas recomendadas para a sua base de dados. Tudo o que precisa de fazer é clicar em **Adicionar Máscara** para uma ou mais colunas e, em seguida, **guardar** para aplicar uma máscara para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar máscara de dados dinâmica para a sua base de dados utilizando cmdlets PowerShell

### <a name="data-masking-policies"></a>Políticas de mascaramento de dados

- [Get-AzSqlDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regras de mascaramento de dados

- [Get-AzSqlDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [Novo AzSqlDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remover-AzSqlDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Configurar máscara de dados dinâmica para a sua base de dados utilizando a API REST

Pode utilizar a API REST para gerir programáticamente a política e regras de mascaramento de dados. A API REST publicada apoia as seguintes operações:

### <a name="data-masking-policies"></a>Políticas de mascaramento de dados

- [Criar ou atualizar:](/rest/api/sql/datamaskingpolicies/createorupdate)Cria ou atualiza uma política de mascaramento de dados de base de dados.
- [Obter:](/rest/api/sql/datamaskingpolicies/get)Obtém uma política de mascaramento de dados de base de dados. 

### <a name="data-masking-rules"></a>Regras de mascaramento de dados

- [Criar ou atualizar:](/rest/api/sql/datamaskingrules/createorupdate)Cria ou atualiza uma regra de mascaramento de dados de base de dados.
- [Lista por base de dados](/rest/api/sql/datamaskingrules/listbydatabase): Obtém uma lista das regras de mascaramento de dados de bases de dados.
