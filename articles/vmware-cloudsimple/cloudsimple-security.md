---
title: Segurança para serviços de CloudSimple
description: Descreve os modelos de responsabilidade partilhada para a segurança dos serviços de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595329"
---
# <a name="cloudsimple-security-overview"></a>Descrição geral da segurança de CloudSimple

Este artigo fornece uma descrição geral de como a segurança na solução de VMware do Azure é implementada pelo serviço CloudSimple, infraestrutura e de datacenters. Saiba sobre proteção de dados e segurança, segurança de rede e como são geridos vulnerabilidades e patches.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Solução de VMware do Azure por CloudSimple utiliza um modelo de responsabilidade partilhada para a segurança. Segurança fidedigna na cloud é obtida por meio das responsabilidades partilhadas de clientes e a Microsoft como um fornecedor de serviços. Essa matriz de responsabilidade proporciona uma maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure 

Considerações de segurança da infraestrutura do Azure incluem a localização de datacenters e de equipamento.

### <a name="datacenter-security"></a>Segurança do Centro de dados 

A Microsoft tem uma divisão de inteiro dedicado a conceber, criar e utilizar os recursos físicos que suportam o Azure. Esta equipe é investido em manter a segurança física de topo de gama. Para obter mais informações sobre a segurança física, consulte [recursos do Azure, no local e a segurança física](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Localização de equipamento

O equipamento de bare-metal de hardware que executa a nuvens privadas está alojado em localizações de datacenters do Azure. Autenticação de dois fatores baseada em biométricas é necessária para obter acesso aos compartimentos onde reside este equipamento.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes de CloudSimple obtém dedicados de bare-metal anfitriões com discos locais anexados que estão fisicamente isolados de outro hardware de inquilino. Um hipervisor de ESXi com vSAN é executado em cada nó. Os nós são geridos através do cliente dedicados VMware vCenter e NSX. Hardware entre inquilinos de partilha não fornece uma camada adicional de proteção de segurança e isolamento.

## <a name="data-security"></a>Segurança de dados

Os clientes manter o controlo e a propriedade dos respetivos dados. Senso de gerenciamento de dados dos dados dos clientes é da responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados inativos e dados em movimento em redes internas

Para dados Inativos no ambiente de nuvem privada, pode utilizar a encriptação de vSAN. encriptação de vSAN funciona com servidores de VMware com certificação externa gestão de chaves (KMS) em sua própria rede virtual ou no local. Controla as chaves de encriptação de dados por conta própria. Para dados em movimento na nuvem privada, vSphere suporta a encriptação de dados durante a transmissão para todo o tráfego VMkernel, de que inclui o tráfego do vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Proteção de dados para dados necessários para percorrer a redes públicas

Para proteger os dados que se move através de redes públicas, pode criar IPsec e SSL VPN túneis para nuvens privadas. São suportados métodos de encriptação comum, incluindo AES de 128 e 256 bytes. Dados em trânsito, incluindo autenticação, acesso administrativo e dados dos clientes, são encriptados com mecanismos de encriptação padrão, como RDP seguro, TLS 1.2 e SSH. A comunicação que transporta informações confidenciais utiliza os mecanismos de encriptação padrão.

### <a name="secure-disposal"></a>Eliminação segura 

Se o seu serviço CloudSimple expira ou é encerrado, é responsável por remover ou eliminar os seus dados. CloudSimple cooperates com para eliminar ou devolver todos os dados do cliente, conforme indicado no contrato do cliente, exceto na medida CloudSimple é exigido pela lei aplicável para reter alguns ou todos os dados pessoais. Se for necessário para reter dados pessoais, CloudSimple arquiva dados e implementa razoáveis medidas para impedir que os dados do cliente qualquer processamento adicional.

### <a name="data-location"></a>Localização de dados

Quando configurar nuvens privadas, escolher a região do Azure onde estão implementadas. Dados da máquina virtual VMware não são movidos entre esse datacenter físico, a menos que executar a migração de dados ou a cópia de segurança de dados de armazenamento externo. Também pode alojar cargas de trabalho e armazenar dados em várias regiões do Azure, se for adequado para as suas necessidades.

Os dados do cliente que é residentes em nós de hiperconvergido de nuvem privada não atravessam locais sem a ação explícita do administrador inquilino. É sua responsabilidade implementar cargas de trabalho de uma forma de elevada disponibilidade.

### <a name="data-backups"></a>Cópias de segurança de dados
CloudSimple não fazer cópias de segurança ou arquivar dados do cliente. CloudSimple efetuar cópia de segurança periódica do vCenter e os dados NSX para proporcionar elevada disponibilidade dos servidores de gestão. Antes da cópia de segurança, todos os dados são encriptados na origem de vCenter através de APIs de VMware. Os dados encriptados são transportados e armazenados num blob do Azure. Chaves de encriptação para as cópias de segurança são armazenadas num cofre CloudSimple gerido altamente seguro que é executado na rede virtual CloudSimple no Azure.

## <a name="network-security"></a>Segurança da rede

A solução de CloudSimple se baseia em camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança de borda do Azure

Os serviços de CloudSimple são criados sobre a segurança de rede base fornecida pelo Azure. Azure aplica-se técnicas de defesa em profundidade para deteção e resposta rápida para ataques baseados na rede associados à entrada anómala ou padrões de tráfego de saída e distribuídos (DDoS) distribuídos do denial-of-service. Este controlo de segurança se aplica a ambientes de nuvem privada e o software de plano de controle desenvolvidos por CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço de CloudSimple tem redes de camada 2 logicamente distintas que restringem o acesso a suas próprias redes privadas no seu ambiente de nuvem privada. Pode proteger ainda mais suas redes de nuvem privada através de uma firewall. No portal do CloudSimple, pode definir regras de controlo de tráfego de rede este-oeste e Norte-Sul para todo tráfego de rede, que inclui o tráfego de nuvem flui privada, o tráfego de nuvem privada entre, tráfego geral para a Internet e tráfego de rede no local através de ligação de IPsec VPN ou o Azure ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gerenciamento de patches e de vulnerabilidade 

CloudSimple é responsável pela aplicação de patches de segurança periódica de software gerenciado de VMware, como NSX, vCenter e ESXi.

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

Os clientes podem autenticar a sua conta do Azure (no Azure Active Directory) ao utilizar a autenticação multifator ou SSO como preferencial. No portal do Azure, pode iniciar o portal de CloudSimple sem entrar novamente as credenciais.

CloudSimple suporta a configuração opcional de uma origem de identidades para o vCenter de nuvem privada. Pode utilizar um [origem de identidades no local](https://docs.azure.cloudsimple.com/set-vcenter-identity), uma nova origem de identidade para a nuvem privada, ou [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Por predefinição, os clientes recebem os privilégios necessários para operações diárias do vCenter na nuvem privada. Este nível de permissão não inclui o acesso administrativo ao vCenter. Se o acesso administrativo é temporariamente necessário, pode [aumentar os seus privilégios](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) por um período limitado, enquanto as tarefas administrativas.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço de CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).
