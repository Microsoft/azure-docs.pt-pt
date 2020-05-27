---
title: Configure as definições de atualização do Windows para a Gestão de Atualização de Automação Do Azure
description: Este artigo diz como configurar as definições de Atualização do Windows para funcionar com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: ea05e75c0d1db1ef27ae2e8e9364327528a7c8ed
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837166"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Configure as definições de atualização do Windows para a Gestão de Atualização de Automação Do Azure

A Azure Automation Update Management conta com o [cliente da Atualização do Windows](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) para descarregar e instalar atualizações do Windows. Existem definições específicas que são utilizadas pelo cliente da Atualização do Windows ao ligar-se aos Serviços de Atualização do Servidor do Windows (WSUS) ou ao Windows Update. Muitas destas configurações podem ser geridas com:

- Editor de Políticas de Grupo Local
- Política de Grupo
- PowerShell
- Edição direta do Registo

A Atualização Management respeita muitas das definições especificadas para controlar o cliente da Atualização do Windows. Se utilizar as definições para ativar atualizações não Windows, a Atualização também irá gerir essas atualizações. Se pretender permitir o download de atualizações antes de ocorrer uma implementação de atualização, a implementação da atualização pode ser mais rápida, eficiente e menos provável de exceder a janela de manutenção.

Para recomendações adicionais sobre a configuração do WSUS na sua subscrição Azure e manter de forma segura as suas máquinas virtuais windows atualizadas, reveja [o Plano de Utilização da sua implementação para atualizar as máquinas virtuais do Windows em Azure utilizando o WSUS](https://docs.microsoft.com/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o descarregamento automático das atualizações sem as instalar automaticamente, pode utilizar a Política do Grupo para [configurar a definição de Atualizações Automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) para 3. Esta definição permite downloads das atualizações necessárias em segundo plano e notifica-o que as atualizações estão prontas a instalar. Desta forma, a Atualização de Gestão mantém-se no controlo dos horários, mas permite o download de atualizações fora da janela de manutenção da Gestão de Atualizações. Este comportamento previne erros na Gestão de `Maintenance window exceeded` Atualização.

Pode ativar esta definição no PowerShell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurar as definições de reiniciar

As teclas de registo listadas na Configuração de [Atualizações Automáticas, editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) teclas de registo e [registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício podem fazer com que as suas máquinas **reiniciem,** mesmo que especifique never reboot nas definições de Implementação de **Atualização.** Configure estas chaves de registo para melhor se adequar ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Ativar atualizações para outros produtos da Microsoft

Por padrão, o cliente do Windows Update está configurado para fornecer atualizações apenas para windows. Se ativar as **atualizações Give me para outros produtos** da Microsoft quando atualizo a definição do Windows, também recebe atualizações para outros produtos, incluindo patches de segurança para o Microsoft SQL Server e outros softwares da Microsoft. Pode configurar esta opção se tiver descarregado e copiado os mais recentes [ficheiros de modelo suportáveis](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) disponíveis para o Windows 2016 e posteriormente.

Se tiver máquinas a executar o Windows Server 2012 R2, não pode configurar esta definição através da Política de Grupo. Executar o seguinte comando PowerShell nestas máquinas:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Faça definições de configuração wSUS

A Atualização Management suporta as definições do WSUS. Pode especificar fontes para digitalizar e descarregar atualizações utilizando instruções em especificar a localização do [serviço De saúde Intranet Microsoft Update](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por defeito, o cliente do Windows Update está configurado para descarregar atualizações a partir do Windows Update. Quando especifica um servidor WSUS como fonte para as suas máquinas, se as atualizações não forem aprovadas no WSUS, a atualização falha. 

Para restringir as máquinas ao serviço de atualização interna, não [se conecto a nenhuma localização](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)da Internet do Windows Update . 

## <a name="next-steps"></a>Passos seguintes

Agende uma implementação de atualização seguindo as instruções em [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
