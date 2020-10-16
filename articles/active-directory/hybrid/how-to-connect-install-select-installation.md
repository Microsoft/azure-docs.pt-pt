---
title: 'Azure AD Connect: Selecione o seu tipo de instalação / Microsoft Docs'
description: Este tópico acompanha-o como selecionar o tipo de instalação a utilizar para Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358706"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selecionar o tipo de instalação a utilizar para o Azure AD Connect
O Azure AD Connect tem dois tipos de instalação para nova instalação: Expresso e personalizado. Este tópico ajuda-o a decidir que opção usar durante a instalação.

## <a name="express"></a>Express
O Expresso é a opção mais comum e é usado por cerca de 90% de todas as novas instalações. Foi projetado para fornecer uma configuração que funciona para os cenários mais comuns do cliente.

Assume:

- Tens uma única floresta de Diretório Ativo no local.
- Tem uma conta de administrador da empresa que pode usar para a instalação.
- Tem menos de 100.000 objetos no seu Diretório Ativo.

Tem:

- Sincronização de hash de [palavra-passe](how-to-connect-password-hash-synchronization.md) de instalações para Azure AD para um único s-on.
- Uma configuração que sincroniza [os utilizadores, grupos, contactos e computadores Windows 10.](concept-azure-ad-connect-sync-default-configuration.md)
- Sincronização de todos os objetos elegíveis em todos os domínios e todas as OUs.
- [A atualização automática](how-to-connect-install-automatic-upgrade.md) está ativada para se certificar de que utiliza sempre a versão mais recente disponível.

Opções onde ainda pode utilizar o Express:

- Se não quiser sincronizar todas as OUs, ainda pode utilizar o Express e na última página, desmarcar **Iniciar o processo de sincronização...***** Em seguida, volte a executar o assistente de instalação e altere as OUs nas [opções de configuração](how-to-connect-installation-wizard.md#customize-synchronization-options) e ative a sincronização programada.
- Pretende ativar uma das funcionalidades do Azure AD Premium, como a gravação de Password. Primeiro passe pelo expresso para completar a instalação inicial. Em seguida, volte a executar o assistente de instalação e altere as [opções de configuração](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizado
O caminho personalizado permite muito mais opções do que expresso. Deve ser utilizado em todos os casos em que a configuração descrita na secção anterior para expresso não seja representativa para a sua organização.

Utilize se:

- Não tem acesso a uma conta de administração da empresa no Ative Directory.
- Tens mais do que uma floresta ou planeias sincronizar mais do que uma floresta no futuro.
- Tem domínios na sua floresta que não são alcançáveis a partir do servidor Connect.
- Planeia utilizar a federação ou a autenticação de passagem para o súmis do utilizador.
- Tem mais de 100.000 objetos e precisa de usar um Servidor SQL completo.
- Planeia utilizar a filtragem baseada em grupo e não apenas a filtragem baseada em domínios ou em ou.

## <a name="upgrade-from-dirsync"></a>Atualização do DirSync
Se estiver a utilizar o DirSync, siga os passos [de Upgrade da DirSync](how-to-dirsync-upgrade-get-started.md) para atualizar a sua configuração existente. Existem duas opções de upgrade diferentes:

- Upgrade no local para instalar Connect no mesmo servidor.
- Implementação paralela para instalar Connect num novo servidor enquanto o servidor DirSync existente ainda estiver operacional.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade a partir de Azure AD Sync
Se está atualmente a utilizar o Azure AD Sync, então pode seguir os [mesmos passos](how-to-upgrade-previous-version.md) que quando atualiza de uma versão Connect para uma versão mais recente. Existem duas opções de upgrade diferentes:

- Upgrade no local para instalar Connect no mesmo servidor.
- Migração de balanço para instalar Connect num novo servidor enquanto o servidor Azure AD Sync existente ainda está operacional.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrar do FIM2010 ou MIM2016
Se está atualmente a usar o Gestor de Identidade da Vanguarda 2010 ou o Microsoft Identity Manager 2016 com o Azure AD Connector, então a sua única opção é uma migração. Siga os passos descritos na [migração do balanço.](how-to-upgrade-previous-version.md#swing-migration) Nos passos, substitua qualquer menção do Azure AD Sync pelo FIM2010/MIM2016.

## <a name="next-steps"></a>Passos seguintes
Dependendo da opção selecionada para utilizar, utilize a tabela de conteúdos à esquerda para encontrar o seu artigo com os passos detalhados.
