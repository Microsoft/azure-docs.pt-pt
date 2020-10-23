---
title: Ligar uma conta do Azure a um ID de parceiro
description: Acompanhe as cativações dos clientes do Azure ao ligar um ID de parceiro à conta de utilizador que utiliza para gerir os recursos do cliente.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: 96b6467d0d529f5839c33182057f3aa3c39cb6e7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132555"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Ligar um ID de parceiro às contas do Azure

Os parceiros da Microsoft fornecem serviços que ajudam os clientes a alcançar os objetivos de negócios e de missão ao utilizar os produtos da Microsoft. Ao agir em nome do cliente que gere, configura e dá suporte aos serviços do Azure, os utilizadores parceiros precisarão de aceder ao ambiente do cliente. Com a Ligação do Administrador de Parceiros (PAL), os parceiros podem associar o seu ID do Microsoft Partner Network às credenciais utilizadas para a entrega do serviço.

A PAL permite que a Microsoft identifique e reconheça os parceiros que impulsionam o sucesso do cliente do Azure. A Microsoft pode atribuir influência e receita consumida do Azure à sua organização com base nas permissões da conta (função do Azure) e no âmbito (subscrição, grupo de recursos, recurso).

## <a name="get-access-from-your-customer"></a>Obter acesso a partir do cliente

Antes de ligar o ID de parceiro, o cliente deve fornecer acesso aos recursos do Azure através de uma das seguintes opções:

- **Utilizador convidado**: o cliente pode adicioná-lo como utilizador convidado e atribuir-lhe qualquer função do Azure. Para obter mais informações, veja [Adicionar utilizadores convidados a partir de outro diretório](../../active-directory/external-identities/what-is-b2b.md).

- **Conta de diretório**: o cliente pode criar uma conta de utilizador para si no seu próprio diretório e atribuir qualquer função do Azure.

- **Principal de serviço**: o cliente pode adicionar uma aplicação ou script da sua organização ao diretório e atribuir qualquer função do Azure. A identidade da aplicação ou do script é conhecida como um principal de serviço.

- **Azure Lighthouse**: O seu cliente pode delegar uma subscrição (ou um grupo de recursos) para que os seus utilizadores possam trabalhar na mesma a partir do seu inquilino. Para obter mais informações, veja [Gestão de recursos delegados do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Ligar a um ID de parceiro

Quando tiver acesso aos recursos do cliente, utilize o portal do Azure, o PowerShell ou a CLI do Azure para ligar o ID do Microsoft Partner Network (ID do MPN) ao ID de utilizador ou ao principal de serviço. Ligue o ID de parceiro em cada inquilino de cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Utilizar o portal do Azure para ligar a um novo ID de parceiro

1. Aceda a [Ligar a um ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Inicie sessão no Portal do Azure.

3. Introduza o ID de parceiro Microsoft. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização. Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro.

   ![Captura de ecrã que mostra Ligar a um ID de parceiro](./media/link-partner-id/link-partner-id01.png)

4. Para ligar a um ID de parceiro a partir de outro cliente, mude o diretório. Em **Mudar diretório**, selecione o seu diretório.

   ![Captura de ecrã que mostra Mudar diretório](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Utilizar o PowerShell para ligar a um novo ID de parceiro

1. Instale o módulo do PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, veja [Iniciar sessão no PowerShell](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Ligação para o novo ID de parceiro. O ID de parceiro é o [ID do Microsoft Partner Network](https://partner.microsoft.com/) da sua organização. Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro.


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

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou o principal de serviço. Para obter mais informações, veja [Iniciar sessão na CLI do Azure](/cli/azure/authenticate-azure-cli).

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

No entanto, se estiver a gerir recursos do cliente através do Azure Lighthouse, terá de criar a ligação no seu inquilino do fornecedor de serviços, utilizando uma conta que tenha acesso aos recursos do cliente. Para obter mais informações, veja [Ligar o seu ID de parceiro para controlar o impacto nos recursos delegados](../../lighthouse/how-to/partner-earned-credit.md).

**Outros parceiros ou clientes podem editar ou remover a ligação para o ID de parceiro?**

A ligação é associada ao nível da conta de utilizador. Apenas o utilizador da conta pode editar ou remover a ligação para o ID do parceiro. O cliente e os outros parceiros não podem alterar a ligação para o ID de parceiro.

**Qual ID do MPN devo utilizar se a minha empresa tiver vários?**

Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro.

**Onde posso encontrar relatórios de receitas influenciadas para o ID de parceiro ligado?**

Os relatórios de Desempenho dos Produtos na Cloud estão disponíveis para os parceiros no Centro de Parceiros no [ painel As Minhas Informações](https://partner.microsoft.com/membership/reports/myinsights). Precisa de selecionar a Ligação de Administração de Parceiros como o tipo de associação de parceiro.

**Por que não consigo ver o meu cliente nos relatórios?**

Não consegue ver o cliente nos relatórios pelos seguintes motivos

1. A conta de utilizador associada não tem o [controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) em nenhuma subscrição ou recurso do Azure do cliente.

2. A subscrição do Azure em que o utilizador tem o [controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) não tem nenhuma utilização.

**O ID de parceiro da ligação funciona com o Azure Stack?**

Sim, pode ligar o ID de parceiro com o Azure Stack.

**Como posso associar o meu ID de parceiro se a minha empresa utilizar o [Azure Lighthouse](../../lighthouse/overview.md) para aceder aos recursos do cliente?**

Se integrar clientes na gestão de recursos delegados do Azure mediante a [publicação de uma oferta de serviços geridos no Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md), o seu ID do MPN é adicionado automaticamente.

Se [integrar clientes através da implementação de modelos do Azure Resource Manager](../../lighthouse/how-to/onboard-customer.md), terá de associar o seu ID do MPN a, pelo menos, uma conta de utilizador que tenha acesso a cada subscrição integrada. Tenha em atenção que terá de fazer isto no inquilino do seu fornecedor de serviços, e não em cada inquilino de cliente. Para simplificar, é recomendável criar uma conta principal de serviço no seu inquilino, associando-a ao seu ID do MPN e, em seguida, concedendo-lhe acesso a todos os clientes que integrar com uma [Função incorporada do Azure, que seja elegível para crédito concedido pelo parceiro](/partner-center/azure-roles-perms-pec). Para obter mais informações, veja [Ligar o seu ID de parceiro para controlar o impacto nos recursos delegados](../../lighthouse/how-to/partner-earned-credit.md).

**Como posso explicar o Partner Admin Link (PAL) ao meu cliente?**

O Partner Admin Link (PAL) permite à Microsoft identificar e reconhecer os parceiros que ajudam os clientes a alcançar os objetos empresariais e a retirar valor da cloud. Os clientes têm de, primeiro, dar acesso ao parceiro ao respetivo recurso do Azure. Depois de concedido o acesso, é associado o ID da Microsoft Partner Network (MPN IN) do parceiro. Essa associação ajuda a Microsoft a conhecer o ecossistema de fornecedores de serviços de TI e a refinar as ferramentas e os programas necessários para melhor apoiar os nossos clientes comuns.

**Que dados são recolhidos pelo PAL?**

A associação do PAL a credenciais já existentes não fornece dados novos dos clientes à Microsoft. Disponibiliza simplesmente a telemetria à Microsoft na qual o parceiro está ativamente envolvido num ambiente do Azure de um cliente. A Microsoft pode atribuir influência e receita consumida do Azure do ambiente do cliente à organização do parceiro com base nas permissões da conta (função do Azure) e no âmbito (Grupo de Gestão, Subscrição, Grupo de Recursos, Recurso) que o cliente dá ao parceiro. 

**Esta associação afeta a segurança dos ambientes do Azure dos clientes?**

A associação do PAL só adiciona o ID da MPN dos parceiros à credencial já aprovisionada e não altera nenhuma outra permissão (função do Azure) nem envia dados adicionais do serviço do Azure ao parceiro ou à Microsoft.