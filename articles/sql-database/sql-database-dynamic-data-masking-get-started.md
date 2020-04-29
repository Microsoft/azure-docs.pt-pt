---
title: Máscara de dados dinâmica
description: A máscara de dados dinâmicos limita a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados para a Base de Dados SQL e para o Synapse Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78192919"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Máscara de dados dinâmicos para Base de Dados Azure SQL e Azure Synapse Analytics

A base de dados dinâmica SQL limita a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados. 

A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

Por exemplo, um representante de serviço num call center pode identificar os chamadores por vários dígitos do número do seu cartão de crédito, mas esses itens de dados não devem estar totalmente expostos ao representante do serviço. Uma regra de máscara pode ser definida que mascara todos, menos os últimos quatro dígitos de qualquer número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger dados de informação pessoalmente identificáveis (PII), de modo a que um desenvolvedor possa consultar ambientes de produção para fins de resolução de problemas sem violar os regulamentos de conformidade.

## <a name="dynamic-data-masking-basics"></a>Fundamentos dinâmicos de mascaramento de dados

Configura uma política dinâmica de máscara de dados no portal Azure selecionando a dinâmica operação de máscara de dados na sua lâmina de configuração de base de dados SQL ou lâmina de configuração. Esta função não pode ser definida utilizando o portal para O Synapse Azure (por favor, utilize powershell ou REST API)

### <a name="dynamic-data-masking-permissions"></a>Permissões dinâmicas de máscara de dados

A máscara de dados dinâmicos pode ser configurada pelas funções de administração da Base de Dados Azure SQL, administração do servidor ou [SQL Security Manager.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Política dinâmica de mascaramento de dados

* **Utilizadores de SQL excluídos da máscara** - um conjunto de utilizadores SQL ou identidades AAD que obtêm dados desmascarados nos resultados da consulta SQL. Os utilizadores com privilégios de administrador são sempre excluídos da máscara e vêem os dados originais sem máscara.
* Regras de **mascaramento** - Um conjunto de regras que definem os campos designados a mascarar e a função de mascaramento que é usada. Os campos designados podem ser definidos com base num nome de esquema de base de dados, nome de mesa e nome de coluna.
* **Funções** de mascaramento - Um conjunto de métodos que controlam a exposição de dados para diferentes cenários.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Padrão** |**Máscara completa de acordo com os tipos de dados dos campos designados**<br/><br/>• Utilize XXXX ou menos Xs se o tamanho do campo for inferior a 4 caracteres para tipos de dados de cordas (nchar, ntext, nvarchar).<br/>• Utilize um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilize 01-01-1900 para tipos de dados de data/hora (data, data2, data, data, dataoffset, hora pequena, hora).<br/>• Para a variante SQL, é utilizado o valor predefinido do tipo atual.<br/>• Para xml, o documento \<mascarado/> é utilizado.<br/>• Utilize um valor vazio para tipos de dados especiais (tabela de carimbos de tempo, hierárquica, GUID, binário, imagem, tipos espaciais varbinary). |
| **Cartão de crédito** |Método de **mascaramento, que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia constante como prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |Método de **mascaramento, que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo de corda constante sob a forma de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento, que gera um número aleatório** de acordo com os limites selecionados e tipos de dados reais. Se os limites designados forem iguais, então a função de mascaramento é um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento, que expõe o primeiro e último caracteres** e adiciona uma corda de enchimento personalizada no meio. Se a cadeia original for mais curta do que o prefixo e o sufixo expostos, apenas a corda de enchimento é utilizada. <br/>prefixo[enchimento]sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O motor de recomendações do DDM, sinaliza certos campos da sua base de dados como campos potencialmente sensíveis, o que pode ser um bom candidato para mascarar. Na lâmina de mascaramento de dados dinâmicos no portal, verá as colunas recomendadas para a sua base de dados. Tudo o que precisa fazer é clicar em **Adicionar Máscara** para uma ou mais colunas e, em seguida, **guardar** para aplicar uma máscara para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar a máscara de dados dinâmicos para a sua base de dados utilizando cmdlets PowerShell

Consulte [os Cmdlets de Base de Dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmicos para a sua base de dados utilizando a API REST

Consulte operações para base de [dados Azure SQL](https://docs.microsoft.com/rest/api/sql/).
