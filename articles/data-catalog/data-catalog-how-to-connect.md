---
title: Como ligar a fontes de dados no Catálogo de Dados do Azure
description: Como fazer o artigo destacando como se conectar a fontes de dados descobertas com o Catálogo de Dados Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976776"
---
# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados
## <a name="introduction"></a>Introdução
O **Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados da empresa. Por outras palavras, o **Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Um aspeto chave deste cenário é a utilização dos dados – uma vez que um utilizador descobre uma fonte de dados e compreende a sua finalidade, o próximo passo é ligar-se à fonte de dados para utilizar os seus dados.

## <a name="data-source-locations"></a>Localizações de fonte de dados
Durante o registo de fonte de dados, o **Azure Data Catalog** recebe metadados sobre a fonte de dados. Estes metadados incluem os detalhes da localização da fonte de dados. Os detalhes da localização variarão de origem de dados para fonte de dados, mas conterá sempre as informações necessárias para se conectar. Por exemplo, a localização de uma tabela SQL Server inclui o nome do servidor, nome da base de dados, nome do esquema e nome da tabela, enquanto a localização de um relatório de Serviços de Informação do Servidor SQL inclui o nome do servidor e o caminho para o relatório. Outros tipos de fonte de dados terão localizações que refletem a estrutura e as capacidades do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas integradas para clientes
A forma mais simples de se ligar a uma fonte de dados é usar o "Open in..." menu no portal Do Catálogo de **Dados Azure.** Este menu apresenta uma lista de opções para a ligação ao ativo de dados selecionado.
Ao utilizar a vista de azulejos predefinido, este menu está disponível em cada azulejo.

 ![Abertura de uma tabela SQL Server no Excel a partir do azulejo de ativos de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Ao utilizar a vista da lista, o menu está disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abertura de um relatório de serviços de reporte de servidores SQL no Relatório Manager](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicações de clientes suportadas
Ao usar o "Open in..." menu para fontes de dados no portal Do Catálogo de Dados Do Azure, a aplicação correta do cliente deve ser instalada no computador cliente.

| Aberto na aplicação | Extensão de ficheiros / protocolo | Versões de aplicação suportadas |
| --- | --- | --- |
| Excel |.odc |Excel 2010 ou mais tarde |
| Excel (Top 1000) |.odc |Excel 2010 ou mais tarde |
| Power Query |.xlsx |Excel 2016 ou Excel 2010 ou Excel 2013 com a Power Query for Excel add-in instalada |
| Power BI Desktop |.pbix |Power BI Desktop julho 2016 ou mais tarde |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 ou mais tarde com ferramenta SQL Server instalada |
| Gestor de Relatórios |http:// |Consulte [os requisitos do navegador para os Serviços de Reporte de Servidores SQL](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Os seus dados, as suas ferramentas
As opções disponíveis no menu dependerão do tipo de ativo de dados atualmente selecionado. Claro que nem todas as ferramentas possíveis serão incluídas no "Open in..." menu, mas ainda é fácil de ligar à fonte de dados usando qualquer ferramenta cliente. Quando um ativo de dados é selecionado no portal do Catálogo de **Dados Do Azure,** a localização completa é exibida no painel de propriedades.

 ![Informações de ligação para uma tabela SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os dados de informação de ligação diferirão do tipo de fonte de dados para o tipo de fonte de dados, mas as informações incluídas no portal dar-lhe-ão tudo o que precisa para se ligar à fonte de dados em qualquer ferramenta do cliente. Os utilizadores podem copiar os detalhes da ligação para as fontes de dados que descobriram utilizando o Catálogo de **Dados Do Azure,** permitindo-lhes trabalhar com os dados na sua ferramenta de eleição.

## <a name="connecting-and-data-source-permissions"></a>Permissões de ligação e fonte de dados
Embora o **Azure Data Catalog** torne as fontes de dados detetáveis, o acesso aos dados em si permanece sob o controlo do proprietário ou administrador de fonte de dados. A descoberta de uma fonte de dados no Catálogo de **Dados do Azure** não dá a um utilizador permissões para aceder à própria fonte de dados.

Para facilitar aos utilizadores que descubram uma fonte de dados mas não tenham autorização para aceder aos seus dados, os utilizadores podem fornecer informações na propriedade do Request Access ao anotar uma fonte de dados. As informações aqui fornecidas – incluindo ligações ao processo ou ponto de contacto para obter acesso à fonte de dados – são apresentadas ao lado da informação de localização de fonte de dados no portal.

 ![Informações de ligação com instruções de acesso a pedido fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
Registar uma fonte de dados com o Catálogo de **Dados do Azure** torna esses dados detetáveis através da cópia de metadados estruturais e descritivos da fonte de dados para o serviço De catálogo. Uma vez registada uma fonte de dados, e descoberta, os utilizadores podem ligar-se à fonte de dados do portal do Catálogo de **Dados Azure** "Open in...". menu ou usando as suas ferramentas de dados de eleição.

## <a name="see-also"></a>Consulte também
* Inicie o tutorial do Catálogo de [Dados Azure](data-catalog-get-started.md) para detalhes passo a passo sobre como se conectar a fontes de dados.
