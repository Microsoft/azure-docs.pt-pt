---
title: Definir configurações de Windows Update para trabalhar com o Azure Gerenciamento de Atualizações
description: Este artigo descreve as configurações de Windows Update que você configura para trabalhar com Gerenciamento de Atualizações
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377567"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Definir configurações de Windows Update para Gerenciamento de Atualizações

Gerenciamento de Atualizações se baseia em Windows Update para baixar e instalar as atualizações do Windows. Como resultado, respeitamos muitas das configurações usadas pelo Windows Update. Se você usar configurações para habilitar atualizações não Windows, Gerenciamento de Atualizações gerenciará essas atualizações também. Se você deseja habilitar o download de atualizações antes que ocorra uma implantação de atualização, as implantações de atualização podem ser mais rápidas e ter menos probabilidade de exceder a janela de manutenção.

## <a name="pre-download-updates"></a>Pré-baixar atualizações

Para configurar o download automático de atualizações no Política de Grupo, você pode definir a [configuração definir atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como **3**. Isso baixa as atualizações necessárias em segundo plano, mas não as instala. Isso mantém Gerenciamento de Atualizações no controle de agendas, mas permite que as atualizações sejam baixadas fora da janela de manutenção do Gerenciamento de Atualizações. Isso pode impedir que a **janela de manutenção exceda** erros no gerenciamento de atualizações.

Você também pode definir isso com o PowerShell, executar o PowerShell a seguir em um sistema para o qual você deseja baixar atualizações automaticamente.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Desabilitar instalação automática

As VMs do Azure têm a instalação automática de atualizações habilitadas por padrão. Isso pode fazer com que as atualizações sejam instaladas antes de você agendá-las para serem instaladas pelo Gerenciamento de Atualizações. Você pode desabilitar esse comportamento definindo a chave do registro `NoAutoUpdate` como `1`. O trecho do PowerShell a seguir mostra uma maneira de fazer isso.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Definir configurações de reinicialização

As chaves do registro listadas em [configurar atualizações automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) e [as chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que os computadores sejam reinicializados, mesmo que você tenha especificado **nunca reinicializar** nas configurações de implantação de atualização . Você deve configurar essas chaves do registro conforme desejado para o seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, Windows Update apenas fornece atualizações para o Windows. Se você habilitar **fornecer atualizações para outros produtos da Microsoft ao atualizar o Windows**, você receberá atualizações para outros produtos, incluindo patches de segurança para SQL Server ou outros softwares de terceiros. Esta opção não pode ser configurada por Política de Grupo. Execute o PowerShell a seguir nos sistemas nos quais você deseja habilitar outros patches de terceiros e Gerenciamento de Atualizações honrará essa configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Definições de configuração do WSUS

**Gerenciamento de atualizações** respeita as definições de configuração do WSUS. A lista de configurações do WSUS que você pode configurar para trabalhar com Gerenciamento de Atualizações está listada abaixo.

### <a name="intranet-microsoft-update-service-location"></a>Local do serviço de atualização da intranet da Microsoft

Você pode especificar as fontes para verificação e download de atualizações no [local do serviço de Microsoft Update da intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Próximos Passos

Depois de definir as configurações de Windows Update, você pode agendar uma implantação de atualização seguindo as instruções em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md)