---
title: Colabore com outros - QnA Maker
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: ef0823fc60424dc861ab70f4112e4689c1b97fb0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665984"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Colabore com outros autores e editores

Colabore com outros autores e editores utilizando o controlo de acesso baseado em papéis (RBAC) colocado no seu recurso QnA Maker.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>O acesso é fornecido no recurso QnA Maker

Todas as permissões são controladas pelas permissões colocadas no recurso QnA Maker. Estas permissões alinham-se para ler, escrever, publicar e ter acesso total.

Esta funcionalidade RBAC inclui:
* O Azure Ative Directory (AAD) é 100% compatível com a autenticação baseada em chaves para proprietários e contribuintes. Os clientes podem utilizar a autenticação baseada em chaves ou autenticação baseada em RBAC nos seus pedidos.
* Adicione rapidamente autores e editores a todas as bases de conhecimento do recurso porque o controlo está ao nível dos recursos, não ao nível da base de conhecimentos.

## <a name="access-is-provided-by-a-defined-role"></a>O acesso é fornecido por uma função definida

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Fluxo de autenticação

O diagrama seguinte mostra o fluxo, do ponto de vista do autor, para assinar no portal QnA Maker e usar as APIs de autoria.

> [!div class="mx-imgBorder"]
> ![O diagrama seguinte mostra o fluxo, do ponto de vista do autor, para assinar no portal QnA Maker e usar as APIs de autoria.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Passos|Descrição|
|--|--|
|1|Portal Adquire ficha para recurso QnA Maker.|
|2|Portal Chama o fabricante de QnA adequado que autoriza a API (APIM) passando o símbolo em vez de chaves.|
|3|A QnA Maker API valida o símbolo.|
|4 |QnA Maker API chama QnAMaker Service.|

Se pretender ligar para as APIs de autoria(.. /Como/colaborar-conhecimento-base.md), saiba mais sobre como configurar a autenticação.

## <a name="authenticate-by-qna-maker-portal"></a>Autenticado pelo portal QnA Maker

Se você é autor e colabora usando o portal QnA Maker, depois de [adicionar o papel apropriado ao recurso para um colaborador](../How-To/collaborate-knowledge-base.md), o portal QnA Maker gere todas as permissões de acesso.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Autenticação por APIs e SDKs fabricante sdKs da QnA

Se você é autor e colabora usando as APIs, seja através do REST ou dos SDKs, você precisa [criar um diretor](../../authentication.md#assign-a-role-to-a-service-principal) de serviço para gerir a autenticação.

## <a name="next-step"></a>Passo seguinte

* Conceber uma base de conhecimento para [idiomas](design-language-culture.md) e [aplicações de clientes](integration-with-other-applications.md)