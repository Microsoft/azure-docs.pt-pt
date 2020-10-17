---
title: Como ligar-se a fontes de dados no Catálogo de Dados Azure
description: Como-a-dia que realça como se conectar a fontes de dados descobertas com o Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 5c8c545f01eb0df53ce9677b7db6f931b4d21af9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151902"
---
# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados
## <a name="introduction"></a>Introdução
**O Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados empresariais. Por outras palavras, **o Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Um aspeto fundamental deste cenário é a utilização dos dados – uma vez que um utilizador descobre uma fonte de dados e compreende a sua finalidade, o próximo passo é ligar-se à fonte de dados para utilizar os seus dados.

## <a name="data-source-locations"></a>Locais de origem de dados
Durante o registo de fonte de dados, **o Azure Data Catalog** recebe metadados sobre a fonte de dados. Estes metadados incluem os detalhes da localização da fonte de dados. Os detalhes da localização variarão de fonte de dados para fonte de dados, mas conterá sempre as informações necessárias para a ligação. Por exemplo, a localização de uma tabela SQL Server inclui o nome do servidor, nome da base de dados, nome do esquema e nome da tabela, enquanto a localização de um relatório dos Serviços de Relato do Servidor SQL inclui o nome do servidor e o caminho para o relatório. Outros tipos de fonte de dados terão localizações que refletem a estrutura e as capacidades do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas integradas do cliente
A forma mais simples de se ligar a uma fonte de dados é usar o "Open in..." menu no portal **do Catálogo de Dados Azure.** Este menu apresenta uma lista de opções para a ligação ao ativo de dados selecionado.
Ao utilizar a vista de azulejos predefinidos, este menu está disponível em cada azulejo.

 ![Abertura de uma tabela de Servidor SQL no Excel a partir do azulejo do ativo de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Ao utilizar a vista da lista, o menu está disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abertura de um relatório de serviços de relatório de relatório de servidores sql no Gestor de Relatórios](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicações de clientes suportadas
Ao usar o "Abrir em..." menu para fontes de dados no portal Azure Data Catalog, a aplicação correta do cliente deve ser instalada no computador cliente.

| Aberto na aplicação | Extensão de ficheiro / protocolo | Versões de aplicações suportadas |
| --- | --- | --- |
| Excel |.odc |Excel 2010 ou mais tarde |
| Excel (Top 1000) |.odc |Excel 2010 ou mais tarde |
| Power Query |.xlsx |Excel 2016 ou Excel 2010 ou Excel 2013 com a Consulta de Potência para o add-in excel instalado |
| Power BI Desktop |.pbix |Power BI Desktop julho 2016 ou mais tarde |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 ou posterior com a ferramenta SQL Server instalada |
| Gestor de Relatórios |http:// |Consulte [os requisitos do navegador para serviços de relatório de servidores SQL](/sql/reporting-services/browser-support-for-reporting-services-and-power-view) |

## <a name="your-data-your-tools"></a>Os seus dados, as suas ferramentas
As opções disponíveis no menu dependerão do tipo de ativo de dados atualmente selecionado. Claro que nem todas as ferramentas possíveis serão incluídas no "Open in..." menu, mas ainda é fácil de ligar à fonte de dados usando qualquer ferramenta do cliente. Quando um ativo de dados é selecionado no portal do **Catálogo de Dados Azure,** a localização completa é exibida no painel de propriedades.

 ![Informações de ligação para uma tabela de servidor SQL](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes da informação de ligação diferirão do tipo de fonte de dados para o tipo de fonte de dados, mas as informações incluídas no portal dar-lhe-ão tudo o que necessita para se ligar à fonte de dados em qualquer ferramenta do cliente. Os utilizadores podem copiar os dados de ligação para as fontes de dados que descobriram utilizando o **Azure Data Catalog**, permitindo-lhes trabalhar com os dados na sua ferramenta de eleição.

## <a name="connecting-and-data-source-permissions"></a>Permissões de ligação e fonte de dados
Embora **o Azure Data Catalog** torne as fontes de dados detetáveis, o acesso aos dados em si permanece sob o controlo do proprietário ou administrador da fonte de dados. A descoberta de uma fonte de dados no **Azure Data Catalog** não dá ao utilizador quaisquer permissões para aceder à própria fonte de dados.

Para facilitar a descoberta de uma fonte de dados por parte dos utilizadores, mas que não tenham permissão para aceder aos seus dados, os utilizadores podem fornecer informações na propriedade 'Acesso ao Pedido' ao anotar uma fonte de dados. As informações aqui fornecidas – incluindo links para o processo ou ponto de contacto para obter acesso a fonte de dados – são apresentadas juntamente com as informações de localização da fonte de dados no portal.

 ![Informações de ligação com instruções de acesso ao pedido fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
Registar uma fonte de dados com **o Azure Data Catalog** torna esses dados detetáveis copiando metadados estruturais e descritivos da fonte de dados para o serviço Catálogo. Uma vez registada uma fonte de dados, e descoberta, os utilizadores podem ligar-se à fonte de dados a partir do portal do Catálogo de **Dados Azure** "Open in...". menu ou usando as suas ferramentas de dados de eleição.

## <a name="see-also"></a>Consulte também
* Começar com o tutorial do Catálogo de [Dados Azure](data-catalog-get-started.md) para detalhes passo a passo sobre como ligar-se a fontes de dados.