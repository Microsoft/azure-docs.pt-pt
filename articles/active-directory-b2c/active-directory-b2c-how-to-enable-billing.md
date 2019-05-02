---
title: Como ligar uma subscrição do Azure - Azure Active Directory B2C | Documentos da Microsoft
description: Guia passo a passo para ativar a faturação para o inquilino do Azure AD B2C para uma subscrição do Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8f9e3e63942c8fac5fc24ca26969b71e6ef4257f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706817"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Ligar uma subscrição do Azure a um inquilino do Azure Active Directory B2C

> [!IMPORTANT]
> Para obter as informações mais recentes sobre a utilização de faturação e preços para o Azure Active Directory (Azure AD) B2C, consulte [preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Os custos de utilização para o Azure AD B2C são faturados a uma subscrição do Azure. Quando é criado um inquilino do Azure AD B2C, o administrador de inquilino tem de ligar explicitamente o inquilino do Azure AD B2C a uma subscrição do Azure. Este artigo mostra-lhe como.

> [!NOTE]
> Uma subscrição ligada a um inquilino do Azure AD B2C pode ser utilizada para a faturação de utilização do Azure AD B2C ou outros recursos do Azure, incluindo recursos adicionais do Azure AD B2C.  Ele não pode ser usado para adicionar outros serviços do Azure baseada na licença ou licenças do Office 365 no inquilino do Azure AD B2C.

A ligação de subscrição é obtida através da criação de um Azure AD B2C "recurso" dentro da subscrição do Azure de destino. Azure AD B2C muitos "recursos" podem ser criados dentro de uma única subscrição do Azure, juntamente com outros recursos do Azure (para armazenamento de dados de exemplo, VMs, LogicApps). Pode ver todos os recursos dentro da subscrição ao aceder ao inquilino do Azure AD que a subscrição está associada à.

Subscrições de fornecedores de soluções Cloud (CSP) do Azure são suportadas no Azure AD B2C. A funcionalidade está disponível através de APIs ou o portal do Azure para o Azure AD B2C e para todos os recursos do Azure. Os administradores de subscrição do CSP podem ligar, mover e eliminar relações com o Azure AD B2C da mesma forma que ele tem feito para todos os recursos do Azure. A gestão do Azure AD B2C, usando o controle de acesso baseado em funções não é afetada pela associação entre uma subscrição do Azure CSP e o inquilino do Azure AD B2C. Controlo de acesso baseado em funções é efetuado através de funções de base de inquilino, não baseados em assinatura funções.

Uma subscrição do Azure válida é necessária para continuar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

Deve primeiro [criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md) que gostaria de ter ligar uma subscrição. Ignore este passo se já tiver criado um inquilino do Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Abrir o portal do Azure no inquilino do Azure AD, que mostra a sua subscrição do Azure

Navegue para o inquilino do Azure AD que mostra a sua subscrição do Azure. Abra o [portal do Azure](https://portal.azure.com)e mude para o inquilino do Azure AD que mostra a subscrição do Azure que pretende utilizar.

![Mudar para o inquilino do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Localizar o Azure AD B2C no Azure Marketplace

Clique nas **criar um recurso** botão. No campo **Procurar no Marketplace**, escreva `B2C`.

![Botão Adicionar realçado e o texto do Azure AD B2C no campo Procurar no marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na lista de resultados, selecione **do Azure AD B2C**.

![O Azure AD B2C selecionado na lista de resultados](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Detalhes sobre o Azure AD B2C são mostrados. Para começar a configurar o seu novo inquilino do Azure Active Directory B2C, clique no botão **Criar**.

No ecrã de criação de recursos, selecione **inquilino de ligação do Azure AD B2C existente, a minha subscrição do Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Criar um recurso do Azure AD B2C dentro da subscrição do Azure

Na caixa de diálogo de criação de recursos, selecione um inquilino do Azure AD B2C no menu pendente. Verá todos os inquilinos que é o administrador global do e os que já não estiverem ligados a uma subscrição.

O nome do recurso do Azure AD B2C vai ser pré-selecionado de acordo com o nome de domínio do inquilino do Azure AD B2C.

Para a subscrição, selecione uma subscrição do Azure Active Directory que é o administrador de.

Selecione um grupo de recursos e a localização do grupo de recursos. A escolha feita aqui não tem qualquer impacto na sua localização do inquilino do Azure AD B2C, o desempenho ou o estado de faturação.

![Criar o recurso B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerir os recursos de inquilino do Azure AD B2C

Depois de um recurso do Azure AD B2C for criado com êxito da subscrição do Azure, deverá ver um novo recurso do tipo "Inquilino de B2C" adicionado juntamente com os outros recursos do Azure.

Pode usar este recurso para:

- Navegue para a subscrição para rever as informações de faturas.
- Aceda ao seu inquilino do Azure AD B2C
- Submeter pedidos de suporte
- Mova o recurso de inquilino do Azure AD B2C para outra subscrição do Azure ou para outro grupo de recursos.

![Definições de recursos de B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="self-imposed-restrictions"></a>Restrições de Self-impostas

Um utilizador pode estabelecer uma restrição regional para a criação de recursos do Azure. Esta restrição pode impedir que a criação de recursos do Azure AD B2C. Para atenuar, relaxe esta restrição.

## <a name="next-steps"></a>Passos Seguintes

Assim que estas etapas forem concluídas para cada um dos seus inquilinos do Azure AD B2C, sua subscrição do Azure é faturada de acordo com os detalhes da sua subscrição Azure Direct ou contrato Enterprise.

Pode rever os detalhes de faturas e utilização na sua subscrição do Azure selecionada. Também pode rever os relatórios de utilização detalhada do dia para dia com o [API de relatórios de utilização](active-directory-b2c-reference-usage-reporting-api.md).
