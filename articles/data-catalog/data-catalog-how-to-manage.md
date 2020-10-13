---
title: Gerir ativos de dados no Catálogo de Dados Azure
description: O artigo destaca como controlar a visibilidade e a propriedade dos ativos de dados registados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 9d035417b613c81487c493bde90cdcc461f04412
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081156"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gerir ativos de dados no Catálogo de Dados Azure
## <a name="introduction"></a>Introdução
O Azure Data Catalog foi concebido para a descoberta de fontes de dados, para que possa facilmente descobrir e compreender as fontes de dados de que necessita para realizar análises e tomar decisões. Estas capacidades de descoberta são o maior impacto quando você e outros utilizadores podem encontrar e entender a mais ampla gama de fontes de dados disponíveis. Com estes elementos em mente, o comportamento padrão do Catálogo de Dados é para que todas as fontes de dados registadas sejam visíveis e detetáveis por todos os utilizadores do catálogo.

O Catálogo de Dados não lhe dá acesso aos dados em si. O acesso aos dados é controlado pelo proprietário da fonte de dados. Com o Data Catalog, pode descobrir fontes de dados e ver os metadados relacionados com as fontes registadas no catálogo.

No entanto, pode haver situações em que as fontes de dados só devem ser visíveis para utilizadores específicos ou para membros de grupos específicos. Nesses cenários, os utilizadores podem apropriar-se de ativos de dados registados dentro do catálogo e, em seguida, controlar a visibilidade dos ativos que possuem.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas na Edição Standard do Catálogo de Dados Azure. A Edição Livre não fornece capacidades de propriedade e de restringir a visibilidade do dado-activo.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gerir a propriedade dos ativos de dados
Por padrão, os ativos de dados registados no Catálogo de Dados não são desmedidos. Qualquer utilizador com permissão para aceder ao catálogo pode descobrir e anotar estes ativos. Os utilizadores podem apropriar-se de ativos de dados não proprietários e, em seguida, limitar a visibilidade dos ativos que possuem.

Quando um ativo de dados no Data Catalog é propriedade, apenas os utilizadores que são autorizados pelos proprietários podem descobrir o ativo e ver os seus metadados, e apenas os proprietários podem eliminar o ativo do catálogo.

> [!NOTE]
> A propriedade no Catálogo de Dados afeta apenas os metadados armazenados no catálogo. A propriedade não confere quaisquer permissões na fonte de dados subjacente.
>
>

### <a name="take-ownership"></a>Obter propriedade
Os utilizadores podem apropriar-se de ativos de dados selecionando a opção **Tomar Propriedade** no portal do Catálogo de Dados. Não são necessárias permissões especiais para se apropriar de um ativo de dados não desnuda. Qualquer utilizador pode apropriar-se de um ativo de dados não despropotado.

### <a name="add-owners-and-co-owners"></a>Adicionar proprietários e coproprietários
Se um ativo de dados já for propriedade, outros utilizadores não podem simplesmente tomar posse. Devem ser adicionados como coproprietários por um proprietário existente. Qualquer proprietário pode adicionar utilizadores adicionais ou grupos de segurança como coproprietários.

> [!NOTE]
> É uma boa prática ter pelo menos dois indivíduos como proprietários para qualquer ativo de dados de propriedade.
>
>

### <a name="remove-owners"></a>Remover proprietários
Assim como qualquer proprietário de ativos pode adicionar coproprietários, qualquer proprietário de ativo pode remover qualquer coproprietário.

Um proprietário de ativos que se remova como proprietário já não pode gerir o ativo. Se o proprietário do ativo se remover como proprietário e não houver outros coproprietários, o ativo reverte para um estado não-dono.

## <a name="control-visibility"></a>Visibilidade de controlo
Os proprietários de ativos de dados podem controlar a visibilidade dos ativos de dados que possuem. Para restringir a visibilidade como o padrão, onde todos os utilizadores do Catálogo de Dados podem descobrir e visualizar o ativo dos dados, o proprietário do ativo pode alternar a definição de visibilidade de **Todos** para **Os Proprietários & Estes Utilizadores** nas propriedades do ativo. Os proprietários podem então adicionar utilizadores específicos e grupos de segurança.

> [!NOTE]
> Sempre que possível, as permissões de propriedade e visibilidade dos bens devem ser atribuídas a grupos de segurança e não a utilizadores individuais.
>
>

## <a name="catalog-administrators"></a>Administradores de catálogos
Os administradores do Catálogo de Dados são implicitamente coproprietários de todos os ativos do catálogo. Os proprietários de ativos não podem remover a visibilidade dos administradores, e os administradores podem gerir a propriedade e a visibilidade de todos os ativos de dados do catálogo.

## <a name="summary"></a>Resumo
O modelo de crowdsourcing do Data Catalog para metadados e descoberta de ativos de dados permite que todos os utilizadores do catálogo contribuam e descubram. A Standard Edition of Data Catalog foi concebida para a propriedade e gestão para limitar a visibilidade e utilização de ativos de dados específicos.
