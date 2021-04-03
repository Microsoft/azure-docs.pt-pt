---
title: Visão geral da implementação do Azure Firewall Manager
description: Conheça as etapas de implantação de alto nível necessárias para o Gestor de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079103"
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
>
> Para questões mais conhecidas, veja [o que é Azure Firewall Manager?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Converter redes virtuais

As seguintes informações aplicam-se se converter uma rede virtual existente numa rede virtual de hub:

- Se a rede virtual tiver um Azure Firewall existente, selecione uma Política de Firewall para associar-se à firewall existente. O estado de provisionamento do firewall será atualizado enquanto a política de firewall substitui as regras de firewall. Durante o estado de provisionamento, a firewall continua a processar o tráfego e não tem tempo de inatividade. Pode importar as regras existentes para uma Política de Firewall utilizando o Firewall Manager ou o Azure PowerShell.
- Se a rede virtual não tiver um Azure Firewall associado, é implantada uma firewall e a Política de Firewall está associada à nova firewall.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md)