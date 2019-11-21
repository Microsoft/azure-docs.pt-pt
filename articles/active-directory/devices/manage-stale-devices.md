---
title: How to manage stale devices in Azure AD | Microsoft Docs
description: Learn how to remove stale devices from your database of registered devices in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1829c56f9804c5aa808461db98a5048d63f55446
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207285"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>How To: Manage stale devices in Azure AD

Idealmente, para concluir o ciclo de vida, deverá anular o registo dos dispositivos registados quando estes já não são necessários. No entanto, devido a vários motivos, como dispositivos perdidos, roubados ou danificados ou a reinstalações do SO, é comum haver dispositivos obsoletos no seu ambiente. Enquanto administrador de TI, é provável que precise de um método para remover os dispositivos obsoletos, para que os seus recursos se possam dedicar à gestão dos dispositivos que têm, efetivamente, de ser geridos.

Neste artigo, vai aprender a gerir de forma eficiente os dispositivos obsoletos no sue ambiente.
  

## <a name="what-is-a-stale-device"></a>O que são dispositivos obsoletos?

Os dispositivos obsoletos são dispositivos registados no Azure AD, mas que já não são utilizados há um determinado período de tempo para aceder a aplicações na cloud. Estes dispositivos têm impacto na capacidade de gerir e dar suporte técnico aos seus dispositivos e utilizadores no inquilino porque: 

- Dispositivos duplicados podem fazer com que seja mais difícil à sua equipa de suporte técnico identificar que dispositivo está atualmente ativo.
- An increased number of devices creates unnecessary device writebacks increasing the time for Azure AD connect syncs.
- Por questões de organização geral e para estar em conformidade, poderá querer ter dispositivos com um estado limpo. 

Os dispositivos obsoletos no Azure AD podem interferir com as políticas gerais de ciclo de vida dos dispositivos da sua organização.

## <a name="detect-stale-devices"></a>Detetar dispositivos obsoletos

Uma vez que os dispositivos obsoletos são definidos como dispositivos registados que já não são utilizados há um determinado período de tempo para aceder a aplicações na cloud, a deteção dos mesmos requer uma propriedade relacionada com carimbo de data/hora. No Azure AD, essa propriedade é denominada **ApproximateLastLogonTimestamp**, ou **activity timestamp**. Se o delta entre o valor de agora e o valor de **carimbo de data/hora da atividade** exceder o período de tempo que definiu para os dispositivos ativos, considera-se que o dispositivo está obsoleto. Este **carimbo de data/hora da atividade** está agora em pré-visualização pública.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Como é gerido o valor do carimbo de data/hora da atividade?  

A avaliação do carimbo de data/hora da atividade é acionado por uma tentativa de autenticação de um dispositivo. O Azure AD avalia o carimbo de data/hora da atividade quando:

- A Conditional Access policies requiring [managed devices](../conditional-access/require-managed-devices.md) or [approved client apps](../conditional-access/app-based-conditional-access.md) has been triggered.
- Os dispositivos Windows 10 associados ao Azure AD ou associados ao Azure AD híbrido estiverem ativos na rede. 
- Os dispositivos geridos do Intune tenham dado entrada no serviço.

If the delta between the existing value of the activity timestamp and the current value is more than 14 days (+/-5 day variance), the existing value is replaced with the new value.

## <a name="how-do-i-get-the-activity-timestamp"></a>Como posso obter o carimbo de data/hora da atividade?

Tem duas opções para obter o valor do carimbo de data/hora da atividade:

- A coluna **Atividade**, na [página de dispositivos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) no portal do Azure.

    ![Carimbo de data/hora da atividade](./media/manage-stale-devices/01.png)

- O cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Carimbo de data/hora da atividade](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planear a limpeza dos dispositivos obsoletos

Para limpar eficazmente os dispositivos obsoletos no seu ambiente, deve definir uma política relacionada. Essa política ajuda-o a ter a certeza de que captura todas as considerações associadas aos dispositivos obsoletos. As seguintes secções mostram-lhe exemplos de considerações comuns para a política. 

### <a name="cleanup-account"></a>Conta de limpeza

Para atualizar um dispositivo no Azure AD, precisa de uma conta com uma das seguintes funções atribuída:

- Administrador Global
- Cloud Device Administrator
- Administrador de Serviços do Intune

Na sua política de limpeza, selecione as contas que têm as funções necessárias atribuídas. 

### <a name="timeframe"></a>Período de tempo

Defina um período de tempo que funcione como indicador de dispositivo obsoleto. When defining your timeframe, factor the window noted for updating the activity timestamp into your value. For example, you shouldn't consider a timestamp that is younger than 21 days (includes variance) as an indicator for a stale device. Há casos que podem fazer com que um dispositivo pareça obsoleto, embora não o esteja. Por exemplo, o proprietário do dispositivo afetado pode estar de férias ou de baixa médica  por um período que excede o que definido para os dispositivos obsoletos.

### <a name="disable-devices"></a>Desativar dispositivos

Não é aconselhável eliminar imediatamente um dispositivo que pareça estar obsoleto, porque não é possível anular a eliminação em caso de falsos positivos. Como melhor prática, desative os dispositivos durante um período de tolerância antes de os eliminar. Na sua política, defina um período de tempo durante o qual um dispositivo estará desativado, antes de o eliminar.

### <a name="mdm-controlled-devices"></a>Dispositivos controlados por MDM

Se o seu dispositivo estiver a ser controlado pelo Intune ou por outra solução de MDM, descontinue-o no sistema de gestão antes de o desativar ou eliminar.

### <a name="system-managed-devices"></a>Dispositivos geridos pelo sistema

Não elimine dispositivos geridos pelo sistema. Geralmente, tratam-se de dispositivo como piloto automático. Once deleted, these devices can't be reprovisioned. Por predefinição, o cmdlet `get-msoldevice` novo exclui os dispositivos geridos pelo sistema. 

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Os seus dispositivos associados ao Azure AD híbrido devem seguir as suas políticas de gestão de dispositivos obsoletos no local. 

Para limpar o Azure AD:

- **Dispositivos Windows 10** - desative ou elimine os dispositivos Windows 10 no seu AD no local e permita que o Azure AD Connect sincronize o estado do dispositivo alterado com o Azure AD.
- **Windows 7/8** - Disable or delete Windows 7/8 devices in your on-premises AD first. Não pode utilizar o Azure AD Connect para desativar ou eliminar dispositivos Windows 7/8 no Azure AD. Instead, when you make the change in your on-premises, you must disable/delete in Azure AD.

> [!NOTE]
>* Deleting devices in your on-premises AD or Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access). Read additional information on how to [remove registration on the client](faq.md#hybrid-azure-ad-join-faq).
>* Deleting a Windows 10 device only in Azure AD will re-synchronize the device from your on-premises using Azure AD connect but as a new object in "Pending" state. A re-registration is required on the device.
>* Removing the device from sync scope for Windows 10/Server 2016 devices will delete the Azure AD device. Adding it back to sync scope will place a new object in "Pending" state. A re-registration of the device is required.
>* If you not using Azure AD Connect for Windows 10 devices to synchronize (e.g. ONLY using AD FS for registration), you must manage lifecycle similar to Windows 7/8 devices.


### <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

Desative ou elimine os dispositivos associados ao Azure AD no Azure AD.

> [!NOTE]
>* Deleting an Azure AD device does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g Conditional Access). 
>* Read more on [how to unjoin on Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD

Desative ou elimine os dispositivos registados no Azure AD no Azure AD.

> [!NOTE]
>* Deleting an Azure AD registered device in Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access).
>* Read more on [how to remove a registration on the client](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Limpar dispositivos obsoletos no portal do Azure  

Embora possa limpar dispositivos obsoletos no portal do Azure, é mais eficiente lidar com esse processo com um script do PowerShell. Utilize o módulo do PowerShell V1 mais recente para utilizar o filtro de carimbo de data/hora e para filtrar os dispositivos geridos pelo sistema, como dispositivos de piloto automático. Neste momento, não se recomendado a utilização do PowerShell V2.

Uma rotina típica consiste nos seguintes passos:

1. Ligar ao Azure Active Directory com o cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)
1. Obter a lista de dispositivos
1. Desativar o dispositivo com o cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0). 
1. Aguarde até ao fim do período de tolerância com o número de dias que escolher antes de eliminar o dispositivo.
1. Remover o dispositivo com o cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0).

### <a name="get-the-list-of-devices"></a>Obter a lista de dispositivos

Para obter todos os dispositivos e armazenar os dados devolvidos num ficheiro CSV:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

If you have a large number of devices in your directory, use the timestamp filter to narrow down the number of returned devices. Para obter todos os dispositivos com um carimbo de data/hora posterior a uma data específica e armazenar os dados devolvidos num ficheiro CSV: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>O que deve saber

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Por que é que o carimbo de data/hora não é atualizado mais frequentemente?

O carimbo de data/hora é atualizado para suportar cenários de ciclo de vida dos dispositivos. Não é uma auditoria. Para atualizações mais frequentes no dispositivo, utilize os registos de auditoria de início de sessão.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Por que me devo preocupar com as chaves BitLocker?

Quando configuradas, as chaves BitLocker para os dispositivos Windows 10 são armazenadas nos objetos dos dispositivos no Azure AD. Se eliminar um dispositivo obsoleto, as chaves BitLocker armazenadas no mesmo também são eliminadas. Antes de eliminar dispositivos obsoletos, deve determinar se a sua política de limpeza está alinhada com o ciclo de vida efetivo dos dispositivos. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Why should I worry about Windows Autopilot devices?

When a Azure AD device was associated with a Windows Autopilot object the following three scenarios can occur if the device will be repurposed in future:
- With Windows Autopilot user-driven deployments without using white glove, a new Azure AD device will be created, but it won’t be tagged with the ZTDID.
- With Windows Autopilot self-deploying mode deployments, they will fail because an associate Azure AD device cannot be found.  (This is a security mechanism to make sure that no “imposter” devices try to join Azure AD with no credentials.) The failure will indicate a ZTDID mismatch.
- With Windows Autopilot white glove deployments, they will fail because an associated Azure AD device cannot be found. (Behind the scenes, white glove deployments use the same self-deploying mode process, so they enforce the same security mechanisms.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Como posso saber todos os tios de dispositivos associados?

Para saber mais sobre os diversos tipos, veja a [descrição geral da gestão de dispositivos](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>O que acontece quando desativo um dispositivo?

Qualquer autenticação em que esteja a ser utilizado um dispositivo para autenticação no Azure é recusada. Alguns exemplos comuns são:

- **Dispositivo associado ao Azure AD híbrido** - os utilizadores poderão utilizar o dispositivo para iniciarem sessão no respetivo domínio no local. Contudo, não podem aceder aos recursos do Azure AD, como o Office 365.
- **Dispositivo associado ao Azure AD** - os utilizadores não podem utilizar o dispositivo para iniciarem sessão. 
- **Dispositivos móveis** - o utilizador não pode aceder aos recursos do Azure AD, como o Office 365. 

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)
