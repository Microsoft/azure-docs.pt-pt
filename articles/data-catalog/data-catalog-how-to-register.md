---
title: Registar fontes de dados no Catálogo de Dados do Azure
description: Este artigo destaca como registar fontes de dados no Catálogo de Dados Azure, incluindo os campos de metadados extraídos durante o registo.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674806"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registar fontes de dados no Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução
O Azure Data Catalog é um serviço de nuvem totalmente gerido que serve como um sistema de registo e descoberta para fontes de dados empresariais. Por outras palavras, o Data Catalog ajuda as pessoas a descobrir, compreender e usar fontes de dados, e ajuda as organizações a obter mais valor a partir dos seus dados existentes. O primeiro passo para tornar uma fonte de dados detetável através do Data Catalog é registar essa fonte de dados.

## <a name="register-data-sources"></a>Registar origens de dados
O registo é o processo de extração de metadados da fonte de dados e de cópia desses dados para o serviço Data Catalog. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.

Para registar uma fonte de dados, faça o seguinte:
1. No portal Azure Data Catalog, inicie a ferramenta de registo de fontes de dados do Catálogo de Dados. 
2. Inscreva-se com o seu trabalho ou conta escolar com as mesmas credenciais do Azure Ative Directory que utiliza para iniciar súb na súmis no portal.
3. Selecione a fonte de dados que pretende registar.

Para mais detalhes passo a passo, consulte o tutorial [do Catálogo de Dados do Azure.](data-catalog-get-started.md)

Depois de ter registado a fonte de dados, o catálogo rastreia a sua localização e indexa os seus metadados. Os utilizadores podem pesquisar, navegar e descobrir a fonte de dados, e depois usar a sua localização para se conectarem a ele utilizando a aplicação ou ferramenta que escolheram.

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas)
Para obter uma lista de fontes de dados atualmente suportadas, consulte [o Catálogo de Dados DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Quando regista uma fonte de dados, a ferramenta de registo extrai informações sobre a estrutura dos objetos que seleciona. Esta informação é referida como metadados estruturais.

Para todos os objetos, estes metadados estruturais incluem a localização do objeto, para que os utilizadores que descubram os dados possam utilizar essa informação para se conectarem ao objeto nas ferramentas do cliente à sua escolha. Outros metadados estruturais incluem nome e tipo de objeto, e nome de atributo/coluna e tipo de dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além dos metadados estruturais fundamentais extraídos da fonte de dados, a ferramenta de registo de fonte de dados extrai metadados descritivos. Para serviços de análise de servidores SQL e serviços de reporte de servidores SQL, estes metadados são retirados das propriedades descrição expostas por estes serviços. Para o SQL Server, os valores fornecidos através da descrição ms \_ propriedade estendida são extraídos. Para a Oracle Database, a ferramenta de registo de fonte de dados extrai a coluna COMMENTS da vista ALL \_ TAB \_ COMMENTS.

Além dos metadados descritivos extraídos da fonte de dados, os utilizadores podem introduzir metadados descritivos utilizando a ferramenta de registo de fonte de dados. Os utilizadores podem adicionar tags e podem identificar especialistas para os objetos que estão a ser registados. Todos estes metadados descritivos são copiados para o serviço data catalogar juntamente com os metadados estruturais.

## <a name="include-previews"></a>Incluir pré-visualizações
Por padrão, apenas os metadados são extraídos de fontes de dados e copiados para o serviço data Catalog, mas a compreensão de uma fonte de dados é muitas vezes facilitada quando se pode ver uma amostra dos dados que contém.

Ao utilizar a ferramenta de registo de dados do Catálogo de Dados, pode incluir uma pré-visualização instantânea dos dados em cada tabela e visualização que está registada. Se optar por incluir pré-visualizações durante o registo, a ferramenta de registo inclui até 20 registos de cada mesa e vista. Esta imagem é então copiada para o catálogo juntamente com os metadados estruturais e descritivos.

> [!NOTE]
> Mesas largas com um grande número de colunas podem ter menos de 20 registos incluídos na sua pré-visualização.
>
>

## <a name="include-data-profiles"></a>Incluir perfis de dados
Tal como a inclusão de pré-visualizações pode fornecer um contexto valioso para os utilizadores que procuram fontes de dados no Catálogo de Dados, incluindo um perfil de dados pode facilitar a compreensão das fontes de dados descobertas.

Ao utilizar a ferramenta de registo de dados do Catálogo de Dados, pode incluir um perfil de dados para cada tabela e visualização que está registada. Se optar por incluir um perfil de dados durante o registo, a ferramenta de registo inclui estatísticas agregadas sobre os dados em cada tabela e vista, incluindo:

* O número de linhas e o tamanho dos dados no objeto.
* A data para a mais recente atualização dos dados e do esquema do objeto.
* O número de registos nulos e valores distintos para colunas.
* Os valores mínimos, máximos, médios e padrão de desvio para colunas.

Estas estatísticas são então copiadas para o catálogo juntamente com os metadados estruturais e descritivos.

> [!NOTE]
> As colunas de texto e data não incluem estatísticas médias ou padrão de desvio no seu perfil de dados.
>
>

## <a name="update-registrations"></a>Atualizar registos
Registar uma fonte de dados torna-o detetável no Catálogo de Dados quando utiliza os metadados e a pré-visualização opcional extraídos durante o registo. Se a fonte de dados precisar de ser atualizada no catálogo (por exemplo, se o esquema de um objeto tiver mudado, as tabelas originalmente excluídas devem ser incluídas, ou pretender atualizar os dados incluídos nas pré-visualizações), a ferramenta de registo de fonte de dados pode ser reendo.

O reencamédimo de uma fonte de dados já registada realiza uma operação de fusão "upsert": os objetos existentes são atualizados e são criados novos objetos. Todos os metadados fornecidos pelos utilizadores através do portal data catalogo são mantidos.

## <a name="summary"></a>Resumo
Como copia metadados estruturais e descritivos de uma fonte de dados para o serviço de catálogo, registar a fonte de dados no Data Catalog facilita a descoberta e compreensão dos dados. Depois de ter registado a fonte de dados, pode anotar, gerir e descobrir utilizando o portal Data Catalog.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o registo de fontes de dados, consulte o tutorial [get start with Azure Data Catalog.](data-catalog-get-started.md)
