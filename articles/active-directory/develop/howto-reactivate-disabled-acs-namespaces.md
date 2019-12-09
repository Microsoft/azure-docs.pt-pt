---
title: Como reativar os namespaces do ACS (serviço de controle de acesso) do Azure desabilitados
description: Saiba como localizar e habilitar seus namespaces do ACS (serviço de controle de acesso) do Azure e solicitar uma extensão para mantê-los habilitados até 4 de fevereiro de 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 590fc99861035ce1a5219a3cfd788159c6c2a19b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917714"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: reativar namespaces de serviço de controle de acesso desabilitados

Em novembro de 2017, anunciamos que Serviço de Controle de Acesso do Microsoft Azure (ACS), um serviço do Azure Active Directory (AD do Azure), está sendo desativado em 7 de novembro de 2018.

Desde então, enviamos emails para o email de administrador de assinaturas do ACS sobre a desativação do ACS 12 meses, 9 meses, 6 meses, 3 meses, 1 mês, 2 semanas, 1 semana e 1 dia antes da data de desativação de 7 de novembro de 2018.

Em 3 de outubro de 2018, anunciamos (por email e [uma postagem de blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) uma oferta de extensão para clientes que não conseguem concluir sua migração antes de 7 de novembro de 2018. O comunicado também tinha instruções para solicitar a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o namespace está desabilitado

Se você ainda não tiver optado pela extensão, começaremos a desabilitar os namespaces do ACS a partir de 7 de novembro de 2018. Você deve ter solicitado a extensão para 4 de fevereiro de 2019 já; caso contrário, você não poderá habilitar os namespaces por meio do PowerShell.

> [!NOTE]
> Você deve ser um administrador de serviços ou coadministrador da assinatura para executar os comandos do PowerShell e solicitar uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Localizar e habilitar seus namespaces do ACS

Você pode usar o ACS no PowerShell para listar todos os namespaces do ACS e reativá-los que foram desabilitados.

1. Baixe e instale o PowerShell do ACS:
    1. Vá para a Galeria do PowerShell e baixe [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instale o módulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obter uma lista de todos os comandos possíveis:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Para obter ajuda sobre um comando específico, execute:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        em que `[Command-Name]` é o nome do comando do ACS.
1. Conecte-se ao ACS usando o cmdlet **Connect-AcsAccount** . 

    Talvez seja necessário alterar a política de execução executando **Set-ExecutionPolicy** antes de executar o comando.
1. Liste suas assinaturas do Azure disponíveis usando o cmdlet **Get-AcsSubscription** .
1. Liste seus namespaces do ACS usando o cmdlet **Get-AcsNamespace** .
1. Confirme se os namespaces estão desabilitados confirmando se `State` está `Disabled`.

    [![confirmar que os namespaces estão desabilitados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Você também pode usar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda está ativo.

1. Habilite seus namespaces do ACS usando o cmdlet **Enable-AcsNamespace** .

    Depois de habilitar seus namespaces, você pode solicitar uma extensão para que os namespaces não sejam desabilitados novamente antes de 4 de fevereiro de 2019. Após essa data, todas as solicitações para o ACS falharão.

## <a name="request-an-extension"></a>Solicitar uma extensão

Estamos fazendo novas solicitações de extensão a partir de 21 de janeiro de 2019.

Começaremos a desabilitar namespaces para clientes que solicitaram extensões para 4 de fevereiro de 2019. Você ainda pode reabilitar namespaces por meio do PowerShell, mas os namespaces serão desabilitados novamente após 48 horas.

Após 4 de março de 2019, os clientes não poderão mais reabilitar os namespaces por meio do PowerShell.

Extensões adicionais não serão mais aprovadas automaticamente. Se você precisar de mais tempo para migrar, entre em contato com o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support) para fornecer uma linha de tempo de migração detalhada.

### <a name="to-request-an-extension"></a>Para solicitar uma extensão

1. Entre no portal do Azure e crie uma [nova solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support).
1. Preencha o novo formulário de solicitação de suporte, conforme mostrado no exemplo a seguir.

    | Campo de Solicitação de suporte | Valor |
    |-----------------------|--------------------|
    | **Tipo de problema** | `Technical` |
    | **Subscrição** | Definir para sua assinatura |
    | **Serviço** | `All services` |
    | **Recurso** | `General question/Resource not available` |
    | **Tipo de problema** | `ACS to SAS Migration` |
    | **Assunto** | Descrever o problema |

   ![Mostra um exemplo de uma nova solicitação de suporte técnico](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Se você tiver algum problema depois de seguir este "como", entre em contato com o [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se você tiver dúvidas ou comentários sobre a desativação do ACS, entre em contato conosco em acsfeedback@microsoft.com.

## <a name="next-steps"></a>Passos seguintes

- Examine as informações sobre a desativação do ACS em [como migrar do serviço de controle de acesso do Azure](active-directory-acs-migration.md).
