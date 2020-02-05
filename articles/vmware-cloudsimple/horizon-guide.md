---
title: Azure VMware Solutions (AVS) - Use o site AVS Private Cloud para alojar uma infraestrutura de ambiente de trabalho virtual utilizando o VMware Horizon
description: Descreve como pode usar o seu site AVS Private Cloud para alojar uma infraestrutura virtual de desktop usando vMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025253"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Use o site AVS Private Cloud para hospedar uma infraestrutura de ambiente de trabalho virtual usando vMware Horizon

Pode utilizar o seu site AVS Private Cloud para alojar uma infraestrutura de ambiente de trabalho virtual (VDI) utilizando o VMware Horizon 7.x. A figura a seguir mostra a arquitetura da solução lógica para o VDI.

![Implantação de horizonte](media/horizon-deployment.png)

Com essa solução, você tem controle total sobre o Gerenciador de exibição de horizonte e o volume do aplicativo. A interface do usuário familiar, API e interfaces CLI permitem o uso de seus scripts e ferramentas existentes.

A solução AVS requer que faça o seguinte:

* Instale, configure e gerencie o VMware Horizon 7.x na sua Nuvem Privada AVS.
* Forneça suas próprias licenças de horizonte.

## <a name="deploy-the-solution"></a>Implementar a solução

As seguintes secções descrevem como implementar uma solução VDI utilizando o Horizon na sua Nuvem Privada AVS.

1. [Verificar se as versões do produto VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimar o tamanho do seu ambiente de desktop](#estimate-the-size-of-your-desktop-environment)
3. [Crie uma Nuvem Privada AVS para o seu ambiente](#create-an-avs-private-cloud-for-your-environment)
4. [Instale o VMware Horizon na sua Nuvem Privada AVS](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificar se as versões do produto VMware são compatíveis

* Verifique se as suas versões atuais e planeadas de Horizon, Volumes de Aplicações, Gateway de Acesso Unificado e Gestor de Ambiente de Utilizador são compatíveis entre si e com vCenter e PSC na Nuvem Privada AVS. Para obter informações de compatibilidade, consulte [matriz de compatibilidade do VMware para o horizonte 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Para saber as versões atuais de vCenter e PSC na sua Nuvem Privada AVS, vá a **Recursos** no [portal AVS,](access-cloudsimple-portal.md)selecione o seu AVS Private Cloud e clique no separador **VSphere Management Network.**

![versões do vCenter e do PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Estimar o tamanho do seu ambiente de desktop

* Verifique se sua configuração identificada está dentro dos limites operacionais do VMware.
* Estime os recursos necessários para todas as suas áreas de trabalho e seus componentes de gerenciamento de horizonte.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada AVS para o seu ambiente

1. Crie uma Nuvem Privada AVS a partir do portal AVS seguindo as instruções em [Configurar um ambiente De Nuvem Privada AVS](quickstart-create-private-cloud.md). O AVS cria um utilizador vCenter padrão chamado 'cloudowner' em cada Nuvem Privada AVS recém-criada. Para mais detalhes sobre o modelo padrão de utilizador e permissão AVS Private Cloud, consulte O modelo de [permissões AVS Private Cloud](learn-private-cloud-permissions.md).
2. Crie um VLAN na sua Nuvem Privada AVS para o plano de gestão horizonte e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md). Essa é a rede onde todos os componentes da solução (gateway de acesso unificado, servidor de conexão, servidor de volume de aplicativo e servidores do Gerenciador de ambiente de usuário) serão instalados.
3. Decida se pretende utilizar um fornecedor de identidade externo com o seu VCenter De Nuvem Privada AVS. Em caso afirmativo, escolha uma destas opções:
    * Utilize o seu Diretório Ativo no local como fornecedor de identidade externa. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Criar um servidor de Diretório Ativo no plano de gestão AVS Private Cloud in Horizon VLAN para utilizar como fornecedor de identidade externa. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Instale um servidor DHCP e DNS no plano de gestão Horizonte VLAN na Nuvem Privada AVS. Para obter instruções, consulte [Configurar aplicações dNS e DHCP e cargas](dns-dhcp-setup.md)de trabalho na sua Nuvem Privada AVS .
4. Configure o reencaminhado DNS no servidor DNS instalado na Nuvem Privada AVS. Para obter instruções, consulte [criar um encaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>Instale o VMware Horizon na sua Nuvem Privada AVS

O diagrama de implantação seguinte retrata uma solução Horizon implantada numa Nuvem Privada AVS. O Unified Access Gateway, o AD/DC, o modo de exibição e o servidor de volume de aplicativo são instalados em uma VLAN 234 criada pelo usuário. O Unified Access Gateway tem um endereço IP público que pode ser acessado pela Internet. As VMs do pool de área de trabalho do horizonte são implantadas no VLAN 235 para fornecer isolamento e segurança adicionais.

![Implantação do Horizonte na Nuvem Privada AVS](media/horizon-private-cloud.png)

As seções a seguir descrevem as instruções para configurar uma implantação semelhante à descrita na figura. Antes de começar, verifique se você tem o seguinte:

* Uma Nuvem Privada AVS criada usando o portal AVS com capacidade suficiente para executar as suas piscinas de ambiente de trabalho.
* Largura de banda suficiente entre o ambiente no local e o ambiente AVS Private Cloud para suportar o tráfego de rede para os seus ambientes de trabalho.
* Um túnel VPN local-a-local configurado entre o centro de dados no local e a Nuvem Privada AVS.
* Alcance IP das subredes de utilizador final no seu ambiente no local para as subredes AVS Private Cloud.
* AD/DHCP/DNS instalado para a sua Nuvem Privada AVS.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal AVS: Criar uma VLAN/subnet dedicada para piscinas de desktop

Crie uma VLAN para os pools da área de trabalho do horizonte e atribua a ele um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md). Essa é a rede onde todas as máquinas virtuais de desktop serão executadas.

Siga as práticas recomendadas de segurança padrão para proteger sua implantação de horizonte:

* Permita somente tráfego de SSH/tráfego de RDP para suas VMs de área de trabalho.
* Permitir somente o tráfego de gerenciamento entre a VLAN do plano de gerenciamento de horizonte e a VLAN do pool de desktops
* Permitir somente o tráfego de gerenciamento da rede local.

Pode impor estas boas práticas configurando regras de [firewall](firewall.md) a partir do portal AVS.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal AVS: Configure regras de firewall para garantir plano de gestão horizonte

Estabeleça as seguintes regras no portal AVS. Para obter instruções, consulte [configurar regras e tabelas de firewall](firewall.md).

1. Configure as regras de firewall na firewall AVS N-S para permitir a comunicação entre as subredes no local e a Gestão Horizonte VLAN de modo a que apenas sejam permitidas as portas de rede listadas na lista de porta sérs do documento VMware [Horizon.](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)

2. Crie regras de firewall E-W entre a vLAN de gestão do Horizonte e o pool de ambiente de trabalho VLAN na Nuvem Privada AVS.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal AVS: Criar um endereço IP público para Gateway de Acesso Unificado

Crie um endereço IP público para o dispositivo de gateway de acesso unificado para habilitar conexões de cliente de desktop da Internet. Para obter instruções, consulte [alocar endereços IP públicos](public-ips.md).

Quando a instalação for concluída, o endereço IP público será atribuído e listado na página IPs públicos.

#### <a name="avs-portal-escalate-privileges"></a>Portal AVS: Escalar privilégios

O utilizador 'cloudowner' padrão não tem privilégios suficientes no VCenter De Nuvem Privada AVS para instalar o Horizon, pelo que os privilégios vCenter do utilizador devem ser aumentados. Para obter mais informações, consulte [escalonar privilégios](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>vCenter UI: Criar um utilizador na Nuvem Privada AVS para a instalação Horizon

1. Entre no vCenter usando as credenciais de usuário ' cloudowner '.
2. Crie um novo usuário, ' horizonte-soln-admin ', no vCenter e adicione o usuário ao grupo de administradores no vCenter.
3. Saia do vCenter como o usuário ' cloudowner ' e entre como o usuário ' horizonte-soln-admin '.

#### <a name="vcenter-ui-install-vmware-horizon"></a>interface do usuário do vCenter: instalar o VMware horizonte

Conforme mencionado na seção arquitetura lógica anterior, a solução de horizonte tem os seguintes componentes:

* Exibição de horizonte do VMware
* Gateway de acesso unificado VMware
* Gerenciador de volumes de aplicativos VMware
* Gerenciador de ambiente de usuário do VMware

Instale os componentes da seguinte maneira:

1. Instale e configure o gateway de acesso unificado seguindo as instruções fornecidas no documento VMware [Implantando e Configurando o VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Instale a Vista Horizonte na Nuvem Privada AVS seguindo as instruções no Guia de [Instalação View](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Instale o Gerenciador de volume de aplicativo seguindo as instruções em [instalar e configurar volumes de aplicativos VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Instale e configure o Gerenciador de ambiente de usuário seguindo as instruções em [sobre como instalar e configurar o Gerenciador de ambiente de usuário do VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Arquivar uma solicitação de suporte para carregar volumes de aplicativos predefinidos do VMware horizonte

Como parte do processo de instalação, o Gerenciador de volume de aplicativo usa volumes predefinidos para provisionar pilhas de aplicativos e volumes graváveis. Esses volumes servem como modelos para pilhas de aplicativos e volumes graváveis.

O upload dos volumes para a loja de dados AVS Private Cloud requer a senha de raiz ESXi. Para obter assistência, envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Fixe o pacote de instalação AppVolumes para que o pessoal de suporte AVS possa carregar os modelos para o seu ambiente AVS Private Cloud.

#### <a name="avs-portal-de-escalate-privileges"></a>Portal AVS: Desescalar privilégios

Agora você pode [redimensionar os privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges) do usuário ' cloudowner '.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gerenciamento contínuo da sua solução de horizonte

Você tem controlo total sobre o software Horizon e App Volume Manager no seu ambiente AVS Private Cloud e espera-se que execute a gestão necessária do ciclo de vida do software. Certifique-se de que quaisquer novas versões de software são compatíveis com o VCenter e PSC private Cloud da AVS antes de atualizar ou atualizar o Volume horizonte ou app.
