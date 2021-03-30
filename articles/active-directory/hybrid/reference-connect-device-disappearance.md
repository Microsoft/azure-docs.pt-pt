---
title: Compreender a Azure AD Connect 1.4.xx.x e o desaparecimento do dispositivo | Microsoft Docs
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176036"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Compreensão Azure AD Connect 1.4.xx.x e desaparecimento do dispositivo
Com a versão 1.4.xx.x do Azure AD Connect, alguns clientes podem ver alguns ou todos os seus dispositivos Windows desaparecerem do Azure AD. Isto não é motivo de preocupação, uma vez que estas identidades do dispositivo não são utilizadas pela Azure AD durante a autorização de Acesso Condicional. Esta alteração não eliminará quaisquer dispositivos Windows que foram corretamente registados no AZure AD para a Hybrid Azure AD Join.

Se vir a eliminação de objetos do dispositivo em Azure AD excedendo o Limiar de Eliminação de Exportação, é aconselhável que o cliente permita que as supressões passem. [Como: permitir que as eliminações fluam quando excedem o limiar de eliminação](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Fundo
Os dispositivos Windows registados como Hybrid Azure AD Join são representados no Azure AD como objetos do dispositivo. Estes objetos do dispositivo podem ser utilizados para acesso condicional. Os dispositivos windows 10 estão sincronizados na nuvem através do Azure AD Connect, os dispositivos Windows de nível inferior são registados diretamente utilizando o AD FS ou um único sinal de saúde sem emenda.

## <a name="windows-10-devices"></a>Dispositivos Windows 10
Apenas os dispositivos Windows 10 com um valor de atributo específico do utilizadorCertificado configurado pela Hybrid Azure AD Join devem ser sincronizados na nuvem pelo Azure AD Connect. Em versões anteriores do Azure AD Connect este requisito não foi rigorosamente aplicado, resultando em objetos de dispositivo desnecessários em Azure AD. Estes dispositivos em Azure AD sempre permaneceram no estado "pendente", uma vez que estes dispositivos não se destinavam a ser registados no Azure AD. 

Esta versão do Azure AD Connect apenas sincronizará os dispositivos do Windows 10 que estão corretamente configurados para serem HíbridoS Azure AD Unidos. Os objetos do dispositivo Windows 10 sem a junção Azure AD serão removidos do Azure AD.

## <a name="down-level-windows-devices"></a>dispositivos Down-Level Windows
O Azure AD Connect nunca deve estar a sincronizar [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Todos os dispositivos em Azure AD previamente sincronizados incorretamente serão agora eliminados do Azure AD. Se o Azure AD Connect estiver a tentar eliminar [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), então o dispositivo não é o criado pelo [Microsoft Workplace Join para computadores não Windows 10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) e não é capaz de ser consumido por qualquer outra funcionalidade AZure AD.

Alguns clientes poderão ter de revisitar [Como: Planear a implementação do seu Azure Ative Directory híbrido](../devices/hybrid-azuread-join-plan.md) para obter os seus dispositivos Windows registados corretamente e garantir que esses dispositivos podem participar plenamente no Acesso Condicional baseado no dispositivo. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Como posso verificar quais os dispositivos que são eliminados com esta atualização?

Para verificar quais os dispositivos eliminados, pode utilizar este script PowerShell: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Este script gera um relatório sobre certificados armazenados em objetos ative directory Computer, especificamente, certificados emitidos pela funcionalidade de junção AD AZure Híbrida.
Verifica os certificados presentes na propriedade userCertificate de um objeto computador em AD e, para cada certificado não caducado presente, valida se o certificado foi emitido para a funcionalidade de ad AD Híbrido Azure (ou seja, nome do sujeito corresponde a CN={ObjectGUID}).
Antes, o Azure AD Connect sincronizaria com a Azure AD qualquer Computador que contivesse pelo menos um certificado válido, mas a partir da versão 1.4 do Azure AD Connect, o motor de sincronização pode identificar certificados de ad Azure Híbrido e 'cloudfilter' o objeto do computador de sincronização para Azure AD, a menos que exista um certificado de ad Ad híbrido válido.
Os dispositivos AD AD azure que já foram sincronizados com AD mas que não têm um certificado de ad Híbrido Azure válido serão eliminados (CloudFiltered=TRUE) pelo motor de sincronização.

## <a name="next-steps"></a>Passos Seguintes
- [Histórico da versão AZure AD Connect](reference-connect-version-history.md)
