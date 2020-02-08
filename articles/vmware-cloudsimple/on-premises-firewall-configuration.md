---
title: Acesso à Azure VMware Solutions (AVS) a partir do local
description: Aceder às suas Soluções Azure VMware (AVS) a partir da sua rede no local através de uma firewall
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082966"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Acesso ao seu ambiente e aplicações AVS Private Cloud a partir de instalações

Uma ligação pode ser configurada desde a rede no local até AVS utilizando o Azure ExpressRoute ou o Site-to-Site VPN. Aceda ao seu VCenter De Nuvem Privada AVS e a todas as cargas de trabalho que executa na Nuvem Privada AVS utilizando a ligação. Você pode controlar quais portas são abertas na conexão usando um firewall em sua rede local. Este artigo aborda alguns dos requisitos de porta de aplicativos típicos. Para qualquer outro aplicativo, consulte a documentação do aplicativo para obter os requisitos de porta.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para acessar o vCenter

Para aceder ao seu VCenter De Nuvem Privada AVS e ao gestor NSX-T, as portas definidas na tabela abaixo devem ser abertas na firewall no local. 

| Porta       | Origem                           | Destino                      | Objetivo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores DNS locais          | Servidores DNS dNS privados da Nuvem AVS        | Necessário para encaminhar a procura de *DNS* de az.cloudsimple.io para servidores DNS DNS privados da AVS a partir da rede no local.     |
| 53 (UDP)   | Servidores DNS dNS privados da Nuvem AVS        | Servidores DNS locais          | Necessário para encaminhar o DNS para cima de nomes de domínio no local do AVS Private Cloud vCenter para servidores DNS no local. |
| 80 (TCP)   | Rede no local              | Rede de gestão de nuvem privada AVS | Necessário para redirecionar o URL vCenter de *http* para *https*.                                                         |
| 443 (TCP)  | Rede no local              | Rede de gestão de nuvem privada AVS | Necessário para acessar o vCenter e o NSX-T Manager da rede local.                                           |
| 8000 (TCP) | Rede no local              | Rede de gestão de nuvem privada AVS | Necessário para vMotion de máquinas virtuais desde as instalações até à Nuvem Privada AVS.                                          |
| 8000 (TCP) | Rede de gestão de nuvem privada AVS | Rede no local              | Necessário para vMotion de máquinas virtuais da AVS Private Cloud para o local.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portas necessárias para usar o Active Directory local como uma fonte de identidade

Para configurar o diretório ativo no local como fonte de identidade no AVS Private Cloud vCenter, as portas definidas na tabela devem ser abertas. Consulte [o Use Azure AD como fornecedor de identidade para vCenter em AVS AVS Private Cloud](https://docs.azure.cloudsimple.com/azure-ad/) para passos de configuração.

| Porta         | Origem                           | Destino                                         | Objetivo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores DNS dNS privados da Nuvem AVS        | Servidores DNS locais                             | Necessário para encaminhar o DNS para cima de nomes de domínio de diretório ativo no local de AVS Private Cloud vCenter para servidores DNS no local.        |
| 389 (TCP/UDP) | Rede de gestão de nuvem privada AVS | Controladores de domínio do Active Directory local     | Necessário para a comunicação LDAP do servidor AVS Private Cloud vCenter para controladores de domínio de diretório ativo para autenticação do utilizador.              |
| 636 (TCP)     | Rede de gestão de nuvem privada AVS | Controladores de domínio do Active Directory local     | Necessária para uma comunicação LDAP (LDAPS) segura do servidor AVS Private Cloud vCenter para controladores de domínio de diretório ativo para autenticação do utilizador. |
| 3268 (TCP)    | Rede de gestão de nuvem privada AVS | Servidores de catálogo global do Active Directory local | Necessário para a comunicação LDAP em implantações de controlador de vários domínios.                                                                      |
| 3269 (TCP)    | Rede de gestão de nuvem privada AVS | Servidores de catálogo global do Active Directory local | Necessário para comunicação LDAPs em implantações de controlador de vários domínios.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para acessar máquinas virtuais de carga de trabalho

O acesso às máquinas virtuais de carga de trabalho em funcionamento na AVS Private Cloud exige que as portas sejam abertas na sua firewall no local. A tabela abaixo mostra algumas das portas comuns necessárias e sua finalidade. Para qualquer requisito de porta específica do aplicativo, consulte a documentação do aplicativo.

| Porta         | Origem                         | Destino                          | Objetivo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede no local            | Rede de carga de trabalho da Nuvem Privada AVS       | Acesso seguro à concha das máquinas virtuais Linux em execução na Nuvem Privada AVS.            |
| 3389 (TCP)    | Rede no local            | Rede de carga de trabalho da Nuvem Privada AVS       | Ambiente de trabalho remoto para janelas máquinas virtuais em execução em AVS Private Cloud.               |
| 80 (TCP)      | Rede no local            | Rede de carga de trabalho da Nuvem Privada AVS       | Aceda a quaisquer servidores web implantados em máquinas virtuais em execução na Nuvem Privada AVS.      |
| 443 (TCP)     | Rede no local            | Rede de carga de trabalho da Nuvem Privada AVS       | Aceda a quaisquer servidores web seguros implantados em máquinas virtuais em execução na Nuvem Privada AVS. |
| 389 (TCP/UDP) | Rede de carga de trabalho da Nuvem Privada AVS | Rede do Active Directory local | Ingresse máquinas virtuais de carga de trabalho do Windows no domínio do Active Directory local.     |
| 53 (UDP)      | Rede de carga de trabalho da Nuvem Privada AVS | Rede no local                  | Acesso do serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS locais.       |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir VLANs e Subnets](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Ligue-se à rede no local utilizando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configuração Site-to-Site VPN a partir de instalações](https://docs.azure.cloudsimple.com/vpn-gateway/)
