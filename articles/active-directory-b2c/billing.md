---
title: Modelo de faturação para Azure Ative Directory B2C
description: Saiba mais sobre o modelo de faturação mensal dos utilizadores ativos da Azure AD B2C (MAU), como ligar um inquilino AZure AD B2C a uma subscrição do Azure e como selecionar o preço de nível premium apropriado.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225213"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de faturação para Azure Ative Directory B2C

Os preços do Azure Ative Directory B2C (Azure AD B2C) baseiam-se em utilizadores ativos mensais (MAU), que é a contagem de utilizadores únicos com atividade de autenticação dentro de um mês civil. Este modelo de faturação aplica-se tanto aos inquilinos AZURE AD B2C como à [colaboração do utilizador convidado Azure AD (B2B)](../active-directory/external-identities/external-identities-pricing.md). A faturação mau ajuda a reduzir custos oferecendo um nível livre e preços flexíveis e previsíveis. Neste artigo, conheça a faturação mau, ligando os seus inquilinos Azure AD B2C a uma subscrição e alterando o seu nível de preços.

> [!IMPORTANT]
> Este artigo não contém detalhes sobre os preços. Para obter as informações mais recentes sobre a faturação e preços de utilização, consulte os preços do [Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Consulte também [a disponibilidade da região AD B2C e a residência de dados](data-residency.md) para mais informações sobre onde está disponível o serviço Azure AD B2C e onde os dados dos utilizadores estão armazenados.

## <a name="what-do-i-need-to-do"></a>O que preciso fazer?

Para tirar partido da faturação mau, o seu inquilino Azure AD B2C deve estar ligado a uma subscrição da Azure. Você também pode precisar de mudar o seu inquilino Azure AD B2C para outro nível de preços se você quiser usar funcionalidades Azure AD B2C Premium P2, como acesso condicional baseado em risco.

|Se o seu inquilino for:  |Tem de:  |
|---------|---------|
| Um inquilino Azure AD B2C já faturado por MAU     | Não fazer nada. Quando os utilizadores autenticarem o seu inquilino Azure AD B2C, será automaticamente faturado utilizando o modelo de faturação baseado na MAU.        |
| Um inquilino Azure AD B2C ainda não está ligado a uma subscrição     |  [Ligue o seu inquilino Azure AD B2C a uma subscrição](#link-an-azure-ad-b2c-tenant-to-a-subscription) para ativar a faturação MAU.     |
| Um inquilino Azure AD B2C que estava ligado a uma subscrição antes de 1 de novembro de 2019    | [Mude para a faturação MAU (recomendada)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)ou permaneça no modelo de faturação por autenticação.     |
| Um inquilino Azure AD B2C e você quer usar funcionalidades premium (como acesso condicional baseado em risco)    | [Mude para um nível de preços Azure AD](#change-your-azure-ad-pricing-tier) que suporta as funcionalidades que pretende utilizar.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Sobre o modelo mensal de faturação de utilizadores ativos (MAU)

A faturação da MAU entrou em vigor para os inquilinos do Azure AD B2C em **1 de novembro de 2019**. Todos os inquilinos Azure AD B2C que criou e ligados a uma subscrição em ou após essa data foram faturados por MAU. Se você tem um inquilino Azure AD B2C que não foi ligado a uma subscrição, você precisará fazê-lo agora. Se tem um inquilino Azure AD B2C existente que estava ligado a uma subscrição antes de 1 de novembro de 2019, recomendamos que atualize para o modelo de faturação mensal de utilizadores ativos (MAU) ou pode ficar no modelo de faturação por autenticação.
  
O seu inquilino Azure AD B2C também deve estar ligado ao nível de preços Azure apropriado com base nas funcionalidades que pretende utilizar. As funcionalidades premium requerem preços Azure AD B2C [Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Poderá ter de atualizar o seu nível de preços à medida que utiliza novas funcionalidades. Por exemplo, Acesso Condicional, você precisará selecionar o nível de preços Azure AD B2C Premium P2 para o seu inquilino.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Ligue um inquilino Azure AD B2C a uma subscrição

Os custos de utilização do Azure Ative Directory B2C (Azure AD B2C) são faturados para uma subscrição do Azure. Você precisa ligar explicitamente um inquilino Azure AD B2C a uma subscrição Azure, criando um *recurso* Azure AD B2C dentro da subscrição target Azure. Vários recursos Azure AD B2C podem ser criados numa única subscrição do Azure, juntamente com outros recursos Azure, como máquinas virtuais, contas de armazenamento e Aplicações Lógicas. Você pode ver todos os recursos dentro de uma subscrição indo para o inquilino Azure Ative Directory (Azure AD) a que a subscrição está associada.

Uma subscrição ligada a um inquilino Azure AD B2C pode ser usada para a faturação de utilização Azure AD B2C ou outros recursos Azure, incluindo recursos adicionais Azure AD B2C. Não pode ser usado para adicionar outros serviços baseados em licenças Azure ou licenças do Office 365 dentro do inquilino Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/free/)
* [Inquilino Azure AD B2C](tutorial-create-tenant.md) que pretende ligar a uma subscrição
  * Deve ser um administrador de inquilinos.
  * O inquilino não deve estar ainda ligado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, selecione o diretório que contém a subscrição Azure que gostaria de usar *(não* o diretório que contém o inquilino Azure AD B2C).
3. **Selecione Criar um recurso,** insira `Active Directory B2C` no campo **'Pesquisar o Mercado'** e, em seguida, selecione **Azure Ative Directory B2C**.
4. Selecione **Criar**.
5. Selecione **Link um inquilino Azure AD B2C existente para a minha assinatura Azure**.
6. Selecione um **Inquilino Azure AD B2C** a partir do dropdown. Apenas inquilinos para os quais você é um administrador global e que ainda não estão ligados a uma subscrição são mostrados. O campo **de nome de recursos Azure AD B2C** é preenchido com o nome de domínio do inquilino Azure AD B2C que seleciona.
7. Selecione uma **subscrição** ativa do Azure da qual é administrador.
8. No **grupo de Recursos**, selecione Criar **novos** e, em seguida, especificar a **localização** do grupo de Recursos . As configurações do grupo de recursos aqui não têm impacto na localização, desempenho ou estado de faturação do seu Azure AD B2C.
9. Selecione **Criar**.

    ![A página de criação de recursos Azure AD B2C no portal Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Depois de completar estes passos para um inquilino Azure AD B2C, a sua subscrição Azure é faturada com base nos detalhes do Seu Acordo Azure Direct ou Enterprise Agreement, se aplicável.

## <a name="change-your-azure-ad-pricing-tier"></a>Mude o seu nível de preços AZure AD

Um inquilino deve estar ligado ao nível de preços adequado da Azure com base nas características que pretende utilizar com o seu inquilino Azure AD B2C. As funcionalidades premium requerem Azure AD B2C Premium P1 ou P2, conforme descrito nos preços do [Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Em alguns casos, terá de atualizar o seu nível de preços à medida que utiliza novas funcionalidades. Por exemplo, se quiser utilizar a Proteção de Identidade, acesso condicional baseado no risco e quaisquer futuras capacidades Premium P2 com Azure AD B2C, terá de selecionar o nível de preços Azure AD B2C Premium P2 para o seu inquilino.

Para alterar o seu nível de preços, siga estes passos.

1. Inicie sessão no portal do Azure.

2. Selecione o filtro **de subscrição Directory +** no menu superior e, em seguida, selecione o diretório que contém a subscrição Azure a que o seu inquilino Azure B2C está ligado *(não* selecione o inquilino Azure AD B2C em si).

3. Na caixa de pesquisa no topo do portal, insira o nome do seu inquilino Azure AD B2C. Em seguida, selecione o inquilino nos resultados de pesquisa em **Recursos.**

4. Na página **'Visão Geral** do recurso', no **nível de preços,** selecione **alteração**.

   ![Alterar escalão de preço](media/billing/change-pricing-tier.png)
 
5. Selecione o nível de preços que inclui as funcionalidades que pretende ativar.

   ![Selecione o nível de preços](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Mudar para faturação MAU (inquilinos azure AD B2C antes de novembro de 2019)

Se ligou o seu inquilino Azure AD B2C a uma subscrição antes de 1 de novembro de **2019,** o modelo anterior de faturação por autenticação está a ser utilizado. Recomendamos que faça upgrade para o modelo de faturação mensal dos utilizadores ativos (MAU). As opções de faturação estão configuradas no seu recurso Azure AD B2C.

A faturação mensal dos utilizadores ativos (MAU) é **irreversível.** Uma vez que converta um recurso Azure AD B2C para o modelo de faturação baseado na MAU, não pode reverter esse recurso para o modelo de faturação por autenticação.

Eis como fazer a troca para a faturação MAU para um recurso Azure AD B2C existente:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como proprietário de subscrição com acesso administrativo ao recurso Azure AD B2C.

2. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório Azure AD B2C que pretende atualizar para a faturação MAU.<br/>

    ![Diretório e filtro de subscrição no portal Azure](./media/billing/portal-mau-01-select-b2c-directory.png)

3. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.

4. Na página **geral** do inquilino Azure AD B2C, selecione o link com **o nome de Recurso**. Você é direcionado para o recurso Azure AD B2C no seu inquilino AZure AD.<br/>

    ![Ligação de recursos Azure AD B2C destacada no portal Azure](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Na página **geral** do recurso Azure AD B2C, em **Unidades Faturadas,** selecione o link **Per Authentication (Alterar para MAU).**<br/>

    ![Alteração à ligação MAU em destaque no portal Azure](./media/billing/portal-mau-03-change-to-mau-link.png)

6. **Selecione Confirmar** para completar a atualização para a faturação MAU.<br/>

    ![Diálogo de confirmação de faturação baseado na MAU no portal Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar quando transitar para a faturação MAU da faturação por autenticação

A medição baseada na MAU está ativada assim que você, o proprietário de subscrição/recurso, confirmar a alteração. A sua fatura mensal refletirá as unidades de autenticação faturadas até à alteração, e novas unidades da MAU a começar pela mudança.

Os utilizadores não são contados duas vezes durante o mês de transição. Os utilizadores ativos únicos que autenticam antes da alteração são cobrados uma taxa por autenticação num mês civil. Esses mesmos utilizadores não estão incluídos no cálculo MAU para o resto do ciclo de faturação da subscrição. Por exemplo:

* O inquilino Contoso B2C tem 1.000 utentes. 250 utilizadores estão ativos num determinado mês. O administrador de subscrição muda de por autenticação para utilizadores ativos mensais (MAU) no dia 10 do mês.
* A faturação da 1ª-10ª é faturada usando o modelo por autenticação.
  * Se 100 utilizadores se inscreverem durante este período (1º-10º), esses utilizadores são marcados como *pagos pelo mês*.
* A faturação a partir do dia 10 (o momento efetivo da transição) é faturada à taxa MAU.
  * Se mais 150 utilizadores se inscreverem durante este período (10º a 30º), apenas os 150 adicionais são cobrados.
  * A continuação da atividade dos primeiros 100 utilizadores não tem impacto na faturação para o resto do mês civil.

Durante o período de faturação da transição, o proprietário da subscrição verá provavelmente as entradas para ambos os métodos (por autenticação e por MAU) aparecerem na sua declaração de faturação de subscrição Azure:

* Uma entrada para a utilização até à data/hora de alteração que reflita por autenticação.
* Uma entrada para a utilização após a alteração que reflete os utilizadores ativos mensais (MAU).

Para obter as informações mais recentes sobre a faturação de utilização e preços para a Azure AD B2C, consulte [os preços do Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerencie os seus recursos de inquilinos Azure AD B2C

Depois de criar o recurso Azure AD B2C numa subscrição do Azure, deverá ver um novo recurso do tipo "inquilino B2C" aparecer com os seus outros recursos Azure.

Pode utilizar este recurso para:

* Navegue para a subscrição para rever informações de faturação
* Obtenha o ID do inquilino do Azure AD B2C em formato GUID
* Vá ao seu inquilino Azure AD B2C
* Submeter um pedido de apoio
* Mova o seu recurso de inquilino Azure AD B2C para outro grupo de subscrição ou recursos da Azure

### <a name="regional-restrictions"></a>Restrições regionais

Se estabeleceu restrições regionais para a criação de recursos Azure na sua subscrição, essa restrição pode impedi-lo de criar o recurso Azure AD B2C.

Para atenuar esta questão, relaxe as suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas Azure Cloud Solution Providers (CSP)

As subscrições Azure Cloud Solution Providers (CSP) são suportadas em Azure AD B2C. A funcionalidade está disponível usando APIs ou o portal Azure para Azure AD B2C e para todos os recursos Azure. Os administradores de subscrição da CSP podem ligar, mover e eliminar relações com a Azure AD B2C, como feito com outros recursos Azure.

A gestão do Azure AD B2C utilizando o controlo de acesso baseado em funções não é afetada pela associação entre o inquilino Azure AD B2C e uma assinatura CSP Azure. O controlo de acesso baseado em funções é alcançado utilizando funções baseadas em inquilinos e não por funções baseadas em assinaturas.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a subscrição de faturação de inquilinoS Azure AD B2C

### <a name="move-using-azure-resource-manager"></a>Mova-se usando o Gestor de Recursos Azure

Os inquilinos Azure AD B2C podem ser transferidos para outra subscrição usando o Azure Resource Manager se as assinaturas de origem e destino existirem dentro do mesmo inquilino do Azure Ative Directory.

Para aprender a mover recursos Azure como o seu inquilino Azure AD B2C para outra subscrição, consulte [mover recursos para novo grupo de recursos ou subscrição.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Antes de iniciar a mudança, não deixe de ler todo o artigo para entender completamente as limitações e requisitos para tal movimento. Além das instruções para a movimentação de recursos, inclui informações críticas como uma lista de verificação pré-movimento e como validar a operação de movimento.

### <a name="move-by-unlinking-and-relinking"></a>Mova-se desvinculando e religando

Se as subscrições de origem e destino estiverem associadas a diferentes inquilinos do Azure Ative Directory, não é possível realizar a mudança através do Azure Resource Manager, como explicado acima. No entanto, ainda pode obter o mesmo resultado desvinculando o inquilino Azure AD B2C da subscrição de origem e religando-o à subscrição de destino. Este método é seguro porque o único objeto que você apaga é o *link de faturação*, não o inquilino Azure AD B2C em si. Nenhum dos utilizadores, apps, fluxos de utilizadores, etc. será afetado.

1. No próprio diretório Ad B2C Ad AD, [convide um utilizador convidado](user-overview.md#guest-user) do destino Azure AD inquilino (aquele a que a subscrição do destino Azure está ligada) e garanta que este utilizador tem o papel **de administrador global** no Azure AD B2C.
1. Navegue para o *recurso Azure* representando Azure AD B2C na sua assinatura Azure fonte, conforme explicado na secção de recursos de [inquilinoS Azure AD B2C](#manage-your-azure-ad-b2c-tenant-resources) acima. Não mude para o inquilino Azure AD B2C.
1. Selecione o botão **Eliminar** na página **'Vista Geral'.** Isto *não* elimina os utilizadores ou aplicações do inquilino Azure AD B2C. Apenas remove o link de faturação da subscrição de origem.
1. Inscreva-se no portal Azure com a conta de utilizador que foi adicionada como administrador em Azure AD B2C no passo 1. Em seguida, navegue para a subscrição de destino Azure, que está ligada ao destino Azure Ative Directory inquilino. 
1. Restabelecer o link de faturação na subscrição do destino seguindo o procedimento [de ligação](#create-the-link) acima.
1. O seu recurso Azure AD B2C mudou-se agora para a subscrição de destino Azure (ligada ao diretório ativo Azure) e será faturado através desta subscrição a partir de agora.

## <a name="next-steps"></a>Passos seguintes

Para obter as informações mais recentes sobre preços, consulte os preços do [Azure Ative Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).