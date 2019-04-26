---
title: Atualizar do DirSync e Azure AD Sync | Documentos da Microsoft
description: Descreve como atualizar do DirSync e Azure AD Sync para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381183"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Atualizar de Directory Sync Active Directory do Windows Azure Directory Sync Active Directory e o Azure
O Azure AD Connect é a melhor forma de ligar o seu diretório no local ao Azure AD e ao Office 365. Esta é uma ótima altura para atualizar para o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync, à medida que estas ferramentas foram preteridas e já não são suportadas desde 13 de Abril de 2017.

As ferramentas de sincronização de dois identidade são preteridas foram oferecidas para clientes de floresta única (DirSync) e para várias florestas e outras avançadas clientes (Azure AD Sync). Estas ferramentas mais antigas foram substituídas por uma solução única que está disponível para todos os cenários: Azure AD Connect. Ele oferece novas funcionalidades, aprimoramentos de recursos e suporte para novos cenários. Para poder continuar a sincronizar os dados de identidade no local para o Azure AD e do Office 365, recomendamos vivamente que Atualize para o Azure AD Connect. A Microsoft não garante essas versões mais antigas para funcionar após 31 de Dezembro de 2017.

A última versão do DirSync foi lançada em Julho de 2014 e a última versão do Azure AD Sync foi lançada em Maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect
Azure AD Connect é o sucessor do DirSync e Azure AD Sync. Combina todos os cenários destes dois suportados. Pode ler mais sobre isso na [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Agenda de preterição
| Date | Comentário |
| --- | --- |
| 13 de Abril de 2016 |Windows Azure Active Directory Sync ("DirSync") e o Microsoft Azure Active Directory Sync ("Azure AD Sync") são anunciadas como preteridos. |
| 13 de Abril de 2017 |O suporte termina. Os clientes já não será capazes de abrir um incidente de suporte sem atualizar primeiro para o Azure AD Connect. |
|31 de Dezembro de 2017|O Azure AD já não poderá aceitar comunicações de Windows Azure Active Directory Sync ("DirSync") e o Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Como fazer a transição para o Azure AD Connect
Se estiver a executar o DirSync, existem duas formas, que pode atualizar: Implementação de atualização e paralela no local. Uma atualização no local é recomendada para a maioria dos clientes e, se tem um recente operativo sistema e menos de 50 000 objetos. Em outros casos, é recomendado efetuar uma implementação paralela em que a configuração do DirSync é movida para um novo servidor com o Azure AD Connect.

| Solução | Cenário |
| --- | --- |
| [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Se tiver um servidor existente do DirSync já em execução.</li> |
| [Atualização do Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Se estiver a mover a partir do Azure AD Sync.</li> |

Se quiser ver como fazer uma atualização no local a partir do DirSync para o Azure AD Connect, em seguida, veja este vídeo do Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>FAQ
**P: Eu recebeu uma notificação por e-mail de equipa do Azure e/ou uma mensagem a partir do Centro de mensagens do Office 365, mas estou usando o Connect.**  
A notificação também foi enviada aos clientes que utilizam o Azure AD Connect com um número de compilação 1.0. \*.0 (usando uma versão de pré-1.1). A Microsoft recomenda que os clientes a manter-se atualizado com as versões do Azure AD Connect. O [a atualização automática](how-to-connect-install-automatic-upgrade.md) recurso introduzido na 1.1 facilita sempre ter uma versão recente do Azure AD Connect instalado.

**P: DirSync/Azure AD Sync deixa de funcionar em 13 de Abril de 2017?**  
DirSync/Azure AD Sync irá continuar a trabalhar em 13 de Abril de 2017.  No entanto, o Azure AD já não poderá aceitar comunicações de DirSync/Azure AD Sync após 31 de Dezembro de 2017.

**P: Quais versões de DirSync pode atualizar do?**  
É suportada para atualizar a partir de qualquer versão de DirSync atualmente a ser utilizado. 

**P: E o conector Azure AD para FIM/MIM?**  
Tem do conector do Azure AD para FIM/MIM **não** foi anunciado como preteridos. Ele está na **congelamento de funcionalidade**; não existem novas funcionalidades adicionadas e recebe sem correções de erros. A Microsoft recomenda que os clientes a usá-lo a planejar a mover-se de para o Azure AD Connect. É vivamente recomendado para não iniciar quaisquer novas implementações usá-lo. Este conector será anunciado preterida no futuro.

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
