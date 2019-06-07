---
title: Ativar aplicações multi-inquilino duplos Digital do Azure | Documentos da Microsoft
description: Como configurar aplicações multi-inquilino do Azure Active Directory duplos Digital do Azure.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mavoge
ms.openlocfilehash: 03b5693fe016cfb11d6f685f9088fe6e94f03b90
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688608"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Ativar aplicações multi-inquilino duplos Digital do Azure

Os desenvolvedores de soluções que criam no duplos Digital do Azure podem achar que pretende suportar vários clientes com um único serviço ou solução. Na verdade, *multi-inquilino* aplicativos estão entre as configurações mais comuns de duplos Digital do Azure.

Este documento descreve como configurar uma aplicação de duplos Digital do Azure para suportar vários inquilinos do Azure Active Directory e os clientes.

## <a name="multitenancy"></a>Arquitetura "multitenancy"

R *multi-inquilino* recursos é uma única instância de aprovisionamento que oferece suporte a vários clientes. Cada cliente tem seus próprios dados independentes e privilégios. Experiência de cada um dos clientes é isolada umas das outras que seus "ver" do aplicativo é diferente.

Para saber mais sobre a arquitetura "multitenancy", leia [aplicações de multi-inquilino no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Cenário de problema

Neste cenário, considere um desenvolvedor criar uma solução de duplos Digital do Azure (**DESENVOLVEDOR**) e um cliente que utiliza essa solução (**cliente**):

- **DESENVOLVEDOR** tem uma subscrição do Azure com um inquilino do Azure Active Directory.
- **DESENVOLVEDOR** implementa uma instância de duplos Digital do Azure na sua subscrição do Azure. O Azure Active Directory automaticamente um principal de serviço criado em **DESENVOLVEDOR**do inquilino do Azure Active Directory.
- Os utilizadores dentro **DESENVOLVEDOR**do inquilino do Azure Active Directory pode então [adquira tokens de OAuth 2.0](./security-authenticating-apis.md) do serviço duplos Digital do Azure.
- **DESENVOLVEDOR** agora cria uma aplicação móvel que é integrado diretamente com as APIs de gestão de duplos Digital do Azure.
- **DESENVOLVEDOR** permite **cliente** a utilização da aplicação móvel.
- **CUSTOMER** tem de ser autorizado a utilizar a API Management da duplos Digital do Azure dentro **PROGRAMADOR**do aplicativo.

O problema:

- Quando **CUSTOMER** inicia sessão na **DESENVOLVEDOR**do aplicativo, a aplicação não é possível adquirir os tokens do **cliente**do utilizadores para se autenticar com as APIs de gestão de duplos Digital do Azure.
- Uma exceção for lançada no Azure Active Directory que indica que duplos Digital do Azure não é reconhecido dentro **cliente**do diretório.

## <a name="problem-solution"></a>Solução de problema

Para resolver o cenário de problema anterior, as seguintes ações são necessários para criar um duplos Digital do Azure principal de serviço dentro de **cliente**do inquilino do Azure Active Directory:

- Se **cliente** ainda não tiver uma subscrição do Azure com um inquilino do Azure Active Directory:

  - **CUSTOMER**do administrador de inquilinos do Azure Active Directory tem de adquirir um [pay as you go de subscrição do Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **CUSTOMER**do administrador de inquilinos do Azure Active Directory, em seguida, deve [vincular o respetivo inquilino com a nova subscrição](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Sobre o [portal do Azure](https://portal.azure.com), **cliente**do administrador de inquilinos do Azure Active Directory realiza as seguintes etapas:

  1. Open **subscrições**.
  1. Selecione a subscrição com o inquilino do Azure Active Directory a ser utilizado no **DESENVOLVEDOR**do aplicativo.

     ![Subscrições do Azure Active Directory][1]

  1. Selecione **fornecedores de recursos**.
  1. Procure **Microsoft.IoTSpaces**.
  1. Selecione **Registar**.

     ![Fornecedores de recursos do Azure Active Directory][2]
  
## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como utilizar funções definidas pelo utilizador duplos Digital do Azure, leia [como criar funções definidas pelo utilizador de duplos Digital do Azure](./how-to-user-defined-functions.md).

- Para saber como utilizar o controlo de acesso baseado em funções para proteger ainda mais o aplicativo com atribuições de funções, leia [como criar e gerir o controlo de acesso baseado em funções de duplos Digital do Azure](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
