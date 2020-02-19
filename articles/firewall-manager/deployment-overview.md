---
title: Visão geral de implementação do Gestor de Firewall Azure
description: Conheça os passos de implementação de alto nível necessários para a Pré-visualização do Gestor de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443130"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Visão geral de implementação do Gestor de Firewall Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Há mais de uma maneira de implementar a Pré-visualização do Gestor de Firewall do Azure, mas recomenda-se o seguinte processo geral.

## <a name="general-deployment-process"></a>Processo de implementação geral

### <a name="hub-virtual-networks"></a>Redes virtuais hub

1.  Criar uma política de firewall

    - Criar uma nova política
<br>*ou*<br>
    - Obtenha uma política de base e personalize uma política local
<br>*ou*<br>
    - Regras de importação de uma Firewall Azure existente. Certifique-se de remover as regras naT das políticas que devem ser aplicadas através de várias firewalls
1. Crie o seu hub e arquitetura falada
   - Crie uma Rede Virtual Hub utilizando o Azure Firewall Manager e o peer spoke redes virtuais para ela usando o peering de rede virtual
<br>*ou*<br>
    - Crie uma rede virtual e adicione conexões de rede virtuais e redes virtuais de pares falou-lhe usando o peering de rede virtual

3. Selecione fornecedores de segurança e associate firewall policy. Atualmente, apenas o Azure Firewall é um fornecedor apoiado.

   - Isto é feito enquanto cria uma Rede Virtual Hub
<br>*ou*<br>
    - Converta uma rede virtual existente para uma Rede Virtual Hub. Também é possível converter várias redes virtuais.

4. Configure as rotas de definição do utilizador para encaminhar o tráfego para a sua firewall de Rede Virtual Hub.


### <a name="secured-virtual-hubs"></a>Centros virtuais seguros

1. Crie o seu hub e arquitetura falada

   - Crie um Hub Virtual Seguro utilizando o Azure Firewall Manager e adicione ligações de rede virtuais.<br>*ou*<br>
   - Crie um Hub WAN Virtual e adicione ligações de rede virtuais.
2. Selecione fornecedores de segurança

   - Feito enquanto cria um Centro Virtual Seguro.<br>*ou*<br>
   - Converta um hub virtual WAN existente para Secure Virtual Hub.
3. Crie uma política de firewall e associe-a ao seu hub

   - Aplicável apenas se utilizar o Firewall Azure.
   - As políticas de segurança de terceiros como serviço (SECaaS) são configuradas através da experiência de gestão de parceiros.
4. Configure as definições de rota para encaminhar o tráfego para o seu centro seguro

   - Encaminhe facilmente o tráfego para o seu hub seguro para filtrar e registar sem rotas definidas pelo utilizador (UDR) em redes virtuais faladas utilizando a página de Definição de Rota do Hub Virtual Seguro.

> [!NOTE]
> - Não se pode ter mais do que um centro por cada um virtual por região. Mas pode adicionar várias WANs virtuais na região para o conseguir.
> - Não pode ter espaços IP sobrepostos para centros num vWAN.
> - As ligações VNet do seu hub devem estar na mesma região que o centro.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com a Pré-visualização do Gestor de Firewall Azure utilizando o portal Azure](secure-cloud-network.md)