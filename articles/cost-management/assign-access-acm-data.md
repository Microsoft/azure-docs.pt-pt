---
title: Atribuir acesso aos dados de gerenciamento de custos do Azure
description: Este artigo orienta você durante a atribuição de permissão aos dados de gerenciamento de custos do Azure para vários escopos de acesso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375135"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso aos dados de gerenciamento de custos

Para usuários com contratos Enterprise do Azure, uma combinação de permissões concedidas no portal do Azure e no portal Enterprise (EA) definem o nível de acesso de um usuário aos dados de gerenciamento de custos do Azure. Para usuários com outros tipos de conta do Azure, definir o nível de acesso de um usuário aos dados de gerenciamento de custos é mais simples usando o controle de acesso baseado em função do Azure. Este artigo orienta você pela atribuição de acesso aos dados de gerenciamento de custos. Depois que a combinação de permissões é atribuída, o usuário exibe os dados no gerenciamento de custos com base no escopo ao qual eles têm acesso e no escopo que eles selecionam na portal do Azure.

O escopo que um usuário seleciona é usado em todo o gerenciamento de custos para fornecer consolidação de dados e controlar o acesso a informações de custo. Ao usar escopos, os usuários não os selecionam de várias seleções. Em vez disso, eles selecionam um escopo maior que os escopos filho acumulam e, em seguida, filtram o que desejam exibir. A consolidação de dados é importante entender porque algumas pessoas não devem ter acesso a um escopo pai do qual os escopos filho são acumulados.

Assista ao vídeo [como atribuir acesso com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=J997ckmwTa8) para saber mais sobre a atribuição de acesso para exibir custos e encargos com o controle de acesso baseado em função do Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Escopos de gerenciamento de custos

O gerenciamento de custos dá suporte a uma variedade de tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). O tipo de conta determina os escopos disponíveis.

### <a name="azure-ea-subscription-scopes"></a>Escopos de assinatura do EA do Azure

Para exibir dados de custo para assinaturas do Azure EA, um usuário deve ter pelo menos acesso de leitura a um ou mais dos escopos a seguir.

| **Âmbito** | **Definido no** | **Acesso necessário para exibir dados** | **Definição de pré-requisitos de EA** | **Consolida dados para** |
| --- | --- | --- | --- | --- |
| Conta de faturação<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador da Empresa | Nenhuma | Todas as subscrições do contrato enterprise |
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

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Habilitar o acesso aos custos no portal de EA

O escopo do departamento requer a opção **da exibição de encargos do da** **habilitada** no portal de ea. Todos os outros escopos exigem a opção ao **Exibir encargos** **habilitada** no portal de ea.

Para habilitar uma opção:

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Para os escopos de gerenciamento de custos aos quais você deseja fornecer acesso, habilite a opção de encargo para os **encargos da exibição do da** e/ou do **modo de exibição**do ao.  
    guia ![Enrollment mostrando as opções de encargos DA exibição de de e para. @ no__t-1

Depois que as opções exibir cobrança estiverem habilitadas, a maioria dos escopos também exigirá a configuração de permissão RBAC (controle de acesso baseado em função) no portal do Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador corporativo

Por padrão, um administrador corporativo tem acesso à conta de cobrança (Enterprise Agreement/registro) e a todos os outros escopos, que são escopos filho. O administrador corporativo atribui acesso aos escopos para outros usuários. Como prática recomendada para a continuidade dos negócios, você sempre deve ter dois usuários com acesso de administrador corporativo. As seções a seguir são exemplos detalhados do administrador corporativo que atribui acesso a escopos para outros usuários.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso ao escopo da conta de cobrança

O acesso ao escopo da conta de cobrança requer permissão de administrador corporativo no portal do EA. O administrador da empresa tem acesso para exibir custos em todo o registro de EA ou em vários registros. Nenhuma ação é necessária no portal do Azure para o escopo da conta de cobrança.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.  
    ![select seu registro no portal de EA @ no__t-1
4. Clique em **+ Adicionar Administrador**.
5. Na caixa Adicionar Administrador, selecione o tipo de autenticação e digite o endereço de email do usuário.
6. Se o usuário tiver acesso somente leitura aos dados de custo e de uso, em **somente leitura**, selecione **Sim**.  Caso contrário, selecione **não**.
7. Clique em **Adicionar** para criar a conta.  
    informações de @no__t 0example mostradas na caixa Adicionar Administrador @ no__t-1

Pode levar até 30 minutos para que o novo usuário possa acessar os dados no gerenciamento de custos.

### <a name="assign-department-scope-access"></a>Atribuir acesso ao escopo do departamento

O acesso ao escopo do departamento requer acesso de administrador do departamento (encargos de exibição DA) no portal de EA. O administrador do departamento tem acesso para exibir os custos e os dados de uso associados a um departamento ou a vários departamentos. Os dados do departamento incluem todas as assinaturas que pertencem a uma conta de registro vinculada ao departamento. Nenhuma ação é necessária no portal do Azure.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.
4. Clique na guia **Departamento** e, em seguida, clique em **Adicionar Administrador**.
5. Na caixa Adicionar Administrador do departamento, selecione o tipo de autenticação e digite o endereço de email do usuário.
6. Se o usuário tiver acesso somente leitura aos dados de custo e de uso, em **somente leitura**, selecione **Sim**.  Caso contrário, selecione **não**.
7. Selecione os departamentos aos quais você deseja conceder permissão administrativa de departamento.
8. Clique em **Adicionar** para criar a conta.  
    ![enter informações necessárias na caixa Adicionar Administrador do departamento @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso ao escopo da conta de registro

O acesso ao escopo da conta de registro requer acesso de proprietário da conta (encargos de exibição do ao) no portal de EA. O proprietário da conta pode exibir os custos e os dados de uso associados às assinaturas criadas por meio dessa conta de registro. Nenhuma ação é necessária no portal do Azure.

1. Entre no portal de EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. Selecione **gerenciar** no painel esquerdo.
3. Na guia **registro** , selecione o registro que você deseja gerenciar.
4. Clique na guia **conta** e, em seguida, clique em **adicionar conta**.
5. Na caixa Adicionar conta, selecione o **Departamento** ao qual associar a conta ou deixe-a como não atribuída.
6. Selecione o tipo de autenticação e digite o nome da conta.
7. Digite o endereço de email do usuário e, opcionalmente, digite o centro de custo.
8. Clique em **Adicionar** para criar a conta.  
    ![enter informações necessárias na caixa Adicionar conta para uma conta de registro @ no__t-1

Depois de concluir as etapas acima, a conta de usuário se tornará uma conta de registro no Enterprise Portal e poderá criar assinaturas. O usuário pode acessar dados de uso e custo para assinaturas que eles criam.

## <a name="assign-management-group-scope-access"></a>Atribuir acesso ao escopo do grupo de gerenciamento

O acesso para exibir o escopo do grupo de gerenciamento requer pelo menos a permissão leitor de gerenciamento de custos (ou leitor). Você pode configurar permissões para um grupo de gerenciamento no portal do Azure. Você deve ter pelo menos a permissão de administrador de acesso do usuário (ou proprietário) para o grupo de gerenciamento para habilitar o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, procure _grupos de gerenciamento_e selecione **grupos de gerenciamento**.
3. Selecione o grupo de gerenciamento na hierarquia.
4. Ao lado do nome do grupo de gerenciamento, clique em **detalhes**.
5. Selecione **controle de acesso (iam)** no painel esquerdo.
6. Clique em **Adicionar**.
7. Em **função**, selecione **leitor de gerenciamento de custos**.
8. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
9. Para atribuir acesso, procure e selecione o usuário.
10. Clique em **Guardar**.  
    informações de @no__t 0example na caixa adicionar permissões para um grupo de gerenciamento @ no__t-1

## <a name="assign-subscription-scope-access"></a>Atribuir acesso ao escopo da assinatura

O acesso a uma assinatura requer pelo menos a permissão leitor de gerenciamento de custos (ou leitor). Você pode configurar permissões para uma assinatura no portal do Azure. Você deve ter pelo menos a permissão de administrador de acesso do usuário (ou proprietário) para a assinatura para habilitar o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, pesquise por _assinaturas_e, em seguida, selecione **assinaturas**.
3. Selecione a sua subscrição.
4. Selecione **controle de acesso (iam)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **função**, selecione **leitor de gerenciamento de custos**.
7. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
8. Para atribuir acesso, procure e selecione o usuário.
9. Clique em **Guardar**.

## <a name="assign-resource-group-scope-access"></a>Atribuir acesso ao escopo do grupo de recursos

O acesso a um grupo de recursos requer pelo menos a permissão leitor de gerenciamento de custos (ou leitor). Você pode configurar permissões para um grupo de recursos no portal do Azure. Você deve ter pelo menos a permissão de administrador de acesso do usuário (ou proprietário) para que o grupo de recursos habilite o acesso para outras pessoas. E, para contas do Azure EA, você também deve ter habilitado a configuração de **encargos de exibição** do ao no portal do ea.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **todos os serviços** na barra lateral, pesquise _grupos de recursos_e, em seguida, selecione **grupos de recursos**.
3. Selecione seu grupo de recursos.
4. Selecione **controle de acesso (iam)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **função**, selecione **leitor de gerenciamento de custos**.
7. Em **atribuir acesso a**, selecione **usuário, grupo ou aplicativo do Azure ad**.
8. Para atribuir acesso, procure e selecione o usuário.
9. Clique em **Guardar**.

## <a name="cross-tenant-authentication-issues"></a>Problemas de autenticação entre locatários

Atualmente, o gerenciamento de custos do Azure tem suporte limitado para autenticação entre locatários. Em algumas circunstâncias, quando você tenta autenticar entre locatários, você pode receber um erro de **acesso negado** na análise de custo. Esse problema pode ocorrer se você configurar o controle de acesso baseado em função (RBAC) para a assinatura de outro locatário e, em seguida, tentar exibir dados de custo.

*Para solucionar o problema*: depois de configurar o RBAC entre locatários, aguarde uma hora. Em seguida, tente exibir os custos na análise de custos ou conceder acesso de gerenciamento de custos aos usuários em ambos os locatários.  


## <a name="next-steps"></a>Passos seguintes

- Se você ainda não tiver concluído o primeiro início rápido para o gerenciamento de custos, leia-o em [comece a analisar os custos](quick-acm-cost-analysis.md).
