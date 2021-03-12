---
title: Windows 10 Enterprise multi-sessão FAQ - Azure
description: Perguntas frequentes e boas práticas para a utilização de várias sessões do Windows 10 Enterprise para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d76fd3b30d823efbf85e84a74a21c694f5a0e6fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565568"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>FAQ de multissessões do Windows 10 Enterprise

Este artigo responde a perguntas frequentes e explica as melhores práticas para o Windows 10 Enterprise multi-sessão.

## <a name="what-is-windows-10-enterprise-multi-session"></a>O que é o Windows 10 Enterprise multi-sessão?

O Windows 10 Enterprise multi-sessão, anteriormente conhecido como Windows 10 Enterprise for Virtual Desktops (EVD), é um novo Anfitrião de Sessão remota de Desktop que permite múltiplas sessões interativas simultâneas. Anteriormente, apenas o Windows Server poderia fazê-lo. Esta capacidade proporciona aos utilizadores uma experiência familiar do Windows 10, enquanto a TI pode beneficiar das vantagens de custos de várias sessões e utilizar o licenciamento existente por utilizador do Windows em vez de Licenças de Acesso ao Cliente RDS (CALs). Para obter mais informações sobre licenças e preços, consulte [os preços do Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quantos utilizadores podem simultaneamente ter uma sessão interativa sobre o Windows 10 Enterprise multi-sessão?

Quantas sessões interativas que podem ser ativas ao mesmo tempo dependem dos recursos de hardware do seu sistema (vCPU, memória, disco e vGPU), como os seus utilizadores usam as suas apps durante o início de uma sessão e quão pesada é a carga de trabalho do seu sistema. Sugerimos que valide o desempenho do seu sistema para entender quantos utilizadores pode ter no Windows 10 Enterprise multi-sessão. Para saber mais, consulte [os preços do Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Porque é que a minha aplicação reporta várias sessões do Windows 10 Enterprise como sistema operativo server?

O Windows 10 Enterprise é uma edição virtual do Windows 10 Enterprise. Uma das diferenças é que este sistema operativo (OS) reporta o [ProdutoType](/windows/win32/cimwin32prov/win32-operatingsystem) como tendo um valor de 3, o mesmo valor que o Windows Server. Esta propriedade mantém o SISTEMA compatível com a ferramenta de gestão RDSH existente, aplicações de conhecimento multi-sessão RDSH e otimizações de desempenho do sistema de baixo nível para ambientes RDSH. Alguns instaladores de aplicações podem bloquear a instalação em várias sessões do Windows 10, dependendo se detetam o ProdutoType definido para o Cliente. Se a sua aplicação não for instalada, contacte o seu fornecedor de aplicações para obter uma versão atualizada.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso executar o Windows 10 Enterprise multi-sessão no local?

O Windows 10 Enterprise não pode funcionar em ambientes de produção no local porque está otimizado para o serviço de desktop virtual do Windows para o Azure. É contra o acordo de licenciamento executar o Windows 10 Enterprise multi-sessão fora de Azure para fins de produção. O Windows 10 Enterprise não será ativado contra os Serviços de Gestão de Chaves (KMS) no local.

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Como posso personalizar a imagem multi-sessão do Windows 10 Enterprise para a minha organização?

Pode iniciar uma máquina virtual (VM) em Azure com o Windows 10 Windows 10 Enterprise multi-sessão e personalizá-la instalando aplicações LOB, sysprep/generalizar e, em seguida, criar uma imagem utilizando o portal Azure.

Para começar, crie um VM em Azure com várias sessões do Windows 10 Enterprise. Em vez de iniciar o VM em Azure, pode baixar o VHD diretamente. Depois disso, poderás utilizar o VHD que descarregaste para criar um novo VM geração 1 num PC Windows 10 com Hiper-V ativado.

Personalize a imagem às suas necessidades instalando aplicações LOB e sysprep a imagem. Quando terminar de personalizar, faça o upload da imagem para Azure com o VHD no interior. Depois disso, obtenha o Windows Virtual Desktop do Azure Marketplace e use-o para implementar uma nova piscina de anfitriões com a imagem personalizada.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Como posso gerir o Windows 10 Enterprise multi-sessão após a implementação?

Pode utilizar qualquer ferramenta de configuração suportada, mas recomendamos a versão 1906 do Gestor de Configuração porque suporta várias sessões do Windows 10 Enterprise. Estamos a trabalhar no suporte da Microsoft Intune.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>O Windows 10 Enterprise pode ser aderido ao Azure Ative Directory (AD)?

O Windows 10 Enterprise multi-sessão é atualmente suportado para ser híbrido Azure AD-joined. Depois de o Windows 10 Enterprise multi-sessão ser associado a domínios, utilize o objeto de política de grupo existente para ativar o registo AD do Azure. Para mais informações, consulte [Plan your hybrid Azure Ative Directory join implementation](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Onde posso encontrar a imagem multi-sessão do Windows 10 Enterprise?

O Windows 10 Enterprise está na galeria Azure. Para encontrá-lo, navegue no portal Azure e procure a versão do Windows 10 Enterprise para desktops virtuais. Para uma imagem integrada com apps microsoft 365 para empresa, vá ao portal Azure e procure **aplicações microsoft Windows 10 + Microsoft 365 para empresa**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Que imagem multi-sessão do Windows 10 Enterprise devo usar?

A galeria Azure tem vários lançamentos, incluindo o Windows 10 Enterprise multi-sessão, a versão 1809 e o Windows 10 Enterprise multi-sessão, versão 1903. Recomendamos a utilização da versão mais recente para melhorar o desempenho e a fiabilidade.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quais as versões multi-sessão do Windows 10 Enterprise suportadas?

Windows 10 Enterprise multi-sessão, versões 1809 e posteriormente são suportadas e estão disponíveis na galeria Azure. Estas versões seguem a mesma política de ciclo de vida de suporte que o Windows 10 Enterprise, o que significa que o lançamento de março é suportado por 18 meses e o lançamento de setembro por 30 meses.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Que solução de gestão de perfis devo utilizar para o Windows 10 Enterprise multi-sessão?

Recomendamos que utilize recipientes de perfil FSLogix quando configurar o Windows 10 Enterprise em ambientes não persistentes ou outros cenários que necessitem de um perfil centralmente armazenado. A FSLogix garante que o perfil do utilizador está disponível e atualizado para cada sessão de utilizador. Recomendamos também que utilize o seu contentor de perfil FSLogix para armazenar um perfil de utilizador em qualquer partilha SMB com permissões apropriadas, mas pode armazenar perfis de utilizador no armazenamento de blob de página Azure, se necessário. Os utilizadores do Windows Virtual Desktop podem utilizar o FSLogix sem custos adicionais.

Para obter mais informações sobre como configurar um recipiente de perfil FSLogix, consulte [configurar o recipiente de perfil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Que licença preciso para aceder a várias sessões do Windows 10 Enterprise?

Para obter uma lista completa das licenças aplicáveis, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Porque é que os meus aplicativos desaparecem depois de eu assinar?

Isto acontece porque está a utilizar várias sessões do Windows 10 Enterprise com uma solução de gestão de perfis como a FSLogix. A sua solução de administração ou perfil configura o seu sistema para eliminar os perfis dos utilizadores quando os utilizadores se inscrevem. Esta configuração significa que quando o seu sistema elimina o perfil do utilizador depois de assinar, também remove quaisquer aplicações que tenha instalado durante a sessão. Se quiser manter as aplicações instaladas, terá de pedir ao seu administrador que provisa estas aplicações para todos os utilizadores do seu ambiente de trabalho virtual do Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Como posso garantir que as aplicações não desapareçam quando os utilizadores assinam?

A maioria dos ambientes virtualizados são configurados por padrão para impedir que os utilizadores instalem aplicações adicionais nos seus perfis. Se quiser certificar-se de que uma aplicação não desaparece quando o utilizador assina fora do Windows Virtual Desktop, tem de providenciar essa aplicação para todos os perfis de utilizador no seu ambiente. Para obter mais informações sobre o provisionamento de apps, confira estes recursos:

- [Publicar aplicativos incorporados no Windows Virtual Desktop](publish-apps.md)
- [Opções de linha de comando de pacote de aplicativos DISM](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Pacote de pacotes adicionais](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Como posso garantir que os utilizadores não descarreguem e instalem aplicações a partir da Microsoft Store?

Pode desativar a aplicação da Microsoft Store para garantir que os utilizadores não descarreguem aplicações extra para além das aplicações que já aprovisionou para as mesmos.

Para desativar a aplicação Store:

1. Criar uma nova Política de Grupo.
2. Selecione   >  **modelos administrativos de** configuração do computador  >  **Componentes do Windows**.
3. Selecione **Loja**.
4. Selecione **a aplicação da loja**.
5. Selecione **Desativado** e, em seguida, selecione **OK**.
6. Selecione **Aplicar**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Windows Virtual Desktop e o Windows 10 Enterprise multi-sessão:

- Leia a nossa [documentação virtual do Windows Desktop](overview.md)
- Visite o nosso [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurar a sua implementação virtual do Windows desktop com os [tutoriais de ambiente de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)