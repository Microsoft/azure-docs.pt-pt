---
title: Gerir recursos de dados no catálogo de dados do Azure
description: O artigo destaca como controlar a visibilidade e a propriedade de recursos de dados registados no catálogo de dados do Azure.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 407e25b7bb1a2220448c9701bbef208195c50b63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65953107"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gerir recursos de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
O catálogo de dados do Azure destina-se a deteção de origem de dados, para que possam facilmente detetar e compreender as origens de dados que tem de executar a análise e tomar decisões. Estas capacidades de deteção certifique o maior impacto quando o utilizador e outros utilizadores possam localizar e compreender a mais ampla variedade de origens de dados disponíveis. Com esses elementos em mente, o comportamento predefinido do catálogo de dados é para todas as origens de dados registados ser visível para e detetável por todos os utilizadores do catálogo.

Catálogo de dados não lhe darão acesso aos dados propriamente ditos. Acesso a dados é controlado pelo proprietário da origem de dados. Com o catálogo de dados, pode detetar origens de dados e ver os metadados que estejam relacionados com as origens registadas no catálogo.

Podem existir situações, no entanto, em que origens de dados devem estar visíveis apenas a utilizadores específicos, ou a membros de grupos específicos. Em tais cenários, os utilizadores podem assumir a propriedade dos recursos de dados registados no catálogo e, em seguida, controlar a visibilidade dos recursos que possuem.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas na Standard Edition do catálogo de dados Azure. A edição gratuita não fornece capacidades de propriedade e visibilidade restrição do recurso de dados.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gerir a propriedade de recursos de dados
Por predefinição, os recursos de dados que estão registados no catálogo de dados são unowned. Qualquer utilizador com permissão para aceder ao catálogo pode detetar e anotar esses recursos. Os utilizadores podem assumir a propriedade de recursos de dados unowned e, em seguida, limitar a visibilidade dos recursos que possuem.

Quando um recurso de dados no catálogo de dados é de propriedade, apenas os utilizadores que estão autorizados pelos proprietários podem detetar o elemento e ver seus metadados, e apenas os proprietários podem eliminar o elemento do catálogo.

> [!NOTE]
> Propriedade no catálogo de dados afeta apenas os metadados armazenados no catálogo. Propriedade não a concessão de quaisquer permissões na origem de dados subjacente.
>
>

### <a name="take-ownership"></a>Obter propriedade
Os utilizadores podem assumir a propriedade de recursos de dados ao selecionar o **obter propriedade** opção no portal do catálogo de dados. Não existem permissões especiais são necessárias para assumir a propriedade de um ativo de dados unowned. Qualquer utilizador pode assumir a propriedade de um ativo de dados unowned.

### <a name="add-owners-and-co-owners"></a>Adicionar proprietários e os coproprietários
Se um recurso de dados já é propriedade, outros usuários simplesmente não é possível obter a propriedade. Eles devem ser adicionados como coproprietários pelo proprietário de uma existente. Qualquer proprietário pode adicionar mais utilizadores ou grupos de segurança, como os coproprietários.

> [!NOTE]
> É recomendável ter, pelo menos, dois indivíduos como proprietários para qualquer recurso de dados pertencentes à empresa.
>
>

### <a name="remove-owners"></a>Remover proprietários
Assim como qualquer proprietário do ativo pode adicionar coproprietários, qualquer proprietário do ativo pode remover qualquer coproprietário.

Um proprietário do recurso que remove si próprios como proprietário já não pode gerir o elemento. Se o proprietário do recurso remove próprios como proprietário e não há nenhum outros coproprietários, o elemento é revertida para um Estado de funcionamento unowned.

## <a name="control-visibility"></a>Visibilidade de controlo
Os proprietários do recurso de dados podem controlar a visibilidade dos ativos de dados que possuem. Para restringir a visibilidade como padrão, onde todos os utilizadores do catálogo de dados podem detetar e ver os recursos de dados, o proprietário do ativo pode alternar a definição de visibilidade da **todas as pessoas** ao **proprietários e estes utilizadores** no as propriedades para o elemento. Proprietários, em seguida, podem adicionar utilizadores e grupos de segurança específicos.

> [!NOTE]
> Sempre que possível, permissões de propriedade e visibilidade do elemento devem ser atribuídas a grupos de segurança e não para utilizadores individuais.
>
>

## <a name="catalog-administrators"></a>Administradores do catálogo
Os administradores do catálogo de dados são, implicitamente, os coproprietários de todos os ativos no catálogo. Os proprietários dos recursos não é possível remover a visibilidade dos administradores e os administradores podem gerir a propriedade e visibilidade para todos os recursos de dados no catálogo.

## <a name="summary"></a>Resumo
O modelo de crowdsourcing do catálogo de dados para a deteção de recursos de dados e metadados permite que todos os utilizadores do catálogo contribuir e descobrir. Edição Standard do catálogo de dados foi concebido para a gestão e de propriedade para limitar a visibilidade e a utilização de recursos de dados específico.
