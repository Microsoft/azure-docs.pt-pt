---
title: Segurança para serviços de CloudSimple
description: Descreve os modelos de responsabilidade partilhada para a segurança dos serviços de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358127"
---
# <a name="cloudsimple-security-overview"></a>Descrição geral da segurança de CloudSimple

Este artigo fornece uma descrição geral de como a segurança é implementada no serviço CloudSimple, infraestrutura e de datacenters.  Saiba sobre proteção de dados e a segurança, a segurança de rede e como são geridos vulnerabilidades e patches.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Solução de VMware do Azure por CloudSimple utiliza um modelo de responsabilidade partilhada para a segurança. Segurança fidedigna na cloud é obtida por meio das responsabilidades partilhadas de clientes e a Microsoft como um fornecedor de serviços. Essa matriz de responsabilidade proporciona uma maior segurança e elimina pontos únicos de falha. 

## <a name="azure-infrastructure"></a>Infraestrutura do Azure 

Considerações de segurança da infraestrutura do Azure incluem a localização de datacenters e de equipamento. 

### <a name="datacenter-security"></a>Segurança do Centro de dados 

A Microsoft tem uma divisão de inteiro dedicado a conceber, criar e utilizar os recursos físicos de suporte do Azure. Esta equipe é investido em manter a segurança física de topo de gama. Para obter detalhes sobre a segurança física, consulte [recursos do Azure, no local e a segurança física](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Localização de equipamento

O equipamento de hardware de metal básico que executa suas nuvens privadas está alojado em localizações de datacenters do Azure.  Compartimentos onde se encontra este equipamento, requer uma autenticação de dois fatores baseada em biométrica para obter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes de CloudSimple obtém anfitriões dedicados de metal bare com discos locais anexados que estão fisicamente isolados de outro hardware de inquilino. Um hipervisor de ESXi com vSAN é executado em cada nó. Os nós são geridos através do cliente dedicado VMware vCenter e NSX. Hardware entre inquilinos de partilha não fornece uma camada adicional de proteção de segurança e isolamento.

## <a name="data-security"></a>Segurança de dados

Os clientes manter o controlo e a propriedade dos respetivos dados. Senso de gerenciamento de dados dos dados dos clientes é da responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados inativos e dados em movimento em redes internas

Para dados Inativos no ambiente de nuvem privada, pode utilizar a encriptação de vSAN. encriptação de vSAN funciona com o VMware certificada servidores de gestão de chaves externas (KMS) na sua própria rede virtual ou no local.  Controla as chaves de encriptação de dados por conta própria. Para dados em movimento na nuvem privada, vSphere suporta a encriptação de dados durante a transmissão para todo o tráfego vmkernel (incluindo o tráfego de vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de dados para dados que é necessário passar por redes públicas

Para proteger os dados que se move através de redes públicas, pode criar IPsec e SSL VPN túneis para suas nuvens privadas. São suportados métodos de encriptação comum, incluindo AES de 128 e 256 bytes. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são criptografados com mecanismos de encriptação padrão (SSH, TLS 1.2 e proteger RDP). A comunicação que transporta informações confidenciais utiliza os mecanismos de encriptação padrão.

### <a name="secure-disposal"></a>Eliminação segura 

Se o seu serviço CloudSimple expira ou é encerrado, é responsável por remover ou eliminar os seus dados. CloudSimple será cooperam com para eliminar ou devolver todos os dados do cliente, conforme indicado no contrato do cliente, exceto na medida CloudSimple é exigido pela lei aplicável para reter alguns ou todos os dados pessoais. Se for necessário para reter dados pessoais, CloudSimple irá arquivar os dados e implementar medidas razoáveis para impedir que os dados do cliente qualquer processamento adicional.

### <a name="data-location"></a>Localização dos Dados

Quando configurar as nuvens privadas, escolher a região do Azure onde serão implementados. Dados da máquina virtual VMware não são movidos desse datacenter físico, a menos que executar a migração de dados ou a cópia de segurança de dados de armazenamento externo. Também pode alojar cargas de trabalho e armazenar dados em várias regiões do Azure, se for adequado para as suas necessidades.

Os dados do cliente que é residentes em nós hiperconvergidos de nuvem privada não atravessam locais sem a ação explícita do administrador inquilino. É sua responsabilidade implementar cargas de trabalho de uma forma de elevada disponibilidade.

### <a name="data-backups"></a>Cópias de segurança de dados
CloudSimple não fazer cópias de segurança ou arquivar dados do cliente. CloudSimple efetuar cópia de segurança periódica do vCenter e os dados NSX para proporcionar elevada disponibilidade dos servidores de gestão. Antes da cópia de segurança, todos os dados são encriptados em origem de vCenter através de APIs de VMware. Os dados encriptados são transportados e armazenados em BLOBs do Azure. Chaves de encriptação para as cópias de segurança são armazenadas num cofre de gerido CloudSimple altamente seguro em execução na rede virtual CloudSimple no Azure.

## <a name="network-security"></a>Segurança de Rede

A solução de CloudSimple se baseia em camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança de borda do Azure

Os serviços de CloudSimple são criados sobre a segurança de rede base fornecida pelo Azure. Azure aplica-se técnicas de defesa em profundidade para deteção e resposta rápida para ataques baseados na rede associados à entrada anómala ou padrões de tráfego de saída e distribuídos (DDoS) distribuídos do denial-of-service. Este controlo de segurança se aplica a ambientes de nuvem privada e o software de plano de controle desenvolvidos por CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço de CloudSimple tem redes de camada 2 logicamente distintas que restringem o acesso a suas próprias redes privadas no seu ambiente de nuvem privada. Pode proteger ainda mais suas redes de nuvem privada a utilizar uma firewall. O Portal de CloudSimple permite-lhe definir o novo e NS rede controles regras de tráfego para todo o tráfego de rede, incluindo dentro do tráfego de nuvem privada, o tráfego de nuvem privado entre, o tráfego geral para a Internet e tráfego de rede para no local através de IPsec VPN ou Ligação do ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Vulnerabilidade e gerenciamento de patches 

CloudSimple é responsável pela aplicação de patches de segurança periódica de software gerenciado do VMware (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestão de Identidades e Acessos

Os clientes podem autenticar-se à sua conta do Azure (no Azure AD) com autenticação multifator ou SSO como preferencial. No portal do Azure, pode iniciar o Portal de CloudSimple sem entrar novamente as credenciais.

CloudSimple suporta a configuração opcional de uma origem de identidades para o vCenter de nuvem privada. Pode utilizar um [origem de identidades no local](https://docs.azure.cloudsimple.com/set-vcenter-identity), uma nova origem de identidade para a nuvem privada, ou [do Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Por predefinição, os clientes recebem os privilégios necessários para operações diárias do vCenter na nuvem privada. Este nível de permissão não inclui o acesso administrativo ao vCenter. Se o acesso administrativo é temporariamente necessário, pode [aumentar os seus privilégios](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) por um período limitado, enquanto as tarefas administrativas.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço de CloudSimple no Azure](quickstart-create-cloudsimple-service.md)
* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
