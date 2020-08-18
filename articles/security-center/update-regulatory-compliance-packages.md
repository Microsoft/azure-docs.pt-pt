---
title: Como atualizar a monitorização dinâmica da conformidade regulamentar no seu painel de conformidade regulamentar do Azure Security Center Microsoft Docs
description: Atualizar os seus pacotes de conformidade regulamentar
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 28b6a70297efb8d8237f085e1f5b08dbb6a09072
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522611"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizar o conjunto de padrões no seu painel de conformidade regulamentar

O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos em padrões, regulamentos e referências da indústria. O **painel de conformidade regulamentar** fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir controlos e requisitos específicos de conformidade.


## <a name="overview-of-compliance-packages"></a>Visão geral dos pacotes de conformidade

As normas do setor, as normas regulamentares e os parâmetros de referência estão representados no Security Center como *pacotes de conformidade.*  Cada pacote é uma iniciativa definida na Política Azure. Para ver os dados de conformidade mapeados como avaliações no seu painel de instrumentos, adicione um pacote de conformidade ao seu grupo de gestão ou subscrição dentro da página de política de **Segurança.** (Saiba mais sobre a Política Azure e iniciativas em [Trabalhar com políticas de segurança](tutorial-security-policy.md).)

Quando você tem bordo um padrão ou benchmark para o seu âmbito selecionado, ele atribui a iniciativa ao âmbito e a norma aparece no seu painel de conformidade regulamentar com todos os dados de conformidade associados mapeados como avaliações. Também pode baixar relatórios sumários para qualquer uma das normas que foram a bordo.

A Microsoft também segue os próprios padrões regulamentares e melhora automaticamente a sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lança novos conteúdos para a iniciativa (novas políticas que mapeiam para mais controlos na norma), o conteúdo adicional aparece automaticamente no seu painel de instrumentos.

> [!TIP]
> Um padrão que melhora ao longo do tempo à medida que a Microsoft lança novos conteúdos é **o Azure CIS 1.1.0 (novo) (mais** formalmente, a [versão benchmark 1.1.0 da CIS Microsoft Foundations).](https://www.cisecurity.org/benchmark/azure/) Você precisará adicionar isto ao seu painel ao lado de "Azure CIS 1.1.0", a representação do Azure CIS que é configurado por padrão em todos os ambientes do Centro de Segurança. Este pacote baseia-se num conjunto estático de regras. O pacote mais recente inclui mais políticas e irá atualizar automaticamente ao longo do tempo. Atualização para o novo pacote dinâmico, conforme descrito abaixo.


## <a name="available-packages"></a>Pacotes disponíveis

Pode adicionar padrões como NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official and UK NHS, Canada Federal PBMM, e Azure CIS 1.1.0 (novo) - uma representação mais completa do Azure CIS 1.1.0. 

Além disso, pode adicionar **Azure Security Benchmark**, as diretrizes específicas da Microsoft para segurança e conformidade com base em quadros comuns de conformidade. (Saiba[mais sobre o Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

As normas adicionais serão suportadas no painel de instrumentos à medida que se tornarem disponíveis. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Adicionar um padrão regulamentar ao seu painel de instrumentos

Os seguintes passos explicam como adicionar um pacote para monitorizar o seu cumprimento a uma das normas regulamentares suportadas.

> [!NOTE]
> Apenas os utilizadores que são proprietários ou colaboradores de políticas têm as permissões necessárias para adicionar normas de conformidade. 

1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** para abrir o painel de conformidade regulamentar. Aqui pode ver os padrões de conformidade atualmente atribuídos às subscrições atualmente selecionadas.   

1. A partir do topo da página, **selecione Gerir**as políticas de conformidade . Aparece a página de Gestão de Políticas.

1. Selecione o grupo de subscrição ou gestão para o qual pretende gerir a postura de conformidade regulamentar. 

    > [!TIP]
    > Recomendamos a seleção da mais alta margem para a qual a norma é aplicável para que os dados de conformidade sejam agregados e rastreados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique em **Adicionar mais padrões**. 

1. A partir da página **de normas de conformidade regulamentar Adicionar,** pode procurar pacotes para qualquer uma das normas disponíveis. Algumas das normas disponíveis são:

    - **Referência de Segurança do Azure**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO e UK NHS**
    - **Canadá PBMM**
    
    ![Adicionar pacotes regulamentares ao painel de conformidade regulamentar do Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **Selecione Adicionar** e insira todos os detalhes necessários para a iniciativa específica, tais como âmbito, parâmetros e remediação.

1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** novamente para voltar ao painel de conformidade regulamentar.
    * O seu novo padrão aparece na sua lista de normas regulamentares & indústria. 
    * Se adicionou **Azure CIS 1.1.0 (Novo),** a visão *estática* original do seu cumprimento Azure CIS 1.1.0 também permanecerá ao seu lado. Pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    [![Painel de conformidade regulamentar mostrando o antigo e novo Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a **adicionar pacotes de conformidade** para monitorizar o cumprimento de normas adicionais. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Referência de Segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Painel de conformidade regulamentar do centro de segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)