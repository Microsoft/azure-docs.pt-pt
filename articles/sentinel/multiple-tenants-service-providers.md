---
title: Gerir vários inquilinos em Azure Sentinel como prestador de serviços de segurança gerido | Microsoft Docs
description: Como embarcar e gerir vários inquilinos em Azure Sentinel como um Fornecedor de Serviços de Segurança Gerido (MSSP) usando o Farol Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578156"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Gerir vários inquilinos em Azure Sentinel como MSSP

Se é um prestador de serviços de segurança gerido (MSSP) e está a usar [o Farol Azure](../lighthouse/overview.md) para oferecer serviços de segurança ao Centro de Operações de Segurança (SOC) aos seus clientes, pode gerir os recursos do Azure Sentinel dos seus clientes diretamente do seu próprio inquilino Azure, sem ter de se ligar ao inquilino do cliente. 

## <a name="prerequisites"></a>Pré-requisitos

- [A bordo do Farol de Azure](../lighthouse/how-to/onboard-customer.md)

- Para que funcione corretamente, o seu inquilino (o inquilino da MSSP) deve ter os fornecedores de recursos Azure Sentinel registados em pelo menos uma subscrição. Além disso, cada um dos inquilinos dos seus clientes deve ter os fornecedores de recursos registados. Se registou a Azure Sentinel no seu inquilino e os seus clientes no deles, está pronto para começar. Para verificar o registo, tome as seguintes medidas:

    1. Selecione **Subscrições** a partir do portal Azure e, em seguida, selecione uma subscrição relevante no menu.

    1. A partir do menu de navegação no ecrã de subscrição, em **Definições,** selecione **Fornecedores de Recursos**.

    1. Do nome de ***subscrição* | Os fornecedores de recursos** rastream, procuram e selecionam *Microsoft.OperationalInsights* e *Microsoft.SecurityInsights* e verifique a coluna **Status.** Se o estado do fornecedor não for *registado,* selecione **Registar-se**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Consulte os fornecedores de recursos":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Como aceder a Azure Sentinel em inquilinos geridos

1. Sob **a subscrição Do Diretório +** selecione os diretórios delegados (diretório = inquilino) e as subscrições onde estão localizados os espaços de trabalho Azure Sentinel do seu cliente.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Escolha inquilinos e assinaturas":::

1. Abra o Azure Sentinel. Você verá todos os espaços de trabalho nas subscrições selecionadas, e você será capaz de trabalhar com eles perfeitamente, como qualquer espaço de trabalho em seu próprio inquilino.

> [!NOTE]
> Não será capaz de implantar conectores em Azure Sentinel dentro de um espaço de trabalho gerido. Para implantar um conector, deve inscrever-se diretamente no inquilino no qual pretende implantar um conector e autenticar lá com as permissões necessárias.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a gerir vários inquilinos do Azure Sentinel de forma perfeita. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

