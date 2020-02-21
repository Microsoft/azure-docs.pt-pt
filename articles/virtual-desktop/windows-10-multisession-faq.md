---
title: Perguntas frequentes sobre várias sessões do Windows 10 Enterprise – Azure
description: Perguntas frequentes e práticas recomendadas para usar o Windows 10 Enterprise Multi-Session para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
ms.openlocfilehash: 426ca10893e6858722b58422400582e4940287e2
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484609"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>FAQ de multissessões do Windows 10 Enterprise

Este artigo responde a perguntas frequentes e explica as melhores práticas para a multi-sessão do Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>O que é o Windows 10 Enterprise Multi-Session?

A multi-sessão do Windows 10 Enterprise, anteriormente conhecida como Windows 10 Enterprise para desktops virtuais (EVD), é um novo Anfitrião de Sessão de Desktop Remoto que permite múltiplas sessões interativas simultâneas. Anteriormente, apenas o Windows Server poderia fazê-lo. Esse recurso oferece aos usuários uma experiência familiar do Windows 10, enquanto pode se beneficiar das vantagens de custo de várias sessões e usar o licenciamento do Windows por usuário existente em vez de CALs (licenças de acesso para cliente) do RDS. Para obter mais informações sobre licenças e preços, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quantos usuários podem simultaneamente ter uma sessão interativa no Windows 10 Enterprise Multi-Session?

Quantas sessões interativas podem estar ativas ao mesmo tempo dependem dos recursos de hardware do seu sistema (vCPU, memória, disco e vGPU), como os usuários usam seus aplicativos enquanto estiverem conectados a uma sessão e a sua carga de trabalho do sistema. Sugerimos que você valide o desempenho do sistema para entender quantos usuários você pode ter no Windows 10 Enterprise Multi-Session. Para saber mais, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Por que meu aplicativo relata o Windows 10 Enterprise Multi-Session como um sistema operacional de servidor?

O Windows 10 Enterprise Multi-Session é uma edição virtual do Windows 10 Enterprise. Uma das diferenças é que este sistema operativo (OS) reporta o [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) como tendo um valor de 3, o mesmo valor que o Windows Server. Essa propriedade mantém o sistema operacional compatível com as ferramentas de gerenciamento de RDSH existentes, aplicativos de regestão de várias sessões de RDSH e, principalmente, otimizações de desempenho do sistema de nível inferior para ambientes de RDSH. Alguns instaladores de aplicativo podem bloquear a instalação em várias sessões do Windows 10, dependendo se detectarem o ProductType está definido como cliente. Se seu aplicativo não for instalado, entre em contato com o fornecedor do aplicativo para obter uma versão atualizada. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso executar o Windows 10 Enterprise com várias sessões no local?

O Windows 10 Enterprise Multi-Session não pode ser executado em ambientes de produção locais porque ele é otimizado para o serviço de área de trabalho virtual do Windows para Azure. Ele está relacionado ao contrato de licenciamento para executar o Windows 10 Enterprise Multi-Session fora do Azure para fins de produção. O Windows 10 Enterprise Multi-Session não será ativado nos serviços de gerenciamento de chaves (KMS) locais.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Como fazer personalizar a imagem de várias sessões do Windows 10 Enterprise para minha organização?

Você pode iniciar uma VM (máquina virtual) no Azure com Windows 10 Windows 10 Enterprise Multi-Session e personalizá-la Instalando aplicativos LOB, Sysprep/generalizar e, em seguida, criar uma imagem usando o portal do Azure.  
 
Para começar, crie uma VM no Azure com Windows 10 Windows 10 Enterprise Multi-Session. Em vez de iniciar a VM no Azure, você pode baixar o VHD diretamente. Depois disso, você poderá usar o VHD que baixou para criar uma nova VM de geração 1 em um PC com Windows 10 com o Hyper-V habilitado.

Personalize a imagem para suas necessidades Instalando aplicativos LOB e Sysprep a imagem. Quando você terminar de personalizar, carregue a imagem no Azure com o VHD dentro. Depois disso, obtenha o Windows Virtual Desktop do Azure Marketplace e use-o para implementar uma nova piscina de anfitriões com a imagem personalizada.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Como fazer gerenciar o Windows 10 Enterprise Multi-Session após a implantação?

Você pode usar qualquer ferramenta de configuração com suporte, mas é recomendável Configuration Manager a versão 1906 porque ela dá suporte a Windows 10 Enterprise Multi-Session. No momento, estamos trabalhando no suporte Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>O ingresso no Windows 10 Enterprise Multi-Session Azure Active Directory (AD) pode ser ingressado?

O Windows 10 Enterprise Multi-Session tem suporte no momento para ser ingressado no Azure AD híbrido. Depois que o Windows 10 Enterprise Multi-Session estiver ingressado no domínio, use o objeto Política de Grupo existente para habilitar o registro do Azure AD. Para mais informações, consulte [Planize o seu diretório ativo híbrido Azure a aderir à implementação](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Onde posso encontrar a imagem de várias sessões do Windows 10 Enterprise?

O Windows 10 Enterprise Multi-Session está na galeria do Azure. Para encontrá-lo, navegue até a portal do Azure e procure a versão Windows 10 Enterprise para áreas de trabalho virtuais. Para uma imagem integrada com o Office Pro Plus, vá para a portal do Azure e procure Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Qual imagem de várias sessões do Windows 10 Enterprise devo usar?

A galeria do Azure tem várias versões, incluindo Windows 10 Enterprise Multi-Session, versão 1809 e Windows 10 Enterprise Multi-Session, versão 1903. É recomendável usar a versão mais recente para melhorar o desempenho e a confiabilidade.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quais versões de várias sessões do Windows 10 Enterprise têm suporte?

O Windows 10 Enterprise Multi-Session, versões 1809 e posteriores têm suporte e estão disponíveis na galeria do Azure. Essas versões seguem a mesma política de ciclo de vida de suporte que o Windows 10 Enterprise, o que significa que a versão Spring tem suporte por 18 meses e pela versão de outono por 30 meses.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Qual solução de gerenciamento de perfil devo usar para o Windows 10 Enterprise Multi-Session?

Recomendamos que você use contêineres de perfil FSLogix ao configurar o Windows 10 Enterprise em ambientes não persistentes ou outros cenários que precisam de um perfil armazenado centralmente. O FSLogix garante que o perfil do usuário esteja disponível e atualizado para cada sessão de usuário. Também recomendamos que você use seu contêiner de perfil do FSLogix para armazenar um perfil de usuário em qualquer compartilhamento SMB com as permissões apropriadas, mas você pode armazenar perfis de usuário no armazenamento de blobs de páginas do Azure, se necessário. Os usuários da área de trabalho virtual do Windows podem usar o FSLogix sem custo adicional.
 
Para obter mais informações sobre como configurar um recipiente de perfil FSLogix, consulte [Configurar o recipiente de perfil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Qual licença preciso para acessar várias sessões do Windows 10 Enterprise?

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
2. **Selecione a configuração** do computador > **modelos administrativos** > **componentes do Windows**.
3. Selecione **Loja**.
4. Selecione **Aplicação da loja**.
5. Selecione **Desativar**e, em seguida, selecione **OK**.
6. Selecione **Aplicar**.
 
## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a área de trabalho virtual do Windows e a várias sessões do Windows 10 Enterprise:

- Leia a documentação de [pré-visualização virtual do Windows](overview.md)
- Visite o nosso [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurar a implementação do seu Ambiente de Trabalho Virtual windows com os [tutoriais do Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
