---
title: Upgrade de DirSync e Azure AD Sync Microsoft Docs
description: Descreve como fazer upgrade de DirSync e Azure AD Sync para Azure AD Connect.
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
ms.openlocfilehash: 713ec3a4020434fa73aad2e04676129cf43853be
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165846"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Atualizar o Windows Azure Active Directory Sync e o Azure Active Directory Sync
O Azure AD Connect é a melhor forma de ligar o seu diretório no local com o Azure AD e o Microsoft 365. Este é um ótimo momento para fazer upgrade para Azure AD Connect do Windows Azure Ative Directory Sync (DirSync) ou Azure AD Sync (AADSync), uma vez que estas ferramentas estão agora depreciadas e já não são suportadas a partir de 13 de abril de 2017.

As duas ferramentas de sincronização de identidade que são depreciadas foram oferecidas a clientes florestais individuais (DirSync) e a clientes multi-florestais e outros clientes avançados (Azure AD Sync). Estas ferramentas mais antigas foram substituídas por uma única solução disponível para todos os cenários: Azure AD Connect. Oferece novas funcionalidades, melhorias de funcionalidades e suporte para novos cenários. Para poder continuar a sincronizar os seus dados de identidade no local para Azure AD e Microsoft 365, recomendamos vivamente que atualize para Azure AD Connect. A Microsoft não garante que estas versões mais antigas funcionem depois de 31 de dezembro de 2017.

O último lançamento da DirSync foi lançado em julho de 2014 e o último lançamento do Azure AD Sync foi lançado em maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect
Azure AD Connect é o sucessor de DirSync e Azure AD Sync. Combina todos os cenários que estes dois apoiaram. Pode ler mais sobre isso na [integração das suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Horário de depreciação
| Data | Comentário |
| --- | --- |
| 13 de abril de 2016 |O Windows Azure Ative Directory Sync ("DirSync") e o Microsoft Azure Ative Directory Sync ("Azure AD Sync") são anunciados como precários. |
| 13 de abril de 2017 |O apoio termina. Os clientes deixarão de poder abrir um caso de suporte sem atualizar em primeiro lugar o Azure AD Connect. |
|31 de dezembro de 2017|O Azure AD pode deixar de aceitar comunicações do Windows Azure Ative Directory Sync ("DirSync") e do Microsoft Azure Ative Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Como transitar para Azure AD Connect
Se estiver a executar o DirSync, há duas formas de atualizar: upgrade no local e implementação paralela. Recomenda-se uma atualização no local para a maioria dos clientes e se tiver um sistema operativo recente e menos de 50.000 objetos. Noutros casos, recomenda-se fazer uma implementação paralela onde a sua configuração DirSync é transferida para um novo servidor que executa o Azure AD Connect.

| Solução | Cenário |
| --- | --- |
| [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Se já tiver um servidor DirSync já em funcionamento.</li> |
| [Upgrade a partir de Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Se estiver a mudar-se do Azure AD Sync.</li> |

Se quiser ver como fazer uma atualização no local de DirSync para Azure AD Connect, consulte este vídeo do Canal 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>FAQ
**P: Recebi uma notificação por e-mail da Equipa Azure e/ou uma mensagem do centro de mensagens Microsoft 365, mas estou a usar o Connect.**  
A notificação foi também enviada aos clientes que utilizam o Azure AD Connect com um número de construção 1.0. \* 0 (utilizando uma libertação pré-1.1). A Microsoft recomenda que os clientes se mantenham atuais com as versões Azure AD Connect. A funcionalidade [de atualização automática](how-to-connect-install-automatic-upgrade.md) introduzida no 1.1 facilita sempre a instalação de uma versão recente do Azure AD Connect.

**P: A DirSync/Azure AD Sync vai deixar de funcionar a 13 de abril de 2017?**  
A DirSync/Azure AD Sync continuará a funcionar no dia 13 de abril de 2017.  No entanto, a Azure AD pode deixar de aceitar comunicações da DirSync/Azure AD Sync após 31 de dezembro de 2017.

**P: Quais as versões DirSync que posso atualizar?**  
É suportado para atualizar a partir de qualquer versão DirSync atualmente usada. 

**P: E o Conector AD Azure para FIM/MIM?**  
O Conector AZURE AD para FIM/MIM **não** foi anunciado como precotado. Está no **congelamento de características;** nenhuma nova funcionalidade é adicionada e não recebe correções de bugs. A Microsoft recomenda que os clientes o utilizem para planearem passar do mesmo para o Azure AD Connect. Recomenda-se vivamente que não inicie novas implementações utilizando-a. Este Conector será anunciado preprecado no futuro.

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
