---
title: Trabalhar com vários locatários para o Azure Sentinel para provedores de serviço MSSP | Microsoft Docs
description: Como trabalhar com vários locatários para o Azure Sentinel para provedores de serviço MSSP.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240497"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Trabalhar com vários locatários no Azure Sentinel 

Se você for um MSSP (provedor de serviços de segurança gerenciado) e estiver usando o [Azure Lighthouse](../lighthouse/overview.md) para gerenciar o SOC (Security Operations centers) dos seus clientes, poderá gerenciar os recursos do Azure Sentinel de seus clientes sem se conectar diretamente ao locatário do cliente, de seu próprio locatário do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- [Integração do Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Para que isso funcione corretamente, seu locatário deve ser registrado no provedor de recursos do Azure Sentinel em pelo menos uma assinatura. Se você tiver um Sentinela do Azure registrado em seu locatário, você estará pronto para começar. Caso contrário, na portal do Azure, selecione **assinaturas** seguidas pelos provedores de **recursos** e `Microsoft.Security.Insights` , em seguida, pesquise e selecione **registrar**.
   ![Verificar provedores de recursos](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Como acessar o Azure Sentinel de outros locatários
1. Em **diretório + assinatura**, selecione os diretórios delegados e as assinaturas nas quais os espaços de trabalho do Azure Sentinel do seu cliente estão localizados.

   ![Gerar incidentes de segurança](media/multiple-tenants-service-providers/directory-subscription.png)

1. Abra o Azure Sentinel. Você verá todos os espaços de trabalho nas assinaturas selecionadas e poderá trabalhar com eles diretamente, como qualquer espaço de trabalho em seu próprio locatário.

> [!NOTE]
> Você não poderá implantar conectores no Azure Sentinel de dentro de um espaço de trabalho gerenciado. Para implantar um conector, você deve entrar diretamente no locatário no qual deseja implantar um conector e autenticar-se com as permissões necessárias.





## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a gerenciar vários locatários do Azure Sentinel diretamente. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

