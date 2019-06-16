---
title: Resolver erros de diretório sem correspondência para domínios geridos existentes do Azure AD Domain Services | Documentos da Microsoft
description: Compreender e resolver erros de diretório sem correspondência para domínios geridos existentes do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: cc5b7286536901a516cb694013f1ad6eb594dcb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66245823"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolver erros de diretório sem correspondência para domínios geridos existentes do Azure AD Domain Services
Tem um domínio gerido do Azure AD Domain Services existente. Quando navegar para o portal do Azure e ver o domínio gerido, verá a seguinte mensagem de erro:

![Erro de diretório sem correspondência](./media/getting-started/mismatched-tenant-error.png)

Não é possível administrar este domínio gerido até que o erro seja resolvido.


## <a name="whats-causing-this-error"></a>O que está causando o erro?
Este erro é causado quando o domínio gerido e a rede virtual que está ativada no pertencem a duas diferentes inquilinos do Azure AD. Por exemplo, tem um domínio gerido chamado 'contoso.com' e esta foi ativada para o inquilino do Azure AD da Contoso. No entanto, a Azure virtual network na qual o domínio gerido foi ativado pertence a Fabrikam - outro inquilino do Azure AD.

O novo portal do Azure (e especificamente a extensão do Azure AD Domain Services) baseia-se no Azure Resource Manager. No ambiente do Azure Resource Manager modernos, determinadas restrições são impostas para fornecer maior segurança e para acesso baseado em funções (RBAC) de controlar a recursos. Ativar os serviços de domínio do Azure AD para um inquilino do Azure AD é uma operação confidencial, uma vez que ele faz com que os hashes de credenciais sejam sincronizados com o domínio gerido. Esta operação requer que seja um administrador inquilino do diretório. Além disso, tem de ter privilégios administrativos através da rede virtual em que habilitar o domínio gerido. Para as verificações RBAC trabalhar de forma consistente, o domínio gerido e a rede virtual devem pertencer ao mesmo inquilino do Azure AD.

Em resumo, não é possível ativar um domínio gerido para "contoso.com" do inquilino do Azure AD numa rede virtual que pertencem a uma subscrição do Azure pertencentes a outro inquilino do Azure AD "fabrikam.com". 

**Configuração válida**: Neste cenário de implementação, o domínio gerido Contoso está ativado para o inquilino Contoso do Azure AD. O domínio gerido é exposto numa rede virtual que pertencem a uma subscrição do Azure pertencentes ao inquilino Contoso do Azure AD. Portanto, tanto o domínio gerido, bem como a rede virtual pertence ao mesmo inquilino do Azure AD. Esta configuração é válido e é totalmente suportada.

![Configuração de inquilinos válido](./media/getting-started/valid-tenant-config.png)

**Configuração do inquilino não correspondentes**: Neste cenário de implementação, o domínio gerido Contoso está ativado para o inquilino Contoso do Azure AD. No entanto, o domínio gerido é exposto numa rede virtual que pertence a uma subscrição do Azure pertencentes ao inquilino do Azure AD a Fabrikam. Por conseguinte, o domínio gerido e a rede virtual pertençam a dois diferentes inquilinos do Azure AD. Esta configuração é a configuração do inquilino não correspondentes e não é suportada. A rede virtual têm de ser movida para o mesmo inquilino do Azure AD (ou seja, Contoso) como o domínio gerido. Consulte a [resolução](#resolution) secção para obter detalhes.

![Configuração do inquilino não correspondentes](./media/getting-started/mismatched-tenant-config.png)

Por conseguinte, quando o domínio gerido e a rede virtual que está ativada no pertencerem a dois diferentes inquilinos do Azure AD, este erro.

As seguintes regras aplicam-se no ambiente do Gestor de recursos:
- Um diretório do Azure AD pode ter várias subscrições do Azure.
- Uma subscrição do Azure pode ter vários recursos, tais como redes virtuais.
- Um único domínio gerido do Azure AD Domain Services está ativado para um diretório do Azure AD.
- Um domínio gerido do Azure AD Domain Services pode ser ativado numa rede virtual que pertençam a qualquer uma das subscrições do Azure no mesmo inquilino do Azure AD.


## <a name="resolution"></a>Resolução
Tem duas opções para resolver o erro de diretório sem correspondência. Pode:

- Clique nas **eliminar** domínio gerido do botão para excluir os existentes. Voltar a criar com o [portal do Azure](https://portal.azure.com), para que o domínio gerido e a rede virtual está disponível no pertencem ao diretório do Azure AD. Junte-se a todas as máquinas anteriormente associadas ao domínio eliminado para o domínio gerido criado recentemente.

- Mova a subscrição do Azure que contém a rede virtual para o diretório do Azure AD, ao qual pertence o domínio gerido. Siga os passos a [transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md) artigo.


## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](create-instance.md)
* [Solução de problemas - serviços de domínio do Azure AD](troubleshoot.md)
