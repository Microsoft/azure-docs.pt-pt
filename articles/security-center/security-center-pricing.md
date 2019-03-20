---
title: Atualizar para o escalão Standard do Centro de segurança para uma maior segurança | Documentos da Microsoft
description: Este artigo fornece informações sobre preços do Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: monhaber
ms.openlocfilehash: 5f49de453f9cd694598918500a8ac0221937bdb1
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201692"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para o escalão Standard do Centro de segurança para uma maior segurança
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Fornece visibilidade e controlo sobre a cloud cargas de trabalho híbrida, Active Directory defesas que reduzem sua exposição a ameaças e deteção inteligente para o ajudar a manter o ritmo com ataques cibernéticos a evoluir rapidamente.

## <a name="pricing-tiers"></a>Escalões de preço
Centro de segurança é disponibilizado em dois escalões:

- O **gratuito** camada é ativada automaticamente todas as subscrições do Azure e fornece a política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para o ajudar a proteger os seus recursos do Azure.
- O **padrão** escalão expande as capacidades do escalão gratuito para cargas de trabalho em execução em particular e noutras clouds públicas, fornecendo unificado proteção de gestão e ameaças de segurança em suas cargas de trabalho de cloud híbrida. O escalão Standard adiciona também o advanced threat capacidades de deteção, que utiliza a análise comportamental incorporada e machine learning para identificar ataques e explorações de dia zero, controlos de acesso e aplicação para reduzir a exposição a ataques à rede e software maligno, e mais. Pode experimentar gratuitamente o escalão Standard. Centro de segurança Standard suporta Azure recursos, incluindo VMs, VM de conjuntos de dimensionamento, o serviço de aplicações, servidores SQL e contas de armazenamento. Se tiver Azure Security Center Standard pode optar por recusar suporte com base no tipo de recurso. 


Para obter mais informações, consulte o Centro de segurança [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-30-days"></a>Experimente gratuitamente durante 30 dias do Standard
O escalão Standard é gratuito durante os primeiros 30 dias. No final de 30 dias, se optar por continuar a utilizar o serviço, será automaticamente começamos a cobrar pela utilização.

Pode atualizar uma subscrição do Azure completa para o escalão Standard, que é herdado por todos os recursos dentro da subscrição.

Para obter o escalão Standard:

1. Selecione **política de segurança** sobre o **Centro de segurança** menu principal.
2. Selecione a subscrição que pretende atualizar para Standard.
3. Sobre o **política de segurança** painel, selecione **escalão de preço**.
4. Selecione **padrão** para atualizar.
5. Clique em **Guardar**.

![Preços do Centro de segurança](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de segurança, tem de aplicar o padrão de escalão de preço para a subscrição ou grupo de recursos que contém as máquinas virtuais aplicável. Configurar a preços de uma área de trabalho não ativa just-in-acesso à VM do tempo, controlos de aplicação adaptável e deteções de rede para recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para Standard?
Centro de segurança oferece maior segurança e proteção contra ameaças para as cloud cargas de trabalho híbrida, incluindo:

- **Segurança híbrida** – obtenha uma visão unificada de segurança em todos os no local e na cloud cargas de trabalho. Aplicar políticas de segurança e avaliar continuamente a segurança das suas cargas de trabalho de cloud híbrida para garantir a conformidade com normas de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **Deteção de ameaças avançadas** -utilize análises avançadas e o Microsoft Intelligent Security Graph para obter informações sobre a evolução de ataques informáticos.  Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controlos de acesso e aplicação** -bloqueie o malware e outras aplicações indesejadas ao aplicar recomendações de lista aprovada adaptadas às suas cargas de trabalho específicas e com tecnologia de machine Learning. Reduza a superfície de ataque rede com acesso just-in-time e controlado às portas de gestão em VMs do Azure, reduzindo drasticamente a exposição a ataques de força bruta e outros ataques de rede.


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, foram introduzidas para preços do Centro de segurança. Para saber mais sobre o escalão Standard maior segurança e proteção avançada contra ameaças, consulte:

- [Deteção avançada de ameaças](security-center-threat-report.md)
- [Just-in-controlo de acesso VM de tempo](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
