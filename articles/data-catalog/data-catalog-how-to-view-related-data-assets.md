---
title: Como exibir ativos de dados relacionados no catálogo de dados do Azure
description: Este artigo explica como exibir ativos de dados relacionados de um ativo de dados selecionado no catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737023"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como exibir ativos de dados relacionados no catálogo de dados do Azure?
O catálogo de dados do Azure permite que você exiba ativos de dados relacionados a um ativo de dados selecionado e exiba relações entre eles. 

## <a name="supported-data-sources"></a>Origens de dados suportadas 
Quando você registra ativos de dados das fontes de dados a seguir, o catálogo de dados do Azure registra automaticamente os metadados sobre as relações de junção entre os ativos de dados selecionados. 

- SQL Server
- Base de Dados SQL do Azure
- MySQL
- Oracle

> [!NOTE]
> Para que o catálogo de dados importe a relação entre dois ativos de dados, você deve registrar ambos os ativos ao mesmo tempo. Se você adicionou um deles separadamente, adicione-o novamente e o outro ativo de dados para importar a relação entre eles.

## <a name="view-related-data-assets"></a>Ver recursos de dados relacionados
Para exibir ativos de dados que estão relacionados a um DataSet selecionado, use a guia **relações** , conforme mostrado na imagem a seguir: 

![Catálogo de dados do Azure – exibir ativos de dados relacionados](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Neste exemplo, há duas relações para o ativo de dados **ProductSubcategory** selecionado: 

- A coluna ProductSubcategoryID da tabela Product tem uma relação de chave estrangeira com a coluna ProductSubcategoryID da tabela ProductSubcategory selecionada. 
- A coluna ProductCategoryID da tabela ProductSubCategory tem uma relação de chave estrangeira com a coluna ProductCategoryID da tabela ProductCategory selecionada.

> [!NOTE]
> Observe a direção da seta na exibição de árvore relações.  

Para ver mais detalhes, como o nome totalmente qualificado da coluna, mova o mouse sobre e você verá um pop-up semelhante à imagem a seguir: 

![Catálogo de dados do Azure-pop-up relação](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Para incluir relações entre ativos que já foram registrados, registre novamente esses ativos.

## <a name="next-steps"></a>Passos seguintes
- [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)