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
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603832"
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

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

As secções seguintes cobrem perguntas frequentes sobre a ligação de um ID de parceiro às contas power apps.

### <a name="who-should-link-the-partner-id"></a>Quem deve ligar a iD do parceiro?

Qualquer utilizador da organização parceira que trabalhe nos recursos power apps de um cliente pode ligar o ID do parceiro à conta. Idealmente, a associação em PAL deve ser feita no início do projeto. No entanto, pode ser realizado sempre que tiver acesso no diretório do cliente.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>Um ID de parceiro pode ser alterado depois de ter sido ligado?

Sim. Um ID de parceiro ligado pode ser alterado, adicionado ou removido. Um exemplo para esta situação pode ser quando um funcionário da sua empresa deixa a sua organização. Outro exemplo pode ser quando um projeto ou contrato com o cliente termina.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>E se um utilizador tiver uma conta em mais de um inquilino do cliente?

A ligação entre o ID de parceiro e a conta é feita para cada inquilino do cliente. Ligue o ID de parceiro em cada inquilino de cliente.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Outros parceiros ou clientes podem editar ou remover a ligação para o ID de parceiro?

A ligação é associada ao nível da conta de utilizador. Apenas o utilizador da conta pode editar ou remover a ligação para o ID do parceiro. O cliente e os outros parceiros não podem alterar a ligação para o ID de parceiro.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Qual ID do MPN devo utilizar se a minha empresa tiver vários?

Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro. Normalmente é a associação de identificação de conta local com a sua organização.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Como explico pal ao meu cliente?

O PAL permite à Microsoft identificar e reconhecer os parceiros que estão a ajudar os clientes a alcançar objetivos de negócio e a perceber valor na nuvem. Os clientes devem primeiro fornecer um parceiro de acesso aos seus recursos Power Apps. Uma vez concedido o acesso, o ID da Rede de Parceiros microsoft do parceiro (MPN ID) está associado. Esta associação ajuda a Microsoft a compreender os fornecedores de serviços e a aperfeiçoar as ferramentas e programas necessários para apoiar melhor os clientes.

### <a name="what-data-does-pal-collect"></a>Que dados são recolhidos pelo PAL?

A associação do PAL a credenciais já existentes não fornece dados novos dos clientes à Microsoft. Fornece a informação à Microsoft onde um parceiro está ativamente envolvido nos ambientes de Power Apps de um cliente. A Microsoft pode atribuir uso e influência do ambiente do cliente à organização parceira com base nas permissões da conta (função de Power Apps) e âmbito (inquilino, Grupo de Recursos, Recursos) fornecido ao parceiro pelo cliente.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>A associação PAL afeta a segurança do ambiente de Power Apps de um cliente?

A associação PAL só adiciona o ID MPN do parceiro à credencial já prevista. Não altera quaisquer permissões (função Power Apps) nem fornece dados de serviço extra de Power Apps para o parceiro ou Microsoft.

### <a name="next-steps"></a>Passos seguintes

- Participe na discussão na [Comunidade de Parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.
- Leia a [especialização avançada de Desenvolvimento de Aplicações de Código Baixo FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) para associação de Aplicações de Energia baseadas em PAL para desenvolvimento de aplicações de código baixo avançada especialização.
