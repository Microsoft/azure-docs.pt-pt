---
title: Atribuir acesso a dados do Azure Cost Management
description: Este artigo mostra-lhe os passos para a atribuição de permissão a dados do Azure Cost Management para vários âmbitos de acesso.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: cb54c5f7334120f6cd01ed1704939c5c1a55e7c6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645282"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados do Cost Management

Para os utilizadores com contratos Azure Enterprise, uma combinação de permissões concedidas no portal do Azure e no portal Enterprise (EA) define o nível de acesso de um utilizador aos dados do Azure Cost Management. Relativamente aos utilizadores com outros tipos de contas do Azure, a definição do nível de acesso de um utilizador aos dados do Cost Management é mais simples através da utilização do controlo de acesso baseado em funções do Azure (RBAC do Azure). Este artigo mostra-lhe os passos para atribuir acesso a dados do Cost Management. Depois de a combinação de permissões ser atribuída, o utilizador vê os dados no Cost Management com base no seu âmbito de acesso e no âmbito que seleciona no portal do Azure.

O âmbito que um utilizador seleciona será utilizado em todo o Cost Management para fornecer a consolidação de dados e para controlar o acesso às informações relativas aos custos. Ao utilizarem os âmbitos, os utilizadores não podem selecionar vários. Em vez disso, selecionam um âmbito maior onde os âmbitos subordinados estão incluídos e, em seguida, filtram para o que querem visualizar. É importante que compreenda a consolidação dos dados, porque algumas pessoas não devem aceder a um âmbito principal no qual os âmbitos subordinados estão incluídos.

Veja o vídeo [Cost Management controlling access](https://www.youtube.com/watch?v=_uQzQ9puPyM) (Controlo de acesso do Cost Management) para saber mais sobre a atribuição de acesso para ver custos e cobranças com o controlo de acesso baseado em funções do Azure (RBAC do Azure). Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Âmbitos do Cost Management

O Cost Management suporta uma variedade de tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md). O tipo de conta determina os âmbitos disponíveis.

### <a name="azure-ea-subscription-scopes"></a>Âmbitos de subscrição do Azure EA

Para ver os dados dos custos das subscrições do Azure EA, um utilizador tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos.

| **Âmbito** | **Definido no** | **Acesso necessário para ver dados** | **Definição de pré-requisitos de EA** | **Consolida dados para** |
| --- | --- | --- | --- | --- |
| Conta de faturação<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador da Empresa | Nenhuma | Todas as subscrições do contrato enterprise |
| Departamento | [https://ea.azure.com](https://ea.azure.com/) | Administrador do Departamento | **Custos da vista do AD** ativados | Todas as subscrições que pertencem a uma conta de inscrição que está associada ao departamento |
| Conta de inscrição<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietário da Conta | **Custos da vista do PC** ativados | Todas as subscrições da conta de inscrição |
| Grupo de gestão | [https://portal.azure.com](https://portal.azure.com/) | Leitor de Gestão de Custos (ou Contribuinte) | **Custos da vista do PC** ativados | Todas as subscrições abaixo o grupo de gestão |
| Subscrição | [https://portal.azure.com](https://portal.azure.com/) | Leitor de Gestão de Custos (ou Contribuinte) | **Custos da vista do PC** ativados | Todos os recursos/grupos de recursos na subscrição |
| Grupo de recursos | [https://portal.azure.com](https://portal.azure.com/) | Leitor de Gestão de Custos (ou Contribuinte) | **Custos da vista do PC** ativados | Todos os recursos no grupo de recursos |

<sup>1</sup> A conta de faturação é também referida como o Contrato Enterprise ou Inscrição.

<sup>2</sup> A conta de inscrição é também referida como o proprietário da conta.


## <a name="other-azure-account-scopes"></a>Outros âmbitos de contas do Azure

Para ver os dados de custos de outras subscrições do Azure, um utilizador tem de ter, pelo menos, acesso de leitura a um ou mais dos seguintes âmbitos:

- Grupo de gestão
- Subscrição
- Grupo de recursos

Estão disponíveis vários âmbitos depois de os parceiros integrarem os clientes num Contrato de Cliente Microsoft. Os clientes de CSP podem, depois, utilizar as funcionalidades do Cost Management quando estas forem ativadas pelo seu parceiro de CSP. Para obter mais informações, veja [Introdução ao Azure Cost Management para parceiros](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Permitir o acesso aos custos no portal do Azure

O âmbito de departamento requer a opção **Os administradores de departamento podem ver os custos** definida como **Ativado**. Configure a opção no portal do Azure ou no EA Portal. Todos os outros âmbitos requerem a opção **Os proprietários de contas podem ver os custos** definida como **Ativado**.

Para ativar uma opção no portal do Azure:

1. Inicie sessão no portal do Azure em https://portal.azure.com com uma conta de administrador empresarial.
1. Selecione o item de menu **Cost Management + Faturação**.
1. Selecione **Âmbitos de faturação** para ver uma lista de âmbitos de faturação e contas de faturação disponíveis.
1. Selecione a sua **Conta de Faturação** na lista de contas de faturação disponíveis.
1. Em **Definições**, selecione o item de menu **Políticas** e configure a definição.  
    ![Políticas de âmbito de faturação que mostram as opções de visualização de custos](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Depois de as opções das vistas de custos serem ativadas, a maioria dos âmbitos também requer uma configuração de permissão de controlo de acesso baseado em funções do Azure (RBAC do Azure) no portal do Azure.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Ativar o acesso aos custos no EA Portal

O âmbito do departamento requer a opção **Custos da vista do AD** como **Ativada** no EA Portal. Configure a opção no portal do Azure ou no EA Portal. Todos os outros âmbitos requerem a opção **Custos da vista do CP** como **Ativada** no EA Portal.

Para ativar uma opção no EA Portal:

1. Inicie sessão no EA Portal em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador empresarial.
2. Selecione **Gerir** no painel esquerdo.
3. Para os âmbitos de gestão de custos aos quais pretende fornecer acesso, ative a opção de custos para **Custos da vista do AD** e/ou **Custos da vista do CP**.  
    ![Separador Inscrição que mostra as opções de custos da vista do AD e do CP](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Depois de as opções das vistas de custos serem ativadas, a maioria dos âmbitos também requer uma configuração de permissão de controlo de acesso baseado em funções do Azure (RBAC do Azure) no portal do Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador empresarial

Por predefinição, um administrador empresarial pode aceder à conta de faturação (Contrato Enterprise/inscrição) e a todos os outros âmbitos, que são âmbitos subordinados. O administrador empresarial atribui acesso aos âmbitos a outros utilizadores. Enquanto melhor prática para a continuidade empresarial, deve ter sempre dois utilizadores com acesso de administrador empresarial. As secções que se seguem são exemplos de instruções de um administrador empresarial a atribuir acesso a âmbitos a outros utilizadores.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso ao âmbito da conta de faturação

O acesso ao âmbito da conta de faturação requer a permissão do administrador empresarial no EA Portal. O administrador empresarial pode ver os custos em toda a inscrição de EA ou em várias inscrições. Não é necessária qualquer ação no portal do Azure para o âmbito de conta de faturação.

1. Inicie sessão no EA Portal em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador empresarial.
2. Selecione **Gerir** no painel esquerdo.
3. No separador **Inscrição**, selecione a inscrição que quer gerir.  
    ![selecionar a inscrição no EA Portal](./media/assign-access-acm-data/ea-portal.png)
4. Selecione **+ Adicionar Administrador**.
5. Na caixa Adicionar Administrador, selecione o tipo de autenticação e escreva o endereço de e-mail do utilizador.
6. Caso o utilizador deva ter acesso só de leitura aos dados de custos e utilização, em **Só de leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione **Adicionar** para criar a conta.  
    ![informações de exemplo mostradas na caixa Adicionar Administrador](./media/assign-access-acm-data/add-admin.png)

Poderá ter de esperar até 30 minutos até que o novo utilizador tenha acesso aos dados no Cost Management.

### <a name="assign-department-scope-access"></a>Atribuir acesso de âmbito de departamento

O acesso de âmbito de departamento requer acesso de administrador de departamento (custos da vista do AD) no EA Portal. O administrador de departamento pode ver os custos e os dados de utilização associados a um departamento ou a vários departamentos. Os dados do departamento incluem todas as subscrições que pertencem a uma conta de inscrição que está associada ao departamento. Não é necessária qualquer ação no portal do Azure.

1. Inicie sessão no EA Portal em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador empresarial.
2. Selecione **Gerir** no painel esquerdo.
3. No separador **Inscrição**, selecione a inscrição que quer gerir.
4. Selecione o separador **Departamento** e, em seguida, selecione **Adicionar Administrador**.
5. Na caixa Adicionar Administrador de Departamento, selecione o tipo de autenticação e, em seguida, escreva o endereço de e-mail do utilizador.
6. Caso o utilizador deva ter acesso só de leitura aos dados de custos e utilização, em **Só de leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione os departamentos aos quais quer conceder permissão administrativa de departamentos.
8. Selecione **Adicionar** para criar a conta.  
    ![introduzir as informações necessárias na caixa Adicionar administrador de departamento](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso de âmbito de conta de inscrição

O acesso ao âmbito de conta de inscrição requer o acesso de proprietário da conta (custos de vista do PC) no EA Portal. O proprietário da conta pode ver os dados de custos e de utilização associados às subscrições criadas nessa conta de inscrição. Não é necessária qualquer ação no portal do Azure.

1. Inicie sessão no EA Portal em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador empresarial.
2. Selecione **Gerir** no painel esquerdo.
3. No separador **Inscrição**, selecione a inscrição que quer gerir.
4. Selecione o separador **Conta** e, em seguida, selecione **Adicionar Conta**.
5. Na caixa Adicionar Conta, selecione o **Departamento** a associar à conta ou deixe-o como não atribuído.
6. Selecione o tipo de autenticação e escreva o nome da conta.
7. Escreva o endereço de e-mail do utilizador e, em seguida, escreva o centro de custos (opcional).
8. Selecione **Adicionar** para criar a conta.  
    ![introduzir as informações necessárias na caixa Adicionar Conta numa conta de inscrição](./media/assign-access-acm-data/add-account.png)

Depois de completar os passos acima, a conta de utilizador torna-se numa conta de inscrição no EA Portal e pode criar subscrições. O utilizador pode aceder aos dados dos custos e da utilização das subscrições que criar.

## <a name="assign-management-group-scope-access"></a>Atribuir acesso de âmbito de grupo de gestão

O acesso para ver o âmbito de grupo de gestão requer, no mínimo, a permissão de Leitor do Cost Management (ou Leitor). Pode configurar permissões para um grupo de gestão no portal do Azure. Tem de ter, no mínimo, a permissão de Administrador de Acesso de Utilizadores (ou Proprietário) para o grupo de gestão para permitir o acesso aos outros. No caso das contas do Azure EA, também tem de ativar a definição **Custos de vista do CP** no EA Portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Todos os Serviços** na barra lateral, procure _grupos de gestão_ e, em seguida, selecione **grupos de gestão**.
3. Selecione o grupo de gestão na hierarquia.
4. Ao lado do nome do grupo de gestão, selecione **Detalhes**.
5. Selecione **Controlo de Acesso (IAM)** no painel esquerdo.
6. Selecione **Adicionar**.
7. Em **Função**, selecione **Leitor do Cost Management**.
8. Em **Atribuir acesso a**, selecione **Utilizador, grupo ou aplicação do Azure Active Directory**.
9. Para atribuir acesso, procure o utilizador e selecione-o.
10. Selecione **Guardar**.  
    ![informações de exemplo na caixa Adicionar permissões num grupo de gestão](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Atribuir acesso de âmbito de subscrição

O acesso a uma subscrição requer, no mínimo, a permissão de Leitor do Cost Management (ou Leitor). Pode configurar permissões para uma subscrição no portal do Azure. Tem de ter, no mínimo, a permissão de Administrador de Acesso de Utilizadores (ou Proprietário) para a subscrição para permitir o acesso aos outros. No caso das contas do Azure EA, também tem de ativar a definição **Custos de vista do CP** no EA Portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Todos os Serviços** na barra lateral, procure _subscrições_ e, em seguida, selecione **Subscrições**.
3. Selecione a sua subscrição.
4. Selecione **Controlo de Acesso (IAM)** no painel esquerdo.
5. Selecione **Adicionar**.
6. Em **Função**, selecione **Leitor do Cost Management**.
7. Em **Atribuir acesso a**, selecione **Utilizador, grupo ou aplicação do Azure Active Directory**.
8. Para atribuir acesso, procure o utilizador e selecione-o.
9. Selecione **Guardar**.

## <a name="assign-resource-group-scope-access"></a>Atribuir acesso de âmbito de grupo de recursos

O acesso a um grupo de recursos requer, no mínimo, a permissão de Leitor do Cost Management (ou Leitor). Pode configurar permissões para um grupo de recursos no portal do Azure. Tem de ter, no mínimo, a permissão de Administrador de Acesso de Utilizadores (ou Proprietário) para o grupo de recursos para permitir o acesso aos outros. No caso das contas do Azure EA, também tem de ativar a definição **Custos de vista do CP** no EA Portal.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Todos os Serviços** na barra lateral, procure _grupos de recursos_ e, em seguida, selecione **grupos de recursos**.
3. Selecione o seu grupo de recursos.
4. Selecione **Controlo de Acesso (IAM)** no painel esquerdo.
5. Selecione **Adicionar**.
6. Em **Função**, selecione **Leitor do Cost Management**.
7. Em **Atribuir acesso a**, selecione **Utilizador, grupo ou aplicação do Azure Active Directory**.
8. Para atribuir acesso, procure o utilizador e selecione-o.
9. Selecione **Guardar**.

## <a name="cross-tenant-authentication-issues"></a>Problemas de autenticação entre inquilinos

Atualmente, o Azure Cost Management oferece suporte limitado para a autenticação entre inquilinos. Em algumas circunstâncias, quanto tenta autenticar em vários inquilinos, poderá receber um erro **Acesso negado** na análise de custos. Este problema poderá ocorrer se configurar o controlo de acesso baseado em funções do Azure (RBAC do Azure) para outra subscrição do inquilino e, depois, tentar ver os dados dos custos.

*Para contornar o problema*: depois de configurar o RBAC do Azure entre inquilinos, aguarde uma hora. Depois, tente ver os custos na análise de custos ou conceda acesso ao Cost Management a utilizadores em ambos os inquilinos.  


## <a name="next-steps"></a>Passos seguintes

- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](quick-acm-cost-analysis.md).
