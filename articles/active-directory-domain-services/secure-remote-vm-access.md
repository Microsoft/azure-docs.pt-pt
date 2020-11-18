---
title: Acesso remoto seguro em VM em Serviços de Domínio Azure AD / Microsoft Docs
description: Saiba como garantir o acesso remoto aos VMs utilizando o Network Policy Server (NPS) e a Autenticação Multi-Factor AD Azure com uma implementação de Serviços de Ambiente de Trabalho remoto num domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: a08b5bf4fb575f0cd2098b3ef180860bb8fbd6e0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840241"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Acesso remoto seguro a máquinas virtuais em Azure Ative Directory Domain Services

Para garantir o acesso remoto a máquinas virtuais (VMs) que funcionam num domínio gerido por Azure Ative Directory Domain Services (Azure AD DS), pode utilizar serviços de secretária remoto (RDS) e Servidor de Política de Rede (NPS). O Azure AD DS autentica os utilizadores à medida que solicitam acesso através do ambiente RDS. Para uma maior segurança, pode integrar a Autenticação Multi-Factor Azure AD para fornecer uma solicitação de autenticação adicional durante os eventos de entrada. A Azure AD Multi-Factor Authentication utiliza uma extensão para NPS para fornecer esta funcionalidade.

> [!IMPORTANT]
> A forma recomendada de ligar-se de forma segura aos seus VMs num domínio gerido Azure AD DS está a utilizar o Azure Bastion, um serviço PaaS totalmente gerido pela plataforma que fornece dentro da sua rede virtual. Um hospedeiro de bastião fornece conectividade segura e sem emenda do Protocolo remoto de Ambiente de Trabalho (PDR) aos seus VMs diretamente no portal Azure sobre SSL. Quando se conecta através de um hospedeiro de bastião, os seus VMs não precisam de um endereço IP público, e não precisa de usar grupos de segurança de rede para expor o acesso ao RDP na porta TCP 3389.
>
> Recomendamos vivamente que utilize o Bastião Azure em todas as regiões onde é apoiado. Nas regiões sem disponibilidade do Bastião Azure, siga os passos detalhados neste artigo até que o Bastião Azure esteja disponível. Tome cuidado com a atribuição de endereços IP públicos a VMs unidos ao Azure AD DS onde todo o tráfego RDP de entrada é permitido.
>
> Para mais informações, veja [o que é Azure Bastion?][bastion-overview]

Este artigo mostra-lhe como configurar RDS em Azure AD DS e utilizar opcionalmente a extensão NPS de autenticação multi-factor Azure AD.

![Visão geral dos Serviços de Ambiente de Trabalho Remoto (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].
* Uma *sub-rede de cargas de trabalho* criada na sua rede virtual Azure Ative Directory Domain Services.
    * Se necessário, [configure a rede virtual para um domínio gerido por Serviços de Domínio do Diretório Ativo Azure][configure-azureadds-vnet].
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Implementar e configurar o ambiente de ambiente de trabalho remoto

Para começar, crie um mínimo de dois VMs Azure que executam o Windows Server 2016 ou o Windows Server 2019. Para redundância e alta disponibilidade do ambiente de ambiente de trabalho remoto (RD), pode adicionar e carregar anfitriões adicionais de equilíbrio mais tarde.

Uma implantação de RDS sugerida inclui os seguintes dois VMs:

* *RDGVM01* - Executa o servidor RD Connection Broker, o servidor DE Acesso Web RD e o servidor RD Gateway.
* *RDSHVM01* - Executa o servidor do Anfitrião da Sessão RD.

Certifique-se de que os VMs são implantados numa sub-rede de *cargas* de trabalho da sua rede virtual Azure AD DS e, em seguida, junte-se aos VMs para o domínio gerido. Para obter mais informações, consulte como [criar e juntar um VM do Windows Server a um domínio gerido.][tutorial-create-join-vm]

A implantação do ambiente RD contém uma série de passos. O guia de implantação RD existente pode ser utilizado sem alterações específicas para utilizar num domínio gerido:

1. Inscreva-se em VMs criados para o ambiente RD com uma conta que faz parte do grupo de *administradores AD DC Azure,* como *contosoadmin*.
1. Para criar e configurar RDS, utilize o guia de [implementação do ambiente de ambiente de trabalho remoto][deploy-remote-desktop]existente . Distribua os componentes do servidor RD através dos seus VMs Azure conforme desejado.
    * Específico para Azure AD DS - quando configurar o licenciamento RD, desfie-o para modo **Por Dispositivo,** e não **por Utilizador,** como indicado no guia de implementação.
1. Se pretender fornecer acesso através de um navegador web, [crie o cliente web remote desktop para os seus utilizadores.][rd-web-client]

Com o RD implantado no domínio gerido, pode gerir e utilizar o serviço como faria com um domínio AD DS no local.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Implementar e configurar NPS e a extensão Azure AD MFA NPS

Se quiser aumentar a segurança da experiência de inscrição do utilizador, pode integrar opcionalmente o ambiente RD com a autenticação multi-factor Ad Azure. Com esta configuração, os utilizadores recebem uma solicitação adicional durante a s início de sôm.

Para fornecer esta capacidade, é instalado um Servidor de Política de Rede adicional (NPS) no seu ambiente juntamente com a extensão NPS de autenticação multi-factor Azure AD. Esta extensão integra-se com a Azure AD para solicitar e devolver o estado das solicitações de autenticação de vários fatores.

Os utilizadores devem estar [registados para utilizar a autenticação multi-factor Azure AD,][user-mfa-registration]que pode necessitar de licenças AD adicionais.

Para integrar a autenticação multi-factor AD Ad no ambiente de ambiente de trabalho remoto Azure AD DS, crie um Servidor NPS e instale a extensão:

1. Crie um VM adicional do Windows Server 2016 ou 2019, como *o NPSVM01,* que esteja ligado a uma sub-rede de *cargas* de trabalho na sua rede virtual Azure AD DS. Junte-se ao VM ao domínio gerido.
1. Inscreva-se no NPS VM como conta que faz parte do grupo de administradores da *Azure AD DC,* como *contosoadmin*.
1. A partir do **Gestor do Servidor,** selecione **Adicionar Funções e Funcionalidades** e, em seguida, instale a função *De Política de Rede e Serviços de Acesso.*
1. Utilize o artigo de como [instalar e configurar a extensão Azure AD MFA NPS][nps-extension].

Com o servidor NPS e a extensão NPS de autenticação multi-factor Azure AD instalada, preencha a secção seguinte para configure-a para utilização com o ambiente RD.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integrar gateway remoto de desktop e autenticação multi-factor Azure AD

Para integrar a extensão NPS de autenticação multi-factor Azure AD, utilize o artigo de como fazer para integrar a [sua infraestrutura remote desktop Gateway utilizando a extensão do Servidor de Política de Rede (NPS) e AD Azure][azure-mfa-nps-integration].

São necessárias as seguintes opções adicionais de configuração para integrar-se com um domínio gerido:

1. Não [registe o servidor NPS no Ative Directory][register-nps-ad]. Este passo falha num domínio gerido.
1. No [passo 4 para configurar a política de rede,][create-nps-policy]verifique também a caixa para **ignorar as propriedades de marcação da conta de utilizador**.
1. Se utilizar o Windows Server 2019 para o servidor NPS e a extensão NPS de autenticação multi-factor AD Azure, executar o seguinte comando para atualizar o canal seguro para permitir que o servidor NPS comunique corretamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Os utilizadores são agora solicitados para um fator de autenticação adicional quando iniciarem sação, como uma mensagem de texto ou solicitação na aplicação Microsoft Authenticator.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a melhoria da resiliência da sua implementação, consulte [Serviços de Ambiente de Trabalho Remoto - Alta disponibilidade][rds-high-availability].

Para obter mais informações sobre a obtenção de sing-in do utilizador, consulte [como funciona: Autenticação multi-factor Ad Azure][concepts-mfa].

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
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability