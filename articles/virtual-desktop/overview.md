---
title: O que é o Windows Virtual Desktop? - Azure
description: Uma visão geral da área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 12/17/2019
ms.author: helohr
ms.openlocfilehash: dd5167af5f45ebae0529e16f224065627085e9b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348816"
---
# <a name="what-is-windows-virtual-desktop"></a>O que é o Windows Virtual Desktop? 

A área de trabalho virtual do Windows é um serviço de desktop e de virtualização de aplicativo que é executado na nuvem.

Veja o que você pode fazer ao executar a área de trabalho virtual do Windows no Azure:

* Configurar uma implantação de várias sessões do Windows 10 que fornece um Windows 10 completo com escalabilidade
* Virtualizar o Office 365 ProPlus e otimizá-lo para ser executado em cenários virtuais de vários usuários
* Fornecer áreas de trabalho virtuais do Windows 7 com atualizações de segurança estendidas gratuitas
* Traga seus aplicativos e desktops Serviços de Área de Trabalho Remota (RDS) e Windows Server existentes para qualquer computador
* Virtualização de desktops e aplicativos
* Gerencie desktops e aplicativos Windows 10, Windows Server e Windows 7 com uma experiência de gerenciamento unificada

## <a name="introductory-video"></a>Vídeo introdutório

Saiba mais sobre a área de trabalho virtual do Windows, por que é exclusivo e o que há de novo neste vídeo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Para obter mais vídeos sobre a área de trabalho virtual do Windows, consulte [nossa lista de reprodução](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Principais capacidades

Com a área de trabalho virtual do Windows, você pode configurar um ambiente escalonável e flexível:

* Crie um ambiente de virtualização de área de trabalho completo em sua assinatura do Azure sem precisar executar servidores de gateway adicionais.
* Publique quantos pools de hosts forem necessários para acomodar suas diversas cargas de trabalho.
* Traga sua própria imagem para cargas de trabalho de produção ou teste na galeria do Azure.
* Reduza os custos com recursos de várias sessões em pool. Com a nova funcionalidade de várias sessões do Windows 10 Enterprise exclusiva para a função de RDSH (área de trabalho virtual do Windows e Host da Sessão da Área de Trabalho Remota) no Windows Server, você pode reduzir muito o número de máquinas virtuais e a sobrecarga de sistema operacional (SO) enquanto ainda fornecendo os mesmos recursos aos seus usuários.
* Forneça propriedade individual por meio de áreas de trabalho pessoais (persistentes).

Você pode implantar e gerenciar áreas de trabalho virtuais:

* Use o PowerShell da área de trabalho virtual do Windows e as interfaces REST para configurar os pools de hosts, criar grupos de aplicativos, atribuir usuários e publicar recursos.
* Publique aplicativos de área de trabalho completa ou remotos individuais de um único pool de hosts, crie grupos de aplicativos individuais para diferentes conjuntos de usuários ou até mesmo Atribua usuários a vários grupos de aplicativos para reduzir o número de imagens.
* Ao gerenciar seu ambiente, use o acesso delegado interno para atribuir funções e coletar diagnósticos para entender vários erros de configuração ou de usuário.
* Use o novo serviço de diagnóstico para solucionar erros.
* Gerencie apenas a imagem e as máquinas virtuais, não a infraestrutura. Você não precisa gerenciar pessoalmente as funções de Área de Trabalho Remota como faz com Serviços de Área de Trabalho Remota, apenas as máquinas virtuais em sua assinatura do Azure.

Você também pode atribuir e conectar usuários a suas áreas de trabalho virtuais:

* Depois de atribuídas, os usuários podem iniciar qualquer cliente de área de trabalho virtual do Windows para conectar os usuários aos seus aplicativos e desktops Windows publicados. Conecte-se de qualquer dispositivo por meio de um aplicativo nativo no seu dispositivo ou do cliente Web do HTML5 da área de trabalho virtual do Windows.
* Estabeleça usuários com segurança por meio de conexões inversas com o serviço, para que você nunca precise deixar as portas de entrada abertas.

## <a name="requirements"></a>Requisitos

Há algumas coisas que você precisa para configurar a área de trabalho virtual do Windows e conectar seus usuários com êxito a seus aplicativos e desktops Windows.

Planejamos adicionar suporte para os seguintes SOS, portanto, verifique se você tem as [licenças apropriadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para seus usuários com base na área de trabalho e nos aplicativos que planeja implantar:

|SO|Licença necessária|
|---|---|
|Windows 10 Enterprise Multi-Session ou Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|CAL (licença de acesso para cliente) do RDS com Software Assurance|

Sua infraestrutura precisa dos seguintes itens para dar suporte à área de trabalho virtual do Windows:

* Um [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Um Active Directory do Windows Server em sincronia com o Azure Active Directory. Você pode configurar isso com um dos seguintes:
  * Azure AD Connect (para organizações híbridas)
  * Azure AD Domain Services (para organizações híbridas ou de nuvem)
* Uma assinatura do Azure que contém uma rede virtual que contém ou está conectada ao Windows Server Active Directory
  
As máquinas virtuais do Azure criadas para a área de trabalho virtual do Windows devem ser:

* [Ingressado no AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan) [padrão](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) ou híbrido. As máquinas virtuais não podem ser ingressadas no Azure AD.
* Executando uma das seguintes [imagens de sistema operacional com suporte](#supported-virtual-machine-os-images).

>[!NOTE]
>Se precisar de uma assinatura do Azure, você poderá [se inscrever para uma avaliação gratuita de um mês](https://azure.microsoft.com/free/). Se você estiver usando a versão de avaliação gratuita do Azure, deverá usar Azure AD Domain Services para manter o Active Directory do Windows Server em sincronia com Azure Active Directory.

As máquinas virtuais do Azure que você cria para a área de trabalho virtual do Windows devem ter acesso TCP 443 de saída às seguintes URLs:

* *. wvd.microsoft.com
* *.blob.core.windows.net
* *.core.windows.net
* *.servicebus.windows.net
* prod.warmpath.msftcloudes.com
* catalogartifact.azureedge.net

>[!NOTE]
>Abrir essas URLs é essencial para uma implantação de área de trabalho virtual do Windows confiável. Não há suporte para o bloqueio de acesso a essas URLs e isso afetará a funcionalidade do serviço. Essas URLs correspondem apenas aos sites e recursos da área de trabalho virtual do Windows e não incluem URLS para outros serviços como o Azure AD.

A área de trabalho virtual do Windows inclui os aplicativos e as áreas de trabalho do Windows que você fornece aos usuários e à solução de gerenciamento, que é hospedada como um serviço no Azure pela Microsoft. Desktops e aplicativos podem ser implantados em máquinas virtuais (VMs) em qualquer região do Azure, e a solução de gerenciamento e os dados para essas VMs residirão no Estados Unidos. Isso pode resultar na transferência de dados para o Estados Unidos.

Para obter um desempenho ideal, verifique se sua rede atende aos seguintes requisitos:

* Latência de ida e volta (RTT) da rede do cliente para a região do Azure em que os pools de hosts foram implantados devem ser inferiores a 150 ms.
* O tráfego de rede pode fluir fora das bordas de país/região quando as VMs que hospedam áreas de trabalho e aplicativos se conectam ao serviço de gerenciamento.
* Para otimizar o desempenho da rede, recomendamos que as VMs do host de sessão sejam colocadas na mesma região do Azure que o serviço de gerenciamento.

## <a name="supported-remote-desktop-clients"></a>Clientes Área de Trabalho Remota com suporte

Os seguintes Área de Trabalho Remota clientes dão suporte à área de trabalho virtual do Windows:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (versão prévia)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Imagens do sistema operacional da máquina virtual com suporte

A área de trabalho virtual do Windows dá suporte às seguintes imagens do sistema operacional x64:

* Windows 10 Enterprise Multi-Session, versão 1809 ou posterior
* Windows 10 Enterprise, versão 1809 ou posterior
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

A área de trabalho virtual do Windows não oferece suporte a imagens do sistema operacional x86 (32 bits), Windows 10 Enterprise N ou Windows 10 Enterprise KN.

As opções de automação e implantação disponíveis dependem do sistema operacional e da versão que você escolher, conforme mostrado na tabela a seguir: 

|Sistema operativo|Galeria de imagens do Azure|Implantação manual de VM|Integração do modelo de Azure Resource Manager|Provisionar pools de hosts no Azure Marketplace|Atualizações do agente de área de trabalho virtual do Windows|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 de várias sessões, versão 1903|Sim|Sim|Sim|Sim|Automático|
|Windows 10 de várias sessões, versão 1809|Sim|Sim|Não|Não|Automático|
|Windows 10 Enterprise, versão 1903|Sim|Sim|Sim|Sim|Automático|
|Windows 10 Enterprise, versão 1809|Sim|Sim|Não|Não|Automático|
|Windows 7 Enterprise|Sim|Sim|Não|Não|Manual|
|Windows Server 2019|Sim|Sim|Não|Não|Automático|
|Windows Server 2016|Sim|Sim|Sim|Sim|Automático|
|Windows Server 2012 R2|Sim|Sim|Não|Não|Automático|

## <a name="next-steps"></a>Passos seguintes

Para começar, você precisará criar um locatário. Para saber mais sobre como criar um locatário, continue para o tutorial de criação de locatário.

> [!div class="nextstepaction"]
> [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
