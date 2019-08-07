---
title: Gerenciar ativos de dados no catálogo de dados do Azure
description: O artigo realça como controlar a visibilidade e a propriedade de ativos de dados registrados no catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736331"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gerenciar ativos de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
O catálogo de dados do Azure foi projetado para descoberta de fonte de dados, para que você possa descobrir e entender facilmente as fontes de dados necessárias para realizar a análise e tomar decisões. Esses recursos de descoberta têm o maior impacto quando você e outros usuários podem encontrar e entender a mais ampla variedade de fontes de dados disponíveis. Com esses elementos em mente, o comportamento padrão do catálogo de dados é que todas as fontes de dados registradas sejam visíveis e detectáveis por todos os usuários do catálogo.

O catálogo de dados não dá acesso aos dados em si. O acesso a dados é controlado pelo proprietário da fonte de dados. Com o catálogo de dados, você pode descobrir fontes de dados e exibir os metadados relacionados às fontes registradas no catálogo.

No entanto, pode haver situações em que as fontes de dados só devem ser visíveis para usuários específicos ou para membros de grupos específicos. Nesses cenários, os usuários podem assumir a propriedade dos ativos de dados registrados no catálogo e, em seguida, controlar a visibilidade dos ativos que eles possuem.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas na edição Standard do catálogo de dados do Azure. A edição gratuita não fornece recursos para propriedade e restrição da visibilidade de ativos de dados.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gerenciar a propriedade de ativos de dados
Por padrão, os ativos de dados registrados no catálogo de dados não são proprietários. Qualquer usuário com permissão para acessar o catálogo pode descobrir e anotar esses ativos. Os usuários podem apropriar-se de ativos de dados sem proprietário e, em seguida, limitar a visibilidade dos ativos que possuem.

Quando um ativo de dados no catálogo de dados é propriedade, somente os usuários autorizados pelos proprietários podem descobrir o ativo e exibir seus metadados, e somente os proprietários podem excluir o ativo do catálogo.

> [!NOTE]
> A propriedade no catálogo de dados afeta apenas os metadados armazenados no catálogo. A propriedade não concede nenhuma permissão na fonte de dados subjacente.
>
>

### <a name="take-ownership"></a>Obter propriedade
Os usuários podem assumir a propriedade dos ativos de dados selecionando a opção apropriar- **se** no portal do catálogo de dados. Nenhuma permissão especial é necessária para apropriar-se de um ativo de dados sem proprietário. Qualquer usuário pode apropriar-se de um ativo de dados sem proprietário.

### <a name="add-owners-and-co-owners"></a>Adicionar proprietários e coproprietários
Se um ativo de dados já tiver sido possuído, outros usuários não poderão simplesmente assumir a propriedade. Eles devem ser adicionados como coproprietários por um proprietário existente. Qualquer proprietário pode adicionar usuários ou grupos de segurança adicionais como coproprietários.

> [!NOTE]
> É uma prática recomendada ter pelo menos duas pessoas como proprietários para qualquer ativo de dados de propriedade.
>
>

### <a name="remove-owners"></a>Remover proprietários
Assim como qualquer proprietário do ativo pode adicionar coproprietários, qualquer proprietário do ativo pode remover qualquer coadministrador.

Um proprietário de ativo que se remove como um proprietário não pode mais gerenciar o ativo. Se o proprietário do ativo se remover como um proprietário e não houver outros coproprietários, o ativo será revertido para um estado sem proprietário.

## <a name="control-visibility"></a>Visibilidade de controle
Os proprietários de ativos de dados podem controlar a visibilidade dos ativos de dados que eles possuem. Para restringir a visibilidade como o padrão, em que todos os usuários do catálogo de dados podem descobrir e exibir o ativo de dados, o proprietário do ativo pode alternar a configuração de visibilidade de **todos** para **proprietários & esses usuários** nas propriedades do ativo. Os proprietários podem então adicionar usuários e grupos de segurança específicos.

> [!NOTE]
> Sempre que possível, as permissões de propriedade e visibilidade de ativos devem ser atribuídas a grupos de segurança e não a usuários individuais.
>
>

## <a name="catalog-administrators"></a>Administradores de catálogo
Os administradores do catálogo de dados são coproprietários implicitamente de todos os ativos no catálogo. Os proprietários de ativos não podem remover a visibilidade dos administradores, e os administradores podem gerenciar a propriedade e a visibilidade de todos os ativos de dados no catálogo.

## <a name="summary"></a>Resumo
O modelo de crowdsourcing do catálogo de dados para metadados e a descoberta de ativos de dados permite que todos os usuários do catálogo contribuam e descubram. A edição padrão do catálogo de dados é projetada para propriedade e gerenciamento para limitar a visibilidade e o uso de ativos de dados específicos.
