---
title: Terminologia do catálogo de dados do Azure
description: Este artigo fornece uma introdução aos conceitos e termos usados na documentação do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736280"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do catálogo de dados do Azure

Este artigo fornece uma introdução aos conceitos e termos usados na documentação do catálogo de dados do Azure.

## <a name="catalog"></a>catálogo

O catálogo de dados do Azure é um repositório de metadados baseado em nuvem no qual fontes de dados e ativos de dados podem ser registrados. O catálogo serve como um local de armazenamento central para metadados estruturais extraídos de fontes de dados e para metadados descritivos adicionados pelos usuários.

## <a name="data-source"></a>Origem de dados

Uma fonte de dados é um sistema ou contêiner que gerencia ativos de dados. Os exemplos incluem bancos de dados SQL Server, bancos de dados Oracle, bancos de dados SQL Server Analysis Services (tabela ou multidimensional) e servidores SQL Server Reporting Services.

## <a name="data-asset"></a>Ativo de dados

Ativos de dados são objetos contidos em fontes de dados que podem ser registrados com o catálogo. Os exemplos incluem tabelas e exibições de SQL Server, tabelas e exibições do Oracle, SQL Server Analysis Services medidas, dimensões e KPIs e relatórios de SQL Server Reporting Services.

## <a name="data-asset-location"></a>Local do ativo de dados

O catálogo armazena o local de uma fonte de dados ou ativo de dados, que pode ser usado para se conectar à fonte usando um aplicativo cliente. O formato e os detalhes do local variam de acordo com o tipo de fonte de dados. Por exemplo, uma tabela SQL Server pode ser identificada por seu nome de quatro partes – nome do servidor, nome do banco de dados, nome do esquema, nome do objeto – enquanto um relatório SQL Server Reporting Services pode ser identificado por sua URL.

## <a name="structural-metadata"></a>Metadados estruturais

Metadados estruturais são os metadados extraídos de uma fonte de dados que descreve a estrutura de um ativo de dados. Isso inclui o local dos ativos, seu nome e tipo de objeto e características específicas de tipo adicionais. Por exemplo, os metadados estruturais para tabelas e exibições incluem os nomes e tipos de dados para as colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou a intenção de um ativo de dados. Normalmente, os metadados descritivos são adicionados pelos usuários do catálogo usando o portal do catálogo de dados do Azure, mas também podem ser extraídos da fonte de dados durante o registro. Por exemplo, a ferramenta de registro do catálogo de dados do Azure extrairá descrições da Propriedade Description em SQL Server Analysis Services e SQL Server Reporting Services, e da [propriedade estendida MS_Description](https://technet.microsoft.com/library/ms190243.aspx) em bancos de dados SQL Server, se essas propriedades foram preenchidas com valores.

## <a name="request-access"></a>Pedir acesso

Os metadados descritivos de um ativo de dados podem incluir informações sobre como solicitar acesso ao ativo de dados ou à fonte de dados. Essas informações são apresentadas com o local do ativo de dados e podem incluir uma ou mais das seguintes opções:

* O endereço de email do usuário ou da equipe responsável por conceder acesso à fonte de dados.
* A URL do processo documentado que os usuários devem seguir para obter acesso à fonte de dados.
* A URL de uma ferramenta de gerenciamento de identidades e acesso (como Microsoft Identity Manager) que pode ser usada para obter acesso à fonte de dados.
* Uma entrada de texto livre que descreve como os usuários podem obter acesso à fonte de dados.

## <a name="preview"></a>Pré-visualização

Uma visualização no catálogo de dados do Azure é um instantâneo de até 20 registros que podem ser extraídos da fonte de dados durante o registro e armazenados no catálogo com os metadados de ativo de dados. A visualização pode ajudar os usuários que descobrirem um ativo de dados a compreender melhor sua função e finalidade. Em outras palavras, Ver os dados de exemplo pode ser mais valioso do que ver apenas os nomes de coluna e os tipos de dados.
As visualizações só têm suporte para tabelas e exibições e devem ser selecionadas explicitamente pelo usuário durante o registro.

## <a name="data-profile"></a>Perfil de Dados

Um perfil de dados no catálogo de dados do Azure é um instantâneo de metadados em nível de tabela e de coluna sobre um ativo de dados registrado que pode ser extraído da fonte de dados durante o registro e armazenado no catálogo com os metadados de ativo de dados. O perfil de dados pode ajudar os usuários que descobrirem um ativo de dados a compreender melhor sua função e finalidade. De forma semelhante às visualizações, os perfis de dados devem ser explicitamente selecionados pelo usuário durante o registro.

> [!NOTE]
> A extração de um perfil de dados pode ser uma operação dispendiosa para grandes tabelas e exibições e pode aumentar significativamente o tempo necessário para registrar uma fonte de dados.


## <a name="user-perspective"></a>Perspectiva do usuário

No catálogo de dados do Azure, qualquer usuário pode fornecer metadados descritivos para um ativo de dados registrado. Cada usuário tem uma perspectiva distinta sobre os dados e seu uso. Por exemplo, o administrador responsável por um servidor pode fornecer os detalhes de seu SLA (contrato de nível de serviço) ou de backup do Windows; um administrador de dados pode fornecer links para a documentação dos processos de negócios aos quais os dados dão suporte; e um analista pode fornecer uma descrição nos termos que são mais relevantes para outros analistas e que pode ser mais valioso para os usuários que precisam descobrir e entender os dados.

Cada uma dessas perspectivas é inerentemente valiosa e, com o catálogo de dados do Azure, cada usuário pode fornecer as informações que são significativas para elas, enquanto todos os usuários podem usar essas informações para entender os dados e sua finalidade.

## <a name="expert"></a>Especialista

Um especialista é um usuário que foi identificado como tendo uma perspectiva "especializada" informada para um ativo de dados. Qualquer usuário pode adicionar a si mesmo ou outro usuário como um especialista para um ativo. Estar listado como um especialista não transmite nenhum privilégio adicional no catálogo de dados do Azure; Ele permite que os usuários localizem facilmente essas perspectivas que são mais prováveis de serem úteis ao examinar os metadados descritivos de um ativo.

## <a name="owner"></a>Owner

Um proprietário é um usuário que tem privilégios adicionais para gerenciar um ativo de dados no catálogo de dados do Azure. Os usuários podem apropriar-se de ativos de dados registrados e os proprietários podem adicionar outros usuários como coproprietários. Para obter mais informações, consulte [como gerenciar ativos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> A propriedade e o gerenciamento estão disponíveis apenas na edição Standard do catálogo de dados do Azure.

## <a name="registration"></a>Registo

O registro é o ato de extrair metadados de ativos de dados de uma fonte de dados e copiá-los para o serviço do catálogo de dados do Azure. Os ativos de dados que foram registrados podem ser anotados e descobertos.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar um catálogo de dados do Azure](data-catalog-get-started.md) 
