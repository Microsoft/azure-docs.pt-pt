---
title: QAF multissessões do Windows 10 Enterprise - Azure
description: Perguntas e boas práticas frequentes para utilizar a multi-sessão do Windows 10 Enterprise para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4301aaed0152e07eb2a941b56637239b66c33878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127349"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>FAQ de multissessões do Windows 10 Enterprise

Este artigo responde a perguntas frequentes e explica as melhores práticas para a multi-sessão do Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>O que é a multi-sessão do Windows 10 Enterprise?

A multi-sessão do Windows 10 Enterprise, anteriormente conhecida como Windows 10 Enterprise para desktops virtuais (EVD), é um novo Anfitrião de Sessão de Desktop Remoto que permite múltiplas sessões interativas simultâneas. Anteriormente, apenas o Windows Server poderia fazê-lo. Esta capacidade proporciona aos utilizadores uma experiência familiar do Windows 10, enquanto o TI pode beneficiar das vantagens de custos de várias sessões e utilizar o licenciamento existente por utilizador do Windows em vez de licenças de acesso ao cliente RDS (CALs). Para obter mais informações sobre licenças e preços, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quantos utilizadores podem simultaneamente ter uma sessão interativa na multi-sessão do Windows 10 Enterprise?

Quantas sessões interativas que podem ser ativas ao mesmo tempo depende dos recursos de hardware do seu sistema (vCPU, memória, disco e vGPU), como os seus utilizadores usam as suas apps enquanto assinam uma sessão e quão pesada é a carga de trabalho do seu sistema. Sugerimos que valide o desempenho do seu sistema para entender quantos utilizadores pode ter na multi-sessão do Windows 10 Enterprise. Para saber mais, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Porque é que a minha aplicação reporta o Windows 10 Enterprise multi-sessão como um sistema operativo server?

A multi-sessão do Windows 10 Enterprise é uma edição virtual do Windows 10 Enterprise. Uma das diferenças é que este sistema operativo (OS) reporta o [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) como tendo um valor de 3, o mesmo valor que o Windows Server. Esta propriedade mantém o OS compatível com as ferramentas de gestão RDSH existentes, aplicações multi-sessões conscientes rdsh e, na maioria, otimizações de desempenho do sistema de baixo nível para ambientes RDSH. Alguns instaladores de aplicações podem bloquear a instalação em várias sessões do Windows 10, dependendo se detetam o ProductType definido para o Cliente. Se a sua aplicação não instalar, contacte o fornecedor da sua aplicação para obter uma versão atualizada. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso executar o Windows 10 Enterprise multi-sessão no local?

A multi-sessão do Windows 10 Enterprise não pode funcionar em ambientes de produção no local porque está otimizado para o serviço de desktop virtual windows para o Azure. É contra o acordo de licenciamento executar a multi-sessão do Windows 10 Enterprise fora do Azure para fins de produção. A multi-sessão da Empresa Windows 10 não será ativada contra os Serviços de Gestão de Chaves (KMS) no local.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Como personalizar a imagem multi-sessão do Windows 10 Enterprise para a minha organização?

Pode iniciar uma máquina virtual (VM) em Azure com a multi-sessão do Windows 10 Windows 10 Enterprise e personalizá-la instalando aplicações LOB, sysprep/generalizar e, em seguida, criar uma imagem usando o portal Azure.  
 
Para começar, crie um VM em Azure com a multi-sessão do Windows 10 Windows 10 Enterprise. Em vez de iniciar o VM em Azure, pode baixar o VHD diretamente. Depois disso, poderá utilizar o VHD que descarregou para criar um novo VM de Geração 1 num PC Windows 10 com hiper-V ativado.

Personalize a imagem às suas necessidades instalando aplicações LOB e sysprep a imagem. Quando terminar de personalizar, faça o upload da imagem para Azure com o VHD no interior. Depois disso, obtenha o Windows Virtual Desktop do Azure Marketplace e use-o para implementar uma nova piscina de anfitriões com a imagem personalizada.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Como posso gerir a multi-sessão do Windows 10 Enterprise após a implementação?

Pode utilizar qualquer ferramenta de configuração suportada, mas recomendamos a versão 1906 do Gestor de Configuração, pois suporta a multi-sessão do Windows 10 Enterprise. Estamos neste momento a trabalhar no suporte da Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Pode o Windows 10 Enterprise multi-sessão ser azure Ative Directory (AD)-joined?

A multi-sessão do Windows 10 Enterprise é suportada atualmente para ser a adesão híbrida azure. Depois de a multi-sessão do Windows 10 Enterprise ser unida pelo domínio, utilize o Objeto político de grupo existente para permitir o registo da AD Azure. Para mais informações, consulte [Planize o seu diretório ativo híbrido Azure a aderir à implementação](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Onde posso encontrar a imagem multi-sessão do Windows 10 Enterprise?

A multi-sessão do Windows 10 Enterprise está na galeria Azure. Para encontrá-lo, navegue para o portal Azure e procure o lançamento da Empresa Windows 10 para desktops virtuais. Para obter uma imagem integrada no Office Pro Plus, vá ao portal Azure e procure o Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Que imagem multissessão do Windows 10 Enterprise devo usar?

A galeria Azure tem vários lançamentos, incluindo a multi-sessão do Windows 10 Enterprise, a versão 1809 e a multi-sessão do Windows 10 Enterprise, versão 1903. Recomendamos a utilização da versão mais recente para melhorar o desempenho e a fiabilidade.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quais as versões multissessões do Windows 10 Enterprise?

As multissessões do Windows 10 Enterprise, as versões 1809 e posteriormente são suportadas e estão disponíveis na galeria Azure. Estes lançamentos seguem a mesma política de suporte de ciclo de vida que o Windows 10 Enterprise, o que significa que o lançamento da primavera é suportado por 18 meses e o lançamento do outono por 30 meses.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Que solução de gestão de perfis devo utilizar para a multi-sessão do Windows 10 Enterprise?

Recomendamos que utilize recipientes de perfil FSLogix quando configurar o Windows 10 Enterprise em ambientes não persistentes ou outros cenários que necessitem de um perfil centralmente armazenado. O FSLogix garante que o perfil do utilizador está disponível e atualizado para cada sessão de utilizador. Recomendamos também que utilize o seu recipiente de perfil FSLogix para armazenar um perfil de utilizador em qualquer partilha SMB com permissões adequadas, mas pode armazenar os perfis dos utilizadores no armazenamento de blob da página Azure, se necessário. Os utilizadores do Windows Virtual Desktop podem utilizar o FSLogix sem custos adicionais.
 
Para obter mais informações sobre como configurar um recipiente de perfil FSLogix, consulte [Configurar o recipiente de perfil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Que licença preciso para aceder a várias sessões do Windows 10 Enterprise?

Para obter uma lista completa das licenças aplicáveis, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Porque é que as minhas aplicações desaparecem depois de eu assinar?

Isto acontece porque está a utilizar a multi-sessão do Windows 10 Enterprise com uma solução de gestão de perfis como o FSLogix. A sua solução de administrador ou perfil configurou o seu sistema para eliminar os perfis do utilizador quando os utilizadores assinam. Esta configuração significa que quando o seu sistema elimina o seu perfil de utilizador após a sua assinatura, também remove quaisquer aplicações instaladas durante a sua sessão. Se quiser manter as aplicações instaladas, terá de pedir ao seu administrador que forme estas aplicações para todos os utilizadores no ambiente de ambiente de trabalho virtual do Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Como posso garantir que as aplicações não desaparecem quando os utilizadores assinam?

A maioria dos ambientes virtualizados são configurados por padrão para impedir que os utilizadores instalem aplicações adicionais nos seus perfis. Se pretender certificar-se de que uma aplicação não desaparece quando o utilizador assina fora do Windows Virtual Desktop, tem de fornecer essa aplicação para todos os perfis de utilizador no seu ambiente. Para obter mais informações sobre o fornecimento de aplicações, consulte estes recursos:

- [Publique aplicações incorporadas no Windows Virtual Desktop](publish-apps.md)
- [Pacote de aplicativo DISM que serve opções de linha de comando](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Pacote Add-AppxProvisioned](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Como posso garantir que os utilizadores não descarregam e instalam aplicações a partir da Microsoft Store?

Pode desativar a aplicação da Microsoft Store para garantir que os utilizadores não descarregam aplicações extra para além das aplicações que já disponibilizou para as mesmas.

Para desativar a aplicação Loja:

1. Criar uma nova Política de Grupo.
2. Selecione**modelos administrativos** > de **configuração** > de computador**Componentes do Windows**.
3. Selecione **Loja**.
4. Selecione **Aplicação da loja**.
5. Selecione **Desativar**e, em seguida, selecione **OK**.
6. Selecione **Aplicar**.
 
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Windows Virtual Desktop e windows 10 Enterprise multi-sessão:

- Leia a documentação de [pré-visualização virtual do Windows](overview.md)
- Visite o nosso [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurar a implementação do seu Ambiente de Trabalho Virtual windows com os [tutoriais do Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
