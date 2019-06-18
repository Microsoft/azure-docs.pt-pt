---
title: Como ligar a origens de dados no catálogo de dados do Azure
description: Artigo que mostra como realce como ligar a origens de dados detetadas com o catálogo de dados do Azure.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: c64340491dba11870364610a6c2ff62e25c1328a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001858"
---
# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço cloud totalmente gerido que funciona como um sistema de registo e sistema de deteção de origens de dados empresariais. Em outras palavras, **catálogo de dados do Azure** é tudo para ajudar as pessoas a detetar, compreender e utilizar origens de dados e ajuda as organizações a obter mais valor dos seus dados existentes. Um aspecto fundamental desse cenário está a utilizar os dados – depois de um utilizador Deteta uma origem de dados e compreende o seu objetivo, a próxima etapa é ligar à origem de dados para colocar os seus dados a utilizar.

## <a name="data-source-locations"></a>Localizações de origem de dados
Durante o registo da origem de dados, **catálogo de dados do Azure** recebe os metadados sobre a origem de dados. Esses metadados incluem os detalhes de localização da origem de dados. Os detalhes da localização irão variar de origem de dados à origem de dados, mas sempre conterá as informações necessárias para ligar. Por exemplo, a localização para uma tabela do SQL Server inclui o nome do servidor, nome de base de dados, o nome do esquema e nome de tabela, embora a localização de um relatório do SQL Server Reporting Services inclui o nome do servidor e o caminho para o relatório. Outros tipos de origens de dados terão as localizações que refletem a estrutura e os recursos do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas de cliente integradas
A forma mais simples de ligar a uma origem de dados é usar o "Abrir em...." menu a **catálogo de dados do Azure** portal. Esse menu apresenta uma lista de opções para ligar ao recurso de dados selecionada.
Quando utilizar a vista de mosaico do padrão, esse menu está disponível em cada mosaico.

 ![Abrir uma tabela do SQL Server no Excel a partir do mosaico de elemento de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Quando utilizar a vista de lista, o menu está disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abrir um relatório do SQL Server Reporting Services no Gestor de relatórios da barra de pesquisa](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicações de cliente suportados
Ao utilizar o "Abrir em...." menu para origens de dados no portal do catálogo de dados do Azure, o aplicativo correto do cliente tem de estar instalado no computador cliente.

| Abrir na aplicação | Extensão de ficheiro protocolo | Versões de aplicações suportados |
| --- | --- | --- |
| Excel |.odc |Excel 2010 ou posterior |
| Excel (Top 1000) |.odc |Excel 2010 ou posterior |
| Power Query |.xlsx |Excel 2016 ou o Excel 2010 ou o Excel 2013 com o Power Query para o suplemento do Excel instalado |
| Power BI Desktop |.pbix |Power BI Desktop Julho 2016 ou posterior |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 atualização 4 ou posterior com as ferramentas do SQL Server instalada |
| Gestor de relatórios |http:// |Consulte [requisitos de browser do SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Os dados, as ferramentas
As opções disponíveis no menu irão depender do tipo de recurso de dados atualmente selecionado. É claro, nem todas as ferramentas possíveis serão incluídas no "Abrir em...." menu, mas é ainda mais fácil ligar à origem de dados através de qualquer ferramenta de cliente. Quando um recurso de dados é selecionado na **catálogo de dados do Azure** portal, a localização completa é apresentada no painel de propriedades.

 ![Informações de ligação para uma tabela do SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes de informações de ligação serão diferentes do tipo de origem de dados para o tipo de origem de dados, mas as informações incluídas no portal do fornecerá a tudo o que precisa para ligar à origem de dados em qualquer ferramenta de cliente. Os utilizadores podem copiar os detalhes de ligação para as origens de dados que eles tenham detetados usando **catálogo de dados do Azure**, permitindo-lhes trabalhar com os dados na ferramenta de sua escolha.

## <a name="connecting-and-data-source-permissions"></a>Permissões de origem de dados e ligar
Embora **catálogo de dados do Azure** torna os dados detetável, acesso aos dados propriamente dito permanece sob o controlo do administrador ou proprietário da origem de dados. Detetar uma origem de dados **catálogo de dados do Azure** não confere a um utilizador as permissões para aceder à origem de dados em si.

Para tornar mais fácil para os utilizadores que detetam uma origem de dados, mas não tem permissão para aceder aos seus dados, os utilizadores podem fornecer informações na propriedade do pedido de acesso, quando anotar uma origem de dados. Informações fornecidas aqui, incluindo links para o processo ou o ponto de contacto para obter acesso à origem de dados – são apresentadas juntamente com as informações de localização de origem de dados no portal.

 ![Informações de ligação com as instruções de acesso de pedido fornecido](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
Registar uma origem de dados com **catálogo de dados do Azure** faz com que esses dados detetável ao copiar os metadados estruturais e descritivo da origem de dados para o serviço de catálogo. Depois de uma origem de dados tiver sido registrada e detetada, os utilizadores podem ligar à origem de dados a partir do **catálogo de dados do Azure** portal "abrir no...." " menu ou com as ferramentas de dados à escolha.

## <a name="see-also"></a>Consulte também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como ligar a origens de dados.
