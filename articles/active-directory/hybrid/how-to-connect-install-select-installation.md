---
title: 'Ligação Azure AD: Selecione o seu tipo de instalação / Microsoft Docs'
description: Este tópico acompanha-o através de como selecionar o tipo de instalação a utilizar para azure AD Connect
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
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60348285"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selecionar o tipo de instalação a utilizar para o Azure AD Connect
O Azure AD Connect tem dois tipos de instalação para nova instalação: Expresso e personalizado. Este tópico ajuda-o a decidir qual a opção a utilizar durante a instalação.

## <a name="express"></a>Express
O Expresso é a opção mais comum e é usado por cerca de 90% de todas as novas instalações. Foi projetado para fornecer uma configuração que funciona para os cenários mais comuns do cliente.

Assume:

- Tens uma única floresta de Diretório ativo no local.
- Tem uma conta de administrador da empresa que pode utilizar para a instalação.
- Tem menos de 100.000 objetos no seu Diretório Ativo no local.

Tem:

- Sincronização de hash de [palavra-passe](how-to-connect-password-hash-synchronization.md) de in-local para Azure AD para um único sinal.
- Uma configuração que sincroniza [utilizadores, grupos, contactos e computadores Windows 10.](concept-azure-ad-connect-sync-default-configuration.md)
- Sincronização de todos os objetos elegíveis em todos os domínios e todas as OUs.
- [A atualização automática](how-to-connect-install-automatic-upgrade.md) está ativada para se certificar de que utiliza sempre a versão mais recente disponível.

Opções onde ainda pode utilizar o Express:

- Se não quiser sincronizar todas as OUs, ainda pode utilizar o Express e na última página, desmarcar **Iniciar o processo de sincronização...***. Em seguida, volte a executar o assistente de instalação e altere as OUs em opções de [configuração](how-to-connect-installation-wizard.md#customize-synchronization-options) e ative a sincronização programada.
- Deseja ativar uma das funcionalidades do Azure AD Premium, como a reescrita da Password. Primeiro passe por expresso para completar a instalação inicial. Em seguida, volte a executar o assistente de instalação e altere as opções de [configuração](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizado
O caminho personalizado permite muito mais opções do que expressar. Deve ser utilizado em todos os casos em que a configuração descrita na secção anterior para expresso não é representativa para a sua organização.

Utilize se:

- Não tem acesso a uma conta de administração da empresa no Ative Directory.
- Tens mais do que uma floresta ou planeias sincronizar mais do que uma floresta no futuro.
- Tem domínios na sua floresta que não são acessíveis a partir do servidor Connect.
- Planeia utilizar a federação ou a autenticação pass-through para o utilizador de sessão.
- Tem mais de 100.000 objetos e precisa de usar um Servidor SQL completo.
- Planeia utilizar filtragem baseada em grupo e não apenas filtragem baseada em domínios ou OU.

## <a name="upgrade-from-dirsync"></a>Atualização do DirSync
Se está atualmente a utilizar o DirSync, siga os passos de [Upgrade do DirSync](how-to-dirsync-upgrade-get-started.md) para atualizar a configuração existente. Existem duas opções diferentes de upgrade:

- Upgrade no local para instalar o Connect no mesmo servidor.
- Implementação paralela para instalar o Connect num novo servidor enquanto o servidor DirSync existente ainda está operacional.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade do Azure AD Sync
Se está a utilizar atualmente o Azure AD Sync, então pode seguir os [mesmos passos](how-to-upgrade-previous-version.md) que quando atualiza de uma versão Connect para uma versão mais recente. Existem duas opções diferentes de upgrade:

- Upgrade no local para instalar o Connect no mesmo servidor.
- A migração de balançopara instalar o Connect num novo servidor enquanto o servidor Azure AD Sync existente ainda está operacional.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrar de FIM2010 ou MIM2016
Se está atualmente a utilizar o Forefront Identity Manager 2010 ou o Microsoft Identity Manager 2016 com o Conector AD Azure, então a sua única opção é uma migração. Siga os passos descritos na [migração de balanços.](how-to-upgrade-previous-version.md#swing-migration) Nas etapas, substitua qualquer menção ao Azure AD Sync por FIM2010/MIM2016.

## <a name="next-steps"></a>Passos seguintes
Dependendo da opção que selecionou para utilizar, use a tabela de conteúdos à esquerda para encontrar o seu artigo com os passos detalhados.
