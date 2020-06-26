---
title: Modelo de faturação para Azure Ative Directory B2C
description: Saiba mais sobre o modelo de faturação mensal dos utilizadores ativos da Azure AD B2C (MAU) e como permitir a faturação para uma subscrição específica do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f88993db2ca7fa697aadb584fdfcbd9fe200b11c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386067"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de faturação para Azure Ative Directory B2C

O uso do Azure Ative Directory B2C (Azure AD B2C) é faturado a uma subscrição Azure ligada e utiliza um modelo de faturação mensal de utilizadores ativos (MAU). Saiba como ligar um recurso Azure AD B2C a uma subscrição e como o modelo de faturação MAU funciona nas seguintes secções.

> [!IMPORTANT]
> Este artigo não contém informações sobre preços. Para obter as informações mais recentes sobre a faturação e preços de utilização, consulte os preços do [Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Faturação mensal de utilizadores ativos (MAU)

A faturação AZURE AD B2C é medido na contagem de utilizadores únicos com atividade de autenticação dentro de um mês civil, conhecido como faturação mensal ativa de utilizadores ativos (MAU).

A partir de **01 de novembro de 2019,** todos os inquilinos recém-criados AZURE AD B2C são cobrados mensalmente por utilizadores ativos (MAU). Os inquilinos existentes que estejam [ligados a uma subscrição](#link-an-azure-ad-b2c-tenant-to-a-subscription) em ou depois de 01 de novembro de 2019 serão cobrados mensalmente por utilizadores ativos mensais (MAU).

Se tiver um inquilino Azure AD B2C existente que estava ligado a uma subscrição antes de 01 de novembro de 2019, pode optar por fazer um dos seguintes:

* Upgrade para o modelo de faturação mensal de utilizadores ativos (MAU) ou
* Mantenha-se no modelo de faturação por autenticação

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Upgrade para modelo de faturação de utilizadores ativos mensais

Os proprietários de subscrições Azure que tenham acesso administrativo ao recurso Azure AD B2C podem mudar para o modelo de faturação MAU. As opções de faturação estão configuradas no seu recurso Azure AD B2C.

A faturação mensal dos utilizadores ativos (MAU) é **irreversível.** Uma vez que converta um recurso Azure AD B2C para o modelo de faturação baseado na MAU, não pode reverter esse recurso para o modelo de faturação por autenticação.

Eis como fazer a troca para a faturação MAU para um recurso Azure AD B2C existente:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como proprietário de subscrição.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório Azure AD B2C que pretende atualizar para a faturação MAU.<br/>
    ![Diretório e filtro de subscrição no portal Azure](./media/billing/portal-mau-01-select-b2c-directory.png)
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Na página **geral** do inquilino Azure AD B2C, selecione o link com **o nome de Recurso**. Você é direcionado para o recurso Azure AD B2C no seu inquilino AZure AD.<br/>
    ![Ligação de recursos Azure AD B2C destacada no portal Azure](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na página **geral** do recurso Azure AD B2C, em **Unidades Faturadas,** selecione o link **Per Authentication (Alterar para MAU).**<br/>
    ![Alteração à ligação MAU em destaque no portal Azure](./media/billing/portal-mau-03-change-to-mau-link.png)
1. **Selecione Confirmar** para completar a atualização para a faturação MAU.<br/>
    ![Diálogo de confirmação de faturação baseado na MAU no portal Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar quando transitar para a faturação MAU da faturação por autenticação

A medição baseada na MAU está ativada assim que você, o proprietário de subscrição/recurso, confirmar a alteração. A sua fatura mensal refletirá as unidades de autenticação faturadas até à alteração, e novas unidades da MAU a começar pela mudança.

Os utilizadores não são contados duas vezes durante o mês de transição. Os utilizadores ativos únicos que autenticam antes da alteração são cobrados uma taxa por autenticação num mês civil. Esses mesmos utilizadores não estão incluídos no cálculo MAU para o restante do ciclo de faturação da subscrição. Por exemplo:

* O inquilino Contoso B2C tem 1.000 utentes. 250 utilizadores estão ativos num determinado mês. O administrador de subscrição muda de por autenticação para utilizadores ativos mensais (MAU) no dia 10 do mês.
* A faturação da 1ª-10ª é faturada usando o modelo por autenticação.
  * Se 100 utilizadores se inscreverem durante este período (1º-10º), esses utilizadores são marcados como *pagos pelo mês*.
* A faturação a partir do dia 10 (o momento efetivo da transição) é faturada à taxa MAU.
  * Se mais 150 utilizadores se inscreverem durante este período (10º a 30º), apenas os 150 adicionais são cobrados.
  * A continuação da atividade dos primeiros 100 utentes não tem impacto na faturação para o resto do mês civil.

Durante o período de faturação da transição, o proprietário da subscrição verá provavelmente as entradas para ambos os métodos (por autenticação e por MAU) aparecerem na sua declaração de faturação de subscrição Azure:

* Uma entrada para a utilização até à data/hora de alteração que reflita por autenticação.
* Uma entrada para a utilização após a alteração que reflete os utilizadores ativos mensais (MAU).

Para obter as informações mais recentes sobre a faturação de utilização e preços para a Azure AD B2C, consulte [os preços do Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Ligue um inquilino Azure AD B2C a uma subscrição

Os custos de utilização do Azure Ative Directory B2C (Azure AD B2C) são faturados para uma subscrição do Azure. Quando um inquilino Azure AD B2C é criado, o administrador do inquilino precisa ligar explicitamente o inquilino Azure AD B2C a uma assinatura Azure.

O link de subscrição é alcançado através da criação de um *recurso* Azure AD B2C dentro da subscrição target Azure. Vários recursos Azure AD B2C podem ser criados numa única subscrição do Azure, juntamente com outros recursos Azure, como máquinas virtuais, contas de armazenamento e Aplicações Lógicas. Você pode ver todos os recursos dentro de uma subscrição indo para o inquilino Azure Ative Directory (Azure AD) a que a subscrição está associada.

Uma subscrição ligada a um inquilino Azure AD B2C pode ser usada para a faturação de utilização Azure AD B2C ou outros recursos Azure, incluindo recursos adicionais Azure AD B2C. Não pode ser usado para adicionar outros serviços baseados em licenças Azure ou licenças office 365 dentro do inquilino Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/free/)
* [Inquilino Azure AD B2C](tutorial-create-tenant.md) que pretende ligar a uma subscrição
  * Deve ser um administrador de inquilinos.
  * O inquilino não deve estar ainda ligado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, selecione o diretório que contém a subscrição Azure que gostaria de usar *(não* o diretório que contém o inquilino Azure AD B2C).
1. **Selecione Criar um recurso,** insira `Active Directory B2C` no campo **'Pesquisar o Mercado'** e, em seguida, selecione **Azure Ative Directory B2C**.
1. Selecione **Criar**
1. Selecione **Link um inquilino Azure AD B2C existente para a minha assinatura Azure**.
1. Selecione um **Inquilino Azure AD B2C** a partir do dropdown. Apenas são apresentados inquilinos para os quais é administrador global e que ainda não estão ligados a uma subscrição. O campo **de nome de recursos Azure AD B2C** é preenchido com o nome de domínio do inquilino Azure AD B2C que seleciona.
1. Selecione uma **Subscrição** Azure ativa da qual é administrador.
1. No **grupo de Recursos**, selecione Criar **novos**e, em seguida, especificar a **localização**do grupo de Recursos . As configurações do grupo de recursos aqui não têm impacto na localização, desempenho ou estado de faturação do seu Azure AD B2C.
1. Selecione **Criar**.
    ![A página de criação de recursos Azure AD B2C no portal Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Depois de completar estes passos para um inquilino Azure AD B2C, a sua subscrição Azure é faturada de acordo com os detalhes do Acordo Azure Direct ou Enterprise Agreement, se aplicável.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerencie os seus recursos de inquilinos Azure AD B2C

Depois de criar o recurso Azure AD B2C numa subscrição do Azure, deverá ver um novo recurso do tipo "inquilino B2C" aparecer com os seus outros recursos Azure.

Pode utilizar este recurso para:

* Navegue para a subscrição para rever informações de faturação
* Obtenha o ID do inquilino do Azure AD B2C em formato GUID
* Vá ao seu inquilino Azure AD B2C
* Submeter um pedido de apoio
* Mova o seu recurso de inquilino Azure AD B2C para outro grupo de subscrição ou recursos da Azure

![Página de definições de recursos B2C no portal Azure](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrições regionais

Se estabeleceu restrições regionais para a criação de recursos Azure na sua subscrição, essa restrição pode impedi-lo de criar o recurso Azure AD B2C.

Para atenuar esta questão, relaxe as suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas Azure Cloud Solution Providers (CSP)

As subscrições Azure Cloud Solution Providers (CSP) são suportadas em Azure AD B2C. A funcionalidade está disponível usando APIs ou o portal Azure para Azure AD B2C e para todos os recursos Azure. Os administradores de subscrição da CSP podem ligar, mover e eliminar relações com a Azure AD B2C, como feito com outros recursos Azure.

A gestão do Azure AD B2C utilizando o controlo de acesso baseado em funções não é afetada pela associação entre o inquilino Azure AD B2C e uma assinatura CSP Azure. O controlo de acesso baseado em funções é alcançado utilizando funções baseadas em inquilinos e não por funções baseadas em assinaturas.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a subscrição de faturação de inquilinoS Azure AD B2C

Os inquilinos Azure AD B2C podem ser transferidos para outra subscrição se as assinaturas de origem e destino existirem dentro do mesmo inquilino do Azure Ative Directory.

Para aprender a mover recursos Azure como o seu inquilino Azure AD B2C para outra subscrição, consulte [mover recursos para novo grupo de recursos ou subscrição.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Antes de iniciar a mudança, certifique-se de ler todo o artigo para entender completamente as limitações e requisitos para tal movimento. Além das instruções para a movimentação de recursos, inclui informações críticas como uma lista de verificação pré-movimento e como validar a operação de movimento.

## <a name="next-steps"></a>Passos seguintes

Para obter as informações mais recentes sobre preços, consulte os preços do [Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
