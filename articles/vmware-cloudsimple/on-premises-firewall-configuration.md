---
title: Acessando a solução do Azure VMware por CloudSimple do local
description: Acessando sua solução do Azure VMware por meio do CloudSimple de sua rede local por meio de um firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dadf5fb64ba727b388c373f9b8befb54592c49d9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958515"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Acessando seu ambiente de nuvem privada do CloudSimple e aplicativos do local

Uma conexão pode ser configurada da rede local para CloudSimple usando o Azure ExpressRoute ou VPN site a site.  Acesse o vCenter de nuvem privada do CloudSimple e as cargas de trabalho que você executa na nuvem privada usando a conexão.  Você pode controlar quais portas são abertas na conexão usando um firewall em sua rede local.  Este artigo aborda alguns dos requisitos de porta de aplicativos típicos.  Para qualquer outro aplicativo, consulte a documentação do aplicativo para obter os requisitos de porta.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para acessar o vCenter

Para acessar sua nuvem privada do vCenter e o Gerenciador do NSX-T, as portas definidas na tabela a seguir devem ser abertas no firewall local.  

| Port       | Source                           | Destino                      | Objetivo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores DNS locais          | Servidores DNS da nuvem privada        | Necessário para encaminhar a pesquisa DNS de *AZ.cloudsimple.Io* para servidores DNS da nuvem privada da rede local.       |
| 53 (UDP)   | Servidores DNS da nuvem privada        | Servidores DNS locais          | Necessário para o encaminhamento de DNS pesquisar nomes de domínio locais do vCenter de nuvem privada para servidores DNS locais. |
| 80 (TCP)   | Rede no local              | Rede de gerenciamento de nuvem privada | Necessário para redirecionar a URL do vCenter de *http* para *https*.                                                           |
| 443 (TCP)  | Rede no local              | Rede de gerenciamento de nuvem privada | Necessário para acessar o vCenter e o NSX-T Manager da rede local.                                             |
| 8000 (TCP) | Rede no local              | Rede de gerenciamento de nuvem privada | Necessário para o vMotion de máquinas virtuais do local para a nuvem privada.                                            |
| 8000 (TCP) | Rede de gerenciamento de nuvem privada | Rede no local              | Necessário para o vMotion de máquinas virtuais da nuvem privada para o local.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portas necessárias para usar o Active Directory local como uma fonte de identidade

Para configurar o Active Directory local como uma fonte de identidade no vCenter de nuvem privada, as portas definidas na tabela devem ser abertas.  Consulte [usar o Azure ad como um provedor de identidade para o vCenter na nuvem privada do CloudSimple](https://docs.azure.cloudsimple.com/azure-ad/) para obter as etapas de configuração.

| Port         | Source                           | Destino                                         | Objetivo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores DNS da nuvem privada        | Servidores DNS locais                             | Necessário para o encaminhamento de DNS pesquisar nomes de domínio do Active Directory local do vCenter de nuvem privada para servidores DNS locais.          |
| 389 (TCP/UDP) | Rede de gerenciamento de nuvem privada | Controladores de domínio do Active Directory local     | Necessário para a comunicação LDAP do servidor vCenter de nuvem privada para controladores de domínio do Active Directory para autenticação de usuário.                |
| 636 (TCP)     | Rede de gerenciamento de nuvem privada | Controladores de domínio do Active Directory local     | Necessário para comunicação LDAP (LDAPs) segura do servidor vCenter de nuvem privada para controladores de domínio do Active Directory para autenticação de usuário. |
| 3268 (TCP)    | Rede de gerenciamento de nuvem privada | Servidores de catálogo global do Active Directory local | Necessário para a comunicação LDAP em implantações de controlador de vários domínios.                                                                        |
| 3269 (TCP)    | Rede de gerenciamento de nuvem privada | Servidores de catálogo global do Active Directory local | Necessário para comunicação LDAPs em implantações de controlador de vários domínios.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para acessar máquinas virtuais de carga de trabalho

As máquinas virtuais de carga de trabalho de acesso em execução na nuvem privada exigem que as portas sejam abertas no firewall local.  A tabela abaixo mostra algumas das portas comuns necessárias e sua finalidade.  Para qualquer requisito de porta específica do aplicativo, consulte a documentação do aplicativo.

| Port         | Source                         | Destino                          | Objetivo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede no local            | Rede de carga de trabalho de nuvem privada       | Proteja o acesso do Shell a máquinas virtuais Linux em execução na nuvem privada.              |
| 3389 (TCP)    | Rede no local            | Rede de carga de trabalho de nuvem privada       | Área de trabalho remota para máquinas virtuais do Windows em execução na nuvem privada.                 |
| 80 (TCP)      | Rede no local            | Rede de carga de trabalho de nuvem privada       | Acessar qualquer servidor Web implantado em máquinas virtuais em execução na nuvem privada.        |
| 443 (TCP)     | Rede no local            | Rede de carga de trabalho de nuvem privada       | Acessar qualquer servidor Web seguro implantado em máquinas virtuais em execução na nuvem privada. |
| 389 (TCP/UDP) | Rede de carga de trabalho de nuvem privada | Rede do Active Directory local | Ingresse máquinas virtuais de carga de trabalho do Windows no domínio do Active Directory local.       |
| 53 (UDP)      | Rede de carga de trabalho de nuvem privada | Rede no local                  | Acesso do serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS locais.         |

## <a name="next-steps"></a>Passos Seguintes

* [Criar e gerenciar VLANs e sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Conectar-se à rede local usando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurar VPN site a site do local](https://docs.azure.cloudsimple.com/vpn-gateway/)