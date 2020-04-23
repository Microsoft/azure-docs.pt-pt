---
title: Understanding Azure AD Connect 1.4.xx.x e desaparecimento de dispositivos Microsoft Docs
description: Este documento descreve um problema que surge com a versão 1.4.xx.x do Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176036"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Compreensão Azure AD Connect 1.4.xx.x e desaparecimento do dispositivo
Com a versão 1.4.xx.x do Azure AD Connect, alguns clientes podem ver alguns ou todos os seus dispositivos Windows desaparecerem do Azure AD. Isto não é motivo de preocupação, uma vez que estas identidades do dispositivo não são utilizadas pela Azure AD durante a autorização de acesso condicional. Esta alteração não eliminará nenhum dispositivo Windows que tenha sido corretamente registado com o Azure AD para Hybrid Azure AD Join.

Se vir a eliminação de objetos do dispositivo em Azure AD que exceda o Limiar de Eliminação de Exportação, é aconselhável que o cliente permita que as supressões passem. [Como: permitir que os apagues fluam quando excederem o limiar de eliminação](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Segundo plano
Os dispositivos Windows registados como Hybrid Azure AD Joined estão representados no Azure AD como objetos de dispositivo. Estes objetos do dispositivo podem ser utilizados para acesso condicional. Os dispositivos Windows 10 estão sincronizados com a nuvem através do Azure AD Connect, os dispositivos Windows de nível inferior estão registados diretamente usando AD FS ou um único sinal de entrada sem emenda.

## <a name="windows-10-devices"></a>Dispositivos Windows 10
Apenas os dispositivos Windows 10 com um valor específico de atributo do UserCertificate configurado pela Hybrid Azure AD Join devem ser sincronizados com a nuvem pelo Azure AD Connect. Em versões anteriores do Azure AD Connect este requisito não foi rigorosamente aplicado, resultando em objetos de dispositivos desnecessários em Azure AD. Estes dispositivos em Azure AD sempre permaneceram no estado "pendente", uma vez que estes dispositivos não se destinavam a ser registados na Azure AD. 

Esta versão do Azure AD Connect apenas irá sincronizar dispositivos Windows 10 que estão corretamente configurados para serem Hybrid Azure AD Joined. Os objetos do dispositivo Windows 10 sem o Azure AD juntam-se a um utilizador específicoO Certificado de utilizador será removido da AD Azure.

## <a name="down-level-windows-devices"></a>Dispositivos Windows de nível inferior
O Azure AD Connect nunca deve sincronizar [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Quaisquer dispositivos em AD Azure previamente sincronizados incorretamente serão agora eliminados da AD Azure. Se o Azure AD Connect está a tentar eliminar [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), então o dispositivo não é o que foi criado pela [Microsoft Workplace Join para computadores não Windows 10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) e não é capaz de ser consumido por qualquer outra funcionalidade da AD Azure.

Alguns clientes poderão ter de revisitar [How To: Plan your hybrid Azure Ative Directory join implementy](../devices/hybrid-azuread-join-plan.md) to get their Windows devices registered correcte and ensure that such devices can fully participate in device's Conditional Access. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Como posso verificar quais os dispositivos que são eliminados com esta atualização?

Para verificar quais os dispositivos eliminados, pode utilizar este script PowerShell:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Este script gera um relatório sobre certificados armazenados em objetos de Computador de Diretório Ativo, especificamente, certificados emitidos pela funcionalidade de adesão do Hybrid Azure AD.
Verifica os certificados presentes na propriedade userCertificate de um objeto de computador em AD e, para cada certificado não expirado presente, valida se o certificado foi emitido para a função de adesão do Hybrid Azure AD (ou seja, nome do assunto corresponde ao CN={ObjectGUID}).
Antes, o Azure AD Connect sincronizaria com o Azure AD qualquer computador que contivesse pelo menos um certificado válido mas a partir da versão 1.4 do Azure AD Connect, o motor de sincronização pode identificar certificados de adesão híbrido sinuoso e irá 'filtrar' o objeto do computador desde a sincronização para a AD Azure, a menos que haja um certificado de adesma Híbrido Azure AD.
Os Dispositivos Azure AD que já estavam sincronizados com a AD mas que não possuam um certificado de adesão híbrido Azure AD válido serão eliminados (CloudFiltered=TRUE) pelo motor de sincronização.

## <a name="next-steps"></a>Passos Seguintes
- [Histórico da versão Azure AD Connect](reference-connect-version-history.md)
