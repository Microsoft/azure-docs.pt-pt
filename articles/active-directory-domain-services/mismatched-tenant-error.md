---
title: Resolver erros de diretório incompatíveis no Azure AD Domain Services | Microsoft Docs
description: Entender e resolver erros de diretório incompatíveis para domínios gerenciados Azure AD Domain Services existentes
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 4978f7b782271daff996807172a24103bd8d9860
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617283"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolver erros de diretório incompatíveis para domínios gerenciados Azure AD Domain Services existentes
Você tem um domínio gerenciado Azure AD Domain Services existente. Ao navegar até o portal do Azure e exibir o domínio gerenciado, você verá a seguinte mensagem de erro:

![Erro de diretório incompatível](./media/getting-started/mismatched-tenant-error.png)

Você não pode administrar este domínio gerenciado até que o erro seja resolvido.


## <a name="whats-causing-this-error"></a>O que está causando esse erro?
Esse erro é causado quando o domínio gerenciado e a rede virtual em que ela está habilitada pertencem a dois locatários diferentes do Azure AD. Por exemplo, você tem um domínio gerenciado chamado ' contoso.com ' e foi habilitado para o locatário do Azure AD da contoso. No entanto, a rede virtual do Azure na qual o domínio gerenciado foi habilitado pertence à Fabrikam-um locatário diferente do Azure AD.

O novo portal do Azure (e, especificamente, a extensão Azure AD Domain Services) é criado em Azure Resource Manager. No ambiente de Azure Resource Manager moderno, determinadas restrições são impostas para fornecer maior segurança e controle de acesso baseado em funções (RBAC) para recursos. A habilitação de Azure AD Domain Services para um locatário do Azure AD é uma operação confidencial, pois faz com que os hashes de credenciais sejam sincronizados com o domínio gerenciado. Esta operação exige que você seja um administrador de locatários para o diretório. Além disso, você deve ter privilégios administrativos sobre a rede virtual na qual você habilita o domínio gerenciado. Para que as verificações do RBAC funcionem de forma consistente, o domínio gerenciado e a rede virtual devem pertencer ao mesmo locatário do Azure AD.

Em suma, você não pode habilitar um domínio gerenciado para um locatário do Azure AD ' contoso.com ' em uma rede virtual que pertence a uma assinatura do Azure de propriedade de outro locatário do Azure AD ' fabrikam.com '. 

**Configuração válida**: Nesse cenário de implantação, o domínio gerenciado da Contoso é habilitado para o locatário do Azure AD da contoso. O domínio gerenciado é exposto em uma rede virtual que pertence a uma assinatura do Azure de Propriedade do locatário do Azure AD da contoso. Portanto, o domínio gerenciado, bem como a rede virtual, pertencem ao mesmo locatário do Azure AD. Essa configuração é válida e tem suporte total.

![Configuração de locatário válida](./media/getting-started/valid-tenant-config.png)

**Configuração de locatário incompatível**: Nesse cenário de implantação, o domínio gerenciado da Contoso é habilitado para o locatário do Azure AD da contoso. No entanto, o domínio gerenciado é exposto em uma rede virtual que pertence a uma assinatura do Azure de Propriedade do locatário do Azure AD da Fabrikam. Portanto, o domínio gerenciado e a rede virtual pertencem a dois locatários diferentes do Azure AD. Esta configuração é a configuração de locatário incompatível e não tem suporte. A rede virtual deve ser movida para o mesmo locatário do Azure AD (ou seja, contoso) como o domínio gerenciado. Consulte a seção [resolução](#resolution) para obter detalhes.

![Configuração de locatário incompatível](./media/getting-started/mismatched-tenant-config.png)

Portanto, quando o domínio gerenciado e a rede virtual em que ele está habilitado pertencem a dois locatários diferentes do Azure AD, você verá esse erro.

As regras a seguir se aplicam no ambiente do Resource Manager:
- Um diretório do Azure AD pode ter várias assinaturas do Azure.
- Uma assinatura do Azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerenciado Azure AD Domain Services é habilitado para um diretório do Azure AD.
- Um Azure AD Domain Services domínio gerenciado pode ser habilitado em uma rede virtual que pertence a qualquer uma das assinaturas do Azure dentro do mesmo locatário do Azure AD.


## <a name="resolution"></a>Resolução
Você tem duas opções para resolver o erro de diretório incompatível. Você pode:

- Clique no botão **excluir** para excluir o domínio gerenciado existente. Recrie usando o [portal do Azure](https://portal.azure.com), para que o domínio gerenciado e a rede virtual em que ele está disponível pertençam ao diretório do Azure AD. Junte-se a todos os computadores adicionados anteriormente ao domínio excluído ao domínio gerenciado recém-criado.

- Mova a assinatura do Azure que contém a rede virtual para o diretório do Azure AD ao qual seu domínio gerenciado pertence. Siga as etapas no artigo [transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md) .


## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](tutorial-create-instance.md)
* [Guia de solução de problemas-Azure AD Domain Services](troubleshoot.md)
