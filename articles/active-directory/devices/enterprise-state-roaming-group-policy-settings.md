---
title: Políticas de Grupo e definições de MDM para ESR - Diretório Ativo Azure
description: Definições de gestão para roaming do estado da empresa
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252938"
---
# <a name="group-policy-and-mdm-settings"></a>Políticas de Grupo e configurações de MDM

Utilize estas definições de política de grupo e gestão de dispositivos móveis (MDM) apenas em dispositivos de propriedade corporativa, uma vez que estas políticas são aplicadas a todo o dispositivo do utilizador. A aplicação de uma política de MDM para desativar a sincronização de configurações para um dispositivo pessoal e de propriedade do utilizador irá afetar negativamente a utilização desse dispositivo. Além disso, outras contas de utilizadores no dispositivo também serão afetadas pela política.

As empresas que pretendam gerir o roaming para dispositivos pessoais (não geridos) podem usar o portal Azure para permitir ou desativar o roaming, em vez de utilizar a Política de Grupo ou o MDM.
As tabelas que se seguem descrevem as definições de política disponíveis.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado em HTML do Microsoft Edge Legacy lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Chromium microsoft Edge lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Definições de MDM

As definições de política do MDM aplicam-se tanto ao Windows 10 como ao Windows 10 Mobile.  O suporte móvel do Windows 10 existe apenas para a conta da Microsoft baseada em roaming através da conta OneDrive do utilizador. Consulte [os Dispositivos e pontos finais](enterprise-state-roaming-windows-settings-reference.md) para obter mais detalhes sobre quais os dispositivos suportados para sincronização baseada em Azure.

| Nome | Descrição |
| --- | --- |
| Permitir a ligação da conta da Microsoft |Permite que os utilizadores autentem a autenticação utilizando uma conta Microsoft no dispositivo |
| Permitir sincronizar as minhas definições |Permite que os utilizadores vagueiem pelas definições do Windows e dados de aplicações; Desativar esta política irá desativar a sincronização, bem como cópias de segurança em dispositivos móveis |

## <a name="group-policy-settings"></a>Definições da política de grupo

As definições de política de grupo aplicam-se aos dispositivos Windows 10 que se juntam a um domínio ative directory. A tabela também inclui definições antigas que parecem gerir as definições de sincronização, mas que não funcionam para o Roaming do Estado da Empresa para o Windows 10, que são notadas com "Não usar" na descrição.

Estas definições situam-se em: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nome | Descrição |
| --- | --- |
| Contas: Bloquear contas da Microsoft |Esta definição de política impede os utilizadores de adicionarem novas contas da Microsoft neste computador |
| Não sincronize |Impede os utilizadores de percorrerem as definições do Windows e os dados das aplicações |
| Não sincronize personalizar |Desativa a sincronização do grupo Temas |
| Não sincronize as definições do navegador |Desativa a sincronização do grupo Internet Explorer |
| Não sincronize palavras-passe |Desativa sincronização do grupo Passwords |
| Não sincronize outras definições do Windows |Desativa sincronização do grupo de definições de Outras Janelas |
| Não sincronize a personalização do ambiente de trabalho |Não utilizar; não tem efeito |
| Não sincronize em ligações com medição |Desativa o roaming em ligações medidos, tais como celular 3G |
| Não sincronize apps |Não utilizar; não tem efeito |
| Não sincronizar as definições da aplicação |Desativa o roaming dos dados das aplicações |
| Não sincronizar as definições de início |Não utilizar; não tem efeito |

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte [a visão geral do Roaming do Estado da empresa.](enterprise-state-roaming-overview.md)
