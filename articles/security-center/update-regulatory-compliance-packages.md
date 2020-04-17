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
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537785"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Atualização para pacotes dinâmicos de conformidade no seu dashboard de conformidade regulamentar

O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos nos padrões, regulamentos e referências da indústria. O dashboard de **conformidade regulamentar** fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir controlos e requisitos específicos de conformidade.

Um padrão para o qual pode acompanhar a sua postura de conformidade é [o Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (mais formalmente, a versão 1.1.0 do "CIS Microsoft Azure Foundations Benchmark" 1.1.0). 

A representação do Azure CIS que aparece inicialmente no seu dashboard de conformidade baseia-se num conjunto estático de regras que está incluída no Centro de Segurança.

Com a funcionalidade **de pacotes dinâmicos** de conformidade, o Security Center melhora automaticamente a sua cobertura dos padrões da indústria ao longo do tempo. Os pacotes de conformidade são essencialmente iniciativas definidas na Política Azure. Podem ser atribuídos ao seu âmbito selecionado (subscrição, grupo de gestão, e assim por diante). Para ver os dados de conformidade mapeados como avaliações no seu dashboard, adicione um pacote de conformidade ao seu grupo de gestão ou subscrição a partir da Política de Segurança. A adição de um pacote de conformidade atribui efetivamente a iniciativa de conformidade regulamentar ao seu âmbito selecionado. Desta forma, pode acompanhar as iniciativas regulamentares recentemente publicadas como padrões de conformidade no seu painel de instrumentos. Quando a Microsoft lança novos conteúdos para a iniciativa (novas políticas que mapeiam para mais controlos no padrão), o conteúdo adicional aparece automaticamente no seu dashboard.

O pacote dinâmico de conformidade para o referencial Azure CIS, **Azure CIS 1.1.0 (novo),** melhora a versão *estática* original por:

* Incluindo mais políticas
* Atualizar automaticamente com nova cobertura à medida que é adicionada 

Atualização para o novo pacote dinâmico, conforme descrito abaixo.

## <a name="adding-a-dynamic-compliance-package"></a>Adicionar um pacote dinâmico de conformidade

Os seguintes passos explicam como adicionar o pacote dinâmico para monitorizar a sua conformidade com o benchmark Azure CIS v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Atualização para o Pacote de conformidade dinâmica Azure CIS 1.1.0 

1. Abra a página da política de **segurança.** Esta página mostra o número de grupos de gestão, subscrições, espaços de trabalho e a estrutura do seu grupo de gestão.

1. Selecione o grupo de subscrição ou gestão para o qual pretende gerir a postura de conformidade regulamentar. Recomendamos a seleção do âmbito mais elevado para o qual a norma é aplicável para que os dados de conformidade sejam agregados e rastreados para todos os recursos aninhados. 

1. Na secção de normas regulamentares da Indústria &, verá que o Azure CIS 1.1.0 pode ser atualizado para novos conteúdos. Clique em **Atualizar agora**. 

1. Opcionalmente, clique **Em adicionar mais padrões** para abrir a página de padrões de **conformidade regulamentar Adicionar.** Lá, você pode pesquisar manualmente por **Azure CIS 1.1.0 (Novo)** e pacotes dinâmicos para outros padrões de conformidade tais como **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020,** **UKO e UK NHS**, e Canada **PBMM**.
    
    > [!TIP]
    > Apenas os utilizadores que são proprietários ou contribuintes têm as permissões necessárias para adicionar padrões de conformidade. 

    ![Adicionar pacotes regulamentares ao painel de controlo regulamentar do Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** para abrir o painel de conformidade regulamentar. 
    * O Azure CIS 1.1.0 (Novo) aparece agora na sua lista de normas regulamentares & Indústria. 
    * A vista *estática* original do seu Azure CIS 1.1.0 também permanecerá ao seu lado. Pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.


    [![Painel de controlo regulamentar que mostra cIS antigo e novo do Azure](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeste:

* Como **atualizar as normas** mostradas no seu dashboard de conformidade regulamentar para os novos pacotes *dinâmicos*
* Como **adicionar pacotes** de conformidade para monitorizar o seu cumprimento com padrões adicionais. 

Para outros materiais relacionados, consulte os seguintes artigos: 

- [Painel de conformidade regulamentar do centro de segurança](security-center-compliance-dashboard.md)
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)
- [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center - Aprenda a usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.