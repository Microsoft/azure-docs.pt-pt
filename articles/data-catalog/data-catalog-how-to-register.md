---
title: Registar fontes de dados no Catálogo de Dados do Azure
description: Este artigo destaca como registar fontes de dados no Catálogo de Dados do Azure, incluindo os campos de metadados extraídos durante o registo.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736329"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registar fontes de dados no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Azure Data Catalog é um serviço de cloud totalmente gerido que serve como um sistema de registo e descoberta para fontes de dados da empresa. Por outras palavras, o Data Catalog ajuda as pessoas a descobrir, compreender e usar fontes de dados, e ajuda as organizações a obter mais valor a partir dos seus dados existentes. O primeiro passo para tornar uma fonte de dados descoberta através do Data Catalog é registar essa fonte de dados.

## <a name="register-data-sources"></a>Registar origens de dados
O registo é o processo de extração de metadados da fonte de dados e cópia desses dados para o serviço Data Catalog. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.

Para registar uma fonte de dados, faça o seguinte:
1. No portal Do Catálogo de Dados Do Azure, inicie a ferramenta de registo de fonte de dados do Catálogo de Dados. 
2. Inscreva-se no seu trabalho ou conta escolar com as mesmas credenciais de Diretório Ativo Azure que usa para iniciar sessão no portal.
3. Selecione a fonte de dados que pretende registar.

Para mais detalhes passo a passo, consulte o [tutorial Get Started with Azure Data Catalog.](data-catalog-get-started.md)

Depois de ter registado a fonte de dados, o catálogo rastreia a sua localização e indexa os seus metadados. Os utilizadores podem pesquisar, navegar e descobrir a fonte de dados e, em seguida, usar a sua localização para se conectar a ele usando a aplicação ou ferramenta à sua escolha.

## <a name="supported-data-sources"></a>Origens de dados suportadas
Para obter uma lista de fontes de dados atualmente suportadas, consulte data [Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Ao registar uma fonte de dados, a ferramenta de registo extrai informações sobre a estrutura dos objetos que seleciona. Esta informação é referida como metadados estruturais.

Para todos os objetos, estes metadados estruturais incluem a localização do objeto, para que os utilizadores que descubram os dados possam usar essa informação para se conectarem ao objeto nas ferramentas do cliente à sua escolha. Outros metadados estruturais incluem nome e tipo de objeto, e nome de atributo/coluna e tipo de dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além dos metadados estruturais fundamentais extraídos da fonte de dados, a ferramenta de registo de fonte de dados extrai metadados descritivos. Para os Serviços de Análise de Servidores SQL e serviços de reporte de servidores SQL, estes metadados são retirados das propriedades da Descrição expostas por estes serviços. Para o Servidor SQL, os\_valores fornecidos utilizando a descrição ms propriedade estendida são extraídos. Para a Oracle Database, a ferramenta de registo de\_fontes de dados extrai a coluna DE COMENTÁRIOS DA visão DE TODOS OS COMENTÁRIOS DO TAB.\_

Além dos metadados descritivos extraídos da fonte de dados, os utilizadores podem introduzir metadados descritivos utilizando a ferramenta de registo de fonte de dados. Os utilizadores podem adicionar tags e identificar especialistas para os objetos que estão a ser registados. Todos estes metadados descritivos são copiados para o serviço data catalog juntamente com os metadados estruturais.

## <a name="include-previews"></a>Incluir pré-visualizações
Por padrão, apenas os metadados são extraídos de fontes de dados e copiados para o serviço data Catalog, mas compreender uma fonte de dados é muitas vezes facilitado quando se pode visualizar uma amostra dos dados que contém.

Ao utilizar a ferramenta de registo de fonte de dados do Data Catalog, pode incluir uma pré-visualização instantânea dos dados em cada tabela e visualização que está registada. Se optar por incluir pré-visualizações durante o registo, a ferramenta de registo inclui até 20 registos de cada tabela e vista. Este instantâneo é então copiado para o catálogo juntamente com os metadados estruturais e descritivos.

> [!NOTE]
> Mesas largas com um grande número de colunas podem ter menos de 20 registos incluídos na sua pré-visualização.
>
>

## <a name="include-data-profiles"></a>Incluir perfis de dados
Assim como a inclusão de pré-visualizações pode fornecer um contexto valioso para os utilizadores que procuram fontes de dados no Data Catalog, incluindo um perfil de dados pode facilitar a compreensão de fontes de dados descobertas.

Ao utilizar a ferramenta de registo de fonte de dados do Data Catalog, pode incluir um perfil de dados para cada tabela e visualização que está registada. Se optar por incluir um perfil de dados durante o registo, a ferramenta de registo inclui estatísticas agregadas sobre os dados em cada tabela e vista, incluindo:

* O número de linhas e o tamanho dos dados no objeto.
* A data para a mais recente atualização dos dados e o esquema do objeto.
* O número de registos nulos e valores distintos para colunas.
* Os valores mínimos, máximos, médios e padrão de desvio para colunas.

Estas estatísticas são então copiadas para o catálogo juntamente com os metadados estruturais e descritivos.

> [!NOTE]
> As colunas de texto e data sem incluir estatísticas médias ou padrão de desvio no seu perfil de dados.
>
>

## <a name="update-registrations"></a>Atualizar registos
Registar uma fonte de dados torna-a detetável no Data Catalog quando utiliza os metadados e a pré-visualização opcional extraída durante o registo. Se a fonte de dados tiver de ser atualizada no catálogo (por exemplo, se o esquema de um objeto tiver mudado, as tabelas originalmente excluídas devem ser incluídas ou se pretender atualizar os dados incluídos nas pré-visualizações), a ferramenta de registo de fontes de dados pode ser reexecutada.

O reregisto de uma fonte de dados já registada realiza uma operação de fusão "upsert": os objetos existentes são atualizados e novos objetos são criados. Os metadados fornecidos pelos utilizadores através do portal Data Catalog são retidos.

## <a name="summary"></a>Resumo
Uma vez que copia metadados estruturais e descritivos de uma fonte de dados para o serviço de catálogo, registar a fonte de dados no Data Catalog facilita a descoberta e a compreensão dos dados. Depois de ter registado a fonte de dados, pode anotar, gerir e descobrir através do portal Data Catalog.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o registo de fontes de dados, consulte o [tutorial Get Started with Azure Data Catalog.](data-catalog-get-started.md)
