---
title: Azure VMware Solutions (AVS) - Segurança para Serviços AVS
description: Descreve os modelos de responsabilidade partilhada para a segurança dos serviços AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024998"
---
# <a name="avs-security-overview"></a>Visão geral da segurança da AVS

Este artigo fornece uma visão geral de como a segurança é implementada na Solução Azure VMware pelo serviço, infraestrutura e datacenter. Aprende-se sobre proteção de dados e segurança, segurança da rede e como as vulnerabilidades e patches são geridos.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

A Azure VMware Solution by AVS utiliza um modelo de responsabilidade partilhada para a segurança. A segurança fidedigna na nuvem é alcançada através das responsabilidades partilhadas dos clientes e da Microsoft como prestadora de serviços. Esta matriz de responsabilidade proporciona maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança da infraestrutura Azure incluem os datacenters e a localização do equipamento.

### <a name="datacenter-security"></a>Segurança do datacenter

A Microsoft tem uma divisão inteira dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Esta equipa está a investir na manutenção da segurança física de última geração. Para mais detalhes sobre segurança física, consulte [instalações, instalações e segurança física do Azure.](../security/azure-physical-security.md)

### <a name="equipment-location"></a>Localização do equipamento

O equipamento de hardware de metal nu que executa o seu AVS Private Clouds está hospedado em localizações de datacenter Azure. As gaiolas onde esse equipamento está, requerem autenticação biométrica baseada em dois fatores para ter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço AVS, todos os clientes AVS recebem anfitriões de metal nus dedicados com discos ligados locais que estão fisicamente isolados de outros hardware de inquilino. Um hipervisor ESXi com vSAN corre em cada nó. Os nódosos são geridos através do vCenter e NSX dedicados ao cliente. A não partilha de hardware entre inquilinos proporciona uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança dos dados

Os clientes mantêm o controlo e a propriedade dos seus dados. A gestão de dados dos dados dos clientes é da responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados em repouso e dados em movimento dentro das redes internas

Para dados em repouso no ambiente AVS Private Cloud, pode utilizar encriptação vSAN. a encriptação vSAN funciona com servidores de gestão de chaves externas certificados vMware (KMS) na sua própria rede virtual ou no local. Você controla as chaves de encriptação de dados. Para dados em movimento dentro da Nuvem Privada AVS, a vSphere suporta a encriptação de dados sobre o fio para todo o tráfego vmkernel (incluindo o tráfego vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de Dados para dados que são necessários para circular através de redes públicas

Para proteger os dados que se movem através de redes públicas, pode criar túneis VpN IPsec e SSL para as suas Nuvens Privadas AVS. São suportados métodos comuns de encriptação, incluindo 128 bytes e 256 byte AES. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são encriptados com mecanismos de encriptação padrão (SSH, TLS 1.2 e Secure RDP). A comunicação que transporta informação sensível utiliza os mecanismos padrão de encriptação.

### <a name="secure-disposal"></a>Eliminação Segura

Se o seu serviço AVS expirar ou for encerrado, é responsável pela remoção ou eliminação dos seus dados. A AVS cooperará consigo para eliminar ou devolver todos os dados do cliente conforme fornecido no contrato de cliente, exceto na medida em que a AVS é exigida pela lei aplicável para reter alguns ou todos os dados pessoais. Se necessário para reter quaisquer dados pessoais, a AVS arquivará os dados e implementará medidas razoáveis para impedir que os dados do cliente sejam posteriormente processados.

### <a name="data-location"></a>Localização dos Dados

Ao configurar as suas Nuvens Privadas AVS, escolha a região de Azure onde serão implantadas. Os dados da máquina virtual VMware não são transferidos desse datacenter físico a menos que realize a migração de dados ou a cópia de segurança de dados fora do local. Também pode alojar cargas de trabalho e armazenar dados em várias regiões do Azure, se for caso disso para as suas necessidades.

Os dados do cliente residentes em nódeos hiper-convergentes da AVS Private Cloud não atravessam locais sem a ação explícita do administrador inquilino. É da sua responsabilidade implementar as suas cargas de trabalho de forma altamente disponível.

### <a name="data-backups"></a>Backups de dados

A AVS não faz o backup nem arquiva os dados dos clientes. O AVS realiza cópias de segurança periódicas de dados vCenter e NSX para fornecer uma elevada disponibilidade de servidores de gestão. Antes da cópia de segurança, todos os dados são encriptados na fonte vCenter utilizando APIs VMware. Os dados encriptados são transportados e armazenados em blob Azure. As chaves de encriptação para cópias de segurança são armazenadas num cofre gerido a adsbordo a funcionar na rede virtual AVS em Azure.

## <a name="network-security"></a>Segurança da Rede

A solução AVS baseia-se em camadas de segurança da rede.

### <a name="azure-edge-security"></a>Segurança de borda azul

Os serviços AVS são construídos em cima da segurança da rede base fornecida pelo Azure. O Azure aplica técnicas de defesa em profundidade para deteção e resposta atempada a ataques baseados em rede associados a padrões de tráfego anómalos ou a padrões de tráfego e a ataques distribuídos de negação de serviço (DDoS). Este controlo de segurança aplica-se aos ambientes AVS Private Cloud e ao software de plano de controlo desenvolvido pela AVS.

### <a name="segmentation"></a>Segmentação

O serviço AVS tem logicamente redes de Camada 2 que restringem o acesso às suas próprias redes privadas no seu ambiente AVS Private Cloud. Pode proteger ainda mais as suas redes AVS Private Cloud utilizando uma firewall. O portal AVS permite definir regras de controlo de tráfego de rede EW e NS para todo o tráfego de rede, incluindo tráfego intra AVS Private Cloud, tráfego inter-AVS Private Cloud, tráfego geral para a Internet, e tráfego de rede para as instalações através de IPsec VPN ou Ligação ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gestão de vulnerabilidades e patch

A AVS é responsável por patching de segurança periódica de software VMware gerido (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestão de identidades e acesso

Os clientes podem autenticar a sua conta Azure (em Azure AD) utilizando a autenticação de vários fatores ou SSO como preferido. A partir do portal Azure, pode lançar o portal AVS sem reintroduzir credenciais.

A AVS suporta a configuração opcional de uma fonte de identidade para o VCenter De Nuvem Privada AVS. Você pode usar uma [fonte de identidade no local](set-vcenter-identity.md), uma nova fonte de identidade para a Nuvem Privada AVS, ou [Azure AD](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para operações diárias de vCenter dentro da Nuvem Privada AVS. Este nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, pode [aumentar os seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto completa as tarefas administrativas.
