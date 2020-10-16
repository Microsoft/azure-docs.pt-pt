---
title: Configurar as definições da Atualização do Windows para a gestão de atualização de automação Azure
description: Este artigo diz como configurar as definições do Windows Update para funcionar com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: aa4e49d0e79704b6fc9f19eb1b736b2358cf3a14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450740"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Configurar as definições da Atualização do Windows para a gestão de atualização de automação Azure

A Azure Automation Update Management conta com o [cliente Windows Update](/windows/deployment/update/windows-update-overview) para descarregar e instalar atualizações do Windows. Existem definições específicas que são utilizadas pelo cliente do Windows Update ao ligar-se aos Serviços de Atualização do Windows Server (WSUS) ou ao Windows Update. Muitas destas configurações podem ser geridas com:

- Editor de Políticas de Grupo Local
- Política de Grupo
- PowerShell
- Editar diretamente o Registo

A Atualização Management respeita muitas das definições especificadas para controlar o cliente Do Windows Update. Se utilizar definições para ativar atualizações não-Windows, a Gestão de Atualizações também gerirá essas atualizações. Se pretender ativar o download de atualizações antes de ocorrer uma implementação de atualização, a implementação da atualização pode ser mais rápida, mais eficiente e menos provável de ultrapassar a janela de manutenção.

Para recomendações adicionais sobre a configuração do WSUS na sua subscrição Azure e mantenha as suas máquinas virtuais Windows atualizadas, reveja [o Plano de implementação para atualizar máquinas virtuais do Windows em Azure utilizando o WSUS](/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o descarregamento automático de atualizações sem as instalar automaticamente, pode utilizar a Política de Grupo para [configurar a definição de Atualizações Automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) para 3. Esta definição permite o descarregamento das atualizações necessárias em segundo plano e notifica-o de que as atualizações estão prontas a ser instaladas. Desta forma, a Update Management continua a controlar os horários, mas permite o download de atualizações fora da janela de manutenção da Gestão de Atualização. Este comportamento previne `Maintenance window exceeded` erros na Gestão de Atualização.

Pode ativar esta definição no PowerShell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurar configurações de reinicialização

As teclas de registo listadas nas [atualizações automáticas configurando, editando as teclas de registo](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e registo utilizadas para gerir o [reinício,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que as suas máquinas reiniciem, mesmo que especifique **Nunca Reiniciar** nas definições de **Atualização.** Configure estas chaves de registo para melhor se adequar ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Ativar atualizações para outros produtos da Microsoft

Por predefinição, o cliente do Windows Update está configurado para fornecer atualizações apenas para o Windows. Se ativar as **atualizações DoAr-me para outros produtos da Microsoft quando atualizar a** definição do Windows, também recebe atualizações para outros produtos, incluindo patches de segurança para o Microsoft SQL Server e outros softwares da Microsoft. Pode configurar esta opção se tiver descarregado e copiado os mais recentes [ficheiros de modelos Administrativos](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) disponíveis para o Windows 2016 e mais tarde.

Se tiver máquinas a executar o Windows Server 2012 R2, não pode configurar esta definição através da Política de Grupo. Executar o seguinte comando PowerShell nestas máquinas:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Faça configurações de configuração WSUS

A Atualização Gestão suporta as definições da WSUS. Pode especificar as fontes para digitalizar e descarregar atualizações usando instruções no [ponto de localização do serviço intranet Microsoft Update](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por predefinição, o cliente do Windows Update está configurado para descarregar atualizações a partir do Windows Update. Quando especificar um servidor WSUS como fonte para as suas máquinas, se as atualizações não forem aprovadas no WSUS, a implementação da atualização falha. 

Para restringir as máquinas ao serviço de atualização interna, decida [Não ligar a nenhuma localização da Internet do Windows Update](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Passos seguintes

Agende uma atualização seguindo as instruções em [Gerir atualizações e patches para os seus VMs](update-mgmt-manage-updates-for-vm.md).
