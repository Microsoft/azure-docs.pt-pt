---
title: Como ver os recursos de dados relacionados no catálogo de dados do Azure
description: Este artigo explica como ver os recursos de dados relacionados de um recurso de dados selecionados no catálogo de dados do Azure.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b01c328812113ad721b7632978ad28e54a6a3ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61000132"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como ver dados relacionados ativos no catálogo de dados do Azure?
O catálogo de dados do Azure permite-lhe ver os recursos de dados relacionados com um relações de recurso e da vista de dados selecionados entre eles. 

## <a name="supported-data-sources"></a>Origens de dados suportadas 
Ao registar recursos de dados das seguintes origens de dados, o catálogo de dados do Azure regista automaticamente metadados sobre relações de associação entre os recursos de dados selecionada. 

- SQL Server
- Base de Dados SQL do Azure
- MySQL
- Oracle

> [!NOTE]
> Para importar a relação entre os recursos de dados de dois catálogo de dados, tem de registar os ativos de ambas ao mesmo tempo. Se tivesse adicionado um deles separadamente, adicione-a novamente e os outros recursos de dados para importar a relação entre elas.

## <a name="view-related-data-assets"></a>Ver recursos de dados relacionados
Para ver os recursos de dados que estão relacionados com um conjunto de dados selecionado, utilize o **relações** separador conforme mostrado na imagem seguinte: 

![Catálogo de dados do Azure - recursos de dados relacionados com o modo de exibição](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Neste exemplo, existem duas relações para selecionado **ProductSubcategory** recurso de dados: 

- ProductSubcategoryID coluna da tabela de produto tem uma relação de chave externa com ProductSubcategoryID coluna da tabela ProductSubcategory selecionada. 
- ProductCategoryID coluna da tabela ProductSubCategory tem uma relação de chave externa com ProductCategoryID coluna da tabela ProductCategory selecionada.

> [!NOTE]
> Tenha em atenção a direção da seta na vista de árvore de relações.  

Para ver mais detalhes, como o nome completamente qualificado da coluna, mova o rato sobre e verá um pop-up semelhante à imagem seguinte: 

![Catálogo de dados do Azure – pop-up de relação](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Para incluir as relações entre os recursos de que já tenham sido registadas, volte a registar esses ativos.

## <a name="next-steps"></a>Passos Seguintes
- [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)