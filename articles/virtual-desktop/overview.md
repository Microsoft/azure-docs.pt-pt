---
title: O que é o Windows Virtual Desktop? - Azure
description: Uma visão geral do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ab1d0318464f6b44e1f46bd30dc76272584fde64
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929830"
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
* Reduzir os custos com recursos conjuntos e multi-sessões. Com a nova capacidade multi-sessão do Windows 10 Enterprise exclusiva para o Windows Virtual Desktop e remote Desktop Session Host (RDSH) no Windows Server, pode reduzir consideravelmente o número de máquinas virtuais e sistema operativo (OS) sobrecarga, fornecendo ainda os mesmos recursos aos seus utilizadores.
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
|Windows 10 Enterprise multi-sessão ou Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Janelas E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Janelas E3, E5, A3, A5|
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

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ter acesso aos seguintes URLs:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Atualizações de pilhas de agente e SXS|AzureCloud|
|*.core.windows.net|443|Tráfego de agente|AzureCloud|
|*.servicebus.windows.net|443|Tráfego de agente|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Tráfego de agente|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Ativação do Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Suporte do portal Azure|AzureCloud|

>[!IMPORTANT]
>O Windows Virtual Desktop suporta agora a etiqueta FQDN. Para mais informações, consulte utilize firewall [Azure para proteger as implementações](../firewall/protect-windows-virtual-desktop.md)de ambiente de trabalho virtual da janela .
>
>Recomendamos que utilize etiquetas FQDN ou etiquetas de serviço em vez de URLs para evitar problemas de serviço. Os URLs e etiquetas listados apenas correspondem a sites e recursos do Windows Virtual Desktop. Não incluem URLs para outros serviços como o Azure Ative Directory.

A tabela a seguir lista URLs opcionais a que as suas máquinas virtuais Azure possam ter acesso:

|Endereço|Porta TCP de saída|Objetivo|Etiqueta de serviço|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticação aos Serviços Online Da MS|Nenhum|
|*.events.data.microsoft.com|443|Serviço de Telemetria|Nenhum|
|www.msftconnecttest.com|443|Deteta se o SO está ligado à internet|Nenhum|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nenhum|
|login.windows.net|443|Login para MS Serviços Online, Office 365|Nenhum|
|*.sfx.ms|443|Atualizações para software de cliente OneDrive|Nenhum|
|*.digicert.com|443|Verificação de revogação do certificado|Nenhum|


>[!NOTE]
>O Windows Virtual Desktop atualmente não tem uma lista de intervalos de endereços IP que pode whitelist para permitir o tráfego de rede. Só apoiamos urLs específicos de whitelisting neste momento.
>
>Para obter uma lista de URLs relacionados com o Office, incluindo URLs relacionados com o Diretório Ativo Azure necessários, consulte os intervalos de [endereços office 365 URLs e IP](/office365/enterprise/urls-and-ip-address-ranges).
>
>Deve utilizar o caracteres wildcard (*) para URLs que envolvam tráfego de serviço. Se preferir não usar * para tráfego relacionado com agentes, eis como encontrar os URLs sem wildcards:
>
>1. Registe as suas máquinas virtuais na piscina de anfitriões do Windows Virtual Desktop.
>2. Abra **o espectador do Evento** e navegue para windows **logs** > **Application** > **WVD-Agent** e procure o Id do evento 3702.
>3. Whitelist os URLs que encontra no âmbito do Id 3702 do Evento. Os URLs ao abrigo do Id do evento 3702 são específicos da região. Terá de repetir o processo de listagem de whitelisting com os URLs relevantes para cada região onde pretende implantar as suas máquinas virtuais.

O Windows Virtual Desktop compreende os desktops e aplicações do Windows que entrega aos utilizadores e a solução de gestão, que é hospedada como um serviço no Azure pela Microsoft. Os desktops e aplicações podem ser implantados em máquinas virtuais (VMs) em qualquer região do Azure, e a solução de gestão e dados para estes VMs residem nos Estados Unidos. Isto pode resultar na transferência de dados para os Estados Unidos.

Para um desempenho ótimo, certifique-se de que a sua rede satisfaz os seguintes requisitos:

* A latência de ida e volta (RTT) da rede do cliente para a região de Azure, onde foram implantadas piscinas de acolhimento, deve ser inferior a 150 ms.
* O tráfego de rede pode fluir para fora das fronteiras país/região quando os VMs que acolhem desktops e aplicações se ligam ao serviço de gestão.
* Para otimizar o desempenho da rede, recomendamos que os VMs do anfitrião da sessão estejam localizados na mesma região do Azure que o serviço de gestão.

## <a name="supported-remote-desktop-clients"></a>Clientes de ambiente de trabalho remoto suportados

Os seguintes clientes do Ambiente de Trabalho Remoto suportam o Windows Virtual Desktop:

* [Ambiente de trabalho do Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Pré-visualização)](connect-android.md)

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta o cliente remoteApp e Desktop Connections (RADC) ou o cliente de Ligação remota de ambiente de trabalho (MSTSC).

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta atualmente o cliente Remote Desktop a partir da Windows Store. O apoio a este cliente será adicionado num futuro lançamento.

Os clientes do Ambiente de Trabalho Remoto devem ter acesso aos seguintes URLs:

|Endereço|Porta TCP de saída|Objetivo|Cliente(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|Todos|
|*.servicebus.windows.net|443|Dados de resolução de problemas|Todos|
|go.microsoft.com|443|Microsoft FWLinks|Todos|
|aka.ms|443|Encurtador de URL da Microsoft|Todos|
|docs.microsoft.com|443|Documentação|Todos|
|privacy.microsoft.com|443|Declaração de privacidade|Todos|
|query.prod.cms.rt.microsoft.com|443|Atualizações de clientes|Ambiente de Trabalho do Windows|

>[!IMPORTANT]
>A abertura destes URLs é essencial para uma experiência de cliente fiável. O bloqueio do acesso a estes URLs não é suportado e afetará a funcionalidade do serviço. Estes URLs apenas correspondem aos sites e recursos dos clientes, e não incluem URLs para outros serviços como o Azure Ative Directory.

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

|Sistema operativo|Galeria de Imagem Azure|Implantação manual de VM|Integração do modelo do Gestor de Recursos Azure|Piscinas de anfitriões de provisão no Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 multi-sessão, versão 1903|Sim|Sim|Sim|Sim|
|Windows 10 multi-sessão, versão 1809|Sim|Sim|Não|Não|
|Windows 10 Enterprise, versão 1903|Sim|Sim|Sim|Sim|
|Windows 10 Enterprise, versão 1809|Sim|Sim|Não|Não|
|Windows 7 Enterprise|Sim|Sim|Não|Não|
|Windows Server 2019|Sim|Sim|Não|Não|
|Windows Server 2016|Sim|Sim|Sim|Sim|
|Windows Server 2012 R2|Sim|Sim|Não|Não|

## <a name="next-steps"></a>Passos seguintes

Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019, pode começar com o nosso tutorial na [Create a tenant in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Se estiver a utilizar o lançamento do Windows Virtual Desktop Spring 2020, terá de criar uma piscina de anfitriões. Dirija-se ao seguinte tutorial para começar.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitriões com o portal do Azure](create-host-pools-azure-marketplace.md)
