---
title: Azure VMware Solution - DNS reencaminhando de nuvem privada para as instalações
description: Descreve como ativar o seu servidor DNS private Cloud Simplificador para encaminhar o lookup de recursos no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3571455db6ecc600bf0948087b40c281d72512ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091254"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Ativar os servidores DNS private Cloud do Cloud para encaminhar a procura de DNS dos recursos no local para os seus servidores DNS

Os servidores DNS private Cloud podem encaminhar a procura de DNS para quaisquer recursos no local para os seus servidores DNS.  Ativar o lookup permite que os componentes Private Cloud vSphere procurem quaisquer serviços em execução no seu ambiente no local e comuniquem com eles utilizando nomes de domínio totalmente qualificados (FQDN).

## <a name="scenarios"></a>Cenários 

Reencaminhamento de DNS para o seu servidor DNS no local permite-lhe utilizar a sua Cloud Privada para os seguintes cenários:

* Use a Private Cloud como configuração de recuperação de desastres para a sua solução VMware no local
* Use no local Ative Directory como fonte de identidade para o seu Private Cloud vSphere
* Utilize HCX para migrar máquinas virtuais de instalações para Nuvem Privada

## <a name="before-you-begin"></a>Antes de começar

Uma ligação de rede deve estar presente da sua rede Private Cloud para a sua rede de acesso ao local para o encaminhamento de DNS para funcionar.  Pode configurar a ligação de rede utilizando:

* [Ligue-se de instalações para CloudSimple usando ExpressRoute](on-premises-connection.md)
* [Configurar um portal VPN site-to-site](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

As portas de firewall devem ser abertas nesta ligação para o encaminhamento de DNS para funcionar.  As portas utilizadas são a porta TCP 53 ou a porta UDP 53.

> [!NOTE]
> Se estiver a utilizar a VPN site-to-site, a sub-rede do servidor DNS no local deve ser adicionada como parte dos prefixos no local.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Solicito o encaminhamento de DNS da Private Cloud para as instalações

Para permitir o encaminhamento de DNS da Private Cloud para o local, apresente um pedido de [apoio,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.

* Tipo de emissão: **Técnico**
* Subscrição: **Subscrição onde o serviço CloudSimple é implementado**
* Serviço: **Solução VMware by CloudSimple**
* Tipo de problema: **Aviso ou Como faço...**
* Subtipo de problema: **Precisa de ajuda com NW**
* Forneça o nome de domínio do seu domínio no local no painel de detalhes.
* Forneça a lista dos seus servidores DNS no local para os quais o lookup será reencaminhado a partir da sua nuvem privada no painel de detalhes.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a configuração de firewall no local](on-premises-firewall-configuration.md)
* [Configuração do servidor DNS nas instalações](on-premises-dns-setup.md)
