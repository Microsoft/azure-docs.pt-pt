---
title: Quais as novidades no Windows Virtual Desktop? - Azure
description: Novas funcionalidades e atualizações de produtos para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 7052e9203532320a7de5197e983d40cb0a34b50d
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374785"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Quais as novidades no Windows Virtual Desktop?

Atualizações de Ambiente de Trabalho Virtual do Windows regularmente. Este artigo é onde você vai descobrir sobre:

- As últimas atualizações
- Novas funcionalidades
- Melhorias às características existentes
- Correções de erros

Este artigo é atualizado mensalmente. Certifique-se de voltar aqui muitas vezes para acompanhar novas atualizações.

## <a name="june-2020"></a>Junho de 2020

No mês passado, introduzimos a atualização virtual do Windows Desktop Spring 2020 na pré-visualização. Esta atualização tem muitas novidades interessantes que gostaríamos de lhe contar. Eis as novidades para a atualização da primavera de 2020.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>O Windows Virtual Desktop está agora integrado com o Azure Resource Manager (Preview)

O Windows Virtual Desktop está agora integrado no Azure Resource Manager. Na mais recente atualização, todos os objetos de desktop virtual do Windows são agora recursos do Gestor de Recursos Azure. Esta atualização também está integrada com controlos de acesso baseados em funções Azure (RBAC). Veja [o que é Azure Resource Manager?](../azure-resource-manager/management/overview.md)

Eis o que esta mudança faz por si:

- O Windows Virtual Desktop está agora integrado com o portal Azure. Isto significa que você pode gerir tudo diretamente no portal, sem PowerShell, aplicações web ou ferramentas de terceiros necessárias. Para começar, confira o nosso tutorial na [Create a host pool com o portal Azure.](create-host-pools-azure-marketplace.md)

- Antes da atualização da primavera de 2020, só poderia publicar RemoteApps e Desktops para utilizadores individuais. Com o Azure Resource Manager, já pode publicar recursos para os grupos Azure Ative Directory.

- A versão anterior do Windows Virtual Desktop tinha quatro funções de administração incorporadas que você poderia atribuir a um inquilino ou piscina de anfitrião. Estas funções estão agora no controlo de acesso baseado em [funções do](../role-based-access-control/overview.md)Azure. Pode aplicar estas funções em todos os objetos do Windows Virtual Desktop Azure Resource Manager, o que lhe permite ter um modelo de delegação completo e rico.

- Na atualização da primavera de 2020, já não precisa de executar o Azure Marketplace ou o modelo GitHub repetidamente para expandir uma piscina de anfitriões. Tudo o que precisa para expandir uma piscina de anfitriões é ir à sua piscina de anfitriões no portal Azure e selecionar **+ Adicionar** para implementar anfitriões de sessão adicionais.

- A implementação da piscina de anfitriões está agora totalmente integrada com a [Galeria de Imagem Partilhada Azure.](../virtual-machines/windows/shared-image-galleries.md) Shared Image Gallery é um serviço Azure separado que armazena definições de imagem de máquina virtual (VM), incluindo a versão de imagem. Também pode usar a replicação global para copiar e enviar as suas imagens para outras regiões do Azure para implantação local.

- As funções de monitorização que costumavam ser feitas através do PowerShell ou da aplicação web do Serviço de Diagnóstico mudaram-se agora para Log Analytics no portal Azure. Também tem agora duas opções para visualizar os seus relatórios. Pode executar consultas kusto e usar livros de trabalho para criar relatórios visuais.

- Já não é obrigado a concluir o consentimento do Azure Ative Directory (Azure AD) para utilizar o Windows Virtual Desktop. Na atualização da primavera de 2020, o inquilino AZure AD na sua subscrição Azure autentica os seus utilizadores e fornece controlos RBAC para os seus administradores.


### <a name="powershell-support"></a>Suporte do PowerShell

Adicionámos novos cmdlets AzWvd ao Módulo Azure PowerShell Az com a atualização primavera de 2020. Este novo módulo é suportado no PowerShell Core, que funciona em .NET Core.

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
