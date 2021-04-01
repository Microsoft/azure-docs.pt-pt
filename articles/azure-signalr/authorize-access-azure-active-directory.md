---
title: Autorizar o acesso com o Azure Active Directory
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Serviço Azure SignalR utilizando o Azure Ative Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797581"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autorizar o acesso aos recursos do Serviço Azure SignalR utilizando o Diretório Ativo Azure
O Serviço Azure SignalR suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos aos recursos do Serviço Azure SignalR. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação. Para saber mais sobre papéis e atribuições de papéis, consulte [compreender os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Descrição Geral
Quando um diretor de segurança (uma aplicação) tenta aceder a um recurso do Serviço Azure SignalR, o pedido deve ser autorizado. Com o Azure AD, o acesso a um recurso é um processo em duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para solicitar um token é `https://signalr.azure.com/` .
 2. Em seguida, o token é passado como parte de um pedido ao Serviço Azure SignalR para autorizar o acesso ao recurso especificado.

O passo de autenticação requer que um pedido de candidatura contenha um token de acesso OAuth 2.0 no tempo de execução. Se o seu servidor hub estiver a funcionar dentro de uma entidade Azure, como um Azure VM, um conjunto de escala de máquina virtual ou uma aplicação Azure Function, pode usar uma identidade gerida para aceder aos recursos. Para obter forma de autenticar pedidos feitos por identidade gerida ao Serviço Azure SignalR, consulte [autenticar o acesso aos recursos do Serviço Azure SignalR com o Azure Ative Directory e identidades geridas para a Azure Resources](authenticate-managed-identity.md). 

O passo de autorização requer que uma ou mais funções do RBAC sejam atribuídas ao diretor de segurança. O Serviço Azure SignalR fornece funções RBAC que englobam conjuntos de permissões para recursos Azure SignalR. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para obter mais informações sobre as funções do RBAC, consulte [as funções incorporadas do Azure para o Serviço Azure SignalR](#azure-built-in-roles-for-azure-signalr-service). 

O SignalR Hub Server que não esteja a funcionar dentro de uma entidade Azure também pode autorizar com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar pedidos de recursos do Serviço Azure SignalR, consulte [autenticar o acesso ao Serviço Azure SignalR com Azure AD a partir de uma aplicação.](authenticate-application.md) 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Funções Azure Built-in para O Serviço Azure SignalR

- [Signalr App Server]
- [Signalr Service Reader]
- [Dono do Serviço SignalR]

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções do RBAC para direitos de acesso
O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através [do controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). O Serviço Azure SignalR define um conjunto de funções incorporadas Azure que englobam conjuntos comuns de permissões usadas para aceder ao Serviço Azure SignalR e também pode definir funções personalizadas para aceder ao recurso.

Quando um papel rbac é atribuído a um diretor de segurança Azure, Azure concede acesso a esses recursos para esse chefe de segurança. O acesso pode ser analisado ao nível de subscrição, ao grupo de recursos ou a qualquer recurso do Serviço Azure SignalR. Um diretor de segurança Azure AD pode ser um utilizador, ou uma aplicação, ou uma [identidade gerida para os recursos da Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Funções incorporadas para o Serviço Azure SignalR
A Azure fornece as seguintes funções incorporadas Azure para autorizar o acesso ao recurso Azure SignalR Service utilizando Azure AD e OAuth:

### <a name="signalr-app-server"></a>Servidor de aplicativo SignalR

Use esta função para dar acesso a Obter uma chave de acesso temporário para assinar fichas de cliente.

### <a name="signalr-serverless-contributor"></a>Contribuinte sem servidor SignalR

Utilize esta função para dar acesso a Obter um símbolo de cliente do Serviço Azure SignalR diretamente.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos relacionados:

- [Autenticar uma aplicação com Azure AD para aceder ao Serviço Azure SignalR](authenticate-application.md)
- [Autenticar uma identidade gerida com Azure AD para aceder ao Serviço Azure SignalR](authenticate-managed-identity.md)