---
title: Access Azure VMware Solution by CloudSimple from-in-premises
titleSuffix: Azure VMware Solution by CloudSimple
description: Aceder à sua Solução Azure VMware by CloudSimple a partir da sua rede no local através de uma firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082966"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Aceder ao seu ambiente CloudSimple Private Cloud e aplicações a partir de instalações

Uma ligação pode ser configurada a partir da rede no local para cloudSimple usando Azure ExpressRoute ou Site-to-Site VPN.  Aceda ao vCenter CloudSimple Private Cloud e a todas as cargas de trabalho que executa na Cloud Privada utilizando a ligação.  Pode controlar quais as portas abertas na ligação utilizando uma firewall na sua rede no local.  Este artigo discute alguns dos requisitos típicos da porta de aplicações.  Para quaisquer outras candidaturas, consulte a documentação da aplicação para os requisitos da porta.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para aceder ao vCenter

Para aceder ao seu vCenter de Nuvem Privada e ao seu gestor NSX-T, as portas definidas na tabela abaixo devem ser abertas na firewall no local.  

| Porta       | Origem                           | Destino                      | Objetivo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores DNS no local          | Servidores DNS de Nuvem Privada        | Necessário para encaminhar a procura de *DNS* de az.cloudsimple.io para servidores DNS de Nuvem Privada a partir da rede no local.       |
| 53 (UDP)   | Servidores DNS de Nuvem Privada        | Servidores DNS no local          | Necessário para encaminhar o DNS para cima de nomes de domínio no local do private Cloud vCenter para servidores DNS no local. |
| 80 (TCP)   | Rede no local              | Rede privada de gestão cloud | Necessário para redirecionar o URL vCenter de *http* para *https*.                                                           |
| 443 (TCP)  | Rede no local              | Rede privada de gestão cloud | Necessário para aceder ao vCenter e ao gestor NSX-T a partir da rede no local.                                             |
| 8000 (TCP) | Rede no local              | Rede privada de gestão cloud | Necessário para vMotion de máquinas virtuais desde as instalações até à Nuvem Privada.                                            |
| 8000 (TCP) | Rede privada de gestão cloud | Rede no local              | Necessário para vMotion de máquinas virtuais de Private Cloud para no local.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portos necessários para utilização de diretório ativo no local como fonte de identidade

Para configurar o diretório ativo no local como fonte de identidade no private Cloud vCenter, as portas definidas na tabela devem ser abertas.  Consulte [o Use Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/azure-ad/) para obter passos de configuração.

| Porta         | Origem                           | Destino                                         | Objetivo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores DNS de Nuvem Privada        | Servidores DNS no local                             | Necessário para encaminhar o DNS para cima de nomes de domínio de diretório ativo no local do Private Cloud vCenter para servidores DNS no local.          |
| 389 (TCP/UDP) | Rede privada de gestão cloud | Controladores de domínio de diretório ativo no local     | Necessária para a comunicação LDAP do servidor Private Cloud vCenter para controladores de domínio de diretório ativo para autenticação do utilizador.                |
| 636 (TCP)     | Rede privada de gestão cloud | Controladores de domínio de diretório ativo no local     | Necessária para uma comunicação LDAP (LDAPS) segura do servidor Private Cloud vCenter para controladores de domínio de diretório ativo para autenticação do utilizador. |
| 3268 (TCP)    | Rede privada de gestão cloud | No local, servidores de catálogo global de diretórios ativos | Necessária para a comunicação LDAP em implementações de controladores de vários domínios.                                                                        |
| 3269 (TCP)    | Rede privada de gestão cloud | No local, servidores de catálogo global de diretórios ativos | Necessário para a comunicação LDAPS em implementações de controladores de vários domínios.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para aceder a máquinas virtuais de carga de trabalho

O acesso às máquinas virtuais de carga de trabalho em funcionamento na Cloud Privada requer que as portas sejam abertas na sua firewall no local.  A tabela abaixo mostra alguns dos portos comuns necessários e o seu propósito.  Para qualquer pedido específico de requisitos portuário, consulte a documentação da aplicação.

| Porta         | Origem                         | Destino                          | Objetivo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede no local            | Rede privada de carga de trabalho em nuvem       | Acesso seguro à concha das máquinas virtuais Linux em funcionamento na Cloud Privada.              |
| 3389 (TCP)    | Rede no local            | Rede privada de carga de trabalho em nuvem       | Ambiente de trabalho remoto para janelas máquinas virtuais em funcionamento em Nuvem Privada.                 |
| 80 (TCP)      | Rede no local            | Rede privada de carga de trabalho em nuvem       | Aceda a quaisquer servidores web implantados em máquinas virtuais em funcionamento em Private Cloud.        |
| 443 (TCP)     | Rede no local            | Rede privada de carga de trabalho em nuvem       | Aceda a quaisquer servidores web seguros implantados em máquinas virtuais em funcionamento em Cloud Privada. |
| 389 (TCP/UDP) | Rede privada de carga de trabalho em nuvem | Rede ativa de diretórios no local | Junte as máquinas virtuais de carga de trabalho do Windows para o domínio de diretório ativo no local.       |
| 53 (UDP)      | Rede privada de carga de trabalho em nuvem | Rede no local                  | Acesso ao serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS no local.         |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir VLANs e Subnets](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Ligue-se à rede no local utilizando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configuração Site-to-Site VPN a partir de instalações](https://docs.azure.cloudsimple.com/vpn-gateway/)
