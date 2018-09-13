---
title: Máscara de dados dinâmica de base de dados SQL do Azure | Documentos da Microsoft
description: Máscara de dados dinâmicos da base de dados SQL limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: c4d9eb5f7105e0e1b16ad994d397885b9232a0ea
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719533"
---
# <a name="sql-database-dynamic-data-masking"></a>Máscara de dados dinâmicos da base de dados SQL

Máscara de dados dinâmicos da base de dados SQL limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. 

A máscara de dados dinâmica ajuda a evitar acessos não autorizados a dados confidenciais, ao permitir aos clientes designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

Por exemplo, um representante do serviço num call center pode identificar os autores de chamadas por vários dígitos do seu número de cartão de crédito, mas esses itens de dados não devem ser totalmente expostos para o representante do serviço. Uma regra de máscara pode ser definida, mas os últimos quatro dígitos de qualquer número de cartão de crédito no resultado de máscaras de todos os definir de qualquer consulta. Como outro exemplo, pode ser definida uma máscara de dados adequadas para proteger os dados de informações de identificação pessoal (PII), para que um desenvolvedor pode consultar a ambientes de produção para fins de resolução de problemas sem violar os regulamentos de conformidade.

## <a name="sql-database-dynamic-data-masking-basics"></a>Noções básicas de máscara de dados dinâmicos da base de dados SQL
Configurar um máscara de política no portal do Azure ao selecionar os operação no seu painel de configuração de base de dados SQL ou o painel de definições de máscara de dados dinâmicos de dados dinâmicos.

### <a name="dynamic-data-masking-permissions"></a>Permissões de máscara de dados dinâmicos
Máscara de dados dinâmica pode ser configurada por funções de diretor de segurança, administrador de servidor ou o administrador de banco de dados do Azure.

### <a name="dynamic-data-masking-policy"></a>Política de máscara de dados dinâmicos
* **Utilizadores SQL excluídos da máscara** – um conjunto de utilizadores do SQL ou resultados de consulta de identidades do AAD que obtêm dados sem máscara no SQL. Os utilizadores com privilégios de administrador são sempre excluídos da máscara e ver os dados originais sem qualquer máscara.
* **Regras de máscara** -um conjunto de regras que definem os campos designados para ser mascarado e a função de máscara que é utilizada. Os campos designados podem ser definidos com um nome de esquema de base de dados, o nome da tabela e o nome da coluna.
* **As funções de máscara** -um conjunto de métodos que controlam a exposição dos dados para diferentes cenários.

| Função de máscara | A máscara de lógica |
| --- | --- |
| **Predefinição** |**Máscara de completa, de acordo com os tipos de dados dos campos designados**<br/><br/>• Utilize XXXX ou Xs menos se o tamanho do campo for inferior a 4 carateres para tipos de dados de cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Utilizar um valor de zero para tipos de dados numéricos (bigint, bits, decimal, int, dinheiro, numérico, smallint, smallmoney, tinyint, número de vírgula flutuante, real).<br/>• Utilizar 01-01-1900 para tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, tempo).<br/>• Para variante do SQL, o valor predefinido do tipo atual é utilizado.<br/>• Para o documento XML <masked/> é utilizado.<br/>• Utilizar um valor em branco para tipos de dados especial (tabela de timestamp, chamado hierarchyid, GUID, binário, imagem, tipos espaciais do varbinary). |
| **Cartão de crédito** |**A máscara de método, que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**A máscara de método, que expõe a primeira letra e substitui o domínio com XXX.com** usando um prefixo de cadeia de caracteres constante na forma de um endereço de e-mail.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**A máscara de método, que gera um número aleatório** , de acordo com os limites selecionados e os tipos de dados reais. Se dos limites designados forem iguais, a função de máscara é um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método, que expõe os primeiro e último carateres de máscara** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original é menor do que o prefixo exposto e o sufixo, apenas a cadeia de preenchimento é utilizada. <br/>prefixo [preenchimento] sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar
O motor de recomendações de ddm de autor, sinalizadores de determinados campos do seu banco de dados como campos potencialmente confidenciais, que podem ser bons candidatos à máscara. No painel máscara de dados dinâmicos no portal, verá as colunas recomendadas para a base de dados. Tudo o que precisa fazer é clicar **adicionar máscara** para uma ou mais colunas e, em seguida **guardar** para aplicar uma máscara para estes campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar a máscara da base de dados utilizando cmdlets do Powershell de dados dinâmicos
Ver [Cmdlets de base de dados SQL do Azure](https://docs.microsoft.com/powershell/module/azurerm.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmicos da base de dados com a REST API
Ver [operações para a base de dados SQL do Azure](https://msdn.microsoft.com/library/dn505719.aspx).

