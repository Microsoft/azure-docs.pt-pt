---
title: Quais as novidades no Windows Virtual Desktop? - Azure
description: Novas funcionalidades e atualizações de produtos para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 2712115f19c7cc64a0475061e134d6be6de5d1ca
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867408"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Quais as novidades no Windows Virtual Desktop?

Windows Virtual Desktop atualiza regularmente. Este artigo é onde você vai descobrir sobre:

- As últimas atualizações
- Novas funcionalidades
- Melhorias às características existentes
- Correções de erros

Este artigo é atualizado mensalmente. Certifique-se de voltar aqui muitas vezes para acompanhar novas atualizações.

## <a name="client-updates"></a>Atualizações de clientes

Consulte estes artigos para saber mais sobre as atualizações para os nossos clientes para Windows Virtual Desktop e Remote Desktop Services:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Atualizações do Windows Virtual Desktop Agent

O agente virtual do Windows atualiza pelo menos uma vez por mês.

Eis o que mudou no Windows Virtual Desktop Agent:

- Versão 1.0.2990.800: Esta atualização foi lançada a 13 de abril de 2021 e tem as seguintes alterações:
    - Mensagens de erro do agente atualizadas.
    - Adiciona uma exceção que o impede de instalar agentes não Windows 7 em VMs windows 7.
    - Tem uma lógica atualizada do serviço de batimentos cardíacos.
- Versão 1.0.2944.1400: Esta atualização foi lançada a 7 de abril de 2021 e tem as seguintes alterações:
    - Colocou links para o guia de resolução de problemas do Windows Virtual Desktop Agent nos registos de visualização do caso para erros de agente.
    - Acrescentou uma exceção adicional para um melhor manuseamento de erros.
    - Acrescentou o WVDAgentUrlTool.exe que permite aos clientes verificarem quais os URLs necessários a que podem aceder.
- Versão 1.0.2866.1500: Esta atualização foi lançada a 26 de março de 2021 e corrige um problema com o cheque de saúde da stack.
- Versão 1.0.2800.2802: Esta atualização foi lançada a 10 de março de 2021 e possui melhorias gerais e correções de bugs.
- Versão 1.0.2800.2800: Esta atualização foi lançada a 2 de março de 2021 e corrige um problema de ligação inversa.
- Versão 1.0.2800.2700: Esta atualização foi lançada a 10 de fevereiro de 2021 e possui melhorias gerais e correções de bugs.
- Versão 1.0.2800.2700: Esta atualização foi lançada a 4 de fevereiro de 2021 e corrige um problema de orquestração negado de acesso.

## <a name="fslogix-updates"></a>Atualizações da FSLogix

Curioso sobre as últimas atualizações para fSLogix? Confira [as novidades da FSLogix.](/fslogix/whats-new)

## <a name="march-2021"></a>março de 2021

Eis o que mudou em março de 2021.

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Atualizações para o portal Azure UI para Windows Virtual Desktop

Fizemos as seguintes atualizações para o Windows Virtual Desktop para o portal Azure:

- Permitimos novas opções de disponibilidade (disponibilidade definida e zonas) para os fluxos de trabalho criarem piscinas hospedeiras e adicionarem VMs.
- Resolvemos um problema em que um hospedeiro com o estatuto de "Assistência às Necessidades" parecia indisponível. Agora o anfitrião terá um ícone de aviso ao lado.
- Permitimos a triagem para sessões ativas.
- Pode agora enviar mensagens ou assinar utilizadores específicos no separador detalhes do anfitrião.
- Mudamos o campo limite máximo de sessão.
- Adicionámos um caminho de validação da U ao fluxo de trabalho para criar uma piscina de anfitriões.
- Agora pode utilizar a versão mais recente da imagem do Windows 10 quando criar um pool de anfitriões pessoais.

### <a name="generation-2-images-and-trusted-launch"></a>Imagens da Geração 2 e Lançamento Fidedigno

O Azure Marketplace conta agora com imagens da Geração 2 para o Windows 10 Enterprise e para o Windows 10 Enterprise multi-sessão. Estas imagens permitir-lhe-ão utilizar VMs de lançamento fidedignos. Saiba mais sobre os VMs da Geração 2 em [Caso eu crie uma máquina virtual de geração 1 ou 2.](../virtual-machines/generation-2.md) Para saber como providenciar VMs de lançamento fidedignos de desktop virtual do Windows, consulte [o nosso post TechCommunity](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170).

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix está agora pré-instalado em imagens multi-sessão do Windows 10 Enterprise

Com base no feedback dos clientes, criamos uma nova versão da imagem multi-sessão do Windows 10 Enterprise que tem uma versão não configurada do FSLogix já instalada. Esperamos que isto facilite a implementação do seu Windows Virtual Desktop.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>O Monitor Azure para o Ambiente de Trabalho Virtual do Windows está agora em Disponibilidade Geral

O Azure Monitor para Windows Virtual Desktop está agora disponível para o público. Esta funcionalidade é um serviço automatizado que monitoriza as suas implementações e permite-lhe visualizar eventos, saúde e sugestões de resolução de problemas num único local. Para mais informações, consulte a [nossa documentação](azure-monitor.md) ou consulte o [nosso posto TechCommunity.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>Atualizações de março de 2021 para Equipas no Windows Virtual Desktop

Fizemos as seguintes atualizações para equipas no Windows Virtual Desktop:

- Melhorámos o desempenho da qualidade do vídeo nas chamadas e no modo 2x2.
- Reduzimos a utilização do CPU em 5-10% (dependendo da geração cpU) usando o descarregamento de hardware do processamento de vídeo (XVP).
- As máquinas mais antigas podem agora utilizar o XVP e a descodagem de hardware para exibir mais streams de vídeo que chegam suavemente no modo 2x2.
- Atualizámos a stack WebRTC de M74 a M88 para um melhor desempenho de sincronização de AV e menos problemas transitórios.
- Substituímos o nosso codificar de software H264 por OpenH264 (OSS usado em Equipas na web), o que aumentou a qualidade de vídeo da câmara de saída.
- Adorámos o modo 2x2 para o Teams Server para o público em geral a 30 de março. O modo 2x2 mostra até quatro streams de vídeo ao mesmo tempo.

### <a name="start-vm-on-connect-public-preview"></a>Iniciar VM na pré-visualização pública do Connect

A nova configuração da piscina do anfitrião, Start VM on Connect, já está disponível em pré-visualização pública. Esta definição permite ligar os seus VM sempre que precisar. Se quiser economizar custos, terá de negociar os seus VMs configurando as definições do Azure Compute. Para mais informações, confira o [nosso blog post](https://aka.ms/wvdstartvmonconnect) e a nossa [documentação.](start-virtual-machine-connect.md)

### <a name="windows-virtual-desktop-specialty-certification"></a>Certificação de especialidade de desktop virtual do Windows

Lançámos uma versão beta do exame AZ-140 que lhe permitirá provar a sua experiência no Windows Virtual Desktop em Azure. Para saber mais, confira o [nosso post TechCommunity.](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)

## <a name="february-2021"></a>Fevereiro de 2021

Eis o que mudou em fevereiro de 2021.

### <a name="portal-experience"></a>Experiência do portal

Melhorámos a experiência do portal Azure das seguintes formas:

- Modo de drenagem a granel nos anfitriões no separador grelha do anfitrião da sessão. 
- O anexo de aplicações MSIX já está disponível para pré-visualização pública.
- Informação geral da piscina de anfitrião fixa para modo escuro.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Armazenamento de metadados da UE agora em pré-visualização pública

Estamos agora a realizar uma pré-visualização pública da geografia da Europa (UE) como uma opção de armazenamento para metadados de serviço no Windows Virtual Desktop. Os clientes podem escolher entre o Oeste e o Norte da Europa quando criam os seus objetos de serviço. Os objetos de serviço e metadados para as piscinas hospedeiras serão armazenados na geografia Azure associada a cada região. Para saber mais, leia [a nossa publicação de blog anunciando a pré-visualização do público.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Equipas em atualizações de plugin virtual do Windows Desktop

Melhorámos a qualidade das chamadas de vídeo no plugin virtual do Windows Desktop, abordando os problemas mais relatados, como quando o ecrã de repente escureceu ou o vídeo e o som dessincronizados. Estas melhorias devem aumentar o desempenho da visualização de um único vídeo com a comutação ativa do altifalante. Também corrigimos um problema em que dispositivos de hardware com caracteres especiais não estavam disponíveis em Equipas.

## <a name="january-2021"></a>Janeiro de 2021

Eis o que mudou em janeiro de 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nova oferta de desktop virtual do Windows

Os novos clientes poupam 30% nos custos de computação virtual do Windows desktop para máquinas virtuais sérieS D e Bs durante até 90 dias quando utilizarem a solução nativa da Microsoft. Pode resgatar esta oferta no portal Azure antes de 31 de março de 2021. Saiba mais na nossa [página de oferta virtual do Windows Desktop.](https://azure.microsoft.com/services/virtual-desktop/offer/)

### <a name="networksecuritygrouprules-value-change"></a>mudança de valor do NetworkSecurityGroupRules 

No modelo aninhado do Azure Resource Manager, alteramos o valor padrão para o NetworkSecurityGroupRules de um objeto para um array. Isto evitará erros se utilizar managedDisks-customimagevm.jssem especificar um valor para o NetworkSecurityGroupRules. Isto não foi uma mudança de rutura e é compatível com retrocesso.

### <a name="fslogix-hotfix-update"></a>Atualização de hotfix FSLogix

Lançamos a FSLogix, versão 2009 HF_01 (2.9.7654.46150) para resolver problemas no lançamento anterior (2.9.7621.30127). Recomendamos que deixe de usar a versão anterior e atualize o FSLogix o mais rapidamente possível.

Para mais informações, consulte as notas de lançamento no [Que há de novo na FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150).

### <a name="azure-portal-experience-improvements"></a>Melhorias na experiência do portal Azure

Fizemos as seguintes melhorias na experiência do portal Azure:

- Agora pode adicionar credenciais de administração VM locais diretamente em vez de ter que adicionar uma conta local criada com o domínio Ative Directory juntar credenciais de conta.
- Os utilizadores podem agora listar as atribuições individuais e de grupo em separados para utilizadores e grupos individuais.
- O número de versão do Windows Virtual Desktop Agent está agora visível na visão geral da Máquina Virtual para piscinas hospedeiras.
- Exclusão a granel adicionada para piscinas de anfitrião e grupos de aplicação.
- Agora pode ativar ou desativar o modo de drenagem para vários anfitriões de sessão numa piscina hospedeira.
- Removido o campo IP público da página de detalhes VM.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Monitorização de problemas do Agente de Ambiente de Trabalho Virtual do Windows

Recentemente criámos o [guia de resolução de problemas](troubleshoot-agent.md) do Windows Virtual Desktop Agent para ajudar os clientes que encontraram problemas comuns.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender para integração endpoint

O Microsoft Defender para a integração endpoint está disponível em geral. Esta funcionalidade dá aos seus VMs de ambiente de trabalho virtual do Windows a mesma experiência de investigação que uma máquina local do Windows 10. Se estiver a utilizar várias sessões do Windows 10 Enterprise, o Microsoft Defender for Endpoint irá suportar até 50 ligações de utilizador simultâneas, proporcionando-lhe as economias de custos de várias sessões do Windows 10 Enterprise e a confiança do Microsoft Defender para o Endpoint. Para mais informações, confira o nosso [blog post.](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linha de base de segurança Azure para o Windows Virtual Desktop

Publicámos recentemente [um artigo sobre a linha de segurança Azure](security-baseline.md) para o Windows Virtual Desktop a que gostaríamos de chamar a sua atenção. Estas diretrizes incluem informações sobre como aplicar o Azure Security Benchmark, versão 2.0 para o Windows Virtual Desktop. O Azure Security Benchmark descreve as definições e práticas que recomendamos que utilize para proteger as suas soluções de nuvem no Azure.

## <a name="december-2020"></a>Dezembro de 2020

Eis o que mudou em dezembro de 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Monitor Azure para o Windows Virtual Desktop

A pré-visualização pública do Azure Monitor para o Windows Virtual Desktop já se encontra disponível. Esta nova funcionalidade inclui um painel robusto construído em cima de livros de trabalho do Monitor Azure para ajudar os profissionais de TI a compreender os seus ambientes de desktop virtual do Windows. Confira [o anúncio no nosso blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) para mais detalhes. 

### <a name="azure-resource-manager-template-change"></a>Alteração do modelo do gestor de recursos Azure 

Na última atualização, removemos todos os parâmetros de endereço IP públicos do modelo do Gestor de Recursos Azure para criar e aprovisionar piscinas hospedeiras. Recomendamos vivamente que evite utilizar IPs públicos para Windows Virtual Desktop para manter a sua implementação segura. Se a sua implantação dependesse de IPs públicos, terá de o reconfigurar para utilizar iPs privados, caso contrário, a sua implantação não funcionará corretamente.

### <a name="msix-app-attach-public-preview"></a>App MSIX anexa pré-visualização pública 

O anexo de aplicações MSIX é outro serviço que iniciou a sua pré-visualização pública este mês. O anexo de aplicações MSIX é um serviço que apresenta dinamicamente aplicações MSIX para os VMs do anfitrião do Windows Virtual Desktop Session. Confira [o anúncio no nosso blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) para mais detalhes. 

### <a name="screen-capture-protection"></a>Proteção de captura de ecrã 

Este mês também marcou o início da pré-visualização pública para proteção de captura de ecrãs. Pode utilizar esta funcionalidade para evitar que informações sensíveis sejam capturadas nos pontos finais do cliente. Dê uma tentativa à proteção de captura de ecrã, indo para [esta página](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Funções incorporadas

Adicionámos novas funções incorporadas para o Windows Virtual Desktop para permissões de administração. Para obter mais informações, consulte [as funções incorporadas para o Windows Virtual Desktop](rbac.md). 

### <a name="application-group-limit-increase"></a>Aumento do limite do grupo de aplicação

Aumentámos o limite do grupo de candidaturas por padrão por inquilino do Azure Ative Directory para 200 grupos.

### <a name="client-updates-for-december-2020"></a>Atualizações de clientes para dezembro de 2020

Lançamos novas versões dos seguintes clientes: 

- Android
- macOS
- Windows

Para obter mais informações sobre as atualizações do cliente, consulte [as atualizações do Cliente.](whats-new.md#client-updates)

## <a name="november-2020"></a>Novembro de 2020

### <a name="azure-portal-experience"></a>Experiência do portal Azure

Corrigimos dois bugs na experiência do utilizador do portal Azure:

- O nome amigável da aplicação desktop já não está substituído no fluxo de trabalho "Add VM".
- O separador anfitrião da sessão irá agora carregar se os anfitriões da sessão fizerem parte de conjuntos de escala.

### <a name="fslogix-client-version-2009"></a>Cliente FSLogix, versão 2009 

Lançámos uma nova versão do cliente FSLogix com muitas correções e melhorias. Saiba mais na [nossa publicação de blog.](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)

### <a name="rdp-shortpath-public-preview"></a>Pré-visualização pública do VIADP Shortpath

O RDP Shortpath introduz conectividade direta ao anfitrião da sessão virtual do Windows desktop utilizando VPNs ponto a local e site-a-site e ExpressRoute. Introduz também o protocolo de transporte URCP. O RDP Shortpath foi concebido para reduzir a latência e o lúpulo de rede de forma a melhorar a experiência do utilizador. Saiba mais no [Windows Virtual Desktop RDP Shortpath](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, versão 2.0.1

Lançamos a versão 2.0.1 dos cmdlets virtual do Windows Desktop. Esta atualização inclui cmdlets que lhe permitirão gerir o MsIX App Attach. Pode baixar a nova versão [na galeria PowerShell.](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)

### <a name="azure-advisor-updates"></a>Atualizações do Azure Advisor

O Azure Advisor tem agora uma nova recomendação para orientação de proximidade no Windows Virtual Desktop, e uma nova recomendação para otimizar o desempenho em piscinas de anfitriões equilibradas de primeira carga. Saiba mais [no site da Azure.](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)

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

- O Azure CLI suporta agora o Windows Virtual Desktop ( `az desktopvirtualization` ) para o ajudar a automatizar as suas implementações de Ambiente de Trabalho Virtual do Windows. Consulte a [verificação de desktop](/cli/azure/) para obter uma lista de comandos de extensão.

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

- A implementação da piscina de anfitriões está agora totalmente integrada com a [Galeria de Imagem Partilhada Azure.](../virtual-machines/shared-image-galleries.md) Shared Image Gallery é um serviço Azure separado que armazena definições de imagem de máquina virtual (VM), incluindo a versão de imagem. Também pode usar a replicação global para copiar e enviar as suas imagens para outras regiões do Azure para implantação local.

- As funções de monitorização que costumavam ser feitas através do PowerShell ou da aplicação web do Serviço de Diagnóstico mudaram-se agora para Log Analytics no portal Azure. Também tem agora duas opções para visualizar os seus relatórios. Pode executar consultas kusto e usar livros de trabalho para criar relatórios visuais.

- Já não é obrigado a concluir o consentimento do Azure Ative Directory (Azure AD) para utilizar o Windows Virtual Desktop. Nesta atualização, o inquilino Azure AD na sua assinatura Azure autentica os seus utilizadores e fornece controlos Azure RBAC para os seus administradores.

### <a name="powershell-support"></a>Suporte do PowerShell

Adicionámos novos cmdlets AzWvd ao Módulo Azure PowerShell Az com esta atualização. Este novo módulo é suportado no PowerShell Core, que funciona em .NET Core.

Para instalar o módulo, siga as instruções em [Configurar o módulo PowerShell para Windows Virtual Desktop](powershell-module.md).

Também pode ver uma lista de comandos disponíveis na [referência AzWvd PowerShell](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Para mais informações sobre as novas funcionalidades, confira a [nossa publicação de blog.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)

### <a name="additional-gateways"></a>Gateways adicionais

Adicionámos um novo aglomerado de gateways na África do Sul para reduzir a latência da ligação.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams no Windows Virtual Desktop (Pré-visualização)

Fizemos algumas melhorias nas Equipas microsoft para o Windows Virtual Desktop. Mais importante ainda, o Windows Virtual Desktop suporta agora a reorientação áudio e visual para chamadas. A reorientação melhora a latência criando caminhos diretos entre os utilizadores quando ligam usando áudio ou vídeo. Menos distância significa menos lúpulo, o que faz as chamadas parecerem e parecerem mais suaves.

Para saber mais, consulte a [nossa publicação de blog.](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os planos futuros no [roteiro virtual do Microsoft 365 Windows Desktop.](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)
