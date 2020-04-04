---
title: Corrigir erros de diretório desajustados nos Serviços de Domínio da AD do Azure [ Microsoft Docs
description: Saiba o que significa um erro de diretório desajustado e como resolvê-lo nos Serviços de Domínio Da AD azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 6ff12ce5fec8fcc49fa21ef5f3009fc2283300c4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654828"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolver erros de diretório desajustados para os domínios geridos pelos serviços de domínio da Azure AD existentes

Se um domínio gerido pelo Azure Ative Directory Services (Azure AD DS) mostrar um erro de inquilino desajustado, não pode administrar o domínio gerido até ser resolvido. Este erro ocorre se a rede virtual Azure subjacente for transferida para um diretório Azure AD diferente.

Este artigo explica porque o erro ocorre e como resolvê-lo.

## <a name="what-causes-this-error"></a>O que causa este erro?

Um erro de diretório desajustado acontece quando um domínio gerido pela Azure AD DS e uma rede virtual pertencem a dois inquilinos da AD Azure diferentes. Por exemplo, você pode ter um domínio gerido Azure AD DS chamado *aaddscontoso.com* que funciona no inquilino Azure AD de Contoso. No entanto, a rede virtual Azure para domínio gerido faz parte do inquilino da Fabrikam Azure AD.

O Azure utiliza o controlo de acesso baseado em funções (RBAC) para limitar o acesso aos recursos. Quando você ativa O DS Azure num inquilino da AD Azure, hashes credenciais são sincronizados para o domínio gerido. Esta operação requer que seja administrador de inquilinos para o diretório Azure AD, e o acesso às credenciais deve ser controlado. Para desdobrar recursos para uma rede virtual Azure e controlar o tráfego, deve ter privilégios administrativos na rede virtual em que implanta o Azure ADDS.

Para que o RBAC funcione de forma consistente e segura para todos os recursos que o Azure AD DS utiliza, o domínio gerido e a rede virtual devem pertencer ao mesmo inquilino Azure AD.

As seguintes regras aplicam-se no ambiente do Gestor de Recursos:

- Um diretório Azure AD pode ter várias subscrições Azure.
- Uma subscrição azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerido pelos Serviços de Domínio Azure AD está habilitado para um diretório Azure AD.
- Um domínio gerido pelo Azure AD Domain Services pode ser ativado numa rede virtual pertencente a qualquer uma das subscrições azure dentro do mesmo inquilino Azure AD.

### <a name="valid-configuration"></a>Configuração válida

No seguinte cenário de implantação de exemplo, o domínio gerido pela AD DS contoso Azure está habilitado no inquilino da AD Contoso Azure. O domínio gerido é implantado numa rede virtual que pertence a uma subscrição Azure propriedade do inquilino da AD Contoso Azure. Tanto o domínio gerido como a rede virtual pertencem ao mesmo inquilino azure AD. Esta configuração de exemplo é válida e totalmente suportada.

![Configuração válida do inquilino Azure AD DS com o domínio gerido e parte da rede virtual do mesmo inquilino Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configuração de inquilino desajustado

Neste cenário de implantação de exemplo, o domínio gerido pela AD DS contoso Azure está habilitado no inquilino da AD Contoso Azure. No entanto, o domínio gerido é implantado numa rede virtual que pertence a uma subscrição Azure propriedade do inquilino da Fabrikam Azure AD. O domínio gerido e a rede virtual pertencem a dois inquilinos da AD Azure diferentes. Esta configuração de exemplo é um inquilino desajustado e não é suportado. A rede virtual deve ser transferida para o mesmo inquilino Azure AD que o domínio gerido.

![Configuração de inquilino desajustado](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Resolver erro de inquilino desajustado

As duas opções seguintes resolvem o erro de diretório desajustado:

* [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) a partir do seu diretório Azure AD existente. [Crie um domínio gerido azure AD DS](tutorial-create-instance.md) de substituição no mesmo diretório Azure AD que a rede virtual que deseja utilizar. Quando estiver pronto, junte todas as máquinas anteriormente unidas ao domínio eliminado ao domínio gerido recriado.
* [Mova a subscrição Azure](../cost-management-billing/manage/billing-subscription-transfer.md) contendo a rede virtual para o mesmo diretório Azure AD que o domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre problemas de resolução de problemas com o Azure AD DS, consulte o guia de resolução de [problemas](troubleshoot.md).
