---
title: Reativar espaços de controlo de acesso azure desativados (ACS)
description: Encontre e ative os espaços de nome do Serviço de Controlo de Acesso (ACS) do Azure e solicite uma extensão para mantê-los habilitados até 4 de fevereiro de 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 85696a5580c4ed6c03f257787e2693a61a6158de
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164620"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: Reativar espaços de nome do Serviço de Controlo de Acesso desativados

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Em novembro de 2017, anunciamos que o Microsoft Azure Access Control Service (ACS), um serviço da Azure Ative Directory (Azure AD), vai ser reformado a 7 de novembro de 2018.

Desde então, enviamos e-mails para o e-mail administrativo das assinaturas ACS sobre a reforma da ACS 12 meses, 9 meses, 6 meses, 3 meses, 1 mês, 2 semanas, 1 semana e 1 dia antes da data de reforma de 7 de novembro de 2018.

No dia 3 de outubro de 2018, anunciámos (via e-mail e uma publicação de [blog)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)uma oferta de extensão aos clientes que não conseguem terminar a sua migração antes de 7 de novembro de 2018. O anúncio também tinha instruções para solicitar a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o seu espaço de nome é desativado

Se ainda não optou pela extensão, começaremos a desativar os espaços de nome ACS a partir de 7 de novembro de 2018. Já deve ter solicitado a extensão para 4 de fevereiro de 2019; caso contrário, não poderá ativar os espaços de nome através do PowerShell.

> [!NOTE]
> Deve ser administrador de serviçoou coadministrador da subscrição para executar os comandos PowerShell e solicitar uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Encontre e ative os seus espaços de nome ACS

Pode utilizar o ACS PowerShell para listar todos os seus espaços de nome ACS e reativar os que foram desativados.

1. Descarregue e instale o ACS PowerShell:
    1. Vá à Galeria PowerShell e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
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
    
        onde `[Command-Name]` é o nome do comando ACS.
1. Ligue-se ao ACS utilizando o cmdlet **Connect-AcsAccount.** 

    Pode ter de alterar a sua política de execução executando a **Política de Execução de set antes** de poder executar o comando.
1. Enumere as suas subscrições Azure disponíveis utilizando o cmdlet **Get-AcsSubscription.**
1. Enumere os seus espaços de nome ACS utilizando o cmdlet **Get-AcsNamespace.**
1. Confirme se os espaços de nome são desativados confirmando que `State` é `Disabled`.

    [![Confirmar que os espaços de nome estão desativados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Também pode utilizar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda está ativo.

1. Ative o seu espaço de nome ACS utilizando o cmdlet **Enable-AcsNamespace.**

    Uma vez ativado o seu espaço de nome, pode solicitar uma extensão para que o espaço de nome(s) não seja desativado novamente antes de 4 de fevereiro de 2019. Após essa data, todos os pedidos à ACS falharão.

## <a name="request-an-extension"></a>Solicitar uma extensão

Estamos a receber novos pedidos de extensão a partir de 21 de janeiro de 2019.

Começaremos a desativar espaços de nome para clientes que tenham solicitado extensões a 4 de fevereiro de 2019. Ainda pode reativar espaços de nome através do PowerShell, mas os espaços de nome voltarão a ser desativados após 48 horas.

Depois de 4 de março de 2019, os clientes deixarão de poder reativar quaisquer espaços de nome através da PowerShell.

Outras extensões deixarão de ser automaticamente aprovadas. Se precisar de mais tempo para migrar, contacte o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support) para fornecer uma linha temporal de migração detalhada.

### <a name="to-request-an-extension"></a>Para solicitar uma extensão

1. Inscreva-se no portal Azure e crie um novo pedido de [apoio.](https://portal.azure.com/#create/Microsoft.Support)
1. Preencha o novo formulário de pedido de apoio, como mostra o seguinte exemplo.

    | Campo de pedido de apoio | Valor |
    |-----------------------|--------------------|
    | **Tipo de emissão** | `Technical` |
    | **Subscrição** | Definir para a sua subscrição |
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

- Se tiver algum problema depois de seguir este como fazer, contacte o [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se tiver dúvidas ou feedback sobre a reforma da ACS, contacte-nos em acsfeedback@microsoft.com.

## <a name="next-steps"></a>Passos seguintes

- Reveja as informações sobre a reforma da ACS em [Como: Migrar do Serviço de Controlo de Acesso Azure](active-directory-acs-migration.md).
