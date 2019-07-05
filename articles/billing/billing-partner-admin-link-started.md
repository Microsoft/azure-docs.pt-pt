---
title: Ligação para um ID de parceiro de conta do Azure | Documentos da Microsoft
description: Controlar compromissos com os clientes do Azure por um ID de parceiro de ligação para a conta de utilizador que utiliza para gerir os recursos do cliente.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 97866a1f1299c028cdc8f86245308ae4a8b5db88
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502676"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Ligar um ID de parceiro a suas contas do Azure

Parceiros da Microsoft fornecem serviços que ajudam os clientes obter empresariais e os objetivos de missão com produtos da Microsoft. Quando a agir em nome do cliente gerir, configurar e o suporte de serviços do Azure, os parceiro os utilizadores terão acesso ao ambiente do cliente. Utilizar a ligação de administrador do parceiro, parceiros podem associar o ID de rede de parceiros com as credenciais utilizadas para entrega de serviços.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Obtenha acesso a partir do seu cliente

Antes de ligar o seu ID de parceiro, seu cliente tem de dar acesso aos seus recursos do Azure ao utilizar uma das seguintes opções:

- **Utilizador convidado**: Seu cliente pode adicioná-lo como um utilizador convidado e atribuir funções (RBAC) do controle de acesso baseado em funções. Para obter mais informações, consulte [adicionar utilizadores convidados a partir de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta do**: Seu cliente pode criar uma conta de utilizador para si no seu próprio diretório e atribuir qualquer função RBAC.

- **Principal de serviço**: Seu cliente pode adicionar uma aplicação ou script da sua organização em seu diretório e atribuir qualquer função RBAC. A identidade da aplicação ou script é conhecida como um principal de serviço.

## <a name="link-to-a-partner-id"></a>Ligar a um ID de parceiro

Quando tem acesso aos recursos do cliente, utilize o portal do Azure, PowerShell ou a CLI do Azure para ligar a sua ID do Microsoft Partner Network (MPN ID) para o seu ID de utilizador ou principal de serviço. Ligar o ID de parceiro em cada inquilino do cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utilizar o portal do Azure para ligar a um novo ID de parceiro

1. Aceda a [Link para um ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Inicie sessão no Portal do Azure.

3. Introduza o ID de parceiro Microsoft. O parceiro ID é o [Microsoft Partner Network](https://partner.microsoft.com/) ID para a sua organização.

   ![Captura de ecrã que mostra o Link para um ID de parceiro](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Para ligar um ID de parceiro de outro cliente, mude o diretório. Sob **trocar diretório**, selecione o seu diretório.

   ![Captura de ecrã que mostra a trocar diretório](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utilize o PowerShell para ligar a um novo ID de parceiro

1. Instalar o [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) módulo do PowerShell.

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, consulte [iniciar sessão com o PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Ligação para o novo ID de parceiro. O parceiro ID é o [Microsoft Partner Network](https://partner.microsoft.com/) ID para a sua organização.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligadas
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligadas
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligadas
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Utilizar a CLI do Azure para ligar a um novo ID de parceiro
1. Instale a extensão de CLI do Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, consulte [iniciar sessão com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Ligação para o novo ID de parceiro. O parceiro ID é o [Microsoft Partner Network](https://partner.microsoft.com/) ID para a sua organização.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligadas
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligadas
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligadas
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Passos Seguintes

Junte-se a discussão no [Comunidade de parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Quem pode associar o ID de parceiro?**

Qualquer utilizador da organização parceira que gere os recursos do Azure de um cliente pode associar o ID de parceiro na conta.

**Um ID de parceiro pode ser alterado depois que ele está anexado?**

Sim. Um ID de parceiro ligado pode ser alterado, adicionado ou removido.

**E se um utilizador tiver uma conta em mais do que um inquilino do cliente?**

A ligação entre o ID de parceiro e a conta é feita para cada inquilino do cliente. Ligar o ID de parceiro em cada inquilino do cliente.

**Podem outros parceiros ou clientes, editar ou remover a ligação para o ID de parceiro?**

A ligação está associada ao nível da conta de utilizador. Apenas pode editar ou remover a ligação para o ID de parceiro. O cliente e outros parceiros que não é possível alterar a ligação para o ID de parceiro.


**O ID MPN posso utilizar se a minha empresa tem vários?**

Contas de localização de parceiros e os IDs de MPN associado devem ser utilizado para ligar o ID de parceiro.  Saiba mais sobre [contas de parceiros](https://docs.microsoft.com/partner-center/account-structure)

**Onde posso encontrar influenciada receita, geração de relatórios para o ID de parceiro ligado?**

Relatórios de desempenho de produto de nuvem estão disponível para parceiros no Centro de parceiros em [dashboard de conhecimentos My](https://partner.microsoft.com/membership/reports/myinsights). Tem de selecionar a ligação de administrador de parceiro como o tipo de associação de parceiro.

**Por que motivo não vejo meu cliente nos relatórios?**

Não é possível ver o cliente nos relatórios de devido aos motivos seguintes

1. A conta de utilizador ligado não tem [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) em qualquer cliente subscrição do Azure ou recursos.

2. A subscrição do Azure onde o utilizador tiver [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) acesso não tem qualquer utilização.

**Ligar partner que ID funciona com o Azure Stack?**

Sim, pode ligar o seu ID de parceiro para o Azure Stack.
