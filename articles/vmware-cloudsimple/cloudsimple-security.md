---
title: Solução Azure VMware by CloudSimple - Segurança para Serviços CloudSimple
description: Descreve os modelos de responsabilidade partilhada para a segurança dos serviços CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d86c90828c081a542fa5574493a46e8a2e44640
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187482"
---
# <a name="cloudsimple-security-overview"></a>Visão geral de segurança CloudSimple

Este artigo fornece uma visão geral de como a segurança é implementada na Solução Azure VMware pelo serviço, infraestrutura e datacenter Do Azure VMware. Aprende-se sobre proteção de dados e segurança, segurança da rede e como as vulnerabilidades e patches são geridos.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

A Azure VMware Solution by CloudSimple utiliza um modelo de responsabilidade partilhada para a segurança. A segurança fidedigna na nuvem é alcançada através das responsabilidades partilhadas dos clientes e da Microsoft como prestadora de serviços. Esta matriz de responsabilidade proporciona maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança da infraestrutura Azure incluem os datacenters e a localização do equipamento.

### <a name="datacenter-security"></a>Segurança do datacenter

A Microsoft tem uma divisão inteira dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Esta equipa está a investir na manutenção da segurança física de última geração. Para mais detalhes sobre segurança física, consulte [instalações, instalações e segurança física do Azure.](../security/azure-physical-security.md)

### <a name="equipment-location"></a>Localização do equipamento

O equipamento de hardware de metal nu que executa as suas Nuvens Privadas está hospedado em localizações de datacenter do Azure.  As gaiolas onde esse equipamento está, requerem autenticação biométrica baseada em dois fatores para ter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes CloudSimple recebem anfitriões de metal nus dedicados com discos ligados locais que estão fisicamente isolados de outros hardware de inquilinos. Um hipervisor ESXi com vSAN corre em cada nó. Os nódosos são geridos através do vCenter e NSX dedicados ao cliente. A não partilha de hardware entre inquilinos proporciona uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança de dados

Os clientes mantêm o controlo e a propriedade dos seus dados. A gestão de dados dos dados dos clientes é da responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados em repouso e dados em movimento dentro das redes internas

Para dados em repouso no ambiente Private Cloud, pode utilizar encriptação vSAN. a encriptação vSAN funciona com servidores de gestão de chaves externas certificados vMware (KMS) na sua própria rede virtual ou no local.  Você controla as chaves de encriptação de dados. Para dados em movimento dentro da Nuvem Privada, a vSphere suporta a encriptação de dados sobre o fio para todo o tráfego vmkernel (incluindo o tráfego vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de Dados para dados que são necessários para circular através de redes públicas

Para proteger os dados que se movem através de redes públicas, pode criar túneis VPN IPsec e TLS para as suas Nuvens Privadas. São suportados métodos comuns de encriptação, incluindo 128 bytes e 256 byte AES. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são encriptados com mecanismos de encriptação padrão (SSH, TLS 1.2 e Secure RDP). A comunicação que transporta informação sensível utiliza os mecanismos padrão de encriptação.

### <a name="secure-disposal"></a>Eliminação Segura

Se o seu serviço CloudSimple expirar ou for encerrado, é responsável pela remoção ou eliminação dos seus dados. A CloudSimple cooperará consigo para eliminar ou devolver todos os dados do cliente conforme fornecido no contrato de cliente, exceto na medida em que a CloudSimple seja obrigada pela lei aplicável a reter alguns ou todos os dados pessoais. Se necessário para reter quaisquer dados pessoais, a CloudSimple irá arquivar os dados e implementar medidas razoáveis para impedir que os dados do cliente sejam processados.

### <a name="data-location"></a>Localização de Dados

Ao configurar as suas Nuvens Privadas, escolha a região de Azure onde serão implantadas. Os dados da máquina virtual VMware não são transferidos desse datacenter físico a menos que realize a migração de dados ou a cópia de segurança de dados fora do local. Também pode alojar cargas de trabalho e armazenar dados em várias regiões do Azure, se for caso disso para as suas necessidades.

Os dados do cliente residentes em nódos hiper-convergentes Private Cloud não atravessam locais sem a ação explícita do administrador inquilino. É da sua responsabilidade implementar as suas cargas de trabalho de forma altamente disponível.

### <a name="data-backups"></a>Cópias de segurança dos dados

A CloudSimple não faz o backup nem arquiva os dados dos clientes. O CloudSimple executa cópias de segurança periódicas de dados vCenter e NSX para fornecer uma elevada disponibilidade de servidores de gestão. Antes da cópia de segurança, todos os dados são encriptados na fonte vCenter utilizando APIs VMware. Os dados encriptados são transportados e armazenados em blob Azure. As chaves de encriptação para backups são armazenadas num cofre gerido pela CloudSimple altamente seguro que funciona na rede virtual CloudSimple em Azure.

## <a name="network-security"></a>Segurança de Rede

A solução CloudSimple baseia-se em camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança de borda azul

Os serviços CloudSimple são construídos em cima da segurança da rede base fornecida pelo Azure. O Azure aplica técnicas de defesa em profundidade para deteção e resposta atempada a ataques baseados em rede associados a padrões de tráfego anómalos ou a padrões de tráfego e a ataques distribuídos de negação de serviço (DDoS). Este controlo de segurança aplica-se aos ambientes Private Cloud e ao software de controlo de aviões desenvolvido pela CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço CloudSimple tem logicamente redes de Camada 2 que restringem o acesso às suas próprias redes privadas no seu ambiente Private Cloud. Pode proteger ainda mais as suas redes Private Cloud utilizando uma firewall. O portal CloudSimple permite definir regras de controlo de tráfego de rede EW e NS para todo o tráfego de rede, incluindo tráfego intra Private Cloud, tráfego inter-Private Cloud, tráfego geral para a Internet e tráfego de rede para as instalações através da ligação IPsec VPN ou ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gestão de vulnerabilidades e patch

A CloudSimple é responsável por patching de segurança periódica do software VMware gerido (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

Os clientes podem autenticar a sua conta Azure (em Azure AD) utilizando a autenticação de vários fatores ou SSO como preferido. A partir do portal Azure, pode lançar o portal CloudSimple sem reintroduzir credenciais.

CloudSimple suporta configuração opcional de uma fonte de identidade para o vCenter private Cloud. Você pode usar uma [fonte de identidade no local](set-vcenter-identity.md), uma nova fonte de identidade para a Nuvem Privada, ou [Azure AD](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para operações diárias de vCenter dentro da Cloud Privada. Este nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, pode [aumentar os seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto completa as tarefas administrativas.
