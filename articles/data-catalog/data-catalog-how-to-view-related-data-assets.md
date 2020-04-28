---
title: Como visualizar ativos de dados relacionados no Catálogo de Dados do Azure
description: Este artigo explica como visualizar os ativos de dados relacionados de um ativo de dados selecionado no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68737023"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como visualizar ativos de dados relacionados no Catálogo de Dados do Azure?
O Azure Data Catalog permite-lhe visualizar os ativos de dados relacionados com um ativo de dados selecionados e ver relações entre eles. 

## <a name="supported-data-sources"></a>Origens de dados suportadas 
Ao registar os ativos de dados das seguintes fontes de dados, o Azure Data Catalog regista automaticamente metadados sobre relações de união entre os ativos de dados selecionados. 

- SQL Server
- Base de Dados SQL do Azure
- MySQL
- Oracle

> [!NOTE]
> Para que o Data Catalog importe a relação entre dois ativos de dados, deve registar ambos os ativos ao mesmo tempo. Se tivesse adicionado um deles separadamente, adicione-o novamente e o outro ativo de dados para importar relações entre eles.

## <a name="view-related-data-assets"></a>Ver recursos de dados relacionados
Para visualizar os ativos de dados relacionados com um conjunto de dados selecionado, utilize o separador **Relações** como mostrado na seguinte imagem: 

![Catálogo de Dados Azure - Ver ativos de dados relacionados](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Neste exemplo, existem duas relações para o ativo de dados selecionado da **subcategoria ProductSub:** 

- A coluna ProductSubcategoryID da tabela produto tem uma relação chave estrangeira com a coluna ProductSubcategoryID da tabela de subcategorias do Produto selecionado. 
- A coluna CategoryID do quadro ProductSubCategory tem uma relação chave estrangeira com a coluna ProductCategoryID da tabela de categoria seletiva do produto selecionado.

> [!NOTE]
> Reparem na direção da seta na vista da árvore de relacionamentos.  

Para ver mais detalhes, como o nome totalmente qualificado da coluna, mova o rato e veja um popup semelhante à seguinte imagem: 

![Catálogo de Dados Azure - Relação popup](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Para incluir relações entre ativos já registados, reregique esses ativos.

## <a name="next-steps"></a>Passos seguintes
- [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)