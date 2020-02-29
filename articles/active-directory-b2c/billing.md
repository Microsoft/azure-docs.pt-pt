---
title: Modelo de faturação para Azure Ative Directory B2C
description: Saiba mais sobre o modelo de faturação mensal de utilizadores ativos do Azure AD B2C (MAU) e como permitir a faturação de uma subscrição azure específica.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190012"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de faturação para Azure Ative Directory B2C

O uso do Azure Ative Directory B2C (Azure AD B2C) é faturado para uma subscrição azure ligada e utiliza um modelo de faturação de utilizadores ativos mensais (MAU). Saiba como ligar um recurso Azure AD B2C a uma subscrição e como funciona o modelo de faturação MAU nas seguintes secções.

> [!IMPORTANT]
> Este artigo não contém informações sobre preços. Para obter as informações mais recentes sobre a faturação de utilização e os preços, consulte [o preço do Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Faturação mensal de utilizadores ativos (MAU)

A faturação Azure AD B2C é mediante a contagem de utilizadores únicos com atividade de autenticação dentro de um mês civil, conhecido como faturação mensal de utilizadores ativos (MAU).

A partir de 01 de novembro de **2019,** todos os recém-criados inquilinos Azure AD B2C são cobrados por mês de utilizadores ativos (MAU). Os inquilinos existentes que estejam [ligados a uma subscrição](#link-an-azure-ad-b2c-tenant-to-a-subscription) em ou depois de 01 de novembro de 2019 serão cobrados por mês de utilizadores ativos (MAU).

Se tiver um inquilino Azure AD B2C existente que estava ligado a uma subscrição antes de 01 de novembro de 2019, pode optar por fazer um dos seguintes:

* Upgrade para o modelo de faturação mensal dos utilizadores ativos (MAU), ou
* Mantenha-se no modelo de faturação por autenticação

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Upgrade para o modelo de faturação mensal de utilizadores ativos

Os proprietários de subscrições Azure que tenham acesso administrativo ao recurso Azure AD B2C podem mudar para o modelo de faturação MAU. As opções de faturação estão configuradas no seu recurso Azure AD B2C.

A mudança para a faturação mensal de utilizadores ativos (MAU) é **irreversível.** Uma vez convertido um recurso Azure AD B2C para o modelo de faturação baseado em MAU, não pode reverter esse recurso para o modelo de faturação por autenticação.

Aqui está como fazer a mudança para a faturação MAU para um recurso Azure AD B2C existente:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como proprietário da subscrição.
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório Azure AD B2C que pretende atualizar para a faturação mau.<br/>
    ![Diretório e filtro de subscrição no portal Azure](./media/billing/portal-mau-01-select-b2c-directory.png)
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Na página **de visão geral** do inquilino Azure AD B2C, selecione o link sob o nome de **Recurso**. Você é direcionado para o recurso Azure AD B2C no seu inquilino Azure AD.<br/>
    ![ligação de recursos Azure AD B2C destacada no portal Azure](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na página **de visão geral** do recurso Azure AD B2C, em **Unidades De Faturação,** selecione o link **Por Autenticação (Alteração para MAU).**<br/>
    ![Ligação Change to MAU destacada no portal Azure](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Selecione **Confirmar** para completar a atualização para a faturação mau.<br/>
    ![diálogo de confirmação de faturação baseado em MAU no portal Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar quando transitar para a faturação mau de faturação por autenticação

A medição baseada em MAU está ativada assim que você, o proprietário da subscrição/recurso, confirmar a mudança. A sua fatura mensal refletirá as unidades de autenticação faturadas até à mudança, e novas unidades da MAU a começar pela mudança.

Os utilizadores não são contados duas vezes durante o mês de transição. Os utilizadores ativos únicos que autenticam antes da alteração são cobrados uma taxa de autenticação por autenticação num mês civil. Esses mesmos utilizadores não estão incluídos no cálculo mau para o resto do ciclo de faturação da subscrição. Por exemplo:

* O inquilino Contoso B2C tem 1.000 utilizadores. 250 utilizadores estão ativos em qualquer mês. O administrador de subscrição muda de por autenticação para utilizadores ativos mensais (MAU) no dia 10 do mês.
* A faturação para o 1º ao 10º é faturada usando o modelo por autenticação.
  * Se 100 utilizadores se inscreverem durante este período (1º-10º), estes utilizadores são marcados como *pagos durante o mês*.
* A faturação a partir do dia 10 (o tempo de transição efetivo) é faturada à taxa MAU.
  * Se mais 150 utilizadores assinarem durante este período (10º-30º), apenas os 150 adicionais são cobrados.
  * A continuação da atividade dos primeiros 100 utilizadores não tem impacto na faturação para o resto do mês civil.

Durante o período de faturação da transição, o proprietário da subscrição provavelmente verá as entradas para ambos os métodos (por autenticação e por-MAU) aparecerem na sua declaração de faturação de assinaturas Azure:

* Uma entrada para a utilização até à data/hora da alteração que reflita por autenticação.
* Uma entrada para o uso após a alteração que reflete os utilizadores ativos mensais (MAU).

Para obter as informações mais recentes sobre a faturação de utilização e os preços para o Azure AD B2C, consulte o [preço do Diretório Ativo Azure B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Ligue um inquilino Azure AD B2C a uma subscrição

Os encargos de utilização do Azure Ative Directory B2C (Azure AD B2C) são cobrados para uma subscrição Azure. Quando é criado um inquilino do Azure AD B2C, o administrador de inquilino tem de ligar explicitamente o inquilino do Azure AD B2C a uma subscrição do Azure.

A ligação de subscrição é alcançada através da criação de um *recurso* Azure AD B2C dentro da subscrição do Target Azure. Vários recursos Azure AD B2C podem ser criados numa única subscrição Azure, juntamente com outros recursos Azure, como máquinas virtuais, contas de armazenamento e Aplicações Lógicas. Você pode ver todos os recursos dentro de uma subscrição indo para o inquilino Azure Ative Directory (Azure AD) com o qual a subscrição está associada.

Uma subscrição ligada a um inquilino Azure AD B2C pode ser utilizada para a faturação de utilização do Azure AD B2C ou outros recursos Azure, incluindo recursos adicionais Azure AD AD B2C. Ele não pode ser usado para adicionar outros serviços do Azure baseada na licença ou licenças do Office 365 no inquilino do Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/free/)
* [Inquilino Azure AD B2C](tutorial-create-tenant.md) que pretende ligar a uma subscrição
  * Deve ser administrador de inquilinos.
  * O inquilino não deve estar já ligado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém a subscrição Azure que gostaria de utilizar *(não* o diretório que contém o inquilino Azure AD B2C).
1. Selecione **Criar um recurso,** introduza `Active Directory B2C` no campo **Search the Marketplace** e, em seguida, selecione **Azure Ative Directory B2C**.
1. Selecione **Criar**
1. Selecione **Link um Inquilino B2C Azure existente para a minha assinatura Azure**.
1. Selecione um **Inquilino Azure AD B2C** a partir do dropdown. Apenas são apresentados inquilinos para os quais é administrador global e que ainda não estão ligados a uma subscrição. O campo de nome de **recursos Azure AD B2C** é povoado com o nome de domínio do inquilino Azure AD B2C que você seleciona.
1. Selecione uma **subscrição** azure ativa da qual é administrador.
1. No **grupo Recurso,** selecione **Criar novo**e, em **seguida,** especificar a localização do grupo Recursos . As configurações do grupo de recursos aqui não têm impacto na localização, desempenho ou estado de faturação do seu Azure AD AD B2C.
1. Selecione **Criar**.
    ![página de criação de recursos Azure AD B2C no portal Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Depois de completar estes passos para um inquilino Azure AD B2C, a sua subscrição Azure é faturada de acordo com os detalhes do seu Contrato Azure Direct ou Enterprise Agreement, se aplicável.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerir os recursos de inquilino do Azure AD B2C

Depois de criar o recurso Azure AD B2C numa subscrição Azure, deverá ver aparecer um novo recurso do tipo "Inquilino B2C" com os outros recursos Do Azure.

Pode usar este recurso para:

* Navegue para a subscrição para rever informações de faturação
* Obtenha o ID do inquilino Azure AD B2C em formato GUID
* Aceda ao seu inquilino do Azure AD B2C
* Submeter pedidos de suporte
* Mova o seu recurso de inquilino Azure AD B2C para outro grupo de subscrição ou recursos Azure

![Página de definições de recursos B2C no portal Azure](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrições regionais

Se estabeleceu restrições regionais para a criação de recursos Azure na sua subscrição, essa restrição pode impedir-lhe de criar o recurso Azure AD B2C.

Para mitigar esta questão, relaxe as suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas de Fornecedores de Soluções Azure Cloud (CSP)

As subscrições do Azure Cloud Solution Providers (CSP) são suportadas no Azure AD B2C. A funcionalidade está disponível utilizando APIs ou o portal Azure para O Azure AD B2C e para todos os recursos Azure. Os administradores de subscrição do CSP podem ligar, mover e excluir relações com o Azure AD B2C, tal como feito com outros recursos do Azure.

A gestão do Azure AD B2C utilizando o controlo de acesso baseado em funções não é afetada pela associação entre o inquilino Azure AD B2C e uma subscrição de CSP Azure. O controlo de acesso baseado em papéis é alcançado utilizando funções baseadas em inquilinos e não funções baseadas em subscrições.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a subscrição de faturação de inquilino Saque AD B2C do Azure

Os inquilinos da Azure AD B2C podem ser transferidos para outra subscrição se existirem assinaturas de origem e destino dentro do mesmo inquilino do Azure Ative Directory.

Para aprender a mover recursos Azure como o seu inquilino Azure AD B2C para outra subscrição, consulte [os recursos do Move para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de iniciar a mudança, não se esqueça de ler todo o artigo para compreender plenamente as limitações e requisitos para tal movimento. Além das instruções para a movimentação de recursos, inclui informações críticas como uma lista de verificação pré-mover e como validar o funcionamento do movimento.

## <a name="next-steps"></a>Passos seguintes

Para obter as informações mais recentes sobre preços, consulte [o preço do Diretório Ativo Azure B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
