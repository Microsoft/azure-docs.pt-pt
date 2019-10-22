---
title: Definir configurações de Windows Update para trabalhar com o Azure Gerenciamento de Atualizações
description: Este artigo descreve as configurações de Windows Update que você configura para trabalhar com o Gerenciamento de Atualizações do Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690847"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Definir configurações de Windows Update para Gerenciamento de Atualizações

O Azure Gerenciamento de Atualizações conta com Windows Update para baixar e instalar atualizações do Windows. Como resultado, Gerenciamento de Atualizações respeita muitas das configurações usadas pelo Windows Update. Se você usar configurações para habilitar atualizações não Windows, Gerenciamento de Atualizações também gerenciará essas atualizações. Se você quiser habilitar o download de atualizações antes que ocorra uma implantação de atualização, a implantação da atualização poderá ser mais rápida, mais eficiente e menos provável de exceder a janela de manutenção.

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o download automático de atualizações no Política de Grupo, defina a [configuração definir atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como **3**. Essa configuração permite downloads das atualizações necessárias em segundo plano, mas não as instala. Dessa forma, Gerenciamento de Atualizações permanece no controle de agendas, mas as atualizações podem ser baixadas fora da janela de manutenção Gerenciamento de Atualizações. Esse comportamento impede erros de "janela de manutenção excedido" no Gerenciamento de Atualizações.

Você também pode ativar essa configuração executando o seguinte comando do PowerShell em um sistema que você deseja configurar para o download automático de atualizações:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Desabilitar instalação automática

Por padrão, em VMs (máquinas virtuais) do Azure, a instalação automática de atualizações está habilitada. Isso pode fazer com que as atualizações sejam instaladas antes de você agendá-las para instalação pelo Gerenciamento de Atualizações. Você pode desabilitar esse comportamento definindo a chave do registro `NoAutoUpdate` como `1`. O trecho do PowerShell a seguir mostra como fazer isso:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Definir configurações de reinicialização

As chaves do registro listadas na [configuração de atualizações automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [as chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que os computadores sejam reinicializados, mesmo se você especificar **nunca reinicializar** nas configurações de **implantação de atualização** . Você deve configurar essas chaves do registro para se adequar melhor ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, Windows Update fornece atualizações somente para o Windows. Se você habilitar a configuração **fornecer atualizações para outros produtos da Microsoft ao atualizar o Windows** , também receberá atualizações para outros produtos, incluindo patches de segurança para Microsoft SQL Server e outros softwares da Microsoft. Esta opção não pode ser configurada por Política de Grupo. Execute o seguinte comando do PowerShell nos sistemas nos quais você deseja habilitar outras atualizações da Microsoft. Gerenciamento de Atualizações será compatível com essa configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Definições de configuração do WSUS

Gerenciamento de Atualizações está em conformidade com as configurações do Windows Server Update Services (WSUS). As configurações do WSUS que você pode configurar para trabalhar com Gerenciamento de Atualizações estão listadas abaixo.

### <a name="intranet-microsoft-update-service-location"></a>Local do serviço de atualização da intranet da Microsoft

Você pode especificar as fontes para verificação e download de atualizações em [especificar local do serviço de Microsoft Update de intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Passos seguintes

Depois de definir as configurações de Windows Update, você pode agendar uma implantação de atualização seguindo as instruções em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).