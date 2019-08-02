---
title: Vincular uma conta do Azure a uma ID de parceiro | Microsoft Docs
description: Acompanhe os compromissos com clientes do Azure vinculando uma ID de parceiro à conta de usuário que você usa para gerenciar os recursos do cliente.
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
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706395"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Vincular uma ID de parceiro às suas contas do Azure

Os parceiros da Microsoft fornecem serviços que ajudam os clientes a alcançar objetivos de negócios e de missão usando produtos da Microsoft. Ao agir em nome do cliente que gerencia, configura e dá suporte aos serviços do Azure, os usuários do parceiro precisarão acessar o ambiente do cliente. Usando o link de administrador do parceiro, os parceiros podem associar sua ID de rede do parceiro às credenciais usadas para entrega de serviço.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Obter acesso de seu cliente

Antes de vincular sua ID de parceiro, o cliente deve fornecer acesso aos recursos do Azure usando uma das seguintes opções:

- **Usuário convidado**: Seu cliente pode adicionar você como um usuário convidado e atribuir qualquer função de RBAC (controle de acesso baseado em funções). Para obter mais informações, consulte [Adicionar usuários convidados de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório**: O cliente pode criar uma conta de usuário para você em seu próprio diretório e atribuir qualquer função de RBAC.

- **Entidade de serviço**: O cliente pode adicionar um aplicativo ou script de sua organização em seu diretório e atribuir qualquer função de RBAC. A identidade do aplicativo ou script é conhecida como uma entidade de serviço.

## <a name="link-to-a-partner-id"></a>Ligar a um ID de parceiro

Quando você tiver acesso aos recursos do cliente, use o portal do Azure, o PowerShell ou o CLI do Azure para vincular sua ID de Microsoft Partner Network (ID de MPN) à sua ID de usuário ou entidade de serviço. Vincule a ID de parceiro em cada locatário do cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Usar o portal do Azure para vincular a uma nova ID de parceiro

1. Acesse o [link para uma ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) na portal do Azure.

2. Inicie sessão no Portal do Azure.

3. Insira a ID do parceiro da Microsoft. A ID de parceiro é a ID de [Microsoft Partner Network](https://partner.microsoft.com/) para sua organização.

   ![Captura de tela que mostra o link para uma ID de parceiro](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Para vincular uma ID de parceiro a outro cliente, alterne o diretório. Em **Alterar diretório**, selecione seu diretório.

   ![Captura de tela que mostra o diretório do comutador](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Usar o PowerShell para vincular a uma nova ID de parceiro

1. Instale o módulo do PowerShell [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, consulte [entrar com o PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Link para a nova ID de parceiro. A ID de parceiro é a ID de [Microsoft Partner Network](https://partner.microsoft.com/) para sua organização.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Usar o CLI do Azure para vincular a uma nova ID de parceiro
1. Instale a extensão de CLI do Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, consulte [entrar com o CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Link para a nova ID de parceiro. A ID de parceiro é a ID de [Microsoft Partner Network](https://partner.microsoft.com/) para sua organização.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Passos Seguintes

Junte-se à discussão na [comunidade de parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Quem pode vincular a ID de parceiro?**

Qualquer usuário da organização parceira que gerencia os recursos do Azure de um cliente pode vincular a ID de parceiro à conta.

**Uma ID de parceiro pode ser alterada depois de ser vinculada?**

Sim. Uma ID de parceiro vinculada pode ser alterada, adicionada ou removida.

**E se um usuário tiver uma conta em mais de um locatário do cliente?**

O link entre a ID do parceiro e a conta é feito para cada locatário do cliente. Vincule a ID de parceiro em cada locatário do cliente.

**Outros parceiros ou clientes podem editar ou remover o link para a ID de parceiro?**

O link está associado no nível da conta de usuário. Somente você pode editar ou remover o link para a ID do parceiro. O cliente e outros parceiros não podem alterar o link para a ID do parceiro.


**Qual ID de MPN devo usar se minha empresa tiver vários?**

As contas de local de parceiro e as IDs MPN associadas devem ser usadas para vincular a ID de parceiro.  Saiba mais sobre [contas de parceiros](https://docs.microsoft.com/partner-center/account-structure)

**Onde posso encontrar relatórios de receita influenciados para a ID de parceiro vinculada?**

Os relatórios de desempenho do produto em nuvem estão disponíveis para parceiros no Partner Center no [painel](https://partner.microsoft.com/membership/reports/myinsights)do insights. Você precisa selecionar link de administrador de parceiro como o tipo de associação de parceiro.

**Por que não consigo ver meu cliente nos relatórios?**

Você não pode ver o cliente nos relatórios devido aos seguintes motivos

1. A conta de usuário vinculada não tem [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) em nenhuma assinatura ou recurso do Azure do cliente.

2. A assinatura do Azure em que o usuário tem acesso de [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) não tem nenhum uso.

**A ID do parceiro de link funciona com Azure Stack?**

Sim, você pode vincular sua ID de parceiro para Azure Stack.
