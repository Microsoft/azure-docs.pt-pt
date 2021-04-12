---
title: Solução Azure VMware by CloudSimple - Segurança para Serviços CloudSimple
description: Descreve os modelos de responsabilidade partilhada pela segurança dos serviços CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898798"
---
# <a name="cloudsimple-security-overview"></a>Visão geral da segurança cloudSimple

Este artigo fornece uma visão geral de como a segurança é implementada na Solução VMware Azure por serviço CloudSimple, infraestrutura e datacenter. Aprende-se sobre a proteção de dados e segurança, a segurança da rede e como as vulnerabilidades e patches são geridos.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

A Azure VMware Solution by CloudSimple utiliza um modelo de responsabilidade partilhada para a segurança. A segurança confiável na nuvem é conseguida através das responsabilidades partilhadas dos clientes e da Microsoft como prestador de serviços. Esta matriz de responsabilidade proporciona maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança da infraestrutura Azure incluem os datacenters e a localização do equipamento.

### <a name="datacenter-security"></a>Segurança do Datacenter

A Microsoft tem uma divisão inteira dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Esta equipa está investida na manutenção da segurança física de última geração. Para mais informações sobre segurança física, consulte [instalações, instalações e segurança física da Azure.](../security/fundamentals/physical-security.md)

### <a name="equipment-location"></a>Localização do equipamento

O equipamento de hardware de metal nu que executa as suas Nuvens Privadas está hospedado em locais do datacenter Azure.  As gaiolas onde esse equipamento está requerem a autenticação biométrica de dois fatores para ter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes CloudSimple recebem anfitriões de metal dedicados com discos anexados locais que estão fisicamente isolados de outro hardware de inquilino. Um hipervisor ESXi com vSAN funciona em cada nó. Os nós são geridos através do VMware vCenter e NSX dedicados ao cliente. Não partilhar hardware entre inquilinos fornece uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança de dados

Os clientes mantêm o controlo e a propriedade dos seus dados. A gestão de dados dos dados dos clientes é da responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados em repouso e dados em movimento dentro das redes internas

Para obter dados em repouso no ambiente Private Cloud, pode utilizar encriptação vSAN. vSAN encriptação funciona com servidores de gestão de chaves externos certificados VMware (KMS) na sua própria rede virtual ou no local.  Você mesmo controla as chaves de encriptação de dados. Para dados em movimento dentro da Nuvem Privada, o vSphere suporta a encriptação de dados sobre o fio para todo o tráfego vmkernel (incluindo tráfego vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de Dados para dados que são necessários para passar através de redes públicas

Para proteger os dados que se movem através de redes públicas, pode criar túneis VPN IPsec e TLS para as suas Nuvens Privadas. São suportados métodos de encriptação comuns, incluindo 128 byte e 256 byte AES. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são encriptados com mecanismos de encriptação padrão (SSH, TLS 1.2 e Secure RDP). A comunicação que transporta informação sensível utiliza os mecanismos de encriptação padrão.

### <a name="secure-disposal"></a>Eliminação Segura

Se o seu serviço CloudSimple expirar ou for encerrado, é responsável por remover ou eliminar os seus dados. A CloudSimple cooperará consigo para eliminar ou devolver todos os dados do cliente conforme fornecido no contrato com o cliente, exceto na medida em que a CloudSimple for obrigada pela lei aplicável a reter alguns ou todos os dados pessoais. Se necessário para reter quaisquer dados pessoais, o CloudSimple arquivará os dados e implementará medidas razoáveis para impedir que os dados do cliente sejam posteriormente processados.

### <a name="data-location"></a>Localização de Dados

Ao configurar as suas Nuvens Privadas, escolha a região de Azure onde serão implantadas. Os dados da máquina virtual VMware não são movidos a partir desse datacenter físico, a menos que efetue a migração de dados ou cópia de segurança de dados fora do local. Também pode hospedar cargas de trabalho e armazenar dados em várias regiões do Azure, se adequado às suas necessidades.

Os dados do cliente que são residentes em nódos hiper-convergentes private Cloud não atravessam locais sem a ação explícita do administrador do inquilino. É da sua responsabilidade implementar as suas cargas de trabalho de forma altamente disponível.

### <a name="data-backups"></a>Cópias de segurança dos dados

O CloudSimple não faz o back up ou arquiva os dados dos clientes. O CloudSimple realiza cópias de segurança periódicas de dados vCenter e NSX para fornecer alta disponibilidade de servidores de gestão. Antes da cópia de segurança, todos os dados são encriptados na fonte do vCenter utilizando APIs VMware. Os dados encriptados são transportados e armazenados na bolha de Azure. As chaves de encriptação para cópias de segurança são armazenadas num cofre gerido pela CloudSimple altamente seguro, que funciona na rede virtual CloudSimple em Azure.

## <a name="network-security"></a>Segurança de Rede

A solução CloudSimple baseia-se em camadas de segurança da rede.

### <a name="azure-edge-security"></a>Segurança de borda azul

Os serviços CloudSimple são construídos em cima da segurança da rede base fornecida pela Azure. O Azure aplica técnicas de defesa em profundidade para deteção e resposta oportuna a ataques baseados em rede associados a padrões de tráfego anómalos ou saídas e ataques de negação de serviço distribuídos (DDoS). Este controlo de segurança aplica-se aos ambientes Private Cloud e ao software de plano de controlo desenvolvido pela CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço CloudSimple tem logicamente redes Layer 2 que restringem o acesso às suas próprias redes privadas no seu ambiente Private Cloud. Pode proteger ainda mais as suas redes Private Cloud utilizando uma firewall. O portal CloudSimple permite definir as regras de controlo de tráfego de rede EW e NS para todo o tráfego de rede, incluindo tráfego intra-Private Cloud, tráfego inter-Private Cloud, tráfego geral para a Internet e tráfego de rede para as instalações através da ligação IPsec VPN ou ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gestão de vulnerabilidades e correção

O CloudSimple é responsável pela correção periódica de segurança do software VMware gerido (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

Os clientes podem autenticar na sua conta Azure (em Azure AD) utilizando a autenticação de vários fatores ou SSO como preferido. A partir do portal Azure, pode lançar o portal CloudSimple sem reentrar em credenciais.

CloudSimple suporta configuração opcional de uma fonte de identidade para o private Cloud vCenter. Você pode usar uma [fonte de identidade no local](set-vcenter-identity.md), uma nova fonte de identidade para a Nuvem Privada, ou [Azure AD](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para as operações diárias do vCenter dentro da Nuvem Privada. Este nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, pode [aumentar os seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto completa as tarefas administrativas.
