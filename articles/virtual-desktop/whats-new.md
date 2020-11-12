---
title: Quais as novidades no Windows Virtual Desktop? - Azure
description: Novas funcionalidades e atualizações de produtos para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 11/11/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: c82312b40955b392243bf616ee991c0108a13fb5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537395"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Quais as novidades no Windows Virtual Desktop?

Atualizações de Ambiente de Trabalho Virtual do Windows regularmente. Este artigo é onde você vai descobrir sobre:

- As últimas atualizações
- Novas funcionalidades
- Melhorias às características existentes
- Correções de erros

Este artigo é atualizado mensalmente. Certifique-se de voltar aqui muitas vezes para acompanhar novas atualizações.

## <a name="october-2020"></a>Outubro de 2020

Eis o que mudou em outubro de 2020:

### <a name="improved-performance"></a>Desempenho melhorado

- Otimizamos o desempenho reduzindo a latência da ligação nas seguintes geografias Azure:
    - Suíça
    - Canadá

Agora pode utilizar o [Experience Estimador](https://azure.microsoft.com/services/virtual-desktop/assessment/) para estimar a qualidade da experiência do utilizador nestas áreas.

### <a name="azure-government-cloud-availability"></a>Disponibilidade da Nuvem do Governo Azure

A Nuvem do Governo de Azure está agora geralmente disponível. Saiba mais na [nossa publicação de blog.](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Atualizações do portal Virtual Desktop Azure do Windows

Fizemos algumas atualizações para o portal Virtual Desktop Azure do Windows:

- Corrigiu um erro de recursosID que impedia os utilizadores de abrir o separador "Sessões".
- Dinamizou o UI no separador "Anfitriões de Sessão".
- Corrigiu as definições de "Predefinições", "Usabilidade" e "Restaurar predefinições" nas propriedades RDP.
- As funções "Remover" e "Eliminar" são consistentes em todos os separadores.
- O portal valida agora os nomes das aplicações no fluxo de trabalho "Adicionar uma aplicação".
- Corrigiu um problema em que os dados de exportação dos anfitriões da sessão não estavam alinhados nas colunas.
- Corrigiu um problema em que o portal não conseguia recuperar as sessões dos utilizadores.
- Corrigiu um problema na recuperação do anfitrião da sessão que aconteceu quando a máquina virtual foi criada num grupo de recursos diferente.
- Atualizou o separador "Anfitrião de sessão" para listar as sessões ativas e desligadas.
- O separador "Aplicações" tem agora páginas.
- Corrigiu um problema em que o texto "requer linha de comando" não foi apresentado corretamente no separador "Lista de Aplicações".
- Corrigiu um problema quando o portal não conseguia implantar piscinas hospedeiras ou máquinas virtuais enquanto utilizava a versão em língua alemã da Galeria de Imagens Partilhadas.

### <a name="client-updates-for-october-2020"></a>Atualizações de clientes para outubro de 2020

Lançámos novas versões dos clientes. Consulte estes artigos para saber mais:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Para obter mais informações sobre os outros clientes, consulte [as atualizações do Cliente.](#client-updates)

## <a name="september-2020"></a>Setembro de 2020

Eis o que mudou em setembro de 2020:

- Otimizamos o desempenho reduzindo a latência da ligação nas seguintes geografias Azure:
    - Alemanha
    - África do Sul (apenas para ambientes de validação)

Agora pode utilizar o [Experience Estimador](https://azure.microsoft.com/services/virtual-desktop/assessment/) para estimar a qualidade da experiência do utilizador nestas áreas.

- Lançámos a versão 1.2.1364 do cliente windows desktop para o Windows Virtual Desktop. Nesta atualização, fizemos as seguintes alterações:
    - Corrigiu um problema em que um único sinal de s-on (SSO) não funcionou no Windows 7.
    - Corrigiu um problema que fez com que o cliente se desligasse quando um utilizador que viabilizasse a otimização de meios para equipas tentava ligar ou juntar-se a uma reunião das Equipas enquanto outra aplicação tinha um fluxo de áudio aberto em modo exclusivo.
    - Corrigiu um problema em que as Equipas não enumeravam dispositivos de áudio ou vídeo quando a otimização dos meios de comunicação para equipas estava ativada.
    - Acrescentou um "Precisa de ajuda com as configurações?" ligação para a página de definições do ambiente de trabalho.
    - Corrigiu um problema com o botão "Subscrever" que aconteceu ao utilizar temas escuros de alto contraste.
    
- Graças à tremenda ajuda dos nossos utilizadores, corrigimos dois problemas críticos para o cliente do Microsoft Store Remote Desktop. Continuaremos a rever feedback e corrigir problemas à medida que alargamos a nossa versão faseada do cliente a mais utilizadores em todo o mundo.
    
- Adicionámos uma nova funcionalidade que permite alterar a localização do VM, imagem, grupo de recursos, nome prefixo, configuração de rede como parte do fluxo de trabalho para adicionar um VM à sua implementação no portal Azure.

- Os IT Pros podem agora gerir vMs híbridos do Azure Ative Directory com o Windows 10 Enterprise VMs utilizando o Microsoft Endpoint Manager. Para saber mais, consulte a [nossa publicação de blog.](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)

## <a name="august-2020"></a>Agosto de 2020

Eis o que mudou em agosto de 2020:

- Melhorámos o desempenho para reduzir a latência da ligação nas seguintes regiões de Azure: 

    - Reino Unido
    - França
    - Noruega
    - Coreia do Sul

   Pode utilizar o [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) para ter uma ideia geral de como estas alterações irão afetar os seus utilizadores.

- O Cliente de Ambiente de Trabalho Remoto da Microsoft Store (v10.2.1522+) já está disponível em geral! Esta versão do Cliente de Ambiente de Trabalho Remoto da Microsoft Store é compatível com o Windows Virtual Desktop. Também introduzimos fluxos de UI atualizados para melhores experiências de utilizador. Esta atualização inclui design fluente, modos claros e escuros, e muitas outras mudanças emocionantes. Também reecrito o cliente para usar o mesmo motor de protocolo remoto de desktop (RDP) subjacente como os clientes iOS, macOS e Android. Isto permite-nos oferecer novas funcionalidades a um ritmo mais rápido em todas as plataformas. [Descarregue o cliente](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) e tente!

- Corrigimos um problema no cliente Teams Desktop (versão 1.3.00.21759) onde o cliente apenas mostrou o fuso horário UTC no chat, canais e calendário. O cliente atualizado mostra agora o fuso horário da sessão remota.

- O Azure Advisor faz agora parte do Windows Virtual Desktop. Ao aceder ao Windows Virtual Desktop através do portal Azure, pode ver recomendações para otimizar o ambiente de trabalho virtual do Windows. Saiba mais na [Azure Advisor](azure-advisor.md).

- O Azure CLI suporta agora o Windows Virtual Desktop ( `az desktopvirtualization` ) para o ajudar a automatizar as suas implementações de Ambiente de Trabalho Virtual do Windows. Consulte a [verificação de desktop](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true) para obter uma lista de comandos de extensão.

- Atualizámos os nossos modelos de implementação para os tornar totalmente compatíveis com as interfaces do Windows Virtual Desktop Azure Resource Manager. Pode encontrar os modelos no [GitHub.](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)

- O portal Windows Virtual Desktop US Gov está agora em pré-visualização pública. Para saber mais, consulte o [nosso anúncio.](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)

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

Lançamos novas consultas pré-construídas para o espaço de trabalho do Log Analytics. Para aceder às consultas, aceda a **Registos** e na **categoria** , selecione **Windows Virtual Desktop**. Saiba mais no [Use Log Analytics para a funcionalidade de diagnóstico](diagnostics-log-analytics.md).

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

Também pode ver uma lista de comandos disponíveis na [referência AzWvd PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true).

Para mais informações sobre as novas funcionalidades, confira a [nossa publicação de blog.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)

### <a name="additional-gateways"></a>Gateways adicionais

Adicionámos um novo aglomerado de gateways na África do Sul para reduzir a latência da ligação.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams no Windows Virtual Desktop (Pré-visualização)

Fizemos algumas melhorias nas Equipas microsoft para o Windows Virtual Desktop. Mais importante ainda, o Windows Virtual Desktop suporta agora a reorientação áudio e visual para chamadas. A reorientação melhora a latência criando caminhos diretos entre os utilizadores quando ligam usando áudio ou vídeo. Menos distância significa menos lúpulo, o que faz as chamadas parecerem e parecerem mais suaves.

Para saber mais, consulte a [nossa publicação de blog.](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)

## <a name="client-updates"></a>Atualizações de clientes

Consulte estes artigos para saber mais sobre as atualizações para os nossos clientes para Windows Virtual Desktop e Remote Desktop Services:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os planos futuros no [roteiro virtual do Microsoft 365 Windows Desktop.](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)
