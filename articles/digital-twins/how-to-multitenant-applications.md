---
title: Ativar aplicações multiarrendatárias - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs
description: Como configurar aplicações multitenant Azure Ative Directory para Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264938"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Ativar aplicações multiarrendatárias com As Gémeas Digitais Azure

Os desenvolvedores de soluções que se baseiam em Azure Digital Twins podem descobrir que querem apoiar vários clientes com um único serviço ou solução. Na verdade, as aplicações *multiarrendatárias* estão entre as configurações mais comuns das Gémeas Digitais Azure.

Este documento descreve como configurar uma aplicação Azure Digital Twins para apoiar vários inquilinos e clientes do Azure Ative Directory.

## <a name="multitenancy"></a>Multitenancy

Um recurso *multiarrendatário* é um único caso que suporta vários clientes. Cada cliente tem os seus próprios dados e privilégios independentes. A experiência de cada cliente é isolada da experiência de cada cliente para que a sua "visão" da aplicação seja distinta.

Para saber mais sobre multitenancy, leia [Aplicações Multitenant em Azure.](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)

## <a name="problem-scenario"></a>Cenário de problema

Neste cenário, considere um construtor construindo uma solução Azure Digital Twins **(DEVELOPER)** e um cliente que usa essa solução **(CLIENTE):**

- **O DEVELOPER** tem uma subscrição Azure com um inquilino azure Ative Directory.
- **O DEVELOPER** implementa uma instância azure digital twins na sua subscrição Azure. O Azure Ative Directory criou automaticamente um diretor de serviço **sintetizar**o inquilina do Diretório Ativo do DEVELOPER.
- Os utilizadores do inquilino do **Diretório**Ativo Azure da DEVELOPER podem então [adquirir fichas OAuth 2.0](./security-authenticating-apis.md) do serviço Azure Digital Twins.
- **O DEVELOPER** cria agora uma aplicação móvel que se integra diretamente com as APIs de Gestão de Gémeos Digitais Azure.
- **O DEVELOPER** permite ao **CLIENTE** a utilização da aplicação móvel.
- **O CLIENTE** deve ser autorizado a utilizar a API de Gestão de Gémeos Digitais Azure dentro da aplicação do **DEVELOPER.**

O problema:

- Quando o **CLIENTE** entra na aplicação do **DEVELOPER,** a aplicação não pode adquirir fichas para os utilizadores do **CLIENTE**autenticarem com as APIs de Gestão de Gémeos Digitais Azure.
- Uma exceção é emitida no Diretório Ativo Azure indicando que a Azure Digital Twins não é reconhecida no diretório do **CLIENTE.**

## <a name="problem-solution"></a>Solução problemática

Para resolver o cenário de problema anterior, são necessárias as seguintes ações para criar um serviço Azure Digital Twins principal dentro do inquilino do Diretório Ativo do **CLIENTE:**

- Se **o CLIENTE** ainda não tiver uma subscrição Azure com um inquilino do Azure Ative Directory:

  - **Cliente's**Azure Ative Directory inquilino administrador deve adquirir uma [subscrição de Azure pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **Cliente's**Azure Ative Directory inquilino administrador então deve [ligar o seu inquilino com a nova subscrição](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- No [portal Azure,](https://portal.azure.com)o administrador de inquilino sazure ative do **CLIENTE**toma as seguintes etapas:

  1. Procure **por Subscrições** no campo de pesquisa top Azure. Selecione **Subscrições**.
  1. Selecione a subscrição que tem o inquilino do Diretório Ativo Azure para ser usado na aplicação do **DEVELOPER.**

     [![Assinaturas de Diretório Ativo Azure](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Selecione **Fornecedores de Recursos**.
  1. Pesquisa por **Microsoft.IoTSpaces**.
  1. Selecione **Registar**.

     [![Fornecedores de recursos de Diretório Ativo Azure](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como usar funções definidas pelo utilizador com as Gémeas Digitais Azure, leia [como criar funções definidas](./how-to-user-defined-functions.md)pelos utilizadores do Azure Digital Twins.

- Para aprender a usar o controlo de acesso baseado em papéis para garantir ainda mais a aplicação com atribuições de papéis, leia Como criar e gerir o [controlo de acesso baseado em papéis da Azure Digital Twins.](./security-create-manage-role-assignments.md)
