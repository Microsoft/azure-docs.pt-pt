---
title: Política de Grupo e definições de MDM para ESR - Diretório Ativo Azure
description: Definições de gestão para roaming do Estado da Empresa
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672375"
---
# <a name="group-policy-and-mdm-settings"></a>Política de Grupo e definições de MDM

Utilize estas definições de política de grupo e de gestão de dispositivos móveis (MDM) apenas em dispositivos corporativos, uma vez que estas políticas são aplicadas a todo o dispositivo do utilizador. A aplicação de uma política de MDM para desativar a sincronização de definições para um dispositivo pessoal, propriedade do utilizador, terá um impacto negativo na utilização desse dispositivo. Além disso, outras contas de utilizador no dispositivo também serão afetadas pela apólice.

As empresas que pretendam gerir o roaming para dispositivos pessoais (não geridos) podem utilizar o portal Azure para ativar ou desativar o roaming, em vez de utilizarem a Política de Grupo ou OMD.
As tabelas seguintes descrevem as definições de política disponíveis.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado no Microsoft Edge Legacy HTML lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Crómio microsoft Edge, lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Definições de MDM

As definições de política do MDM aplicam-se tanto ao Windows 10 como ao Windows 10 Mobile.  O suporte móvel do Windows 10 existe apenas para a conta da Microsoft, baseada em roaming através da conta OneDrive do utilizador. Consulte os [Dispositivos e pontos finais](enterprise-state-roaming-windows-settings-reference.md) para obter detalhes sobre quais os dispositivos suportados para sincronização baseada em Anúncios Azure.

| Nome | Descrição |
| --- | --- |
| Permitir a ligação à conta da Microsoft |Permite que os utilizadores autentiem usando uma conta Microsoft no dispositivo |
| Permitir sincronizar as minhas definições |Permite que os utilizadores circulem pelas definições do Windows e pelos dados das aplicações; Desativar esta política irá desativar sincronização, bem como backups em dispositivos móveis |

## <a name="group-policy-settings"></a>Definições da política de grupo

As definições de política do grupo aplicam-se aos dispositivos Windows 10 que se juntam a um domínio de Diretório Ativo. A tabela também inclui configurações antigas que parecem gerir definições de sincronização, mas que não funcionam para o Enterprise State Roaming para o Windows 10, que são notadas com 'Não utilizar' na descrição.

Estas definições estão localizadas em: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nome | Descrição |
| --- | --- |
| Contas: Bloquear contas da Microsoft |Esta definição de política impede os utilizadores de adicionar em novo sistema de contas da Microsoft |
| Não sincronizar |Impede que os utilizadores pervertam as definições do Windows e os dados das aplicações |
| Não sincronizar personalizar |Desativa a sincronização do grupo Temáticos |
| Não sincronize as definições do navegador |Desativa a sincronização do grupo Internet Explorer |
| Não sincronizar palavras-passe |Desativa a sincronização do grupo Passwords |
| Não sincronize outras definições do Windows |Desativa a sincronização do grupo de definições de Outras Definições do Windows |
| Não sincronizar a personalização do ambiente de trabalho |Não utilizar; não tem efeito |
| Não sincronizar as ligações medimétricas |Desativa o roaming em ligações medid, tais como celular 3G |
| Não sincronizar apps |Não utilizar; não tem efeito |
| Não sincronize as definições de aplicativos |Desativa o roaming de dados de aplicações |
| Não sincronizar as definições de início |Não utilizar; não tem efeito |

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte a [visão geral do Estado do Roaming.](enterprise-state-roaming-overview.md)
