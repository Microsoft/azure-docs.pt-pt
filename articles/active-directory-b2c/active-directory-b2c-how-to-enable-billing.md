---
title: Modelo de cobrança para Azure Active Directory B2C
description: Saiba mais sobre o modelo de cobrança do MAU (usuários ativos mensais) do Azure AD B2C e como habilitar a cobrança para uma assinatura específica do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 32897994e4ef22845b1d19d6e0eb3e6b2c968c01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930717"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de cobrança para Azure Active Directory B2C

O uso de Azure Active Directory B2C (Azure AD B2C) é cobrado em uma assinatura vinculada do Azure e usa um modelo de cobrança MAU (usuários ativos mensais). Saiba como vincular um recurso de Azure AD B2C a uma assinatura e como o modelo de cobrança MAU funciona nas seções a seguir.

> [!IMPORTANT]
> Este artigo não contém informações sobre preços. Para obter as informações mais recentes sobre cobrança e preços de uso, consulte [preços de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Cobrança de usuários ativos mensais (MAU)

A cobrança Azure AD B2C é medida na contagem de usuários exclusivos com atividade de autenticação em um mês civil, conhecida como cobrança de usuários ativos mensais (MAU).

A partir de **1º de novembro de 2019**, todos os locatários de Azure ad B2C recém-criados são cobrados por mau (usuários ativos por mês). Os locatários existentes que estão [vinculados a uma assinatura](#link-an-azure-ad-b2c-tenant-to-a-subscription) no ou após 01 de novembro de 2019 serão cobrados por usuários ativos por mês (mau).

Se você tiver um locatário Azure AD B2C existente que foi vinculado a uma assinatura antes de 01 de novembro de 2019, você pode optar por fazer o seguinte:

* Atualizar para o modelo de cobrança de usuários ativos mensais (MAU) ou
* Permanecer no modelo de cobrança por autenticação

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Atualizar para o modelo de cobrança de usuários ativos mensais

Os proprietários de assinatura do Azure que têm acesso administrativo ao recurso de Azure AD B2C podem mudar para o modelo de cobrança MAU. As opções de cobrança são configuradas no recurso de Azure AD B2C.

A mudança para a cobrança de usuários ativos mensais (MAU) é **irreversível**. Depois de converter um recurso de Azure AD B2C no modelo de cobrança baseado em MAU, não é possível reverter esse recurso para o modelo de cobrança por autenticação.

Veja como fazer a mudança para a cobrança de MAU para um recurso de Azure AD B2C existente:

1. Entre no [portal do Azure](https://portal.azure.com) como o proprietário da assinatura.
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o Azure ad B2C diretório que você deseja atualizar para a cobrança do mau.<br/>
    ![o filtro de diretório e assinatura no portal do Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-01-select-b2c-directory.png)
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Na página **visão geral** do locatário do Azure ad B2C, selecione o link em **nome do recurso**. Você é direcionado para o recurso de Azure AD B2C em seu locatário do Azure AD.<br/>
    ![link de recurso Azure AD B2C realçado em portal do Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-02-b2c-resource-link.png)
1. Na página **visão geral** do recurso Azure ad B2C, em **unidades faturáveis**, selecione o link **por autenticação (alterar para mau)** .<br/>
    ![alterar para o link MAU realçado em portal do Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-03-change-to-mau-link.png)
1. Selecione **confirmar** para concluir a atualização para a cobrança do mau.<br/>
    ![caixa de diálogo de confirmação de cobrança baseada em MAU no portal do Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar ao fazer a transição para a cobrança do MAU de cobrança por autenticação

A medição baseada em MAU é habilitada assim que você, o proprietário da assinatura/recurso, confirma a alteração. Sua fatura mensal refletirá as unidades de autenticação cobradas até a alteração e as novas unidades de MAU começando com a alteração.

Os usuários não são contados duas vezes durante o mês de transição. Usuários ativos exclusivos que se autenticam antes da alteração são cobrados por uma taxa por autenticação em um mês civil. Esses mesmos usuários não estão incluídos no cálculo de MAU para o restante do ciclo de cobrança da assinatura. Por exemplo:

* O locatário contoso B2C tem 1.000 usuários. 250 os usuários estão ativos em um determinado mês. O administrador da assinatura muda de por autenticação para usuários ativos mensais (MAU) no décimo do mês.
* A cobrança por 1º-10 é cobrada usando o modelo por autenticação.
  * Se 100 usuários entrarem durante esse período (1º de 10º), esses usuários serão marcados como *pagos pelo mês*.
* A cobrança do 10º (o tempo de transição efetivo) é cobrada na taxa de MAU.
  * Se outros usuários 150 entrarem durante esse período (10 a 30), somente os 150 adicionais serão cobrados.
  * A atividade contínua dos primeiros 100 usuários não afeta a cobrança pelo restante do mês do calendário.

Durante o período de cobrança da transição, o proprietário da assinatura provavelmente verá entradas para os dois métodos (por autenticação e por MAU) exibidos em seu demonstrativo de cobrança de assinatura do Azure:

* Uma entrada para o uso até a data/hora da alteração que reflete por autenticação.
* Uma entrada para o uso após a alteração que reflete os usuários ativos mensais (MAU).

Para obter as informações mais recentes sobre cobrança de uso e preços para Azure AD B2C, consulte [preços de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Vincular um locatário Azure AD B2C a uma assinatura

Os encargos de uso de Azure Active Directory B2C (Azure AD B2C) são cobrados em uma assinatura do Azure. Quando é criado um inquilino do Azure AD B2C, o administrador de inquilino tem de ligar explicitamente o inquilino do Azure AD B2C a uma subscrição do Azure.

O link de assinatura é obtido criando um *recurso* de Azure ad B2C dentro da assinatura de destino do Azure. Vários recursos de Azure AD B2C podem ser criados em uma única assinatura do Azure, juntamente com outros recursos do Azure, como máquinas virtuais, contas de armazenamento e aplicativos lógicos. Você pode ver todos os recursos em uma assinatura acessando o locatário do Azure Active Directory (Azure AD) ao qual a assinatura está associada.

Uma assinatura vinculada a um locatário Azure AD B2C pode ser usada para a cobrança de Azure AD B2C uso ou outros recursos do Azure, incluindo recursos de Azure AD B2C adicionais. Ele não pode ser usado para adicionar outros serviços do Azure baseada na licença ou licenças do Office 365 no inquilino do Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/free/)
* [Azure ad B2C locatário](active-directory-b2c-get-started.md) que você deseja vincular a uma assinatura
  * Você deve ser um administrador de locatários
  * O locatário já não deve estar vinculado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e selecione o diretório que contém a assinatura do Azure que você gostaria de usar (*não* o diretório que contém o locatário Azure ad B2C).
1. Selecione **criar um recurso**, insira `Active Directory B2C` no campo **Pesquisar no Marketplace** e, em seguida, selecione **Azure Active Directory B2C**.
1. Selecione **Criar**
1. Selecione **vincular um locatário existente do Azure ad B2C à minha assinatura do Azure**.
1. Selecione um **locatário Azure ad B2C** na lista suspensa. Somente os locatários para os quais você é um administrador global e que ainda não estão vinculados a uma assinatura são mostrados. O campo **nome do recurso de Azure ad B2C** é populado com o nome de domínio do locatário de Azure ad B2C que você selecionar.
1. Selecione uma **assinatura** ativa do Azure da qual você é um administrador.
1. Em **grupo de recursos**, selecione **criar novo**e especifique o **local do grupo de recursos**. As configurações do grupo de recursos aqui não têm impacto sobre o local Azure AD B2C locatário, o desempenho ou o status de cobrança.
1. Selecione **Criar**.
    ![a página de criação de recursos Azure AD B2C no portal do Azure](./media/active-directory-b2c-how-to-enable-billing/portal-01-create-b2c-resource-page.png)

Depois de concluir essas etapas para um locatário Azure AD B2C, sua assinatura do Azure será cobrada de acordo com os detalhes do Azure Direct ou Enterprise Agreement, se aplicável.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerir os recursos de inquilino do Azure AD B2C

Depois de criar o recurso de Azure AD B2C em uma assinatura do Azure, você deverá ver um novo recurso do tipo "locatário do B2C" que aparece com outros recursos do Azure.

Pode usar este recurso para:

* Navegue até a assinatura para examinar as informações de cobrança
* Obter a ID do locatário do Azure AD B2C locatário no formato GUID
* Aceda ao seu inquilino do Azure AD B2C
* Submeter pedidos de suporte
* Mover seu Azure AD B2C recurso de locatário para outra assinatura do Azure ou grupo de recursos

![Página de configurações de recursos do B2C no portal do Azure](./media/active-directory-b2c-how-to-enable-billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrições regionais

Se você estabeleceu restrições regionais para a criação de recursos do Azure em sua assinatura, essa restrição pode impedi-lo de criar o recurso de Azure AD B2C.

Para atenuar esse problema, relaxe suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas do CSP (provedores de soluções na nuvem) do Azure

As assinaturas do CSP (provedores de soluções de nuvem) do Azure têm suporte no Azure AD B2C. A funcionalidade está disponível usando APIs ou o portal do Azure para Azure AD B2C e para todos os recursos do Azure. Os administradores de assinatura do CSP podem vincular, mover e excluir relações com Azure AD B2C como feito com outros recursos do Azure.

O gerenciamento de Azure AD B2C usando o controle de acesso baseado em função não é afetado pela associação entre o locatário do Azure AD B2C e uma assinatura do Azure CSP. O controle de acesso baseado em função é obtido usando funções baseadas em locatário, não funções baseadas em assinatura.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a assinatura de cobrança de locatário Azure AD B2C

Azure AD B2C locatários podem ser movidos para outra assinatura se as assinaturas de origem e de destino existirem dentro do mesmo locatário Azure Active Directory.

Para saber como mover os recursos do Azure como seu locatário de Azure AD B2C para outra assinatura, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de iniciar a movimentação, lembre-se de ler todo o artigo para entender totalmente as limitações e os requisitos para tal movimentação. Além de instruções para mover recursos, ele inclui informações críticas, como uma lista de verificação de pré-movimentação e como validar a operação de movimentação.

## <a name="next-steps"></a>Passos seguintes

Além de examinar os detalhes de uso e cobrança em uma assinatura do Azure selecionada, você pode examinar relatórios de uso diário detalhados usando a API de [relatório de uso](active-directory-b2c-reference-usage-reporting-api.md).
