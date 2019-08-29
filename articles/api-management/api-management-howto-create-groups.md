---
title: Gerenciar contas de desenvolvedor usando grupos no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como gerenciar contas de desenvolvedor usando grupos no gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: f83b91933e2aca4bf77fcab4a30f2123f4eb4ed0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073643"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e usar grupos para gerenciar contas de desenvolvedor no gerenciamento de API do Azure

Na API Management, os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Os produtos são disponibilizados primeiro para grupos e, em seguida, os desenvolvedores nesses grupos podem exibir e assinar os produtos associados aos grupos. 

A Gestão de API tem os seguintes grupos de sistema imutáveis:

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido de grupos externos em inquilinos do Azure Active Directory associados][leverage external groups in associated Azure Active Directory tenants]. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Este guia mostra como os administradores de uma instância de gerenciamento de API podem adicionar novos grupos e associá-los a produtos e desenvolvedores.

Além de criar e gerenciar grupos no portal do Publicador, você pode criar e gerenciar seus grupos usando a entidade de [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) API REST de gerenciamento de API.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as tarefas neste artigo: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Criar um grupo

Esta seção mostra como adicionar um novo grupo à sua conta de gerenciamento de API.

1. Selecione a guia **grupos** à esquerda da tela.
2. Clique em **+ Adicionar**.
3. Insira um nome exclusivo para o grupo e uma descrição opcional.
4. Prima **Criar**.

    ![Adicionar um novo grupo](./media/api-management-howto-create-groups/groups001.png)

Depois que o grupo é criado, ele é adicionado à lista de **grupos** . <br/>Para editar o **nome** ou a **Descrição** do grupo, clique no nome do grupo e **das configurações**.<br/>Para excluir o grupo, clique no nome do grupo e pressione **excluir**.

Agora que o grupo foi criado, ele pode ser associado a produtos e desenvolvedores.

## <a name="associate-group-product"> </a>Associar um grupo a um produto

1. Selecione a guia **produtos** à esquerda.
2. Clique no nome do produto desejado.
3. Pressione **controle de acesso**.
4. Clique em **+ Adicionar grupo**.

    ![Associar um grupo a um produto](./media/api-management-howto-create-groups/groups002.png)
5. Selecione o grupo que você deseja adicionar.

    ![Associar um grupo a um produto](./media/api-management-howto-create-groups/groups003.png)

    Para remover um grupo do produto, clique em **excluir**.

    ![Excluir um grupo](./media/api-management-howto-create-groups/groups004.png)

Depois que um produto é associado a um grupo, os desenvolvedores desse grupo podem exibir e assinar o produto.

> [!NOTE]
> Para adicionar grupos de Azure Active Directory, consulte [como autorizar contas de desenvolvedor usando Azure Active Directory no gerenciamento de API do Azure](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Associar grupos a desenvolvedores

Esta seção mostra como associar grupos a membros.

1. Selecione a guia **grupos** à esquerda da tela.
2. Selecione **Membros**.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups005.png)
3. Pressione **+ Adicionar** e selecione um membro.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups006.png)
4. Pressione **selecionar**.

Depois que a associação for adicionada entre o desenvolvedor e o grupo, você poderá exibi-lo na guia **usuários** .

## <a name="next-steps"> </a>Passos seguintes

* Depois que um desenvolvedor é adicionado a um grupo, ele pode exibir e assinar os produtos associados a esse grupo. Para obter mais informações, consulte [como criar e publicar um produto no gerenciamento de API do Azure][How create and publish a product in Azure API Management],
* Além de criar e gerenciar grupos no portal do Publicador, você pode criar e gerenciar seus grupos usando a entidade de [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) API REST de gerenciamento de API.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
