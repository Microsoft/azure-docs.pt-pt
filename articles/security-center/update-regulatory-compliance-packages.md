---
title: Como atualizar para monitorização dinâmica da conformidade regulamentar no seu painel de conformidade regulamentar do Centro de Segurança Azure [ Microsoft Docs
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
ms.openlocfilehash: 6ba0be3a6fba35e413270dd6770f5d3f47586b5e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873329"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizando o conjunto de normas no seu dashboard de conformidade regulamentar

O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos nos padrões, regulamentos e referências da indústria. O dashboard de **conformidade regulamentar** fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir controlos e requisitos específicos de conformidade.


## <a name="overview-of-compliance-packages"></a>Visão geral dos pacotes de conformidade

As normas do setor, as normas regulamentares e os critérios de referência estão representados no Centro de Segurança como pacotes de *conformidade.*  Cada pacote é uma iniciativa definida na Política Do Azure. Para ver os dados de conformidade mapeados como avaliações no seu dashboard, adicione um pacote de conformidade ao seu grupo de gestão ou subscrição a partir da página de política de **Segurança.** (Saiba mais sobre a Política azure e iniciativas no [trabalho com políticas](tutorial-security-policy.md)de segurança .)

Quando você tem um padrão ou referência ao seu âmbito selecionado, atribui a iniciativa ao âmbito e a norma aparece no seu dashboard de conformidade regulamentar com todos os dados de conformidade associados mapeados como avaliações. Também pode descarregar relatórios sumários para qualquer uma das normas que tenham sido a bordo.

A Microsoft também rastreia os padrões regulamentares em si e melhora automaticamente a sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lança novos conteúdos para a iniciativa (novas políticas que mapeiam para mais controlos no padrão), o conteúdo adicional aparece automaticamente no seu dashboard.

> [!TIP]
> Um padrão que melhora com o tempo à medida que a Microsoft lança novos conteúdos é **o Azure CIS 1.1.0 (novo)** (mais formalmente, a [versão 1.1.0 do CIS Microsoft Azure Foundations Benchmark ).](https://www.cisecurity.org/benchmark/azure/) Terá de adicionar isto ao seu painel de instrumentos ao lado do "Azure CIS 1.1.0", a representação do Azure CIS que é configurado por padrão em todos os ambientes do Centro de Segurança. Este pacote baseia-se num conjunto estático de regras. O pacote mais recente inclui mais políticas e irá automaticamente atualizar ao longo do tempo. Atualização para o novo pacote dinâmico, conforme descrito abaixo.


## <a name="available-packages"></a>Pacotes disponíveis

Pode adicionar padrões como NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official e UK NHS, Canada Federal PBMM e Azure CIS 1.1.0 (novo) - uma representação mais completa do Azure CIS 1.1.0. 

Além disso, pode adicionar o **Azure Security Benchmark,** as diretrizes específicas da Microsoft para as melhores práticas de segurança e conformidade baseadas em quadros comuns de conformidade. ([Saiba mais sobre o Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Normas adicionais serão suportadas no painel de instrumentos à medida que forem disponibilizadas. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Adicionar um padrão regulamentar ao seu painel de instrumentos

Os seguintes passos explicam como adicionar um pacote para monitorizar o seu cumprimento com uma das normas regulamentares suportadas.

> [!NOTE]
> Apenas os utilizadores que são proprietários ou contribuintes têm as permissões necessárias para adicionar padrões de conformidade. 

1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** para abrir o painel de conformidade regulamentar. Aqui pode ver as normas de conformidade atualmente atribuídas às subscrições atualmente selecionadas.   

1. A partir do topo da página, selecione **Gerir as políticas**de conformidade . A página de Gestão de Políticas aparece.

1. Selecione o grupo de subscrição ou gestão para o qual pretende gerir a postura de conformidade regulamentar. 

    > [!TIP]
    > Recomendamos a seleção do âmbito mais elevado para o qual a norma é aplicável para que os dados de conformidade sejam agregados e rastreados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique **em Adicionar mais padrões**. 

1. A partir da página de normas de **conformidade regulamentar Add,** pode procurar pacotes para qualquer uma das normas disponíveis. Algumas das normas disponíveis são:

    - **Referência de Segurança do Azure**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO e UK NHS**
    - **PBMM do Canadá**
    
    ![Adicionar pacotes regulamentares ao painel de controlo regulamentar do Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. A partir da barra lateral do Security Center, selecione o **cumprimento regulamentar** novamente para voltar ao dashboard de conformidade regulamentar.
    * O seu novo padrão aparece na sua lista de normas regulamentares da Indústria &. 
    * Se adicionou **O Azure CIS 1.1.0 (Novo),** a vista *estática* original do seu Azure CIS 1.1.0 também permanecerá ao seu lado. Pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    [![Painel de controlo regulamentar que mostra cIS antigo e novo do Azure](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a **adicionar pacotes** de conformidade para monitorizar o seu cumprimento com padrões adicionais. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Referência de Segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Painel de conformidade regulamentar do centro de segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)