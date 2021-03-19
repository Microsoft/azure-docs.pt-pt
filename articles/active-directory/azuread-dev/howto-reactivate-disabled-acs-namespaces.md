---
title: Reativar espaços de nome do Azure Access Control Service (ACS) desativados
description: Encontre e ative os espaços de nome do Azure Access Control Service (ACS) e solicite uma extensão para mantê-los ativados até 4 de fevereiro de 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383722"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: Reativar espaços de nome do Serviço de Controlo de Acesso desativado

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Em novembro de 2017, anunciou que o Microsoft Azure Access Control Service (ACS), um serviço da Azure Ative Directory (Azure AD), vai ser reformado a 7 de novembro de 2018.

Desde então, enviámos e-mails para o e-mail de administração das assinaturas ACS sobre a reforma da ACS 12 meses, 9 meses, 6 meses, 3 meses, 1 mês, 2 semanas, 1 semana e 1 dia antes da data da reforma de 7 de novembro de 2018.

No dia 3 de outubro de 2018, anunciamos (através de e-mail e [um blog post)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)uma oferta de extensão a clientes que não conseguem terminar a sua migração antes de 7 de novembro de 2018. O anúncio também tinha instruções para solicitar a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o seu espaço de nome está desativado

Se não optou pela extensão, começaremos a desativar os espaços de nomes ACS a partir de 7 de novembro de 2018. Já deve ter solicitado a prorrogação até 4 de fevereiro de 2019; caso contrário, não será capaz de ativar os espaços de nome através do PowerShell.

> [!NOTE]
> Deve ser administrador de serviço ou coadministrador da subscrição para executar os comandos PowerShell e solicitar uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Encontre e ative os seus espaços de nomes ACS

Pode utilizar o ACS PowerShell para listar todos os seus espaços de nome ACS e reativar os que foram desativados.

1. Descarregue e instale o ACS PowerShell:
    1. Vá à PowerShell Gallery e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instale o módulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obtenha uma lista de todos os comandos possíveis:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Para obter ajuda num comando específico, corra:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        onde `[Command-Name]` está o nome do comando ACS.
1. Ligue-se ao ACS utilizando o **cmdlet Connect-AcsAccount.** 

    Pode ter de alterar a sua política de execução executando **a Política de Execução de Conjuntos** antes de poder executar o comando.
1. Enuma as suas subscrições Azure disponíveis utilizando o cmdlet **Get-AcsSubscription.**
1. Enuma os seus espaços de nome ACS utilizando o cmdlet **Get-AcsNamespace.**
1. Confirme se os espaços de nome são desativado confirmando que `State` é `Disabled` .

    [![Confirme que os espaços de nome estão desativados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Também pode usar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda está ativo.

1. Ative o seu espaço de nomes ACS utilizando o cmdlet **Enable-AcsNamespace.**

    Uma vez ativado o seu(s) espaço de nome, pode solicitar uma extensão para que o(s) espaço de nome(s) não seja desativado novamente antes de 4 de fevereiro de 2019. Depois dessa data, todos os pedidos à ACS falharão.

## <a name="request-an-extension"></a>Solicitar uma extensão

Estamos a receber novos pedidos de extensão a partir de 21 de janeiro de 2019.

Vamos começar a desativar espaços de nome para clientes que tenham pedido extensões até 4 de fevereiro de 2019. Ainda é possível reativar espaços de nome através do PowerShell, mas os espaços de nome serão novamente desativados após 48 horas.

Depois de 4 de março de 2019, os clientes deixarão de poder ree capacitar quaisquer espaços de nome através do PowerShell.

As novas prorrogações deixarão de ser automaticamente aprovadas. Se precisar de tempo adicional para migrar, contacte o [suporte da Azure](https://portal.azure.com/#create/Microsoft.Support) para fornecer uma linha temporal de migração detalhada.

### <a name="to-request-an-extension"></a>Para solicitar uma extensão

1. Inscreva-se no portal Azure e crie um [novo pedido de apoio.](https://portal.azure.com/#create/Microsoft.Support)
1. Preencha o novo formulário de pedido de apoio, como mostrado no exemplo seguinte.

    | Campo de pedido de apoio | Valor |
    |-----------------------|--------------------|
    | **Tipo de problema** | `Technical` |
    | **Subscrição** | Configurar para a sua subscrição |
    | **Serviço** | `All services` |
    | **Recurso** | `General question/Resource not available` |
    | **Tipo de problema** | `ACS to SAS Migration` |
    | **Assunto** | Descreva a questão |

   ![Mostra um exemplo de um novo pedido de apoio técnico](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Ajuda e suporte

- Se tiver algum problema depois de seguir este como fazer, contacte o suporte da [Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se tiver dúvidas ou feedback sobre a reforma da ACS, contacte-nos em acsfeedback@microsoft.com .

## <a name="next-steps"></a>Passos seguintes

- Reveja as informações sobre a reforma da ACS em [Como: Migrar do Serviço de Controlo de Acesso Azure](active-directory-acs-migration.md).
