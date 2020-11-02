---
title: Registar-se com as informações do Parceiro CSP na Cloudyn no Azure
description: Saiba mais detalhes sobre o processo de registo utilizado pelos parceiros para integrar os seus clientes no portal do Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543326"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registar-se com o programa de Parceiro CSP e ver dados de custos

Como parceiro CSP e utilizador registado do Cloudyn, pode visualizar e analisar os seus gastos com a cloud no Cloudyn. O [Azure Cost Management está disponível de forma nativa para os parceiros diretos](../costs/get-started-partners.md) que tenham integrado os seus clientes num Contrato de Cliente Microsoft e tenham comprado um Plano do Azure.

O registo concede acesso ao portal Cloudyn. Este guia de introdução detalha o processo de registo necessário para criar uma subscrição de avaliação do Cloudyn e iniciar sessão no portal Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurar o acesso CSP indireto no Cloudyn

Por predefinição, a API do Centro de Parceiros só está acessível para CSPs diretos. No entanto, um fornecedor CSP direto pode configurar o acesso para os respetivos parceiros ou clientes CSP indiretos utilizando grupos de entidades no Cloudyn.

Para ativar o acesso para parceiros ou clientes CSP indiretos, conclua os passos seguintes para segmentar os dados CSP indiretos utilizando grupos de entidades do Cloudyn. Em seguida, atribua as permissões de utilizador adequadas aos grupos de entidades.

1. Crie um grupo de entidades com as informações em [Criar entidades](tutorial-user-access.md#create-and-manage-entities).
2. Siga os passos indicados em [Atribuir subscrições a Entidades de Custo](https://www.youtube.com/watch?v=d9uTWSdoQYo). Associe a conta e as respetivas subscrições do Azure do cliente CSP indireto à entidade que criou anteriormente.
3. Siga os passos indicados em [Criar um utilizador com acesso de administrador](tutorial-user-access.md#create-a-user-with-admin-access) para criar uma conta de utilizador com acesso de Administrador. Em seguida, certifique-se de que a conta de utilizador tem acesso de administrador às entidades específicas que criou anteriormente para a conta indireta.

Os parceiros CSP indiretos iniciam sessão no portal Cloudyn utilizando as contas que criou para os mesmos.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, utilizou as suas informações CSP para efetuar o registo na Cloudyn. Também iniciou sessão no portal Cloudyn e começou a ver os dados dos custos. Para saber mais sobre a Cloudyn, continue para o tutorial da Cloudyn.

> [!div class="nextstepaction"]
> [Rever a utilização e os custos](tutorial-review-usage.md)