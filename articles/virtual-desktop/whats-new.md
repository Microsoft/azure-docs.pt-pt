---
title: Quais as novidades no Windows Virtual Desktop? - Azure
description: Novas funcionalidades e atualizações de produtos para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003526"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Quais as novidades no Windows Virtual Desktop?

Atualizações de Ambiente de Trabalho Virtual do Windows regularmente. Este artigo é onde você vai descobrir sobre:

- As últimas atualizações
- Novas funcionalidades
- Melhorias às características existentes
- Correções de erros

Este artigo é atualizado mensalmente. Certifique-se de voltar aqui muitas vezes para acompanhar novas atualizações.

## <a name="july-2020"></a>Julho de 2020  

Julho foi quando o Windows Virtual Desktop com a integração de Gestão de Recursos Azure tornou-se geralmente disponível.

Eis o que mudou com este novo lançamento: 

- O "lançamento do outono de 2019" é agora conhecido como "Windows Virtual Desktop (Classic)", enquanto o "lançamento da primavera de 2020" é agora apenas "Windows Virtual Desktop". Para mais informações, confira [este post de blog.](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/) 

Para saber mais sobre novas funcionalidades, confira [esta publicação de blog.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) 

### <a name="autoscaling-tool-update"></a>Atualização da ferramenta de autoescalagem

A versão mais recente da ferramenta de autoscalagem que estava em pré-visualização está agora geralmente disponível. Esta ferramenta utiliza uma conta de automação Azure e a App Azure Logic para desligar e reiniciar automaticamente as máquinas virtuais (VMs) de sessão dentro de uma piscina de anfitrião, reduzindo os custos de infraestrutura. Saiba mais em [anfitriões de sessão scale usando Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Portal do Azure

Agora pode fazer as seguintes coisas com o portal Azure no Windows Virtual Desktop: 

- Atribua diretamente os utilizadores a anfitriões pessoais de sessão de desktop  
- Alterar a definição de ambiente de validação para piscinas hospedeiras 

### <a name="diagnostics"></a>Diagnóstico

Lançamos novas consultas pré-construídas para o espaço de trabalho do Log Analytics. Para aceder às consultas, aceda a **Registos** e na **categoria**, selecione **Windows Virtual Desktop**. Saiba mais no [Use Log Analytics para a funcionalidade de diagnóstico](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Atualização para cliente de desktop remoto para Android

O [cliente de Ambiente de Trabalho Remoto para Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) suporta agora as ligações virtuals do Windows Desktop. Começando pela versão 10.0.7, o cliente Android apresenta um novo UI para uma experiência de utilizador melhorada. O cliente também se integra com o Microsoft Authenticator em dispositivos Android para permitir o acesso condicional ao subscrever espaços de trabalho virtuais do Windows Desktop.  

A versão anterior do cliente remote desktop chama-se agora "Remote Desktop 8". Quaisquer ligações existentes que tenha na versão anterior do cliente serão transferidas perfeitamente para o novo cliente. O novo cliente foi reescrito para o mesmo motor central RDP subjacente que os clientes iOS e macOS, o lançamento mais rápido de novas funcionalidades em todas as plataformas. 

### <a name="teams-update"></a>Atualização de equipas

Fizemos melhorias nas Equipas microsoft para o Windows Virtual Desktop. Mais importante ainda, o Windows Virtual Desktop suporta agora a otimização de áudio e vídeo para o cliente Windows Desktop. A reorientação melhora a latência criando caminhos diretos entre os utilizadores quando utilizam áudio ou vídeo em chamadas e reuniões. Menos distância significa menos lúpulo, o que faz as chamadas parecerem e parecerem mais suaves. Saiba mais em [Utilizar equipas no Windows Virtual Desktop](teams-on-wvd.md).

## <a name="june-2020"></a>Junho de 2020

No mês passado, introduzimos o Windows Virtual Desktop com integração do Azure Resource Manager na pré-visualização. Esta atualização tem muitas novidades interessantes que gostaríamos de lhe contar. Eis as novidades para esta versão do Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop está agora integrado com Azure Resource Manager

O Windows Virtual Desktop está agora integrado no Azure Resource Manager. Na mais recente atualização, todos os objetos de desktop virtual do Windows são agora recursos do Gestor de Recursos Azure. Esta atualização também está integrada com o controlo de acesso baseado em funções Azure (Azure RBAC). Veja [o que é Azure Resource Manager?](../azure-resource-manager/management/overview.md)

Eis o que esta mudança faz por si:

- O Windows Virtual Desktop está agora integrado com o portal Azure. Isto significa que você pode gerir tudo diretamente no portal, sem PowerShell, aplicações web ou ferramentas de terceiros necessárias. Para começar, confira o nosso tutorial na [Create a host pool com o portal Azure.](create-host-pools-azure-marketplace.md)

- Antes desta atualização, só era possível publicar RemoteApps e Desktops para utilizadores individuais. Com o Azure Resource Manager, já pode publicar recursos para os grupos Azure Ative Directory.

- A versão anterior do Windows Virtual Desktop tinha quatro funções de administração incorporadas que você poderia atribuir a um inquilino ou piscina de anfitrião. Estas funções estão agora no controlo de acesso baseado em [funções Azure (Azure RBAC)](../role-based-access-control/overview.md). Pode aplicar estas funções em todos os objetos do Windows Virtual Desktop Azure Resource Manager, o que lhe permite ter um modelo de delegação completo e rico.

- Nesta atualização, já não precisa de executar o Azure Marketplace ou o modelo GitHub repetidamente para expandir uma piscina de anfitriões. Tudo o que precisa para expandir uma piscina de anfitriões é ir à sua piscina de anfitriões no portal Azure e selecionar **+ Adicionar** para implementar anfitriões de sessão adicionais.

- A implementação da piscina de anfitriões está agora totalmente integrada com a [Galeria de Imagem Partilhada Azure.](../virtual-machines/windows/shared-image-galleries.md) Shared Image Gallery é um serviço Azure separado que armazena definições de imagem de máquina virtual (VM), incluindo a versão de imagem. Também pode usar a replicação global para copiar e enviar as suas imagens para outras regiões do Azure para implantação local.

- As funções de monitorização que costumavam ser feitas através do PowerShell ou da aplicação web do Serviço de Diagnóstico mudaram-se agora para Log Analytics no portal Azure. Também tem agora duas opções para visualizar os seus relatórios. Pode executar consultas kusto e usar livros de trabalho para criar relatórios visuais.

- Já não é obrigado a concluir o consentimento do Azure Ative Directory (Azure AD) para utilizar o Windows Virtual Desktop. Nesta atualização, o inquilino Azure AD na sua assinatura Azure autentica os seus utilizadores e fornece controlos Azure RBAC para os seus administradores.


### <a name="powershell-support"></a>Suporte do PowerShell

Adicionámos novos cmdlets AzWvd ao Módulo Azure PowerShell Az com esta atualização. Este novo módulo é suportado no PowerShell Core, que funciona em .NET Core.

Para instalar o módulo, siga as instruções em [Configurar o módulo PowerShell para Windows Virtual Desktop](powershell-module.md).

Também pode ver uma lista de comandos disponíveis na [referência AzWvd PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Para mais informações sobre as novas funcionalidades, confira a [nossa publicação de blog.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)

### <a name="additional-gateways"></a>Gateways adicionais

Adicionámos um novo aglomerado de gateways na África do Sul para reduzir a latência da ligação.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams no Windows Virtual Desktop (Pré-visualização)

Fizemos algumas melhorias nas Equipas microsoft para o Windows Virtual Desktop. Mais importante ainda, o Windows Virtual Desktop suporta agora a reorientação áudio e visual para chamadas. A reorientação melhora a latência criando caminhos diretos entre os utilizadores quando ligam usando áudio ou vídeo. Menos distância significa menos lúpulo, o que faz as chamadas parecerem e parecerem mais suaves.

Para saber mais, consulte a [nossa publicação de blog.](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os planos futuros no [roteiro virtual do Microsoft 365 Windows Desktop.](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)

Consulte estes artigos para saber mais sobre as atualizações para os nossos clientes para Windows Virtual Desktop e Remote Desktop Services:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
