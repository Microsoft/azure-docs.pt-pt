---
title: Como reativar desativada espaços de nomes do serviço de controle de acesso (ACS) do Azure
description: Saiba como localizar e ativar os espaços de nomes do serviço de controlo de acesso (ACS) do Azure e solicitar uma extensão para mantê-los ativada até 4 de Fevereiro de 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 534c0463cd8aee9fccfa183586b946032dada722
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094563"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: Reativar espaços de nomes do Serviço de Controlo de Acesso desativados

Em Novembro de 2017, anunciámos que o Microsoft Azure controlo de acesso Service (ACS), um serviço do Azure Active Directory (Azure AD), vai ser reformada no dia 7 de Novembro de 2018.

Uma vez que, em seguida, enviámos e-mails para o e-mail do administrador das subscrições ACS sobre a desativação de ACS 12 meses, 9 meses, 6 meses, três meses, 1, 2 semanas, 1 semana, e 1 dia do mês antes da data de extinção de 7 de Novembro de 2018.

3 de Outubro de 2018, anunciámos (através do e-mail do e [uma mensagem de blogue](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) uma oferta de extensão para os clientes que não é possível concluir a respetiva migração antes de 7 de Novembro de 2018. O anúncio também tinha as instruções para pedir a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o seu espaço de nomes está desativado

Se ainda não selecionada para a extensão, vamos começar desativar espaços de nomes do ACS a partir de 7 de Novembro de 2018. Tem de ter solicitado a extensão para 4 de Fevereiro de 2019 já; caso contrário, não será possível ativar os espaços de nomes através do PowerShell.

> [!NOTE]
> Tem de ser um administrador de serviços ou coadministrador da subscrição para executar os comandos do PowerShell e pedir uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Localizar e ativar os espaços de nomes do ACS

Pode utilizar o PowerShell de ACS para listar todos os seus espaços de nomes do ACS e reativar as que foram desativados.

1. Transferir e instalar o PowerShell do ACS:
    1. Aceda à galeria do PowerShell e transfira [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instale o módulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obter uma lista de todos os comandos de possíveis:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Para obter ajuda sobre um comando específico, execute:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        onde `[Command-Name]` é o nome do comando ACS.
1. Ligar ao ACS utilizando o **Connect-AcsAccount** cmdlet. 

    Poderá ter de alterar sua diretiva de execução, executando **Set-ExecutionPolicy** antes de poder executar o comando.
1. Listar as subscrições do Azure disponíveis com o **Get-AcsSubscription** cmdlet.
1. Listar a sua espaços de nomes do ACS com o **Get-AcsNamespace** cmdlet.
1. Confirme que os espaços de nomes estão desativados por confirmar que `State` é `Disabled`.

    [![Confirme que os espaços de nomes estão desativados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Também pode utilizar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda está ativo.

1. Ativar a sua namespace(s) de ACS com o **Enable-AcsNamespace** cmdlet.

    Assim que tive ativado seu namespace(s), pode pedir uma extensão, para que o namespace(s) não ser desativado novamente antes de 4 de Fevereiro de 2019. Após essa data, todos os pedidos para ACS irão falhar.

## <a name="request-an-extension"></a>Pedir uma extensão

Estamos a tomar novos pedidos de extensão iniciar de 21 de Janeiro de 2019.

Começaremos a desativação de espaços de nomes para os clientes que solicitaram extensões para 4 de Fevereiro de 2019. Pode ainda voltar a ativar espaços de nomes através do PowerShell, mas os espaços de nomes serão desativados novamente após 48 horas.

Após 4 de Março de 2019, os clientes já não poderão voltar a ativar quaisquer espaços de nomes através do PowerShell.

Extensões adicionais já não serão aprovadas automaticamente. Se precisar de mais tempo de migrar, contacte [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support) para fornecer uma linha cronológica de migração detalhadas.

### <a name="to-request-an-extension"></a>Para pedir uma extensão

1. Inicie sessão portal do Azure e crie uma [novo pedido de suporte](https://portal.azure.com/#create/Microsoft.Support).
1. Preencha o novo formulário de pedido de suporte, conforme mostrado no exemplo a seguir.

    | Campo de pedido de suporte | Value |
    |-----------------------|--------------------|
    | **Tipo de problema** | `Technical` |
    | **Subscrição** | Definir a sua subscrição |
    | **Serviço** | `All services` |
    | **Recurso** | `General question/Resource not available` |
    | **Tipo de problema** | `ACS to SAS Migration` |
    | **Assunto** | Descreva o problema |

   ![Novo pedido de suporte técnico](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Caso se depare com quaisquer problemas depois de seguir este procedimentos, contacte [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se tiver questões ou comentários sobre a extinção de ACS, contacte-nos em acsfeedback@microsoft.com.

## <a name="next-steps"></a>Passos Seguintes

- Reveja as informações sobre a extinção de ACS no [como: Migrar a partir do serviço de controle de acesso do Azure](active-directory-acs-migration.md).
