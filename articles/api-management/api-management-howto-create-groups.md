---
title: Gerir contas de programador utilizar grupos na gestão de API do Azure | Documentos da Microsoft
description: Saiba como gerir contas de programador através de grupos na gestão de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658470"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e utilizar grupos para gerir contas de programador na API Management do Azure

Na API Management, os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Produtos primeiro ficam visíveis para os grupos e, em seguida, os desenvolvedores desses grupos podem ver e subscrever os produtos que estão associados aos grupos. 

A Gestão de API tem os seguintes grupos de sistema imutáveis:

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tiram partido dos grupos externos em inquilinos do Azure Active Directory associados][leverage external groups in associated Azure Active Directory tenants]. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Este guia mostra como os administradores de uma instância de gestão de API podem adicionar novos grupos e associá-las com os produtos e desenvolvedores.

Além de criar e gerir grupos no portal do publicador, pode criar e gerir os seus grupos com a API de REST de gestão de API [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entidade.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Concluir as tarefas neste artigo: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Criar um grupo

Esta secção mostra como adicionar um novo grupo à sua conta de gestão de API.

1. Selecione o **grupos** separador à esquerda do ecrã.
2. Clique em **+ adicionar**.
3. Introduza um nome exclusivo para o grupo e uma descrição opcional.
4. Prima **Criar**.

    ![Adicionar um novo grupo](./media/api-management-howto-create-groups/groups001.png)

Assim que o grupo for criado, é adicionado para o **grupos** lista. <br/>Para editar a **Name** ou **Descrição** do grupo, clique no nome do grupo e **definições**.<br/>Para eliminar o grupo, clique no nome do grupo e prima **eliminar**.

Agora que o grupo é criado, ele pode ser associado com produtos e desenvolvedores.

## <a name="associate-group-product"> </a>Associar um grupo de um produto

1. Selecione o **produtos** separador à esquerda.
2. Clique no nome do produto desejado.
3. Prima **controlo de acesso**.
4. Clique em **+ adicionar grupo**.

    ![Associar um grupo de um produto](./media/api-management-howto-create-groups/groups002.png)
5. Selecione o grupo que pretende adicionar.

    ![Associar um grupo de um produto](./media/api-management-howto-create-groups/groups003.png)

    Para remover um grupo de produto, clique em **eliminar**.

    ![Eliminar um grupo](./media/api-management-howto-create-groups/groups004.png)

Assim que um produto estiver associado um grupo, os desenvolvedores nesse grupo podem ver e subscrever o produto.

> [!NOTE]
> Para adicionar grupos do Active Directory do Azure, veja [como autorizar contas de programador através do Azure Active Directory na gestão de API do Azure](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Associar grupos a programadores

Esta secção mostra como associar grupos a membros.

1. Selecione o **grupos** separador à esquerda do ecrã.
2. Selecione **membros**.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups005.png)
3. Prima **+ adicionar** e selecione um membro.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups006.png)
4. Prima **selecione**.

Assim que a associação é adicionada entre o desenvolvedor e o grupo, pode vê-la na **utilizadores** separador.

## <a name="next-steps"> </a>Passos seguintes

* Assim que um desenvolvedor é adicionado a um grupo, pode ver e subscrever os produtos associados a esse grupo. Para obter mais informações, consulte [como criar e publicar um produto na gestão de API do Azure][How create and publish a product in Azure API Management],
* Além de criar e gerir grupos no portal do publicador, pode criar e gerir os seus grupos com a API de REST de gestão de API [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entidade.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
