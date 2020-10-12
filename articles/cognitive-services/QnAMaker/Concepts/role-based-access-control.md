---
title: Colaborar com outros - QnA Maker
description: ''
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 967fdae49f904f6c1cb450b637a8dbc5c481b135
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776889"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Colaborar com outros autores e editores

Colabore com outros autores e editores utilizando o controlo de acesso baseado em funções (RBAC) colocado no seu recurso QnA Maker.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>O acesso é fornecido no recurso QnA Maker

Todas as permissões são controladas pelas permissões colocadas no recurso QnA Maker. Estas permissões alinham-se para ler, escrever, publicar e aceder plenamente.

Esta funcionalidade RBAC inclui:
* O Azure Ative Directory (AAD) é 100% compatível com a autenticação baseada em chaves para proprietários e contribuintes. Os clientes podem utilizar a autenticação baseada em chaves ou a autenticação baseada no RBAC nos seus pedidos.
* Adicione rapidamente autores e editores a todas as bases de conhecimento no recurso porque o controlo está ao nível dos recursos, não ao nível da base de conhecimento.

## <a name="access-is-provided-by-a-defined-role"></a>O acesso é fornecido por uma função definida

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Fluxo de autenticação

O diagrama seguinte mostra o fluxo, do ponto de vista do autor, para iniciar a sessão no portal QnA Maker e utilizar as APIs de autoria.

> [!div class="mx-imgBorder"]
> ![O diagrama seguinte mostra o fluxo, do ponto de vista do autor, para iniciar a sessão no portal QnA Maker e utilizar as APIs de autoria.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Passos|Descrição|
|--|--|
|1|Portal adquire ficha para recurso QnA Maker.|
|2|Portal Chama o fabricante QnA apropriado autor de API (APIM) passando o token em vez de teclas.|
|3|A API do Fabricante QNA valida o token.|
|4 |A API do Fabricante QNA chama o Serviço QnAMaker.|

Se pretender ligar para as [APIs de autoria,](../How-To/collaborate-knowledge-base.md)saiba mais sobre como configurar a autenticação.

## <a name="authenticate-by-qna-maker-portal"></a>Autenticado por portal QnA Maker

Se você autore e colabore usando o portal QnA Maker, depois de [adicionar o papel apropriado ao recurso para um colaborador,](../How-To/collaborate-knowledge-base.md)o portal QnA Maker gere todas as permissões de acesso.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Autenticado por APIs e SDKs do Criador de QnA

Se você autorizar e colaborar usando as APIs, seja através do REST ou dos SDKs, precisa [de criar um principal de serviço](../../authentication.md#assign-a-role-to-a-service-principal) para gerir a autenticação.

## <a name="next-step"></a>Passo seguinte

* Desenhe uma base de conhecimento para [idiomas](design-language-culture.md) e para aplicações de [clientes](integration-with-other-applications.md)
