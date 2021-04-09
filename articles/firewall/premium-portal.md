---
title: Pré-visualização premium Azure Firewall no portal Azure
description: Saiba mais sobre a pré-visualização Azure Firewall Premium no portal Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549836"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Pré-visualização premium Azure Firewall no portal Azure

> [!IMPORTANT]
> O Azure Firewall Premium está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A Azure Firewall Premium Preview é uma firewall de próxima geração com capacidades que são necessárias para ambientes altamente sensíveis e regulados. Inclui as seguintes características:

- **Inspeção TLS** - desencripta o tráfego de saída, processa os dados, em seguida, encripta os dados e envia-os para o destino.
- **IDPS** - Um sistema de deteção e prevenção de intrusões em rede (IDPS) permite monitorizar atividades de rede para atividades maliciosas, registar informações sobre esta atividade, denunciá-la e tentar bloqueá-la opcionalmente.
- **Filtragem de URL** - estende a capacidade de filtragem FQDN da Azure Firewall para considerar um URL inteiro. Por exemplo, `www.contoso.com/a/c` em vez `www.contoso.com` de. .
- **Categorias web** - os administradores podem permitir ou negar o acesso do utilizador a categorias de websites, tais como sites de jogos, sites de redes sociais, entre outros.

Para mais informações, consulte [as funcionalidades Azure Firewall Premium](premium-features.md).

## <a name="deploy-the-firewall"></a>Implementar a firewall

A implementação de uma pré-visualização Azure Firewall Premium é semelhante à implementação de um Azure Firewall padrão:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="implantação portal":::

Para **o nível de Firewall**, selecione Premium **(pré-visualização)** e para **a política de Firewall**, selecione uma política Premium existente ou crie uma nova.

## <a name="configure-the-premium-policy"></a>Configure a política Premium

Configurar uma política de firewall Premium é semelhante à configuração de uma política de firewall Standard. Com uma política Premium, pode configurar as funcionalidades Premium:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Implantação de políticas premium":::

### <a name="rule-configuration"></a>Configuração de regras

Ao configurar as regras de aplicação numa apólice Premium, pode configurar funcionalidades Premium de adição:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Regra premium":::

## <a name="next-steps"></a>Passos seguintes

Para ver as funcionalidades de pré-visualização Azure Firewall Premium em ação, consulte [implementar e configurar a pré-visualização Azure Firewall Premium](premium-deploy.md).