---
title: Azure VMware Solution - DNS a encaminhar da nuvem privada para o local
description: Descreve como permitir que o seu servidor DNS DNS de CloudSimple Private Cloud avance com recursos no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76961130"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Ative os servidores DNS dNS privados da Cloud Simple para reencaminhar a procura de recursos do DNS no local para os seus servidores DNS

Os servidores DNS privados da Cloud podem encaminhar a procura de DNS para quaisquer recursos no local para os seus servidores DNS.  Ativar a procura permite que os componentes private Cloud vSphere procurem quaisquer serviços em funcionamento no seu ambiente no local e comuniquem com eles usando nomes de domínio totalmente qualificados (FQDN).

## <a name="scenarios"></a>Cenários 

Reencaminhamento de procura de DNS para o seu servidor DNS no local permite-lhe utilizar a sua Cloud Privada para os seguintes cenários:

* Use a Private Cloud como uma configuração de recuperação de desastres para a sua solução VMware no local
* Use no local Diretório Ativo como fonte de identidade para a sua nuvem privada vSphere
* Use HCX para migrar máquinas virtuais de instalações para nuvem privada

## <a name="before-you-begin"></a>Antes de começar

Uma ligação de rede deve estar presente desde a sua rede Private Cloud até à sua rede no local para o reencaminhado dNS para funcionar.  Pode configurar a ligação de rede utilizando:

* [Ligue-se a partir das instalações para a CloudSimple usando o ExpressRoute](on-premises-connection.md)
* [Criar um gateway VPN site-to-site](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

As portas de firewall devem ser abertas nesta ligação para o reinício do DNS para o trabalho.  As portas utilizadas são a porta TCP 53 ou a porta UDP 53.

> [!NOTE]
> Se estiver a utilizar VPN site-to-site, a sua subnet de servidor DNS no local deve ser adicionada como parte dos prefixos no local.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Solicitar o reencaminhamento de DNS da Private Cloud para o local

Para permitir o reencaminhamento do DNS da Private Cloud para as instalações, submeta um pedido de [apoio,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.

* Tipo de emissão: **Técnico**
* Subscrição: **Subscrição onde o serviço CloudSimple é implementado**
* Serviço: **Solução VMware by CloudSimple**
* Tipo de problema: **Aviso ou Como...**
* Subtipo de problema: **Precisa de ajuda com a NW**
* Forneça o nome de domínio do seu domínio no local no painel de detalhes.
* Forneça a lista dos seus servidores DNS no local para os quais a procura será reencaminhada da sua nuvem privada no painel de detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a configuração da firewall no local](on-premises-firewall-configuration.md)
* [Configuração do servidor DNS no local](on-premises-dns-setup.md)
