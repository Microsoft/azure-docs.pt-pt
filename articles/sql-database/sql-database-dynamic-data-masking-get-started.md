---
title: Máscara de dados dinâmica
description: A máscara de dados dinâmicos limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios para o banco de dados SQL e data warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: 149c42829762920583948958da8252a01e35ef1f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721919"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Mascaramento de dados dinâmicos para o Azure SQL Database e data warehouse

O mascaramento de dados dinâmicos do Database do SQL limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. 

A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

Por exemplo, um representante de serviço em um Call Center pode identificar os chamadores por vários dígitos de seu número de cartão de crédito, mas esses itens de dados não devem ser totalmente expostos ao representante de serviço. Uma regra de mascaramento pode ser definida para mascarar todos os últimos quatro dígitos de qualquer número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger dados de PII (informações de identificação pessoal), para que um desenvolvedor possa consultar ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## <a name="dynamic-data-masking-basics"></a>Noções básicas de mascaramento de dados dinâmicos

Você configura uma política de mascaramento de dados dinâmicos na portal do Azure selecionando a operação máscara de dados dinâmicos na folha configuração ou folha de configurações do banco de dados SQL. Este recurso não pode ser definido usando o portal para SQL DW (use o PowerShell ou a API REST)

### <a name="dynamic-data-masking-permissions"></a>Permissões de mascaramento de dados dinâmicos

A máscara de dados dinâmicos pode ser configurada pelas funções de administração da Base de Dados Azure SQL, administração do servidor ou [SQL Security Manager.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Política de mascaramento de dados dinâmicos

* **Utilizadores de SQL excluídos da máscara** - um conjunto de utilizadores SQL ou identidades AAD que obtêm dados desmascarados nos resultados da consulta SQL. Os usuários com privilégios de administrador sempre são excluídos do mascaramento e veem os dados originais sem nenhuma máscara.
* Regras de **mascaramento** - Um conjunto de regras que definem os campos designados a mascarar e a função de mascaramento que é usada. Os campos designados podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome de coluna.
* **Funções** de mascaramento - Um conjunto de métodos que controlam a exposição de dados para diferentes cenários.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Predefinição** |**Máscara completa de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos XS se o tamanho do campo for inferior a 4 caracteres para tipos de dados de cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Use um valor zero para tipos de dados numéricos (bigint, bit, Decimal, int, Money, Numeric, smallint, SmallMoney, tinyint, float, real).<br/>• Use 01-01-1900 para tipos de dados de data/hora (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, time).<br/>• Para variante SQL, o valor padrão do tipo atual é usado.<br/>• Para xml, o documento \<mascarado/> é utilizado.<br/>• Use um valor vazio para tipos de dados especiais (carimbo de data/hora, hierarchyid, GUID, binário, imagem, tipos espaciais varbinary). |
| **Cartão de crédito** |Método de **mascaramento, que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia constante como prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |Método de **mascaramento, que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo de corda constante sob a forma de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento, que gera um número aleatório** de acordo com os limites selecionados e tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) de navegação |
| **Texto personalizado** |**Método de mascaramento, que expõe o primeiro e último caracteres** e adiciona uma corda de enchimento personalizada no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada. <br/>prefixo[enchimento]sufixo<br/><br/>![](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) de navegação |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O mecanismo de recomendações do DDM, sinaliza determinados campos do seu banco de dados como campos potencialmente confidenciais, que podem ser bons candidatos para mascaramento. Na folha Máscara de Dados Dinâmicos no portal, você verá as colunas recomendadas para seu banco de dados. Tudo o que precisa fazer é clicar em **Adicionar Máscara** para uma ou mais colunas e, em seguida, **guardar** para aplicar uma máscara para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar o mascaramento de dados dinâmicos para seu banco usando cmdlets do PowerShell

Consulte [os Cmdlets de Base de Dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar o mascaramento de dados dinâmicos para seu banco usando a API REST

Consulte operações para base de [dados Azure SQL](https://docs.microsoft.com/rest/api/sql/).
