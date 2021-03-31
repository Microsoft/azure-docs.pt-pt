---
title: Revisões na Azure API Management | Microsoft Docs
description: Conheça o conceito de revisões na Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403243"
---
# <a name="revisions-in-azure-api-management"></a>Revisões na Gestão AZURE API

As revisões permitem-lhe escrutinar as suas APIs de forma controlada e segura. Quando quiser fazer alterações, crie uma nova revisão. Em seguida, pode editar e testar a API sem perturbar os seus consumidores de API. Quando estiver pronto, faça a sua revisão atual. Ao mesmo tempo, pode publicar opcionalmente uma entrada no registo de alteração, para manter os seus consumidores de API atualizados com o que mudou. O registo de alteração é publicado no seu portal de desenvolvedores.

> [!NOTE]
> O portal de desenvolvedores não está disponível no nível de Consumo.

Com revisões pode:

- Faça alterações seguras nas definições e políticas da API, sem perturbar a sua API de produção.
- Experimente as alterações antes de as publicar.
- Documente as alterações que faz, para que os seus desenvolvedores possam entender o que é novo.
- Recua se encontrares problemas.

[Começa com as revisões seguindo a nossa passagem.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Acesso a revisões específicas

Cada revisão à sua API pode ser acedida através de um URL especialmente formado. Apêndice `;rev={revisionNumber}` no final do seu URL API, mas antes da cadeia de consulta, para aceder a uma revisão específica dessa API. Por exemplo, pode utilizar este URL para aceder à revisão 3 da `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Por predefinição, cada revisão tem as mesmas definições de segurança que a revisão atual. Pode alterar deliberadamente as políticas para uma revisão específica se quiser ter uma segurança diferente aplicada para cada revisão. Por exemplo, é melhor adicionar uma [política de filtragem IP](./api-management-access-restriction-policies.md#RestrictCallerIPs) para impedir que os chamadores externos acedam a uma revisão que ainda está em desenvolvimento.

Uma revisão pode ser desativada, o que torna inacessível aos chamadores, mesmo que tentem aceder à revisão através do seu URL. Pode marcar uma revisão como offline utilizando o portal Azure. Se utilizar o PowerShell, pode utilizar o `Set-AzApiManagementApiRevision` cmdlet e definir o `Path` argumento para `$null` .

> [!NOTE]
> Sugerimos fazer revisões offline quando não as está a usar para testes.

## <a name="current-revision"></a>Revisão atual

Uma única revisão pode ser definida como a revisão *atual.* Esta revisão será a utilizada para todos os pedidos de API que não especificam um número de revisão explícito no URL. Pode voltar a uma revisão anterior definindo esta revisão como atual.

Pode definir uma revisão como corrente utilizando o portal Azure. Se utilizar o PowerShell, pode utilizar o `New-AzApiManagementApiRelease` cmdlet.

## <a name="revision-descriptions"></a>Descrições de revisão

Quando criar uma revisão, pode definir uma descrição para os seus próprios fins de rastreio. As descrições não são reproduzidas para os seus utilizadores da API.

Quando definir uma revisão como atual, também pode especificar opcionalmente uma nota de registo de alteração pública. O registo de alterações está incluído no portal do desenvolvedor para os utilizadores da API visualizar. Pode modificar a sua nota de registo de alteração utilizando o `Update-AzApiManagementApiRelease` cmdlet PowerShell.

## <a name="versions-and-revisions"></a>Versões e revisões

Versões e revisões são características distintas. Cada versão pode ter múltiplas revisões, tal como uma API não ver versão. Pode utilizar revisões sem usar versões ou ao contrário. Normalmente, as versões são usadas para separar versões API com alterações de rutura, enquanto as revisões podem ser usadas para alterações menores e não quebrando uma API.

Caso descubra que a sua revisão tem alterações de rutura, ou se pretender transformar formalmente a sua revisão numa versão beta/teste, pode criar uma versão a partir de uma revisão. Utilizando o portal Azure, clique na versão 'Criar versão a partir de Revisão' no menu de contexto de revisão no separador Revisões.
