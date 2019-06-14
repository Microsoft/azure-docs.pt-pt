---
title: 'Azure AD Connect: Selecione o tipo de instalação | Documentos da Microsoft'
description: Este tópico explica como selecionar o tipo de instalação a utilizar para o Azure AD Connect
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348285"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selecione o tipo de instalação a utilizar para o Azure AD Connect
O Azure AD Connect tem dois tipos de instalação para a nova instalação: Expressa e personalizada. Este tópico ajuda-o a decidir a opção a utilizar durante a instalação.

## <a name="express"></a>Express
Express é a opção mais comum e é utilizado em cerca de 90% de todas as novas instalações. Ele foi projetado para fornecer uma configuração que funciona para os cenários mais comuns do cliente.

Parte do princípio:

- Tem um único do Active Directory no local de floresta.
- Tem uma conta de administrador de empresa que pode utilizar para a instalação.
- Tiver menos de 100 000 objetos no Active Directory no local.

Vai obter:

- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) no local ao Azure AD para início de sessão único.
- Uma configuração de que sincroniza [os utilizadores, grupos, contactos e computadores Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Sincronização de todos os objetos elegíveis todos os domínios e todos os UOs.
- [A atualização automática](how-to-connect-install-automatic-upgrade.md) está ativada para se certificar de que sempre usar a versão mais recente disponível.

Opções de onde pode continuar a utilizar Express:

- Se não quiser sincronizar todas as UOs, ainda pode utilizar o Express e na última página, anule a seleção * * iniciar o processo de sincronização... ***. Em seguida, execute novamente o Assistente de instalação e alterar as UOs na [opções de configuração](how-to-connect-installation-wizard.md#customize-synchronization-options) e ativar a sincronização agendada.
- Pretende ativar um dos recursos no Azure AD Premium, tais como a repetição de escrita de palavra-passe. Em primeiro lugar express para obter a instalação inicial foi concluída. Em seguida, execute novamente o Assistente de instalação e alterar os [opções de configuração](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizado
O caminho personalizado permite muito mais opções que express. Ele deve ser usado em todos os casos em que a configuração descrita na secção anterior para express não é representativa para a sua organização.

Utilize se:

- Não tem acesso a uma conta de administrador de empresa no Active Directory.
- Tiver mais do que uma floresta ou se pretende sincronizar mais do que uma floresta no futuro.
- Ter domínios na sua floresta não acessível a partir do servidor do Connect.
- Planeja usar o Federação ou autenticação pass-through para o início de sessão do utilizador.
- Tem mais de 100 000 objetos e tem de utilizar um SQL Server total.
- Planeja usar a filtragem baseada em grupo e não apenas domínio ou filtragem baseada em UO.

## <a name="upgrade-from-dirsync"></a>Atualização do DirSync
Se estiver a utilizar o DirSync, em seguida, siga os passos em [atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) para atualizar a sua configuração existente. Existem duas opções de atualização diferentes:

- Atualização no local para instalar o Connect no mesmo servidor.
- Implementação paralela para instalar o Connect num servidor novo enquanto o servidor do DirSync existente está ainda está operacional.

## <a name="upgrade-from-azure-ad-sync"></a>Atualização do Azure AD Sync
Se estiver a utilizar o Azure AD Sync, pode seguir a [mesmos passos](how-to-upgrade-previous-version.md) como ao atualizar de uma versão de ligar para uma mais recente. Existem duas opções de atualização diferentes:

- Atualização no local para instalar o Connect no mesmo servidor.
- A migração rotativa para instalar o Connect num servidor novo ao existente o servidor de sincronização do Azure AD é ainda está operacional.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrar do FIM2010 ou MIM2016
Se estiver a utilizar o Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016 com o conector do Azure AD, em seguida, a única opção é uma migração. Siga os passos descritos em [migração rotativa](how-to-upgrade-previous-version.md#swing-migration). Nos passos, substitua qualquer menção do Azure AD Sync FIM2010/MIM2016.

## <a name="next-steps"></a>Passos Seguintes
Consoante a opção que selecionou para utilizar, utilize a tabela de conteúdos para a esquerda para encontrar o seu artigo com os passos detalhados.
