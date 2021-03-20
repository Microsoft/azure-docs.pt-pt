---
title: Gerir contas de programadores utilizando grupos na Azure API Management
titleSuffix: Azure API Management
description: Saiba como gerir contas de programadores utilizando grupos na Azure API Management. Crie grupos e, em seguida, associe-os a produtos ou desenvolvedores.
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
ms.openlocfilehash: ea674981036b4be292329a4b30b43180ed26d642
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92092788"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e utilizar grupos para gerir contas de programador na Gestão de API do Azure

Na API Management, os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Os produtos são primeiro tornados visíveis para grupos, e depois os desenvolvedores nesses grupos podem ver e subscrever os produtos que estão associados aos grupos. 

A Gestão de API tem os seguintes grupos de sistema imutáveis:

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido de grupos externos em inquilinos do Azure Active Directory associados][leverage external groups in associated Azure Active Directory tenants]. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Este guia mostra como os administradores de uma instância de Gestão da API podem adicionar novos grupos e associá-los a produtos e desenvolvedores.

Além de criar e gerir grupos no portal dos editores, pode criar e gerir os seus grupos utilizando a entidade do [Grupo](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) API Management REST.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tarefas completas neste artigo: [Criar uma instância de Gestão API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Criar um grupo

Esta secção mostra como adicionar um novo grupo à sua conta de Gestão API.

1. Selecione o **separador Grupos** à esquerda do ecrã.
2. Clique **+Adicionar**.
3. Insira um nome único para o grupo e uma descrição opcional.
4. Prima **Criar**.

    ![Adicionar um novo grupo](./media/api-management-howto-create-groups/groups001.png)

Uma vez criado o grupo, é adicionado à lista **de Grupos.** <br/>Para editar o **Nome** ou **Descrição** do grupo, clique no nome do grupo e **definições**.<br/>Para eliminar o grupo, clique no nome do grupo e prima **Delete**.

Agora que o grupo é criado, pode ser associado a produtos e desenvolvedores.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Associar um grupo a um produto

1. Selecione o separador **Produtos** para a esquerda.
2. Clique no nome do produto pretendido.
3. Controlo **de acesso à imprensa**.
4. Clique **+ Adicionar grupo**.

    ![Screenshot que realça o botão de grupo Add.](./media/api-management-howto-create-groups/groups002.png)
5. Selecione o grupo que pretende adicionar.

    ![Screenshot que mostra o grupo selecionado e realça o botão Select.](./media/api-management-howto-create-groups/groups003.png)

    Para remover um grupo do produto, clique em **Eliminar**.

    ![Eliminar um grupo](./media/api-management-howto-create-groups/groups004.png)

Uma vez que um produto é associado a um grupo, os desenvolvedores desse grupo podem ver e subscrever o produto.

> [!NOTE]
> Para adicionar grupos de Diretório Ativo Azure, consulte [como autorizar contas de programadores utilizando o Azure Ative Directory in Azure API Management](api-management-howto-aad.md).

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Associar grupos com desenvolvedores

Esta secção mostra como associar grupos com membros.

1. Selecione o **separador Grupos** à esquerda do ecrã.
2. Selecione **Membros**.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups005.png)
3. Prima **+Adicionar** e selecione um membro.

    ![Screenshot que realça o botão Adicionar, o utilizador selecionado e o botão Select.](./media/api-management-howto-create-groups/groups006.png)
4. **Seleção de imprensa**.

Uma vez que a associação é adicionada entre o desenvolvedor e o grupo, você pode vê-lo no **separador Utilizadores.**

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes

* Uma vez que um desenvolvedor é adicionado a um grupo, eles podem ver e subscrever os produtos associados a esse grupo. Para mais informações, consulte [Como criar e publicar um produto na Azure API Management,][How create and publish a product in Azure API Management]
* Além de criar e gerir grupos no portal dos editores, pode criar e gerir os seus grupos utilizando a entidade do [Grupo](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) API Management REST.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
