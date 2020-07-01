---
title: Visão geral da implementação do Azure Firewall Manager
description: Conheça as etapas de implantação de alto nível necessárias para o Gestor de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 90f817ac3bbd475d8a84df44bc284f09fcd19ce3
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565802"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Visão geral da implementação do Azure Firewall Manager

Há mais de uma maneira de implantar o Azure Firewall Manager, mas recomenda-se o seguinte processo geral.

## <a name="general-deployment-process"></a>Processo de implementação geral

### <a name="hub-virtual-networks"></a>Redes virtuais do hub

1.  Criar uma política de firewall

    - Criar uma nova política
<br>*ou*<br>
    - Derivar uma política de base e personalizar uma política local
<br>*ou*<br>
    - Regras de importação de um Azure Firewall existente. Certifique-se de remover as regras NAT de políticas que devem ser aplicadas em várias firewalls
1. Crie o seu centro e tenha arquitetura falada
   - Criar uma rede virtual hub usando Azure Firewall Manager e peer falou redes virtuais para ele usando o espreitamento de rede virtual
<br>*ou*<br>
    - Crie uma rede virtual e adicione ligações virtuais de rede e redes virtuais de portagem por pares usando o espreitamento de rede virtual

3. Selecione os fornecedores de segurança e associe a política de firewall. Atualmente, apenas a Azure Firewall é um fornecedor apoiado.

   - Isto é feito enquanto cria uma Rede Virtual Hub
<br>*ou*<br>
    - Converter uma rede virtual existente numa Rede Virtual Hub. Também é possível converter várias redes virtuais.

4. Configure as rotas de definição do utilizador para encaminhar o tráfego para a firewall da rede virtual hub.


### <a name="secured-virtual-hubs"></a>Centros virtuais seguros

1. Crie o seu centro e tenha arquitetura falada

   - Crie um Hub Virtual Seguro utilizando o Azure Firewall Manager e adicione ligações de rede virtuais.<br>*ou*<br>
   - Crie um Hub WAN Virtual e adicione ligações de rede virtuais.
2. Selecione fornecedores de segurança

   - Feito ao criar um Hub Virtual Seguro.<br>*ou*<br>
   - Converta um hub VIRTUAL WAN existente para secure Virtual Hub.
3. Crie uma política de firewall e associe-a ao seu hub

   - Aplicável apenas se utilizar a Firewall Azure.
   - A segurança de terceiros como um serviço (SECaaS) as políticas são configuradas através da experiência de gestão de parceiros.
4. Configurar as definições de rota para encaminhar o tráfego para o seu hub seguro

   - Encaminhe facilmente o tráfego para o seu hub seguro para filtragem e registo sem rotas definidas pelo utilizador (UDR) em redes virtuais faladas utilizando a página de Definição de Rota do Hub Virtual Segura.

> [!NOTE]
> - Não se pode ter mais do que um centro por cada região. Mas pode adicionar várias WANs virtuais na região para conseguir isso.
> - Não pode ter espaços IP sobrepostos para centros em um vWAN.
> - As ligações VNet do seu hub devem estar na mesma região que o hub.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md)