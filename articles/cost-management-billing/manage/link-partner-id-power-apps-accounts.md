---
title: Ligue um ID de parceiro às suas contas power apps com as suas credenciais Azure
description: Este artigo ajuda os parceiros da Microsoft a usar as suas credenciais Azure para ajudar os clientes a usar as Aplicações De Energia do Microsoft.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727713"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Ligue um ID de parceiro às suas contas power apps

Este artigo ajuda os parceiros da Microsoft, que são fornecedores de serviços power apps, a associar o seu serviço aos clientes nas Aplicações De Energia da Microsoft. Quando você (o parceiro Microsoft) gere, configura e suporta os serviços de Power Apps para o seu cliente, você tem acesso ao ambiente do seu cliente. Pode utilizar as suas credenciais Azure e um Partner Admin Link (PAL) para associar o ID da rede do seu parceiro com as credenciais de conta utilizadas para a prestação de serviços.

O PAL permite à Microsoft identificar e reconhecer parceiros que tenham clientes de Power Apps. A Microsoft atribui o uso a uma organização de parceiros com base nas permissões da conta (função de Power Apps) e âmbito (inquilino, grupo de recursos, recurso).

## <a name="get-access-from-your-customer"></a>Obter acesso a partir do cliente

Antes de ligar o ID do seu parceiro, o seu cliente deve dar-lhe acesso aos seus recursos de Power Apps utilizando uma das seguintes opções:

- **Utilizador convidado** - O seu cliente pode adicioná-lo como utilizador convidado e atribuir quaisquer funções de Power Apps. Para obter mais informações, veja [Adicionar utilizadores convidados a partir de outro diretório](../../active-directory/external-identities/what-is-b2b.md).
- **Conta diretório** - O seu cliente pode criar uma conta de utilizador para si no seu próprio diretório e atribuir qualquer papel de Power Apps.
- **Principal de serviço** - O seu cliente pode adicionar uma aplicação ou script da sua organização no seu diretório e atribuir qualquer papel de Power Apps. A identidade da aplicação ou do script é conhecida como um principal de serviço.
- **Administrador Delegado** - O seu cliente pode delegar um grupo de recursos para que os seus utilizadores possam trabalhar nele a partir do seu inquilino. Para obter mais informações, consulte [Para os parceiros: o Administrador Delegado.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Ligue o cliente a um ID de parceiro

Quando tiver acesso aos recursos do seu cliente, utilize o portal Azure, o PowerShell ou o CLI Azure para ligar o ID da Rede de Parceiros da Microsoft (MPN ID) ao ID do utilizador ou ao principal de serviço. Ligue a identificação do parceiro a cada cliente inquilino.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utilizar o portal do Azure para ligar a um novo ID de parceiro

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Aceda a [Ligar a um ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.
1. Insira o [ID da Rede de Parceiros](https://partner.microsoft.com/) da Microsoft para a sua organização. Certifique-se de que utiliza o  **ID MPN Associado**  mostrado no seu perfil de parceiro.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Screenshot mostrando o Link para uma janela de identificação do parceiro." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Para ligar o seu parceiro iD a outro cliente, mude o diretório. No **diretório da Switch,** selecione o diretório apropriado.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Screenshot mostrando a janela de subscrição Do Diretório + onde pode mudar o seu diretório." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utilizar o PowerShell para ligar a um novo ID de parceiro

Instale o módulo [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell.

Inscreva-se no inquilino do cliente com a conta de utilizador ou com o titular do serviço. Para obter mais informações, veja [Iniciar sessão no PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Ligação para o novo ID de parceiro. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização. Certifique-se de que utiliza o **ID MPN Associado**  mostrado no seu perfil de parceiro.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Obter o ID de parceiro ligado

```azurepowershell-interactive
get-AzManagementPartner
```

Atualizar o ID de parceiro ligado

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Eliminar o ID de parceiro ligado

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Utilizar a CLI do Azure para ligar a um novo ID de parceiro

Primeiro, instale a extensão Azure CLI.

```azurecli-interactive
az extension add --name managementpartner
```

Inscreva-se no inquilino do cliente com a conta de utilizador ou com o titular do serviço. Para obter mais informações, veja [Iniciar sessão na CLI do Azure](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Ligação para o novo ID de parceiro. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Obter o ID de parceiro ligado

```azurecli-interactive
az managementpartner show
```

Atualizar o ID de parceiro ligado

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Eliminar o ID de parceiro ligado

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>Passos seguintes

- Leia a Gestão de [Custos + Faturação de FAQ](../cost-management-billing-faq.yml) para perguntas e respostas sobre a ligação de um ID de parceiro às contas power apps.
- Participe na discussão na [Comunidade de Parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.
- Leia a [especialização avançada de Desenvolvimento de Aplicações de Código Baixo FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) para associação de Aplicações de Energia baseadas em PAL para desenvolvimento de aplicações de código baixo avançada especialização.
