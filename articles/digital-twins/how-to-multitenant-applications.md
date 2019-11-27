---
title: Habilitar aplicativos multilocatários – Azure digital gêmeos | Microsoft Docs
description: Como configurar aplicativos de Azure Active Directory multilocatário para o gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 65e1fa3fe371766566eeeaaa2d33479ea0243d61
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383342"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Habilitar aplicativos multilocatários com o gêmeos digital do Azure

Os desenvolvedores de soluções que criam no Azure digital gêmeos podem descobrir que desejam dar suporte a vários clientes com um único serviço ou solução. Na verdade, os aplicativos *multilocatário* estão entre as configurações mais comuns do Azure digital gêmeos.

Este documento descreve como configurar um aplicativo gêmeos digital do Azure para dar suporte a vários locatários e clientes do Azure Active Directory.

## <a name="multitenancy"></a>Multilocação

Um recurso *multilocatário* é uma instância provisionada única que dá suporte a vários clientes. Cada cliente tem seus próprios dados e privilégios independentes. A experiência de cada cliente é isolada entre si, de modo que sua "exibição" do aplicativo seja distinta.

Para saber mais sobre multilocação, leia [aplicativos multilocatários no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Cenário do problema

Nesse cenário, considere um desenvolvedor que cria uma solução de gêmeos (**desenvolvedor**) do Azure digital e um cliente que usa essa solução (**cliente**):

- O **desenvolvedor** tem uma assinatura do Azure com um locatário Azure Active Directory.
- O **desenvolvedor** implanta uma instância de gêmeos digital do Azure em sua assinatura do Azure. Azure Active Directory criou automaticamente uma entidade de serviço no locatário do Azure Active Directory do **desenvolvedor**.
- Os usuários no locatário Azure Active Directory do **desenvolvedor**podem [adquirir tokens OAuth 2,0](./security-authenticating-apis.md) do serviço gêmeos do Azure digital.
- Agora, o **desenvolvedor** cria um aplicativo móvel que se integra diretamente com as APIs de gerenciamento de gêmeos digital do Azure.
- O **desenvolvedor** permite que o **cliente** use o aplicativo móvel.
- O **cliente** deve estar autorizado a usar a API de gerenciamento de gêmeos digital do Azure no aplicativo do **desenvolvedor**.

O problema:

- Quando o **cliente** faz logon no aplicativo do **desenvolvedor**, o aplicativo não pode adquirir tokens para que os usuários do **cliente**se autentiquem com as APIs de gerenciamento de gêmeos digital do Azure.
- Uma exceção é emitida no Azure Active Directory indicando que o Azure digital gêmeos não é reconhecido no diretório do **cliente**.

## <a name="problem-solution"></a>Solução de problema

Para resolver o cenário do problema anterior, as ações a seguir são necessárias para criar uma entidade de serviço do Azure digital gêmeos no locatário do Azure Active Directory do **cliente**:

- Se o **cliente** ainda não tiver uma assinatura do Azure com um locatário Azure Active Directory:

  - O administrador de locatários Azure Active Directory do **cliente**deve adquirir uma [assinatura paga conforme o uso do Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - O administrador de locatários Azure Active Directory do **cliente**deve [vincular seu locatário à nova assinatura](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- No [portal do Azure](https://portal.azure.com), o administrador de locatários do Azure Active Directory do **cliente**executa as seguintes etapas:

  1. Procure **assinaturas** no campo de pesquisa superior do Azure. Selecione **Subscrições**.
  1. Selecione a assinatura que tem o locatário Azure Active Directory a ser usado no aplicativo do **desenvolvedor**.

     [![Azure Active Directory assinaturas](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Selecione **provedores de recursos**.
  1. Procure **Microsoft. IoTSpaces**.
  1. Selecione **Registar**.

     [provedores de recursos de Azure Active Directory ![](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como usar funções definidas pelo usuário com o gêmeos digital do Azure, leia [como criar funções definidas pelo usuário do Azure digital gêmeos](./how-to-user-defined-functions.md).

- Para saber como usar o controle de acesso baseado em função para proteger ainda mais o aplicativo com as atribuições de função, leia [como criar e gerenciar o controle de acesso baseado em função gêmeos do Azure digital](./security-create-manage-role-assignments.md).
