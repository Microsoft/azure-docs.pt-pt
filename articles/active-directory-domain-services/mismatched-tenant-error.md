---
title: Corrigir erros de diretório desajustados nos Serviços de Domínio Ad da Azure Microsoft Docs
description: Saiba o que significa um erro de diretório desajustado e como resolvê-lo em Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619305"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Resolver erros de diretório desajustados para os domínios geridos pelo Azure Ative Directory Domain Services

Se um domínio gerido por Um Azure Ative Directory Domain Services (Azure AD DS) mostrar um erro de inquilino desajustado, não pode administrar o domínio gerido até ser resolvido. Este erro ocorre se a rede virtual Azure subjacente for transferida para um diretório AD Azure diferente.

Este artigo explica por que o erro ocorre e como resolvê-lo.

## <a name="what-causes-this-error"></a>O que causa este erro?

Um erro de diretório desajustado ocorre quando um domínio gerido AZure AD DS e rede virtual pertencem a dois inquilinos AD Azure diferentes. Por exemplo, você pode ter um domínio gerido chamado *aaddscontoso.com* que funciona no inquilino AD Azure de Contoso. No entanto, a rede virtual Azure para domínio gerido faz parte do inquilino da AD Fabrikam Azure.

O controlo de acesso baseado em funções Azure (Azure RBAC) é utilizado para limitar o acesso aos recursos. Quando você ativa Azure AD DS em um inquilino AZURE AD, as hashes credenciais são sincronizadas para o domínio gerido. Esta operação requer que você seja um administrador inquilino para o diretório AZure AD, e o acesso às credenciais deve ser controlado.

Para implantar recursos para uma rede virtual Azure e controlar o tráfego, você deve ter privilégios administrativos na rede virtual em que você implementa o domínio gerido.

Para que o Azure RBAC funcione de forma consistente e segura no acesso a todos os recursos que a Azure AD usa, o domínio gerido e a rede virtual devem pertencer ao mesmo inquilino AD AZure.

Aplicam-se as seguintes regras para as implantações:

- Um diretório AD Azure pode ter várias subscrições Azure.
- Uma subscrição do Azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerido está ativado para um diretório AD Azure.
- Um domínio gerido pode ser ativado numa rede virtual pertencente a qualquer uma das subscrições Azure dentro do mesmo inquilino AZure AD.

### <a name="valid-configuration"></a>Configuração válida

No seguinte cenário de implantação de exemplo, o domínio gerido por Contoso está ativado no inquilino Da AD Contoso Azure. O domínio gerido é implantado numa rede virtual que pertence a uma subscrição da Azure detida pelo inquilino DaD Contoso Azure.

Tanto o domínio gerido como a rede virtual pertencem ao mesmo inquilino AD AZure. Esta configuração de exemplo é válida e totalmente suportada.

![Configuração de inquilino válido Azure AD DS com o domínio gerido e parte da rede virtual do mesmo inquilino AD AZure](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configuração de inquilino desajustada

Neste cenário de implantação de exemplo, o domínio gerido por Contoso está ativado no inquilino Da AD Contoso Azure. No entanto, o domínio gerido é implantado numa rede virtual que pertence a uma subscrição da Azure detida pelo inquilino da AD Fabrikam Azure.

O domínio gerido e a rede virtual pertencem a dois inquilinos AD Azure diferentes. Esta configuração de exemplo é um inquilino desajustado e não é suportado. A rede virtual deve ser transferida para o mesmo inquilino AZure AD que o domínio gerido.

![Configuração de inquilino desajustada](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Resolver erro de inquilino desajustado

As duas opções seguintes resolvem o erro de diretório desajustado:

* Em primeiro lugar, [elimine o domínio gerido](delete-aadds.md) do seu diretório AD Azure existente. Em seguida, [crie um domínio gerido de substituição](tutorial-create-instance.md) no mesmo diretório AD Azure que a rede virtual que pretende utilizar. Quando estiver pronto, junte todas as máquinas previamente unidas ao domínio eliminado ao domínio gerido recriado.
* [Mover a assinatura Azure](../cost-management-billing/manage/billing-subscription-transfer.md) contendo a rede virtual para o mesmo diretório AD Azure que o domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre problemas de resolução de problemas com a Azure AD DS, consulte o [guia de resolução de problemas](troubleshoot.md).
