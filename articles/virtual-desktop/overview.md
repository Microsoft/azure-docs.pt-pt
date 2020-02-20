---
title: O que é o Windows Virtual Desktop? - Azure
description: Uma visão geral do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/27/2020
ms.author: helohr
ms.openlocfilehash: 6cb8362b2de1f8345f693b00df021afe84f2d5a5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471776"
---
# <a name="what-is-windows-virtual-desktop"></a>O que é o Windows Virtual Desktop? 

O Windows Virtual Desktop é um serviço de virtualização de computadores e aplicativos que funciona na nuvem.

Eis o que pode fazer quando executa o Windows Virtual Desktop no Azure:

* Configurar uma implementação multi-sessão do Windows 10 que oferece um Windows 10 completo com escalabilidade
* Virtualize o Office 365 ProPlus e otimize-o para funcionar em cenários virtuais multiutilizadores
* Forneça ao Windows 7 ambientes de trabalho virtuais com atualizações de segurança estendidas gratuitas
* Traga os seus serviços de ambiente de trabalho remoto (RDS) e os desktops e aplicações do Windows Server para qualquer computador
* Virtualize tanto os desktops como as aplicações
* Gerencie os desktops e aplicações do Windows 10, Windows Server e Windows 7 com uma experiência de gestão unificada

## <a name="introductory-video"></a>Vídeo introdutório

Saiba mais sobre o Windows Virtual Desktop, por que é único, e quais as novidades neste vídeo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Para mais vídeos sobre o Windows Virtual Desktop, consulte a [nossa lista de reprodução.](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)

## <a name="key-capabilities"></a>Principais capacidades

Com o Windows Virtual Desktop, pode configurar um ambiente escalável e flexível:

* Crie um ambiente completo de virtualização no ambiente de trabalho na sua subscrição Azure sem ter de executar quaisquer servidores de gateway adicionais.
* Publique o número de piscinas hospedeiras que precisar para acomodar as suas diversas cargas de trabalho.
* Traga a sua própria imagem para cargas de trabalho de produção ou teste a partir da Galeria Azure.
* Reduzir os custos com recursos conjuntos e multi-sessões. Com a nova capacidade multi-sessão do Windows 10 Enterprise exclusiva para o Windows Virtual Desktop e Remote Desktop Session Host (RDSH) no Windows Server, pode reduzir consideravelmente o número de máquinas virtuais e sistema operativo (OS) enquanto ainda é fornecendo os mesmos recursos aos seus utilizadores.
* Fornecer propriedade individual através de ambientes de trabalho pessoais (persistentes).

Pode implementar e gerir ambientes de trabalho virtuais:

* Utilize as interfaces Windows Virtual Desktop PowerShell e REST para configurar as piscinas de anfitriões, criar grupos de aplicações, atribuir utilizadores e publicar recursos.
* Publique aplicativos remotos completos ou de desktop a partir de um único pool de anfitriões, crie grupos de aplicações individuais para diferentes conjuntos de utilizadores, ou até mesmo atribua utilizadores a vários grupos de aplicações para reduzir o número de imagens.
* À medida que gere o seu ambiente, utilize o acesso delegado incorporado para atribuir funções e colete diagnósticos para compreender várias configurações ou erros do utilizador.
* Utilize o novo serviço de Diagnóstico para resolver erros.
* Só gere a imagem e as máquinas virtuais, não a infraestrutura. Não é necessário gerir pessoalmente as funções de Desktop Remoto, como faz com os Serviços de Ambiente de Trabalho Remoto, apenas as máquinas virtuais da sua subscrição Azure.

Também pode atribuir e ligar os utilizadores aos seus desktops virtuais:

* Uma vez atribuídos, os utilizadores podem lançar qualquer cliente do Windows Virtual Desktop para ligar os utilizadores aos seus desktops e aplicações do Windows publicados. Conecte-se a partir de qualquer dispositivo através de uma aplicação nativa no seu dispositivo ou do cliente web DO Windows Virtual Desktop HTML5.
* Estabeleça os utilizadores de forma segura através de ligações inversas ao serviço, para que nunca tenha de deixar abertas quaisquer portas de entrada.

## <a name="requirements"></a>Requisitos

Existem algumas coisas que precisa para configurar o Windows Virtual Desktop e ligar com sucesso os seus utilizadores aos seus desktops e aplicações windows.

Planeamos adicionar suporte para os seguintes OSes, por isso certifique-se de que tem as [licenças apropriadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para os seus utilizadores com base no ambiente de trabalho e aplicações que planeia implementar:

|SO|Licença obrigatória|
|---|---|
|Windows 10 Enterprise multi-sessão ou Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licença de Acesso ao Cliente RDS (CAL) com Garantia de Software|

A sua infraestrutura necessita das seguintes coisas para suportar o Windows Virtual Desktop:

* Um [Diretório Ativo Azure](/azure/active-directory/)
* Um Diretório Ativo do Windows Server em sincronização com o Diretório Ativo do Azure. Pode configurá-lo com um dos seguintes:
  * Azure AD Connect (para organizações híbridas)
  * Serviços de Domínio Azure AD (para organizações híbridas ou na nuvem)
* Uma subscrição Azure que contém uma rede virtual que contém ou está ligada ao Diretório Ativo do Servidor do Windows
  
As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ser:

* [Standard-joined domínio](../active-directory-domain-services/active-directory-ds-comparison.md) ou [Hybrid AD-joined](../active-directory/devices/hybrid-azuread-join-plan.md). As máquinas virtuais não podem ser ad-joined Azure.
* Executando uma das [seguintes imagens de SO suportadas.](#supported-virtual-machine-os-images)

>[!NOTE]
>Se precisar de uma subscrição Azure, pode [inscrever-se para um teste gratuito de um mês.](https://azure.microsoft.com/free/) Se estiver a utilizar a versão gratuita do Azure, deverá utilizar os Serviços de Domínio Azure AD para manter o diretório Ativo do Windows Server em sintonia com o Diretório Ativo do Azure.

As máquinas virtuais do Azure que você cria para a área de trabalho virtual do Windows devem ter acesso às seguintes URLs:

|Endereço|Porta de saída|Objetivo|
|---|---|---|
|*.wvd.microsoft.com|Porta TCP 443|Tráfego de serviço|
|*.blob.core.windows.net|Porta TCP 443|Agente, atualizações de pilha de SXS e tráfego de agente|
|*.core.windows.net|Porta TCP 443|Tráfego do agente|
|*.servicebus.windows.net|Porta TCP 443|Tráfego do agente|
|prod.warmpath.msftcloudes.com|Porta TCP 443|Tráfego do agente|
|catalogartifact.azureedge.net|Porta TCP 443|Azure Marketplace|
|kms.core.windows.net|Porta TCP 1688|Ativação do Windows 10|

>[!IMPORTANT]
>A abertura destes URLs é essencial para uma implementação fiável do Windows Virtual Desktop. O bloqueio do acesso a estes URLs não é suportado e afetará a funcionalidade do serviço. Estes URLs apenas correspondem a sites e recursos do Windows Virtual Desktop, e não incluem URLs para outros serviços como o Azure Ative Directory.

>[!NOTE]
>O Windows Virtual Desktop atualmente não tem uma lista de intervalos de endereços IP que pode whitelist para permitir o tráfego de rede. Só apoiamos urLs específicos de whitelisting neste momento.
>
>Você deve usar o caractere curinga (*) para URLs que envolvem tráfego de serviço. Se você preferir não usar * para o tráfego relacionado ao agente, veja como encontrar as URLs sem curingas:
>
>1. Registre suas máquinas virtuais no pool de hosts da área de trabalho virtual do Windows.
>2. Abra **o espectador do Evento** e navegue para **registos do Windows** > **Aplicação** > **WVD-Agent** e procure o ID do evento 3702.
>3. Lista de permissões as URLs encontradas na ID do evento 3702. As URLs em ID de evento 3702 são específicas da região. Você precisará repetir o processo de lista de permissões com as URLs relevantes para cada região em que você deseja implantar suas máquinas virtuais.

O Windows Virtual Desktop compreende os desktops e aplicações do Windows que entrega aos utilizadores e a solução de gestão, que é hospedada como um serviço no Azure pela Microsoft. Os desktops e aplicações podem ser implantados em máquinas virtuais (VMs) em qualquer região do Azure, e a solução de gestão e dados para estes VMs residem nos Estados Unidos. Isto pode resultar na transferência de dados para os Estados Unidos.

Para um desempenho ótimo, certifique-se de que a sua rede satisfaz os seguintes requisitos:

* A latência de ida e volta (RTT) da rede do cliente para a região de Azure, onde foram implantadas piscinas de acolhimento, deve ser inferior a 150 ms.
* O tráfego de rede pode fluir para fora das fronteiras país/região quando os VMs que acolhem desktops e aplicações se ligam ao serviço de gestão.
* Para otimizar o desempenho da rede, recomendamos que os VMs do anfitrião da sessão estejam localizados na mesma região do Azure que o serviço de gestão.

## <a name="supported-remote-desktop-clients"></a>Clientes de ambiente de trabalho remoto suportados

Os seguintes clientes do Ambiente de Trabalho Remoto suportam o Windows Virtual Desktop:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Pré-visualização)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Imagens de Os da máquina virtual suportada

O Windows Virtual Desktop suporta as seguintes imagens do sistema operativo X64:

* Windows 10 Enterprise multi-sessão, versão 1809 ou posterior
* Windows 10 Enterprise, versão 1809 ou mais tarde
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

O Windows Virtual Desktop não suporta imagens do sistema operativo X86 (32 bits), Windows 10 Enterprise N ou Windows 10 Enterprise KN. O Windows 7 também não suporta quaisquer soluções de perfil baseadas em VHD ou VHDX alojadas no Armazenamento Azure gerido devido a uma limitação de tamanho do setor.

As opções disponíveis de automação e implementação dependem do SISTEMA e versão que escolher, como mostra a seguinte tabela: 

|Sistema operativo|Galeria de Imagem Azure|Implantação manual de VM|Integração do modelo do Gestor de Recursos Azure|Piscinas de anfitriões de provisão no Azure Marketplace|Atualizações do Windows Virtual Desktop Agent|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multi-sessão, versão 1903|Sim|Sim|Sim|Sim|Automático|
|Windows 10 multi-sessão, versão 1809|Sim|Sim|Não|Não|Automático|
|Windows 10 Enterprise, versão 1903|Sim|Sim|Sim|Sim|Automático|
|Windows 10 Enterprise, versão 1809|Sim|Sim|Não|Não|Automático|
|Windows 7 Enterprise|Sim|Sim|Não|Não|Manual|
|Windows Server 2019|Sim|Sim|Não|Não|Automático|
|Windows Server 2016|Sim|Sim|Sim|Sim|Automático|
|Windows Server 2012 R2|Sim|Sim|Não|Não|Automático|

## <a name="next-steps"></a>Passos seguintes

Para começar, terá de criar um inquilino. Para saber mais sobre como criar um inquilino, continue para o tutorial de criação de inquilinos.

> [!div class="nextstepaction"]
> [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
