---
title: Upgrade de DirSync e Azure AD Sync [ Microsoft Docs
description: Descreve como atualizar de DirSync e Azure AD Sync para Azure AD Connect.
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60381183"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Atualizar o Windows Azure Active Directory Sync e o Azure Active Directory Sync
O Azure AD Connect é a melhor forma de ligar o seu diretório no local ao Azure AD e ao Office 365. Esta é uma ótima altura para fazer upgrade para o Azure AD Connect do Windows Azure Ative Directory Sync (DirSync) ou Azure AD Sync, uma vez que estas ferramentas estão agora deprectadas e já não são suportadas a partir de 13 de abril de 2017.

As duas ferramentas de sincronização de identidade que são depreciadas foram oferecidas para clientes florestais individuais (DirSync) e para clientes multiflorestais e outros clientes avançados (Azure AD Sync). Estas ferramentas mais antigas foram substituídas por uma única solução que está disponível para todos os cenários: Azure AD Connect. Oferece novas funcionalidades, melhorias de funcionalidades e suporte para novos cenários. Para poder continuar a sincronizar os seus dados de identidade no local para o Azure AD e office 365, recomendamos vivamente que faça o upgrade para o Azure AD Connect. A Microsoft não garante que estas versões mais antigas funcionem depois de 31 de dezembro de 2017.

O último lançamento do DirSync foi lançado em julho de 2014 e o último lançamento do Azure AD Sync foi lançado em maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect
Azure AD Connect é o sucessor do DirSync e do Azure AD Sync. Combina todos os cenários que estes dois apoiaram. Pode ler mais sobre isso na Integração das suas identidades no local com o [Diretório Ativo Azure](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Horário de depreciação
| Date | Comentário |
| --- | --- |
| 13 de abril de 2016 |O Windows Azure Ative Directory Sync ("DirSync") e o Microsoft Azure Ative Directory Sync ("Azure AD Sync") são anunciados como depreciados. |
| 13 de abril de 2017 |O apoio termina. Os clientes deixarão de poder abrir um caso de suporte sem atualizar primeiro o Azure AD Connect. |
|31 de dezembro de 2017|A Azure AD já não pode aceitar comunicações do Windows Azure Ative Directory Sync ("DirSync") e do Microsoft Azure Ative Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Como transitar para Azure AD Connect
Se estiver a executar o DirSync, existem duas formas de atualizar: atualização no local e implementação paralela. Uma atualização no local é recomendada para a maioria dos clientes e se você tiver um sistema operativo recente e menos de 50.000 objetos. Noutros casos, recomenda-se fazer uma implementação paralela onde a configuração do DirSync é transferida para um novo servidor que executa o Azure AD Connect.

| Solução | Cenário |
| --- | --- |
| [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Se tiver um servidor DirSync já existente em execução.</li> |
| [Upgrade do Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Se estiver a mudar-se do Azure AD Sync.</li> |

Se quiser ver como fazer uma atualização no local do DirSync para o Azure AD Connect, consulte este vídeo do Canal 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>FAQ
**P: Recebi uma notificação por e-mail da Equipa Azure e/ou uma mensagem do centro de mensagens Office 365, mas estou a usar o Connect.**  
A notificação também foi enviada aos clientes que utilizam o Azure AD Connect com um número de construção número 1.0. \*.0 (utilizando uma libertação pré-1.1). A Microsoft recomenda aos clientes que se mantenham atuais com os lançamentos do Azure AD Connect. A funcionalidade [de atualização automática](how-to-connect-install-automatic-upgrade.md) introduzida no 1.1 facilita a instalação de uma versão recente do Azure AD Connect.

**P: DirSync/Azure AD Sync deixará de funcionar a 13 de abril de 2017?**  
DirSync/Azure AD Sync continuará a trabalhar no dia 13 de abril de 2017.  No entanto, a Azure AD pode deixar de aceitar comunicações da DirSync/Azure AD Sync após 31 de dezembro de 2017.

**P: Quais as versões DirSync que posso atualizar?**  
É suportado para atualizar a partir de qualquer versão DirSync atualmente em uso. 

**P: E o Conector Azure AD para FIM/MIM?**  
O Conector Azure AD para FIM/MIM **não** foi anunciado como depreciado. Está no congelamento de **recursos;** nenhuma nova funcionalidade é adicionada e não recebe correções de bugs. A Microsoft recomenda aos clientes que o utilizem para planear em mudar-se para o Azure AD Connect. Recomenda-se vivamente que não inicie novas implementações utilizando-a. Este Conector será anunciado depreceed no futuro.

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
