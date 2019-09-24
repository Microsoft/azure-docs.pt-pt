---
title: Atualizar para a camada Standard da central de segurança para segurança aprimorada | Microsoft Docs
description: Este artigo fornece informações sobre preços da central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: memildin
ms.openlocfilehash: 0663e97dabe8b5f50ee9605f0bc9319c7a2aa13c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201236"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard da central de segurança para aumentar a segurança
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Ele fornece visibilidade e controle sobre as cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar os ataques cibernéticos em rápida evolução.

## <a name="pricing-tiers"></a>Escalões de preço
Centro de segurança é disponibilizado em dois escalões:

- A camada **gratuita** é habilitada automaticamente em todas as assinaturas do Azure e fornece política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.
- A camada **Standard** estende os recursos da camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos avançados de detecção de ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos para reduzir a exposição a ataques de rede e malware e cada. Você pode experimentar a camada Standard gratuitamente. A central de segurança Standard dá suporte a recursos do Azure, incluindo VMs, conjuntos de dimensionamento de máquinas virtuais, serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tiver a central de segurança do Azure padrão, poderá recusar o suporte com base no tipo de recurso. 


Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/)da central de segurança.

## <a name="try-standard-free-for-30-days"></a>Experimente o Standard gratuitamente por 30 dias
A camada Standard é oferecida gratuitamente nos primeiros 30 dias. No final de 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que é herdada por todos os recursos na assinatura.

Para obter a camada Standard:

1. Selecione **configurações de & de preços** no menu principal da **central de segurança** .
2. Selecione a assinatura que você deseja atualizar para o padrão.
3. Selecione **tipo de preço**.
4. Selecione **Standard** para atualizar.
5. Clique em **Guardar**.

(Os preços na imagem são apenas para fins de exemplo.) ![Preços da central de segurança](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de Segurança, tem de aplicar o escalão de preço Standard à subscrição que contém as máquinas virtuais aplicáveis. A configuração dos preços para uma área de trabalho não ativa o acesso à VM just-in-time, os controlos de aplicação adaptáveis e as deteções de rede para os recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para o padrão?
A central de segurança oferece proteção avançada contra ameaças e segurança para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – obtenha uma exibição unificada de segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida para garantir a conformidade com os padrões de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** -use a análise avançada e a gráfico de segurança inteligente da Microsoft para obter uma borda sobre ataques cibernéticos em evolução.  Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controlos de acesso e aplicação** -bloqueie o malware e outras aplicações indesejadas ao aplicar recomendações de lista aprovada adaptadas às suas cargas de trabalho específicas e com tecnologia de machine Learning. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à força bruta e a outros ataques de rede.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você foi apresentado aos preços da central de segurança. Para saber mais sobre a segurança avançada da camada Standard e a proteção avançada contra ameaças, consulte:

- [Deteção avançada de ameaças](security-center-threat-report.md)
- [Controle de acesso de VM just in time](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
