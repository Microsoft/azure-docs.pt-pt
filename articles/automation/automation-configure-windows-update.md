---
title: Configure as definições de atualização do Windows para funcionar com a Gestão de Atualizações do Azure
description: Este artigo descreve as definições de Atualização do Windows que configura para trabalhar com a Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279029"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configure as definições de atualização do Windows para gestão de atualizações

A Azure Update Management conta com o [cliente do Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) para descarregar e instalar atualizações do Windows. Existem definições específicas que são utilizadas pelo cliente da Atualização do Windows ao ligar-se aos Serviços de Atualização do Servidor do Windows (WSUS) ou ao Windows Update. Muitas destas configurações podem ser geridas com:

- Editor de Políticas de Grupo Local
- Política de Grupo
- PowerShell
- Edição direta do Registo

A Atualização Management respeita muitas das definições especificadas para controlar o cliente da Atualização do Windows. Se utilizar as definições para ativar atualizações não Windows, a Atualização também irá gerir essas atualizações. Se pretender permitir o download de atualizações antes de ocorrer uma implementação de atualização, a implementação da atualização pode ser mais rápida, eficiente e menos provável de exceder a janela de manutenção.

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o descarregamento automático das atualizações, mas não as instale automaticamente, pode utilizar a Política do Grupo para definir a definição de [Atualizações Automáticas configurar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) para **3**. Esta definição permite downloads das atualizações necessárias em segundo plano e notifica-o que as atualizações estão prontas a instalar. Desta forma, a Atualização de Gestão mantém-se no controlo dos horários, mas as atualizações podem ser descarregadas fora da janela de manutenção da Atualização. Este comportamento impede que a **janela de manutenção exceda** os erros na Gestão de Atualização.

Pode ativar esta definição utilizando o PowerShell, executando o seguinte comando:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurar as definições de reiniciar

As teclas de registo listadas na Configuração de [Atualizações Automáticas, editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) teclas de registo e [registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício podem fazer com que as suas máquinas **reiniciem,** mesmo que especifique never reboot nas definições de Implementação de **Atualização.** Configure estas chaves de registo para melhor se adequar ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Ativar atualizações para outros produtos da Microsoft

Por defeito, o cliente do Windows Update está configurado para fornecer atualizações apenas para windows. Se ativar as **atualizações Give me para outros produtos** da Microsoft quando atualizo a definição do Windows, também recebe atualizações para outros produtos, incluindo patches de segurança para o Microsoft SQL Server e outros softwares da Microsoft. Esta opção pode ser configurada se tiver descarregado e copiado os mais recentes [ficheiros de modelo administrativo](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) disponíveis para o Windows 2016 e superiores.

Se estiver a executar o Windows Server 2012 R2, esta definição não pode ser configurada pela Política de Grupo. Execute o seguinte comando PowerShell nessas máquinas. A Atualização de Gestão está em conformidade com esta definição.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Definições de configuração wSUS

A Atualização Management suporta as definições do WSUS. As definições wSUS que pode configurar para trabalhar com a Atualização estão listadas abaixo.

### <a name="intranet-microsoft-update-service-location"></a>Localização do serviço de atualização intranet Microsoft

Pode especificar fontes para digitalizar e descarregar atualizações em especificação da localização do [serviço De microsoft update intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por defeito, o cliente do Windows Update está configurado para descarregar atualizações a partir do Windows Update. Quando especifica um servidor WSUS como fonte para as suas máquinas, se as atualizações não forem aprovadas no WSUS, a atualização falha. 

Para restringir as máquinas a penas a esse serviço de atualização interna, configure [Não se conectem a quaisquer localizações](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)da Internet do Windows Update . 

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições do Windows Update, pode agendar uma implementação de atualização seguindo as instruções em ['Gerir actualizações' e patches para os seus VMs Azure](automation-tutorial-update-management.md).
