---
title: O que é o Windows Virtual Desktop? - Azure
description: Uma visão geral do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6ff1f20314d79c507c696325d19c83e10ec4513f
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386579"
---
# <a name="what-is-windows-virtual-desktop"></a>O que é o Windows Virtual Desktop?

O Windows Virtual Desktop é um serviço de virtualização de computadores de secretária e aplicações que funciona na nuvem.

Eis o que pode fazer quando executar o Windows Virtual Desktop no Azure:

* Configurar uma implementação multi-sessão do Windows 10 que oferece um Windows 10 completo com escalabilidade
* Virtualize as Aplicações microsoft 365 para empresa e otimize-as para executar em cenários virtuais multiutilizadores
* Fornecer desktops virtuais do Windows 7 com atualizações de segurança estendidas gratuitas
* Traga os seus serviços de ambiente de trabalho remoto (RDS) e windows server e aplicativos para qualquer computador
* Virtualize tanto desktops como aplicativos
* Gerir computadores e aplicações windows 10, Windows Server e Windows 7 com uma experiência de gestão unificada

## <a name="introductory-video"></a>Vídeo introdutório

Saiba mais sobre o Windows Virtual Desktop, porque é único, e quais as novidades neste vídeo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Para mais vídeos sobre o Windows Virtual Desktop, consulte [a nossa lista de reprodução](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Principais capacidades

Com o Windows Virtual Desktop, pode configurar um ambiente escalável e flexível:

* Crie um ambiente completo de virtualização no ambiente de trabalho na subscrição do Azure sem ter de executar quaisquer servidores de gateway adicionais.
* Publique quantas piscinas de anfitrião precisar para acomodar as suas diversas cargas de trabalho.
* Traga a sua própria imagem para cargas de trabalho de produção ou teste na Galeria Azure.
* Reduzir custos com recursos multi-sessão em conjunto. Com a nova capacidade multi-sessão do Windows 10 Enterprise exclusiva para o Windows Virtual Desktop e o assistente de sessão de desktop remoto (RDSH) no Windows Server, pode reduzir consideravelmente o número de máquinas virtuais e de sobrecarga do sistema operativo (OS) ao mesmo tempo que fornece os mesmos recursos aos seus utilizadores.
* Fornecer propriedade individual através de ambientes de trabalho pessoais (persistentes).

Pode implementar e gerir ambientes de trabalho virtuais:

* Utilize as interfaces Virtual Desktop PowerShell e REST do Windows para configurar as piscinas hospedeiras, criar grupos de aplicações, atribuir utilizadores e publicar recursos.
* Publique aplicações remotas completas ou individuais a partir de um único conjunto de anfitriões, crie grupos de aplicações individuais para diferentes conjuntos de utilizadores ou até atribua utilizadores a vários grupos de aplicações para reduzir o número de imagens.
* À medida que gere o seu ambiente, utilize o acesso delegado incorporado para atribuir funções e recolher diagnósticos para compreender várias configurações ou erros do utilizador.
* Utilize o novo serviço de Diagnóstico para resolver erros.
* Só gere a imagem e as máquinas virtuais, não a infraestrutura. Não precisa de gerir pessoalmente as funções de Desktop Remoto, como faz com os Serviços de Desktop Remoto, apenas as máquinas virtuais da sua subscrição Azure.

Também pode atribuir e ligar os utilizadores aos seus ambientes de trabalho virtuais:

* Uma vez atribuídos, os utilizadores podem lançar qualquer cliente virtual do Windows desktop para ligar os utilizadores aos seus desktops e aplicações do Windows publicados. Conecte-se a partir de qualquer dispositivo através de uma aplicação nativa no seu dispositivo ou do cliente web VIRTUAL Desktop HTML5 do Windows.
* Estabeleça de forma segura os utilizadores através de ligações inversas ao serviço, para que nunca tenha de deixar portas de entrada abertas.

## <a name="requirements"></a>Requirements

Há algumas coisas que precisa para configurar o Windows Virtual Desktop e ligar com sucesso os seus utilizadores aos seus desktops e aplicações windows.

Pretendemos adicionar suporte para os seguintes OSes, por isso certifique-se de que tem as [licenças apropriadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para os seus utilizadores com base no ambiente de trabalho e aplicações que pretende implementar:

|SO|Licença requerida|
|---|---|
|Windows 10 Enterprise multi-sessão ou Empresa Do Windows 10|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Janelas E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Janelas E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licença de Acesso ao Cliente RDS (CAL) com Garantia de Software|

A sua infraestrutura necessita das seguintes coisas para suportar o Windows Virtual Desktop:

* Um [Diretório Ativo Azure](/azure/active-directory/)
* Um Diretório Ativo do Servidor do Windows em sincronização com o Azure Ative Directory. Pode configurar isto com uma das seguintes:
  * Azure AD Connect (para organizações híbridas)
  * Serviços de Domínio Azure AD (para organizações híbridas ou em nuvem)
* Uma subscrição do Azure que contém uma rede virtual que contém ou está ligada ao Diretor ative do Windows Server

As máquinas virtuais Azure que cria para o Windows Virtual Desktop devem ser:

* [AD de domínio padrão](../active-directory-domain-services/active-directory-ds-comparison.md) ou [híbrido.](../active-directory/devices/hybrid-azuread-join-plan.md) Máquinas virtuais não podem ser adá-la a Azure.
* Executando uma das [seguintes imagens de SO suportadas.](#supported-virtual-machine-os-images)

>[!NOTE]
>Se precisar de uma subscrição do Azure, pode [inscrever-se para um teste gratuito de um mês.](https://azure.microsoft.com/free/) Se estiver a utilizar a versão experimental gratuita do Azure, deverá utilizar os Serviços de Domínio Azure AD para manter o seu Windows Server Ative Ative Directy em sintonia com o Azure Ative Directory.

Para obter uma lista de URLs, deverá desbloquear para que a sua implementação virtual do Windows para trabalhar como pretendido, consulte a nossa [lista de URL seguro](safe-url-list.md).

O Windows Virtual Desktop compreende os desktops e aplicações do Windows que entrega aos utilizadores e a solução de gestão, que é hospedada como um serviço no Azure pela Microsoft. Os desktops e apps podem ser implantados em máquinas virtuais (VMs) em qualquer região de Azure, e a solução de gestão e dados para estes VMs vão residir nos Estados Unidos. Isto pode resultar na transferência de dados para os Estados Unidos.

Para um melhor desempenho, certifique-se de que a sua rede cumpre os seguintes requisitos:

* A latência de ida e volta (RTT) da rede do cliente para a região de Azure, onde foram implantadas piscinas de acolhimento, deve ser inferior a 150 ms.
* O tráfego de rede pode fluir fora das fronteiras país/região quando os VMs que acolhem desktops e aplicações se ligam ao serviço de gestão.
* Para otimizar o desempenho da rede, recomendamos que os VM do anfitrião da sessão sejam collocados na mesma região de Azure que o serviço de gestão.

## <a name="supported-remote-desktop-clients"></a>Clientes de desktop remoto suportados

Os seguintes clientes de ambiente de trabalho remoto suportam o Windows Virtual Desktop:

* [Windows Desktop](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta o cliente RemoteApp e Desktop Connections (RADC) ou o cliente Remote Desktop Connection (MSTSC).

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta atualmente o cliente remote desktop a partir da Windows Store. O suporte a este cliente será adicionado num lançamento futuro.

Para saber mais sobre URLs tem de desbloquear para utilizar os Clientes Remotos, consulte a [lista de URL seguro](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Imagens de SISTEMA de máquinas virtuais suportadas

O Windows Virtual Desktop suporta as seguintes imagens do sistema operativo x64:

* Windows 10 Enterprise multi-sessão, versão 1809 ou posterior
* Windows 10 Enterprise, versão 1809 ou posterior
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

O Windows Virtual Desktop não suporta imagens do sistema operativo X86 (32 bits), Do Windows 10 Enterprise N ou do Sistema operativo Kn do Windows 10. O Windows 7 também não suporta nenhuma solução de perfil baseada em VHD ou VHDX hospedada no Azure Storage gerido devido a uma limitação de tamanho do setor.

As opções de automação e implantação disponíveis dependem do sistema operativo e da versão escolhidas, como mostra o seguinte quadro:

|Sistema operativo|Galeria de Imagens Azure|Implantação manual de VM|Integração do modelo do Gestor de Recursos Azure|Provisão de piscinas de acolhimento no Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 multi-sessão, versão 1903|Sim|Sim|Sim|Sim|
|Windows 10 multi-sessão, versão 1809|Sim|Sim|Não|Não|
|Windows 10 Enterprise, versão 1903|Sim|Sim|Sim|Sim|
|Windows 10 Enterprise, versão 1809|Sim|Sim|Não|Não|
|Windows 7 Enterprise|Sim|Sim|Não|Não|
|Windows Server 2019|Sim|Sim|Não|Não|
|Windows Server 2016|Sim|Sim|Sim|Sim|
|Windows Server 2012 R2|Sim|Sim|Não|Não|

## <a name="next-steps"></a>Próximos passos

Se estiver a utilizar o Windows Virtual Desktop (clássico), pode começar com o nosso tutorial na [Create a tenant in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Se estiver a utilizar o Ambiente de Trabalho Virtual do Windows com integração do Azure Resource Manager, terá de criar uma piscina de anfitriões. Dirija-se ao seguinte tutorial para começar.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitriões com o portal do Azure](create-host-pools-azure-marketplace.md)
