---
title: Atribuir acesso aos dados de gerenciamento de custos do Azure
description: Este artigo descreve apesar de atribuir permissões a dados do Azure Cost Management para vários âmbitos de acesso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219853"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados de gestão de custos

Para usuários com contratos Enterprise do Azure, uma combinação de permissões concedidas no portal do Azure e no portal Enterprise (EA) definem o nível de acesso de um usuário aos dados de gerenciamento de custos do Azure. Para usuários com outros tipos de conta do Azure, definir o nível de acesso de um usuário aos dados de gerenciamento de custos é mais simples usando o controle de acesso baseado em função do Azure. Este artigo orienta-o através da atribuição de acesso a dados de gestão de custos. Após a combinação de permissões é atribuída, os dados de vistas de utilizador no Cost Management com a base do âmbito que têm acesso a e no âmbito que selecionarem no portal do Azure.

O âmbito do que um usuário selecionar é utilizado em toda a gestão de custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Quando utilizar âmbitos, os utilizadores não seleção múltipla-los. Em vez disso, eles selecionam um âmbito maior que âmbitos subordinados até implementar e, em seguida, eles filtro pendente para o que quer ver. Consolidação de dados é importante compreender porque algumas pessoas não devem ter acesso a um âmbito principal que âmbitos subordinados até implementar.

Assista ao vídeo [como atribuir acesso com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=J997ckmwTa8) para saber mais sobre a atribuição de acesso para exibir custos e encargos com o controle de acesso baseado em função do Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Âmbitos de gestão de custos

O gerenciamento de custos dá suporte a uma variedade de tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). O tipo de conta determina os escopos disponíveis.

### <a name="azure-ea-subscription-scopes"></a>Escopos de assinatura do EA do Azure

Para exibir dados de custo para assinaturas do Azure EA, um usuário deve ter pelo menos acesso de leitura a um ou mais dos escopos a seguir.

| **Âmbito** | **Definido no** | **Acesso necessário para exibir dados** | **Definição de pré-requisitos de EA** | **Consolida dados para** |
| --- | --- | --- | --- | --- |
| Conta de faturação<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador da Empresa | Nenhum | Todas as subscrições do contrato enterprise |
| Departamento | [https://ea.azure.com](https://ea.azure.com/) | Administrador do Departamento | **Encargos da exibição do da** habilitada | Todas as subscrições que pertencem a uma conta de inscrição que está associada ao departamento |
| Conta de inscrição<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietário da Conta | **Encargos de exibição** do ao habilitados | Todas as subscrições da conta de inscrição |
| Grupo de gestão | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **Encargos de exibição** do ao habilitados | Todas as subscrições abaixo o grupo de gestão |
| Subscrição | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **Encargos de exibição** do ao habilitados | Todos os recursos/grupos de recursos na subscrição |
| Grupo de recursos | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Cost Management (ou Leitor) | **Encargos de exibição** do ao habilitados | Todos os recursos no grupo de recursos |

<sup>1</sup> a conta de cobrança também é conhecida como o Enterprise Agreement ou o registro.

<sup>2</sup> a conta de registro também é conhecida como o proprietário da conta.

O diagrama a seguir ilustra a relação entre escopos de gerenciamento de custos com funções e configurações do portal de EA.

![Diagrama mostrando a relação entre escopos de gerenciamento de custos com funções e configurações do portal de EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Quando os **encargos da exibição do da** são desabilitados no portal de ea, você verá uma mensagem informando *custos desabilitados para sua organização* ao tentar exibir os custos de departamentos e contas.

Da mesma forma, quando os **encargos de exibição** do ao são desabilitados no portal do ea, você verá uma mensagem informando *os custos desabilitados para sua organização* ao tentar exibir os custos de contas de registro, grupos de gerenciamento, assinaturas e grupos de recursos.

## <a name="other-azure-account-scopes"></a>Outros escopos de conta do Azure

Para exibir dados de custo para outras assinaturas do Azure, um usuário deve ter pelo menos acesso de leitura a um ou mais dos seguintes escopos:

- Conta do Azure
- Grupo de gestão
- Grupo de recursos

Vários escopos estão disponíveis depois que os parceiros integram clientes a um contrato de cliente da Microsoft. Os clientes do CSP podem usar os recursos de gerenciamento de custos quando habilitados pelo parceiro CSP. Para obter mais informações, consulte Introdução [ao gerenciamento de custos do Azure para parceiros](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Ativar o acesso aos custos no portal de EA

O escopo do departamento requer a opção **da exibição de encargos do da** **habilitada** no portal de ea. Todos os outros escopos exigem a opção ao **Exibir encargos** **habilitada** no portal de ea.

Para ativar uma opção:

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Para os escopos de gerenciamento de custos aos quais você deseja fornecer acesso, habilite a opção de encargo para os **encargos da exibição do da** e/ou do **modo de exibição**do ao.  
    ![guia registro mostrando as opções de encargos DA exibição de DA e do ao](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Depois das opções de custo de vista estiverem ativadas, a maioria dos âmbitos também exigem configuração de permissão do controlo (RBAC) de acesso baseado em funções no portal do Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador de empresa

Por predefinição, o administrador da empresa tem acesso à conta de faturação (Enterprise Agreement/inscrição) e a todos os outros âmbitos, que são os âmbitos subordinados. O administrador de empresa atribui o acesso a âmbitos para outros utilizadores. Como melhor prática para continuidade do negócio, deve ter sempre dois utilizadores com acesso de administrador de empresa. As secções seguintes são exemplos passo a passo do acesso de atribuição de administrador empresarial a âmbitos para outros utilizadores.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso de âmbito de conta faturação

Acesso ao âmbito da conta de faturação requer permissão de administrador de empresa no portal do EA. O administrador de empresa tem acesso para ver os custos entre a inscrição de EA toda ou várias inscrições. É necessária nenhuma ação no portal do Azure para o âmbito da conta de faturação.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.  
    ![selecionar seu registro no portal de EA](./media/assign-access-acm-data/ea-portal.png)
4. Clique em **+ Adicionar Administrador**.
5. Na caixa de adicionar administrador, selecione o tipo de autenticação e escreva o endereço de e-mail do utilizador.
6. Se o usuário tiver acesso somente leitura aos dados de custo e de uso, em **somente leitura**, selecione **Sim**.  Caso contrário, selecione **não**.
7. Clique em **Adicionar** para criar a conta.  
    ![informações de exemplo mostradas na caixa Adicionar Administrador](./media/assign-access-acm-data/add-admin.png)

Pode demorar até 30 minutos para que o novo utilizador possa aceder a dados no Cost Management.

### <a name="assign-department-scope-access"></a>Atribuir acesso de âmbito do departamento

Acesso ao âmbito departamento requer o acesso de (custos de vista DA) do departamento administrador no portal do EA. O administrador do departamento tem acesso para ver os custos e os dados de utilização associado a um departamento ou para vários departamentos. Os dados para o departamento de incluem todas as subscrições pertencentes a uma conta de inscrição que estejam ligadas ao departamento. É necessária nenhuma ação no portal do Azure.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.
4. Clique na guia **Departamento** e, em seguida, clique em **Adicionar Administrador**.
5. Na caixa de adicionar administrador de departamento, selecione o tipo de autenticação e, em seguida, escreva o endereço de e-mail do utilizador.
6. Se o usuário tiver acesso somente leitura aos dados de custo e de uso, em **somente leitura**, selecione **Sim**.  Caso contrário, selecione **não**.
7. Selecione os departamentos que pretende conceder permissões administrativas do departamento para.
8. Clique em **Adicionar** para criar a conta.  
    ![Insira as informações necessárias na caixa Adicionar Administrador do departamento](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso de âmbito de conta de inscrição

Acesso ao âmbito da conta de inscrição requer acesso de (custos de exibição de pedidos) de proprietário de conta no portal de EA. O proprietário da conta pode ver os custos e os dados de utilização associados às subscrições criadas a partir dessa conta de inscrição. É necessária nenhuma ação no portal do Azure.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.
4. Clique na guia **conta** e, em seguida, clique em **adicionar conta**.
5. Na caixa Adicionar conta, selecione o **Departamento** ao qual associar a conta ou deixe-a como não atribuída.
6. Selecione o tipo de autenticação e escreva o nome da conta.
7. Escreva o endereço de e-mail do utilizador e, em seguida, opcionalmente, escreva o Centro de custos.
8. Clique em **Adicionar** para criar a conta.  
    ![Insira as informações necessárias na caixa Adicionar conta para uma conta de registro](./media/assign-access-acm-data/add-account.png)

Depois de concluir os passos acima, a conta de utilizador torna-se uma conta de inscrição no portal da empresa e pode criar subscrições. O utilizador pode aceder a dados de utilização e de custo para as subscrições que criaram.

## <a name="assign-management-group-scope-access"></a>Atribuir acesso de âmbito do grupo de gestão

O acesso para exibir o escopo do grupo de gerenciamento requer pelo menos a permissão leitor de gerenciamento de custos (ou leitor). Pode configurar permissões para um grupo de gestão no portal do Azure. Tem de ter, pelo menos, a permissão de administrador de acesso de utilizador (ou proprietário) para o grupo de gestão ativar o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, procure _grupos de gerenciamento_e selecione **grupos de gerenciamento**.
3. Selecione o grupo de gestão na hierarquia.
4. Ao lado do nome do grupo de gerenciamento, clique em **detalhes**.
5. Selecione **controle de acesso (iam)** no painel esquerdo.
6. Clique em **Adicionar**.
7. Em **função**, selecione **leitor de gerenciamento de custos**.
8. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
9. Para atribuir acesso, procure e, em seguida, selecione o utilizador.
10. Clique em **Guardar**.  
    ![informações de exemplo na caixa adicionar permissões para um grupo de gerenciamento](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Atribuir acesso de âmbito da subscrição

Acesso a uma subscrição, pelo menos, requer a permissão do leitor de gestão de custos (ou leitor). Pode configurar as permissões para uma subscrição no portal do Azure. Tem de ter, pelo menos, a permissão de administrador de acesso de utilizador (ou proprietário) para a subscrição ativar o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, pesquise por _assinaturas_e, em seguida, selecione **assinaturas**.
3. Selecione a sua subscrição.
4. Selecione **controle de acesso (iam)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **função**, selecione **leitor de gerenciamento de custos**.
7. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
8. Para atribuir acesso, procure e, em seguida, selecione o utilizador.
9. Clique em **Guardar**.

## <a name="assign-resource-group-scope-access"></a>Atribuir acesso de âmbito do grupo de recursos

Acesso a um grupo de recursos requer, pelo menos, a permissão do leitor de gestão de custos (ou leitor). Pode configurar permissões para um grupo de recursos no portal do Azure. Tem de ter, pelo menos, a permissão de administrador de acesso de utilizador (ou proprietário) para o grupo de recursos ativar o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, pesquise _grupos de recursos_e, em seguida, selecione **grupos de recursos**.
3. Selecione o grupo de recursos.
4. Selecione **controle de acesso (iam)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **função**, selecione **leitor de gerenciamento de custos**.
7. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
8. Para atribuir acesso, procure e, em seguida, selecione o utilizador.
9. Clique em **Guardar**.

## <a name="cross-tenant-authentication-issues"></a>Problemas de autenticação entre locatários

Atualmente, o gerenciamento de custos do Azure tem suporte limitado para autenticação entre locatários. Em algumas circunstâncias, quando você tenta autenticar entre locatários, você pode receber um erro de **acesso negado** na análise de custo. Esse problema pode ocorrer se você configurar o controle de acesso baseado em função (RBAC) para a assinatura de outro locatário e, em seguida, tentar exibir dados de custo.

*Para solucionar o problema*: depois de configurar o RBAC entre locatários, aguarde uma hora. Em seguida, tente exibir os custos na análise de custos ou conceder acesso de gerenciamento de custos aos usuários em ambos os locatários.  


## <a name="next-steps"></a>Passos seguintes

- Se você ainda não tiver concluído o primeiro início rápido para o gerenciamento de custos, leia-o em [comece a analisar os custos](quick-acm-cost-analysis.md).
