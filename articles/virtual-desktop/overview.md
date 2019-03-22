---
title: O que é a área de Trabalho Virtual do Windows? (pré-visualização) - Azure
description: Uma visão geral de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 679c7d3c84237747fd341f92c5dfcc0588d23be2
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338552"
---
# <a name="what-is-windows-virtual-desktop"></a>O que é a área de Trabalho Virtual do Windows? 

Agora disponível em pré-visualização pública, área de Trabalho Virtual do Windows é um serviço de Virtualização de área de trabalho e a aplicação que é executado na cloud.

Eis o que pode fazer ao executar o ambiente de Trabalho Virtual do Windows no Azure:

* Configurar uma implementação do Windows 10 de sessão multi que oferece uma completa com o Windows 10 com escalabilidade
* Virtualizar o Office 365 ProPlus e otimizá-lo para executar em cenários de virtual de vários utilizadores
* Fornecer áreas de trabalho virtuais do Windows 7 com atualizações de segurança expandida gratuita
* Traga as suas aplicações existentes Remote Desktop Services (RDS) e ambientes de trabalho do Windows Server e a qualquer computador
* Virtualização de ambientes de trabalho e aplicações
* Gerir áreas de trabalho Windows 10, Windows Server e Windows 7 e aplicações com uma experiência de gestão unificada

## <a name="key-capabilities"></a>Principais capacidades

Com o Desktop Virtual do Windows, pode configurar um ambiente escalonável e flexível:

* Crie um ambiente de Virtualização de ambiente de trabalho completo na sua subscrição do Azure sem ter de executar todos os servidores de gateway adicionais.
* Publica como conjuntos de anfitrião conforme necessário acomodar as diversas cargas de trabalho.
* Utilize a sua própria imagem para cargas de trabalho de produção ou teste a partir da galeria do Azure.
* Reduza os custos com recursos em pool, multi sessão. Com a novo Windows 10 Enterprise sessão multi capacidade exclusiva para a função de área de Trabalho Virtual do Windows e o anfitrião de sessões de ambiente de trabalho remoto (RDSH) no Windows Server, pode reduzir significativamente o número de máquinas virtuais e o sistema operativo (SO) sobrecarga ainda fornecendo os mesmos recursos aos seus utilizadores.
* Forneça uma propriedade individual por meio de áreas de trabalho pessoais (persistentes).

Pode implementar e gerir ambientes de trabalho virtuais:

* Utilizar as interfaces do Windows PowerShell de ambiente de Trabalho Virtual e REST para configurar os conjuntos de anfitrião, criar grupos de aplicações, atribuir utilizadores e publicar recursos
* Publicar aplicações remotas completo área de trabalho ou individuais de um conjunto de anfitrião único, crie grupos de aplicações individuais para diferentes conjuntos de utilizadores ou até mesmo atribuir utilizadores a vários grupos de aplicações para reduzir o número de imagens.
* Como gerir o seu ambiente, utilize o acesso delegado interno para atribuir funções e recolher diagnósticos para compreender os vários erros de configuração ou o utilizador.
* Utilize o novo serviço de diagnóstico para resolver problemas de erros.
* Gerir apenas a imagem e máquinas virtuais, não na infraestrutura. Não precisa de gerir pessoalmente as funções de ambiente de trabalho remoto, tal como faz com os serviços de ambiente de trabalho remoto, apenas as máquinas virtuais na sua subscrição do Azure.

Também pode atribuir e conectar os usuários a suas áreas de trabalho virtual:

* Uma vez atribuído, os utilizadores podem iniciar qualquer cliente de área de Trabalho Virtual do Windows para conectar os usuários a seus publicadas áreas de trabalho do Windows e aplicativos. Ligar a partir de qualquer dispositivo por meio de um aplicativo nativo no dispositivo ou o cliente de web HTML5 de ambiente de Trabalho Virtual do Windows.
* Estabeleça com segurança os usuários por meio de conexões inversas para o serviço, portanto, nunca mais terá de sair de nenhuma porta de entrada abrir.

## <a name="requirements"></a>Requisitos

Existem algumas coisas que precisa para configurar a área de Trabalho Virtual do Windows e ligar com êxito os usuários a seus ambientes de trabalho do Windows e aplicativos.

Em primeiro lugar, certifique-se de que tem as licenças apropriadas para os seus utilizadores com base no ambiente de trabalho e aplicações que planeia implementar:

|SO|Licença necessária|
|---|---|
|Sessão multi do Windows 10 Enterprise ou Windows 10. o único-sessão|Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5|
|Windows 7|Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS cliente de licença de acesso (CAL) com o Software Assurance|

A infraestrutura precisa dos seguintes pontos para oferecer suporte a área de Trabalho Virtual do Windows:

* An [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Um Windows Server Active Directory em sincronia com o Azure Active Directory. Isto pode ser ativado através de:
  * Azure AD Connect
  * Azure AD Domain Services
* Uma subscrição do Azure, que contém uma rede virtual que contém ou que está ligada ao Windows Server Active Directory
  
As máquinas virtuais do Azure que criar para a área de Trabalho Virtual do Windows tem de ser:

* [Padrão associado a um domínio](https://docs.microsoft.com/microsoft-desktop-optimization-pack/appv-v4/domain-joined-and-non-domain-joined-clients) ou [associados ao AD híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Máquinas virtuais não pode ser associado ao AD do Azure.
* Executar um dos seguintes imagens de SO suportadas:
  * Windows 10 Enterprise multi-session
  * Windows Server 2016

>[!NOTE]
>Se precisar de uma subscrição do Azure, pode [Inscreva-se numa avaliação gratuita de um mês](https://azure.microsoft.com/free/). Se estiver a utilizar o versão de avaliação gratuita do Azure, deve utilizar o Azure AD Domain Services para manter o Windows Server Active Directory sincronizado com o Azure Active Directory.

É composto por área de Trabalho Virtual do Windows a ambientes de trabalho do Windows e aplicações, enviar para utilizadores e a solução de gestão, que é hospedada como um serviço no Azure pela Microsoft. Durante a pré-visualização pública, aplicações e áreas de trabalho podem ser implementadas em máquinas virtuais (VMs) em qualquer região do Azure e a solução de gestão e os dados para estas VMs irão residir nos Estados Unidos (região E.u.a. Leste 2). Isso pode resultar em transferência de dados para os Estados Unidos, enquanto a testar o serviço durante a pré-visualização pública. Vamos começar aumentar horizontalmente a localização de dados e solução de gestão para todas as regiões do Azure a partir de disponibilidade geral.

Para um desempenho ideal, certifique-se de que a sua rede cumpre os seguintes requisitos:

* Latência de ida e volta (RTT) da rede do cliente para a região do Azure em que foram implementados conjuntos de anfitrião deve ser inferior a 150 ms.
* Tráfego de rede pode fluir fora dos limites de país, quando as VMs que alojam aplicações e áreas de trabalho ligar para o serviço de gestão.
* Para otimizar o desempenho da rede, recomendamos que VMs o anfitrião de sessões estão colocalizadas na mesma região do Azure como o serviço de gestão.

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory. Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização.

## <a name="next-steps"></a>Passos Seguintes

Para começar, terá de criar um inquilino. Para saber mais sobre como criar um inquilino, avance para o tutorial de criação do inquilino.

> [!div class="nextstepaction"]
> [Criar um inquilino na área de Trabalho Virtual do Windows](tenant-setup-azure-active-directory.md)
