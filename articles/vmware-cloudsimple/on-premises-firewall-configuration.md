---
title: Access Azure VMware Solution by CloudSimple from on-ins
titleSuffix: Azure VMware Solution by CloudSimple
description: Aceder à sua Solução Azure VMware by CloudSimple a partir da sua rede no local através de uma firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3cc9ad0c694be8f95ddcffbc3d952d59b83f89f3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077215"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Aceder ao seu ambiente cloudsimple private cloud e aplicações a partir de instalações

Uma ligação pode ser configurada da rede no local para a CloudSimple usando a Azure ExpressRoute ou a VPN site-to-site.  Aceda ao seu CloudSimple Private Cloud vCenter e quaisquer cargas de trabalho que execute na Nuvem Privada utilizando a ligação.  Pode controlar as portas abertas na ligação utilizando uma firewall na sua rede no local.  Este artigo discute alguns dos requisitos típicos do porto de aplicações.  Para quaisquer outras aplicações, consulte a documentação do pedido para os requisitos portuários.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para aceder ao vCenter

Para aceder ao seu gestor Private Cloud vCenter e NSX-T, as portas definidas na tabela abaixo devem ser abertas na firewall do local.  

| Porta       | Origem                           | Destino                      | Objetivo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores DNS no local          | Servidores PRIVADOs cloud DNS        | Necessário para encaminhar a procura de DNS de *az.cloudsimple.io* para servidores DNS private Cloud a partir da rede de DNS no local.       |
| 53 (UDP)   | Servidores PRIVADOs cloud DNS        | Servidores DNS no local          | Necessário para encaminhar o DNS procurar nomes de domínio no local, do Private Cloud vCenter para servidores DNS no local. |
| 80 (TCP)   | Rede no local              | Rede privada de gestão de nuvem | Necessário para redirecionar o URL vCenter de *http* para *https*.                                                           |
| 443 (TCP)  | Rede no local              | Rede privada de gestão de nuvem | Necessário para aceder ao gestor vCenter e NSX-T da rede no local.                                             |
| 8000 (TCP) | Rede no local              | Rede privada de gestão de nuvem | Necessário para vMotion de máquinas virtuais de instalações para Nuvem Privada.                                            |
| 8000 (TCP) | Rede privada de gestão de nuvem | Rede no local              | Necessário para vMotion de máquinas virtuais de Private Cloud para o local.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portos necessários para a utilização de diretório ativo no local como fonte de identidade

Para configurar o diretório ativo no local como fonte de identidade no Private Cloud vCenter, devem ser abertas as portas definidas na tabela.  Consulte [o Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud](./azure-ad.md) para etapas de configuração.

| Porta         | Origem                           | Destino                                         | Objetivo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores PRIVADOs cloud DNS        | Servidores DNS no local                             | Necessário para reencaminhar os dns para procurar nomes de domínio de diretório ativo no local, de Private Cloud vCenter para servidores DNS no local.          |
| 389 (TCP/UDP) | Rede privada de gestão de nuvem | Controladores de domínio de diretório ativo no local     | Necessário para a comunicação LDAP do servidor Private Cloud vCenter para controladores de domínio de diretório ativo para a autenticação do utilizador.                |
| 636 (TCP)     | Rede privada de gestão de nuvem | Controladores de domínio de diretório ativo no local     | Necessário para uma comunicação LDAP (LDAPS) segura do servidor Private Cloud vCenter para controladores de domínio de diretório ativo para a autenticação do utilizador. |
| 3268 (TCP)    | Rede privada de gestão de nuvem | Servidores de catálogos globais de diretórios ativos no local | Necessário para a comunicação LDAP em implementações de controlador de vários domínios.                                                                        |
| 3269 (TCP)    | Rede privada de gestão de nuvem | Servidores de catálogos globais de diretórios ativos no local | Necessário para a comunicação LDAPS em implementações de controlador de vários domínios.                                                                       |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para aceder a máquinas virtuais de carga de trabalho

Aceder a máquinas virtuais de carga de trabalho em funcionamento em Private Cloud requer que as portas sejam abertas na sua firewall no local.  A tabela abaixo mostra algumas das portas comuns necessárias e o seu propósito.  Para quaisquer requisitos portuários específicos da aplicação, consulte a documentação do pedido.

| Porta         | Origem                         | Destino                          | Objetivo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede no local            | Rede privada de carga de trabalho em nuvem       | Acesso seguro à concha às máquinas virtuais Do Linux que estão a funcionar na Private Cloud.              |
| 3389 (TCP)    | Rede no local            | Rede privada de carga de trabalho em nuvem       | Ambiente de trabalho remoto para windows máquinas virtuais em execução em Private Cloud.                 |
| 80 (TCP)      | Rede no local            | Rede privada de carga de trabalho em nuvem       | Aceda a quaisquer servidores web implantados em máquinas virtuais em execução na Private Cloud.        |
| 443 (TCP)     | Rede no local            | Rede privada de carga de trabalho em nuvem       | Aceda a quaisquer servidores web seguros implantados em máquinas virtuais em execução na Private Cloud. |
| 389 (TCP/UDP) | Rede privada de carga de trabalho em nuvem | Rede de diretórios ativos no local | Junte as máquinas virtuais de carga de trabalho do Windows ao domínio do diretório ativo no local.       |
| 53 (UDP)      | Rede privada de carga de trabalho em nuvem | Rede no local                  | Acesso de serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS no local.         |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir VLANs e Subnets](./create-vlan-subnet.md)
* [Ligue-se à rede de instalações utilizando a Azure ExpressRoute](./on-premises-connection.md)
* [Configuração VPN site-to-site a partir de instalações](./vpn-gateway.md)
