---
title: Acesso remoto seguro vM nos Serviços de Domínio Azure AD [ Microsoft Docs
description: Saiba como garantir o acesso remoto aos VMs utilizando o Servidor de Política de Rede (NPS) e a Autenticação Multi-Factor Azure com uma implementação remota de Serviços de Ambiente de Trabalho num domínio gerido pelo Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: a17f27831dd0a674c1d55cde6974aba5e1bfcfc3
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105731"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Acesso remoto seguro a máquinas virtuais em Serviços de Domínio de Diretório Ativo Azure

Para garantir o acesso remoto a máquinas virtuais (VMs) que funcionam num domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS), pode utilizar serviços de ambiente de trabalho remotos (RDS) e Servidor de Política de Rede (NPS). O Azure AD DS autentica os utilizadores ao solicitaro acesso através do ambiente RDS. Para uma maior segurança, pode integrar a Autenticação Multi-Factor Azure para fornecer um pedido de autenticação adicional durante os eventos de início de sessão. A Autenticação De Vários Fatores Azure utiliza uma extensão para o NPS fornecer esta funcionalidade.

> [!IMPORTANT]
> A forma recomendada de se ligar de forma segura aos seus VMs num domínio gerido pelo Azure AD DS está a utilizar o Azure Bastion, um serviço PaaS totalmente gerido pela plataforma que disponibiliza dentro da sua rede virtual. Um anfitrião de bastião fornece conectividade segura e perfeita do Protocolo de Ambiente de Trabalho Remoto (RDP) aos seus VMs diretamente no portal Azure sobre o SSL. Quando se conecta através de um hospedeiro de bastião, os seus VMs não precisam de um endereço IP público, e não precisa de usar grupos de segurança de rede para expor o acesso ao RDP na porta 3389 do TCP.
>
> Recomendamos vivamente que utilize o Bastião Azure em todas as regiões onde é apoiado. Nas regiões sem disponibilidade do Bastião Azure, siga os passos detalhados neste artigo até que o Bastião Azure esteja disponível. Tome cuidado com a atribuição de endereços IP públicos aos VMs que se juntaram ao Azure AD DS onde todo o tráfego de RDP é permitido.
>
> Para mais informações, veja [o que é Azure Bastion?][bastion-overview]

Este artigo mostra-lhe como configurar RDS em DS AD Azure e utilizar opcionalmente a extensão nPS de autenticação multi-factor Azure.

![Visão geral dos Serviços de Ambiente de Trabalho Remoto (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma subnet de *cargas* de trabalho criada na sua rede virtual De domínio de Diretório Ativo Azure.
    * Se necessário, configure a rede virtual para um domínio gerido pelo [Azure Ative Directory Domain Services][configure-azureadds-vnet].
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Implementar e configurar o ambiente de ambiente de trabalho remoto

Para começar, crie um mínimo de dois VMs Azure que executam o Windows Server 2016 ou o Windows Server 2019. Para redundância e elevada disponibilidade do seu ambiente remote Desktop (RD), pode adicionar e carregar os anfitriões adicionais mais tarde.

Uma implementação de RDS sugerida inclui os seguintes dois VMs:

* *RDGVM01* - Executa o servidor de corretor de ligação RD, servidor RD Web Access e servidor RD Gateway.
* *RDSHVM01* - Executa o servidor de anfitriões de sessão RD.

Certifique-se de que os VMs são implantados numa subrede de *cargas* de trabalho da sua rede virtual Azure AD DS e, em seguida, junte-se aos VMs para o domínio gerido pela AD DS Azure. Para mais informações, consulte como criar e juntar um VM do [Windows Server a um domínio gerido pelo Azure AD DS][tutorial-create-join-vm].

A implantação do ambiente RD contém uma série de passos. O guia de implantação RD existente pode ser utilizado sem alterações específicas a utilizar num domínio gerido pelo Azure AD DS:

1. Inscreva-se em VMs criados para o ambiente RD com uma conta que faz parte do grupo de administradores da *AD Azure,* como *a contosoadmina.*
1. Para criar e configurar RDS, utilize o guia de [implementação][deploy-remote-desktop]do ambiente remote desktop existente . Distribua os componentes do servidor RD pelos seus VMs Azure, conforme desejado.
    * Específico para O DS AD Azur - quando configurar o licenciamento RD, delineie-o para o modo **Por Dispositivo,** e não **por Utilizador,** conforme se nota no guia de implementação.
1. Se pretender fornecer acesso através de um navegador web, [instale o cliente web do Remote Desktop para os seus utilizadores.][rd-web-client]

Com RD implantado no domínio gerido pelo Azure AD DS, pode gerir e utilizar o serviço como faria com um domínio AD DS no local.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Implementar e configurar nPS e a extensão De NPS Azure MFA

Se pretender aumentar a segurança da experiência de entrada no utilizador, pode integrar opcionalmente o ambiente RD com a Autenticação Multi-Factor Azure. Com esta configuração, os utilizadores recebem um pedido adicional durante o início do sessão para confirmar a sua identidade.

Para fornecer esta capacidade, um Servidor de Política de Rede adicional (NPS) é instalado no seu ambiente juntamente com a extensão NPS de autenticação multi-factor Azure. Esta extensão integra-se com a Azure AD para solicitar e devolver o estado das solicitações de autenticação de vários fatores.

Os utilizadores devem estar [registados para utilizar a Autenticação Multi-Factor Azure,][user-mfa-registration]que pode necessitar de licenças AD Azure adicionais.

Para integrar a autenticação de vários fatores Azure no seu ambiente de trabalho remoto Azure AD DS, crie um Servidor NPS e instale a extensão:

1. Crie um Windows Server adicional 2016 ou 2019 VM, como o *NPSVM01,* que esteja ligado a uma subrede de *cargas* de trabalho na sua rede virtual Azure AD DS. Junte-se ao VM ao domínio gerido pela AD DS Azure.
1. Inscreva-se na NPS VM como conta que faz parte do grupo de administradores da *AD Azure,* como *a contosoadmina*.
1. A partir do Gestor do **Servidor,** **selecione Adicionar Funções e Funcionalidades**e, em seguida, instale a função de *Política de Rede e Serviços* de Acesso.
1. Utilize o artigo de como instalar [e configurar a extensão Do NPS Azure MFA][nps-extension].

Com o servidor NPS e a extensão NPS de autenticação multi-factor Azure instaladas, complete a secção seguinte para configurá-la para utilização com o ambiente RD.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrar gateway de ambiente de trabalho remoto e autenticação de multi-factor estoque

Para integrar a extensão de NPS de autenticação multi-factor Azure, utilize o artigo de como integrar a sua infraestrutura de Gateway de Ambiente de Trabalho Remoto utilizando a extensão do Servidor de Política de [Rede (NPS) e a AD Azure][azure-mfa-nps-integration].

São necessárias as seguintes opções de configuração adicionais para integrar com um domínio gerido pelo Azure AD DS:

1. Não [registe o servidor NPS no Diretório Ativo][register-nps-ad]. Este passo falha num domínio gerido pelo Azure AD DS.
1. No [passo 4 para configurar a política][create-nps-policy]de rede, verifique também a caixa para ignorar as propriedades de acesso telefónico à conta do **utilizador**.
1. Se utilizar o Windows Server 2019 para o servidor NPS e a extensão NPS de Autenticação Multi-Factor Azure, execute o seguinte comando para atualizar o canal de segurança para permitir que o servidor NPS se comunique corretamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Os utilizadores são agora solicitados para um fator de autenticação adicional quando iniciarem o seu insto, como uma mensagem de texto ou um pedido na aplicação Autenticadora Microsoft.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a melhoria da resiliência da sua implementação, consulte [Remote Desktop Services - Alta disponibilidade][rds-high-availability].

Para obter mais informações sobre a obtenção de um início de sessão do utilizador, consulte [como funciona: Autenticação Multi-Factor Azure][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
