---
title: Ligar uma conta do Azure a um ID de parceiro | Microsoft Docs
description: Acompanhe as cativações dos clientes do Azure ao ligar um ID de parceiro à conta de utilizador que utiliza para gerir os recursos do cliente.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 02/13/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: 4e4b039b6ad6fad8a414fc9703309fa76853ef09
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199675"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Ligar um ID de parceiro às contas do Azure

Os parceiros da Microsoft fornecem serviços que ajudam os clientes a alcançar os objetivos de negócios e de missão ao utilizar os produtos da Microsoft. Ao agir em nome do cliente que gere, configura e dá suporte aos serviços do Azure, os utilizadores parceiros precisarão de aceder ao ambiente do cliente. Com a Ligação de Administração de Parceiros, os parceiros podem associar o ID do Microsoft Partner Network às credenciais utilizadas para a entrega do serviço.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Obter acesso a partir do cliente

Antes de ligar o ID de parceiro, o cliente deve fornecer acesso aos recursos do Azure através de uma das seguintes opções:

- **Utilizador convidado**: o cliente pode adicioná-lo como utilizador convidado e atribuir-lhe qualquer função do controlo de acesso baseado em funções (RBAC). Para obter mais informações, veja [Adicionar utilizadores convidados a partir de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório**: o cliente pode criar uma conta de utilizador para si no seu próprio diretório e atribuir qualquer função RBAC.

- **Principal de serviço**: o cliente pode adicionar uma aplicação ou script da sua organização ao diretório e atribuir qualquer função RBAC. A identidade da aplicação ou do script é conhecida como um principal de serviço.

## <a name="link-to-a-partner-id"></a>Ligar a um ID de parceiro

Quando tiver acesso aos recursos do cliente, utilize o portal do Azure, o PowerShell ou a CLI do Azure para ligar o ID do Microsoft Partner Network (ID do MPN) ao ID de utilizador ou ao principal de serviço. Ligue o ID de parceiro em cada inquilino de cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utilizar o portal do Azure para ligar a um novo ID de parceiro

1. Aceda a [Ligar a um ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Inicie sessão no Portal do Azure.

3. Introduza o ID de parceiro Microsoft. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

   ![Captura de ecrã que mostra Ligar a um ID de parceiro](./media/link-partner-id/link-partner-id01.png)

4. Para ligar a um ID de parceiro a partir de outro cliente, mude o diretório. Em **Mudar diretório**, selecione o seu diretório.

   ![Captura de ecrã que mostra Mudar diretório](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utilizar o PowerShell para ligar a um novo ID de parceiro

1. Instale o módulo do PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, veja [Iniciar sessão no PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Ligação para o novo ID de parceiro. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligado
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligado
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligado
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Utilizar a CLI do Azure para ligar a um novo ID de parceiro
1. Instale a extensão da CLI do Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, veja [Iniciar sessão na CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Ligação para o novo ID de parceiro. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligado
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligado
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligado
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Passos seguintes

Participe na discussão na [Comunidade de Parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Quem pode ligar o ID de parceiro?**

Qualquer utilizador da organização parceira que gere os recursos do Azure de um cliente pode ligar o ID de parceiro à conta.

**Um ID de parceiro pode ser alterado depois de ter sido ligado?**

Sim. Um ID de parceiro ligado pode ser alterado, adicionado ou removido.

**E se um utilizador tiver uma conta em mais de um inquilino do cliente?**

A ligação entre o ID de parceiro e a conta é feita para cada inquilino do cliente. Ligue o ID de parceiro em cada inquilino de cliente.

**Outros parceiros ou clientes podem editar ou remover a ligação para o ID de parceiro?**

A ligação é associada ao nível da conta de utilizador. Apenas o utilizador da conta pode editar ou remover a ligação para o ID do parceiro. O cliente e os outros parceiros não podem alterar a ligação para o ID de parceiro.


**Qual ID do MPN devo utilizar se a minha empresa tiver vários?**

As Contas de Localização de Parceiros e os IDs do MPN associados devem ser utilizados para ligar o ID de parceiro.  Saiba mais sobre as [Contas de Parceiros](https://docs.microsoft.com/partner-center/account-structure)

**Onde posso encontrar relatórios de receitas influenciadas para o ID de parceiro ligado?**

Os relatórios de Desempenho dos Produtos na Cloud estão disponíveis para os parceiros no Centro de Parceiros no [ painel As Minhas Informações](https://partner.microsoft.com/membership/reports/myinsights). Precisa de selecionar a Ligação de Administração de Parceiros como o tipo de associação de parceiro.

**Por que não consigo ver o meu cliente nos relatórios?**

Não consegue ver o cliente nos relatórios pelos seguintes motivos

1. A conta de utilizador associada não tem [Acesso Baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/overview) em nenhuma subscrição ou recurso do Azure do cliente.

2. A subscrição do Azure em que o utilizador tem [Acesso Baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/overview) não tem nenhuma utilização.

**O ID de parceiro da ligação funciona com o Azure Stack?**

Sim, pode ligar o ID de parceiro com o Azure Stack.
