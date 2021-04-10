---
title: Como gerir dispositivos em Azure AD | Microsoft Docs
description: Saiba como remover dispositivos antigos da sua base de dados de dispositivos registados no Azure Ative Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6774e172c4e8607bfadd95f947d650afa62a984f
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550427"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Como: Gerir dispositivos em Azure AD

Idealmente, para concluir o ciclo de vida, deverá anular o registo dos dispositivos registados quando estes já não são necessários. No entanto, devido a vários motivos, como dispositivos perdidos, roubados ou danificados ou a reinstalações do SO, é comum haver dispositivos obsoletos no seu ambiente. Enquanto administrador de TI, é provável que precise de um método para remover os dispositivos obsoletos, para que os seus recursos se possam dedicar à gestão dos dispositivos que têm, efetivamente, de ser geridos.

Neste artigo, vai aprender a gerir de forma eficiente os dispositivos obsoletos no sue ambiente.
  

## <a name="what-is-a-stale-device"></a>O que são dispositivos obsoletos?

Os dispositivos obsoletos são dispositivos registados no Azure AD, mas que já não são utilizados há um determinado período de tempo para aceder a aplicações na cloud. Estes dispositivos têm impacto na capacidade de gerir e dar suporte técnico aos seus dispositivos e utilizadores no inquilino porque: 

- Dispositivos duplicados podem fazer com que seja mais difícil à sua equipa de suporte técnico identificar que dispositivo está atualmente ativo.
- Um número crescente de dispositivos cria writebacks de dispositivos desnecessários aumentando o tempo para sincronizações de ligação AD Azure.
- Por questões de organização geral e para estar em conformidade, poderá querer ter dispositivos com um estado limpo. 

Os dispositivos obsoletos no Azure AD podem interferir com as políticas gerais de ciclo de vida dos dispositivos da sua organização.

## <a name="detect-stale-devices"></a>Detetar dispositivos obsoletos

Uma vez que os dispositivos obsoletos são definidos como dispositivos registados que já não são utilizados há um determinado período de tempo para aceder a aplicações na cloud, a deteção dos mesmos requer uma propriedade relacionada com carimbo de data/hora. No Azure AD, essa propriedade é denominada **ApproximateLastLogonTimestamp**, ou **activity timestamp**. Se o delta entre o valor de agora e o valor de **carimbo de data/hora da atividade** exceder o período de tempo que definiu para os dispositivos ativos, considera-se que o dispositivo está obsoleto. Este **carimbo de data/hora da atividade** está agora em pré-visualização pública.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Como é gerido o valor do carimbo de data/hora da atividade?  

A avaliação do carimbo de data/hora da atividade é acionado por uma tentativa de autenticação de um dispositivo. O Azure AD avalia o carimbo de data/hora da atividade quando:

- Foi desencadeada uma política de Acesso Condicional que requer [dispositivos geridos](../conditional-access/require-managed-devices.md) ou [aplicações de clientes aprovadas.](../conditional-access/app-based-conditional-access.md)
- Os dispositivos Windows 10 associados ao Azure AD ou associados ao Azure AD híbrido estiverem ativos na rede. 
- Os dispositivos geridos do Intune tenham dado entrada no serviço.

Se o delta entre o valor existente do calendário de atividade e o valor atual for superior a 14 dias (variação de +/-5 dias), o valor existente é substituído pelo novo valor.

## <a name="how-do-i-get-the-activity-timestamp"></a>Como posso obter o carimbo de data/hora da atividade?

Tem duas opções para obter o valor do carimbo de data/hora da atividade:

- A coluna **Atividade**, na [página de dispositivos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) no portal do Azure.

    :::image type="content" source="./media/manage-stale-devices/01.png" alt-text="Screenshot de uma página no portal Azure que lista o nome, proprietário e outras informações nos dispositivos. Uma coluna lista o carimbo do tempo da atividade." border="false":::

- [O cmdlet Get-AzureADDevice](/powershell/module/azuread/Get-AzureADDevice)

    :::image type="content" source="./media/manage-stale-devices/02.png" alt-text="Screenshot mostrando saída da linha de comando. Uma linha é realçada e lista um carimbo de tempo para o valor aproximadaLastLogonTimeStamp." border="false":::

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planear a limpeza dos dispositivos obsoletos

Para limpar eficazmente os dispositivos obsoletos no seu ambiente, deve definir uma política relacionada. Essa política ajuda-o a ter a certeza de que captura todas as considerações associadas aos dispositivos obsoletos. As seguintes secções mostram-lhe exemplos de considerações comuns para a política. 

### <a name="cleanup-account"></a>Conta de limpeza

Para atualizar um dispositivo no Azure AD, precisa de uma conta com uma das seguintes funções atribuída:

- Administrador Global
- Administrador de dispositivos de nuvem
- Administrador de Serviços do Intune

Na sua política de limpeza, selecione as contas que têm as funções necessárias atribuídas. 

### <a name="timeframe"></a>Período de tempo

Defina um período de tempo que funcione como indicador de dispositivo obsoleto. Ao definir o seu prazo, fator a janela anotado para atualizar o calendário de atividades para o seu valor. Por exemplo, não deve considerar um relógio de tempo com menos de 21 dias (inclui variação) como um indicador para um dispositivo velho. Há casos que podem fazer com que um dispositivo pareça obsoleto, embora não o esteja. Por exemplo, o proprietário do dispositivo afetado pode estar de férias ou de baixa médica  por um período que excede o que definido para os dispositivos obsoletos.

### <a name="disable-devices"></a>Desativar dispositivos

Não é aconselhável eliminar imediatamente um dispositivo que pareça estar obsoleto, porque não é possível anular a eliminação em caso de falsos positivos. Como melhor prática, desative os dispositivos durante um período de tolerância antes de os eliminar. Na sua política, defina um período de tempo durante o qual um dispositivo estará desativado, antes de o eliminar.

### <a name="mdm-controlled-devices"></a>Dispositivos controlados por MDM

Se o seu dispositivo estiver a ser controlado pelo Intune ou por outra solução de MDM, descontinue-o no sistema de gestão antes de o desativar ou eliminar.

### <a name="system-managed-devices"></a>Dispositivos geridos pelo sistema

Não elimine dispositivos geridos pelo sistema. Estes são geralmente dispositivos como o Autopilot. Uma vez eliminados, estes dispositivos não podem ser reprovisionados. Por predefinição, o cmdlet `Get-AzureADDevice` novo exclui os dispositivos geridos pelo sistema. 

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Os seus dispositivos associados ao Azure AD híbrido devem seguir as suas políticas de gestão de dispositivos obsoletos no local. 

Para limpar o Azure AD:

- **Dispositivos Windows 10** - desative ou elimine os dispositivos Windows 10 no seu AD no local e permita que o Azure AD Connect sincronize o estado do dispositivo alterado com o Azure AD.
- **Windows 7/8** - Desative ou elimine os dispositivos Windows 7/8 no seu AD no local primeiro. Não pode utilizar o Azure AD Connect para desativar ou eliminar dispositivos Windows 7/8 no Azure AD. Em vez disso, quando escoar a alteração no seu local, deve desativar/apagar em Azure AD.

> [!NOTE]
>* A eliminação de dispositivos nos seus dispositivos no local AD ou Azure AD não remove o registo no cliente. Só impedirá o acesso a recursos utilizando o dispositivo como identidade (por exemplo, acesso condicional). Leia informações adicionais sobre como [remover o registo no cliente.](faq.yml)
>* A eliminação de um dispositivo Windows 10 apenas no Azure AD irá re-sincronizar o dispositivo a partir das suas instalações utilizando a ligação AD AZure, mas como um novo objeto em estado "Pendente". É necessária uma nova inscrição no aparelho.
>* A remoção do dispositivo do âmbito de sincronização dos dispositivos Windows 10/Server 2016 eliminará o dispositivo AD Azure. A adicioná-lo de volta ao âmbito de sincronização colocará um novo objeto no estado "Pendente". É necessário um re-registo do dispositivo.
>* Se não utilizar o Azure AD Connect para dispositivos windows 10 para sincronizar (por exemplo, utilizar apenas OD FS para registo), tem de gerir o ciclo de vida semelhante aos dispositivos Windows 7/8.


### <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

Desative ou elimine os dispositivos associados ao Azure AD no Azure AD.

> [!NOTE]
>* A eliminação de um dispositivo AZure AD não remove o registo no cliente. Só impedirá o acesso a recursos utilizando o dispositivo como identidade (por exemplo, acesso condicional). 
>* Leia mais sobre [como descomprinar-se no AD Azure](faq.yml) 

### <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD

Desative ou elimine os dispositivos registados no Azure AD no Azure AD.

> [!NOTE]
>* A eliminação de um dispositivo registado Azure AD em Azure AD não remove o registo no cliente. Só impedirá o acesso a recursos utilizando o dispositivo como identidade (por exemplo, acesso condicional).
>* Leia mais sobre [como remover uma inscrição no cliente](faq.yml)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Limpar dispositivos obsoletos no portal do Azure  

Embora possa limpar dispositivos obsoletos no portal do Azure, é mais eficiente lidar com esse processo com um script do PowerShell. Utilize o mais recente módulo PowerShell V1 para utilizar o filtro de hora e filtrar dispositivos geridos pelo sistema, como o Autopilot. Neste momento, não se recomendado a utilização do PowerShell V2.

Uma rotina típica consiste nos seguintes passos:

1. Ligue-se ao Azure Ative Directory utilizando o [cmdlet Connect-AzureAD](/powershell/module/azuread/connect-azuread)
1. Obter a lista de dispositivos
1. Desative o dispositivo utilizando o cmdlet [Set-AzureADDevice](/powershell/module/azuread/Set-AzureADDevice) (desative-o utilizando a opção -AccountEnabled). 
1. Aguarde até ao fim do período de tolerância com o número de dias que escolher antes de eliminar o dispositivo.
1. Retire o dispositivo utilizando o [cmdlet Remove-AzureADDevice.](/powershell/module/azuread/Remove-AzureADDevice)

### <a name="get-the-list-of-devices"></a>Obter a lista de dispositivos

Para obter todos os dispositivos e armazenar os dados devolvidos num ficheiro CSV:

```PowerShell
Get-AzureADDevice -All:$true | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Se tiver um grande número de dispositivos no seu diretório, utilize o filtro de hora para reduzir o número de dispositivos devolvidos. Para obter todos os dispositivos com um carimbo de data/hora posterior a uma data específica e armazenar os dados devolvidos num ficheiro CSV: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-AzureADDevice -All:$true | Where {$_.ApproximateLastLogonTimeStamp -le $dt} | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>O que deve saber

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Por que é que o carimbo de data/hora não é atualizado mais frequentemente?

O carimbo de data/hora é atualizado para suportar cenários de ciclo de vida dos dispositivos. Não é uma auditoria. Para atualizações mais frequentes no dispositivo, utilize os registos de auditoria de início de sessão.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Por que me devo preocupar com as chaves BitLocker?

Quando configuradas, as chaves BitLocker para os dispositivos Windows 10 são armazenadas nos objetos dos dispositivos no Azure AD. Se eliminar um dispositivo obsoleto, as chaves BitLocker armazenadas no mesmo também são eliminadas. Antes de eliminar dispositivos obsoletos, deve determinar se a sua política de limpeza está alinhada com o ciclo de vida efetivo dos dispositivos. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Por que deveria preocupar-me com dispositivos Windows Autopilot?

Quando eliminar um dispositivo AD Azure que estava associado a um objeto Do Piloto Automático do Windows, podem ocorrer três cenários se o dispositivo for reutilizado no futuro:
- Com as implementações orientadas pelo utilizador do Windows Autopilot sem utilizar o pré-fornecimento, será criado um novo dispositivo AD Azure, mas não será marcado com o ZTDID.
- Com as implementações do modo de implantação autónoma do Windows Autopilot, falharão porque não é possível encontrar um dispositivo AD Azure associado.  (Este é um mecanismo de segurança para garantir que nenhum dispositivo "impostor" tente juntar-se à Azure AD sem credenciais.) A falha indicará uma incompatibilidade do ZTDID.
- Com as implementações de pré-provisão do Windows Autopilot, falharão porque não é possível encontrar um dispositivo AZure AD associado. (Nos bastidores, as implementações de pré-provisão utilizam o mesmo processo de modo de implantação autónoma, pelo que impõem os mesmos mecanismos de segurança.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Como posso saber todos os tios de dispositivos associados?

Para saber mais sobre os diversos tipos, veja a [descrição geral da gestão de dispositivos](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>O que acontece quando desativo um dispositivo?

Qualquer autenticação em que esteja a ser utilizado um dispositivo para autenticação no Azure é recusada. Alguns exemplos comuns são:

- **Dispositivo híbrido Azure AD -** Os utilizadores podem ser capazes de usar o dispositivo para iniciar sedução no seu domínio no local. No entanto, não podem aceder a recursos Azure AD como o Microsoft 365.
- **Dispositivo associado ao Azure AD** - os utilizadores não podem utilizar o dispositivo para iniciarem sessão. 
- **Dispositivos móveis** - O utilizador não pode aceder a recursos Azure AD como o Microsoft 365. 

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)
