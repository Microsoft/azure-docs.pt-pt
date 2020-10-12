---
title: Como visualizar ativos de dados relacionados no Catálogo de Dados Azure
description: Este artigo explica como visualizar os ativos de dados relacionados de um ativo de dados selecionado no Catálogo de Dados Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fb5352b4cc84801296d9e621053672328b02692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523371"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como visualizar os ativos de dados relacionados no Catálogo de Dados do Azure?
O Azure Data Catalog permite-lhe visualizar os ativos de dados relacionados com um ativo de dados selecionado e ver relações entre eles. 

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas) 
Quando regista ativos de dados a partir das seguintes fontes de dados, o Azure Data Catalog regista automaticamente metadados sobre a junção de relações entre os ativos de dados selecionados. 

- SQL Server
- Base de Dados SQL do Azure
- MySQL
- Oracle

> [!NOTE]
> Para que o Catálogo de Dados importe a relação entre dois ativos de dados, deve registar ambos os ativos ao mesmo tempo. Se tivesse adicionado um deles separadamente, adicione-o novamente e o outro ativo de dados para importar a relação entre eles.

## <a name="view-related-data-assets"></a>Ver recursos de dados relacionados
Para visualizar os ativos de dados que estão relacionados com um conjunto de dados selecionado, utilize o **separador Relacionamentos** como mostrado na imagem seguinte: 

![Catálogo de Dados Azure - Ver ativos de dados relacionados](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Neste exemplo, existem duas relações para o ativo de dados **de categoria de produto** selecionado: 

- A coluna ProductSubcategoryID da tabela Produto tem uma relação de chave estranha com a coluna ProductSubcategoryID da tabela de categorias de produtos selecionada. 
- A coluna ProductCategoryID da tabela ProductSubCategory tem uma relação de chave estrangeira com a coluna ProductCategoryID da tabela de categorias de produtos selecionada.

> [!NOTE]
> Note a direção da seta na vista da árvore das relações.  

Para ver mais detalhes, como o nome totalmente qualificado da coluna, mova o rato e veja um popup semelhante à seguinte imagem: 

![Catálogo de Dados Azure - Popup de relacionamento](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Para incluir relações entre ativos que já tenham sido registados, re-registar esses ativos.

## <a name="next-steps"></a>Passos seguintes
- [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)
