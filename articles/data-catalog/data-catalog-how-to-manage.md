---
title: Gerir ativos de dados no Catálogo de Dados do Azure
description: O artigo destaca como controlar a visibilidade e a propriedade de ativos de dados registados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736331"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gerir ativos de dados no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Azure Data Catalog foi concebido para a descoberta de fontes de dados, para que possa facilmente descobrir e compreender as fontes de dados que precisa para realizar análises e tomar decisões. Estas capacidades de descoberta têm o maior impacto quando você e outros utilizadores podem encontrar e entender a mais ampla gama de fontes de dados disponíveis. Com estes elementos em mente, o comportamento padrão do Data Catalog é para que todas as fontes de dados registadas sejam visíveis e detetáveis por todos os utilizadores do catálogo.

O Data Catalog não lhe dá acesso aos próprios dados. O acesso a dados é controlado pelo proprietário da fonte de dados. Com o Data Catalog, pode descobrir fontes de dados e ver os metadados relacionados com as fontes que estão registadas no catálogo.

No entanto, pode haver situações em que as fontes de dados só devem ser visíveis para utilizadores específicos ou para membros de grupos específicos. Nesses cenários, os utilizadores podem apropriar-se de ativos de dados registados dentro do catálogo e, em seguida, controlar a visibilidade dos ativos que possuem.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas na Edição Padrão do Catálogo de Dados Azure. A Free Edition não fornece capacidades de propriedade e limita a visibilidade do ativo de dados.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gerir a propriedade de ativos de dados
Por padrão, os ativos de dados registados no Data Catalog não são propriedade. Qualquer utilizador com permissão para aceder ao catálogo pode descobrir e anotar estes ativos. Os utilizadores podem apropriar-se de ativos de dados não detidos e, em seguida, limitar a visibilidade dos ativos que possuem.

Quando um ativo de dados no Data Catalog é propriedade, apenas os utilizadores autorizados pelos proprietários podem descobrir o ativo e ver os seus metadados, e apenas os proprietários podem apagar o ativo do catálogo.

> [!NOTE]
> A propriedade no Data Catalog afeta apenas os metadados armazenados no catálogo. A propriedade não confere permissões na fonte de dados subjacente.
>
>

### <a name="take-ownership"></a>Obter propriedade
Os utilizadores podem apropriar-se dos ativos de dados selecionando a opção **Take Ownership** no portal Data Catalog. Não são necessárias permissões especiais para tomar posse de um ativo de dados não possuído. Qualquer utilizador pode apropriar-se de um ativo de dados não possuído.

### <a name="add-owners-and-co-owners"></a>Adicione proprietários e coproprietários
Se um ativo de dados já for propriedade, outros utilizadores não podem simplesmente tomar posse. Devem ser adicionados como coproprietários por um proprietário existente. Qualquer proprietário pode adicionar utilizadores adicionais ou grupos de segurança como coproprietários.

> [!NOTE]
> É uma boa prática ter pelo menos dois indivíduos como proprietários para qualquer ativo de dados de propriedade.
>
>

### <a name="remove-owners"></a>Remover proprietários
Assim como qualquer proprietário de ativo pode adicionar coproprietários, qualquer proprietário de ativo pode remover qualquer coproprietário.

Um proprietário de ativos que se retira como proprietário já não pode gerir o ativo. Se o proprietário do ativo se retirar como proprietário e não houver outros coproprietários, o ativo reverte para um estado não propriedadedo.

## <a name="control-visibility"></a>Controlar a visibilidade
Os proprietários de ativos de dados podem controlar a visibilidade dos ativos de dados que possuem. Para restringir a visibilidade como padrão, onde todos os utilizadores do Data Catalog podem descobrir e visualizar o ativo de dados, o proprietário do ativo pode alternar a definição de visibilidade de **Todos** para **Proprietários & Estes Utilizadores** nas propriedades para o ativo. Os proprietários podem então adicionar utilizadores específicos e grupos de segurança.

> [!NOTE]
> Sempre que possível, as permissões de propriedade e visibilidade dos ativos devem ser atribuídas a grupos de segurança e não a utilizadores individuais.
>
>

## <a name="catalog-administrators"></a>Administradores de catálogo
Os administradores do Data Catalog são implicitamente coproprietários de todos os ativos do catálogo. Os proprietários de ativos não podem remover a visibilidade dos administradores, e os administradores podem gerir a propriedade e visibilidade de todos os ativos de dados no catálogo.

## <a name="summary"></a>Resumo
O modelo de crowdsourcing do Data Catalog para metadados e descoberta de ativos de dados permite que todos os utilizadores do catálogo contribuam e descubram. A Standard Edition of Data Catalog destina-se à propriedade e gestão para limitar a visibilidade e utilização de ativos específicos de dados.
