---
title: Preços das camadas da central de segurança do Azure
description: A central de segurança do Azure é oferecida em duas camadas – gratuita e Standard. Esta página mostra como atualizar de gratuito para Standard.
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 8ffb571d13270ced80426aee3575197cf95d3805
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559235"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard da central de segurança para aumentar a segurança
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Ele fornece visibilidade e controle sobre as cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar os ataques cibernéticos em rápida evolução.

## <a name="pricing-tiers"></a>Escalões de preço
O Centro de Segurança é disponibilizado em dois escalões:

- A camada **gratuita** é habilitada em todas as suas assinaturas do Azure depois que você visita o painel da central de segurança do azure na portal do Azure pela primeira vez ou, se habilitada programaticamente via API. A camada gratuita fornece política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.
- A camada **Standard** estende os recursos da camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos avançados de detecção de ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos para reduzir a exposição a ataques de rede e malware e cada. Você pode experimentar a camada Standard gratuitamente. A central de segurança Standard dá suporte a recursos do Azure, incluindo VMs, conjuntos de dimensionamento de máquinas virtuais, serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tiver a central de segurança do Azure Standard, poderá recusar o suporte com base no tipo de recurso. 

A maioria das avaliações de segurança de camada gratuita para VMs, bem como muitos dos alertas de segurança de camada Standard, requer a instalação do recurso de Microsoft Monitoring Agent (MMA). Você pode habilitar o provisionamento automático na central de segurança para implantar automaticamente o agente para suas VMs do Azure.

## <a name="try-standard-free-for-30-days"></a>Experimente o Standard gratuitamente por 30 dias
O escalão Standard é gratuito durante os primeiros 30 dias. No final de 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que é herdada por todos os recursos na assinatura.

Para obter a camada Standard:

1. Selecione **configurações de & de preços** no menu principal da **central de segurança** .
2. Selecione a assinatura que você deseja atualizar para o padrão.
3. Selecione **Escalão de preço**.
4. Selecione **Standard** para atualizar.
5. Clique em **Guardar**.

(Os preços na imagem são fornecidos apenas para fins ilustrativos) [preços da central de segurança![](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de Segurança, tem de aplicar o escalão de preço Standard à subscrição que contém as máquinas virtuais aplicáveis. A configuração de preços para um espaço de trabalho não permite o acesso just-in-time à VM, controles de aplicativos adaptáveis e detecções de rede para recursos do Azure.
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para o padrão?
A central de segurança oferece proteção avançada contra ameaças e segurança para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – obtenha uma exibição unificada de segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida para garantir a conformidade com os padrões de segurança. Colete, pesquise e analise dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** -use a análise avançada e a gráfico de segurança inteligente da Microsoft para obter uma borda sobre ataques cibernéticos em evolução. Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controles de acesso e de aplicativo** – bloquear malware e outros aplicativos indesejados aplicando recomendações de lista de permissões de aprendizado de máquina adaptadas para suas cargas de trabalho específicas. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure. Isso reduz drasticamente a exposição à força bruta e a outros ataques de rede.
- **Recursos de segurança do contêiner** -Aproveite o gerenciamento de vulnerabilidades e a detecção de ameaças em tempo real em seus ambientes em contêineres. Ao habilitar o recurso de registros de contêiner, pode levar até 12hrs até que todos os recursos estejam habilitados.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você foi apresentado aos preços da central de segurança. Para saber mais sobre a segurança avançada da camada Standard e a proteção avançada contra ameaças, consulte:

- [Deteção avançada de ameaças](security-center-threat-report.md)
- [Controle de acesso da VM just-in-time](security-center-just-in-time.md)
- [Visão geral de segurança do contêiner](container-security.md)
- [Detalhes de preços na sua moeda de escolha e de acordo com sua região](https://azure.microsoft.com/pricing/details/security-center/)