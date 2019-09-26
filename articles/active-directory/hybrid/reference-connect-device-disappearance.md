---
title: Entendendo Azure AD Connect 1.4. XX. x e desaparecem o dispositivo | Microsoft Docs
description: Este documento descreve um problema que surge com a versão 1.4. XX. x de Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316957"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Entendendo Azure AD Connect 1.4. XX. x e desaparecem o dispositivo
Com a versão 1.4. XX. x de Azure AD Connect, alguns clientes podem ver que alguns ou todos os seus dispositivos Windows desaparecem do Azure AD. Isso não é uma causa de preocupação, pois essas identidades de dispositivo não são usadas pelo AD do Azure durante a autorização de acesso condicional. Essa alteração não excluirá nenhum dispositivo Windows registrado corretamente com o Azure AD para ingresso híbrido no Azure AD.

Se você vir a exclusão de objetos de dispositivo no Azure AD excedendo o limite de exclusão de exportação, é recomendável que o cliente permita que as exclusões passem. [Como: permitir que as exclusões fluam quando excederem o limite de exclusão](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Segundo plano
Os dispositivos Windows registrados como ingressados no Azure AD híbrido são representados no Azure AD como objetos de dispositivo. Esses objetos de dispositivo podem ser usados para acesso condicional. Os dispositivos Windows 10 são sincronizados com a nuvem por meio de Azure AD Connect, os dispositivos Windows de nível inferior são registrados diretamente usando AD FS ou logon único contínuo.

## <a name="windows-10-devices"></a>Dispositivos Windows 10
Somente dispositivos Windows 10 com um valor de atributo usercertificar específico configurado pelo ingresso híbrido do Azure AD devem ser sincronizados com a nuvem pelo Azure AD Connect. Em versões anteriores do Azure AD Connect esse requisito não era rigorosamente imposto, resultando em objetos de dispositivo desnecessários no Azure AD. Esses dispositivos no Azure AD sempre permaneceram no estado "pendente" porque esses dispositivos não se destinam a serem registrados com o Azure AD. 

Esta versão do Azure AD Connect sincronizará somente dispositivos Windows 10 que estão configurados corretamente para serem ingressados no Azure AD híbrido. Os objetos de dispositivo do Windows 10 sem o ingresso do Azure AD específico de userCertificate serão removidos do Azure AD.

## <a name="down-level-windows-devices"></a>Dispositivos Windows de nível inferior
Azure AD Connect nunca deve estar sincronizando [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Todos os dispositivos no Azure AD anteriormente sincronizados incorretamente agora serão excluídos do Azure AD. Se Azure AD Connect estiver tentando excluir [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), o dispositivo não será aquele que foi criado pelo [Microsoft Workplace Join para MSI de computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) e não poderá ser consumido por nenhum outro recurso do Azure AD.

Alguns clientes talvez precisem revisitar [como: Planeje sua implementação](../devices/hybrid-azuread-join-plan.md) de junção de Azure Active Directory híbrida para colocar seus dispositivos Windows registrados corretamente e garantir que esses dispositivos possam participar totalmente do acesso condicional com base no dispositivo. 

## <a name="next-steps"></a>Próximos Passos
- [Histórico de versão do Azure AD Connect](reference-connect-version-history.md)
