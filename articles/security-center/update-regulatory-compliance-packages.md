---
title: Como atualizar para o monitoramento de conformidade regulatória dinâmica no painel de conformidade regulatória da central de segurança do Azure | Microsoft Docs
description: Atualizando seus pacotes de conformidade regulatória (versão prévia)
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
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521790"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Atualizar para pacotes de conformidade dinâmica em seu painel de conformidade regulatória (versão prévia)

A central de segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e benchmarks do setor. O **painel de conformidade regulatória** fornece informações sobre sua postura de conformidade com base em como você está atendendo a requisitos e controles de conformidade específicos.

Um padrão para o qual você pode acompanhar sua postura de conformidade é o [Azure cis 1.1.0](https://www.cisecurity.org/benchmark/azure/) (mais formalmente, o "cis Microsoft Azure Foundations benchmark versão 1.1.0"). 

A representação do Azure CIS que aparece inicialmente em seu painel de conformidade depende de um conjunto estático de regras incluído na central de segurança.

Com o recurso de **pacotes de conformidade dinâmica (versão prévia)** , a central de segurança aprimora automaticamente sua cobertura de padrões do setor ao longo do tempo. Os pacotes de conformidade são essencialmente iniciativas definidas em Azure Policy. Eles podem ser atribuídos ao escopo selecionado (assinatura, grupo de gerenciamento e assim por diante). Para ver os dados de conformidade mapeados como avaliações no seu painel, adicione um pacote de conformidade ao seu grupo de gerenciamento ou assinatura de dentro da política de segurança. Adicionar um pacote de conformidade efetivamente atribui a iniciativa de conformidade regulatória ao escopo selecionado. Dessa forma, você pode controlar as iniciativas regulatórias publicadas recentemente como padrões de conformidade em seu painel. Quando a Microsoft lança novo conteúdo para a iniciativa (novas políticas que são mapeadas para mais controles no padrão), o conteúdo adicional é exibido automaticamente em seu painel.

O pacote de conformidade dinâmica para o parâmetro de comparação de CIS do Azure, **Azure cis 1.1.0 (novo)** , melhora a versão *estática* original por:

* Incluindo mais políticas
* Atualizando automaticamente com a nova cobertura à medida que ela é adicionada 

Atualize para o novo pacote dinâmico, conforme descrito abaixo.

## <a name="adding-a-dynamic-compliance-package"></a>Adicionando um pacote de conformidade dinâmica

As etapas a seguir explicam como adicionar o pacote dinâmico para monitorar sua conformidade com o benchmark do Azure CIS v 1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Atualizar para o pacote de conformidade dinâmica do Azure CIS 1.1.0 (novo) 

1. Abra a página **política de segurança** . Esta página mostra o número de grupos de gerenciamento, assinaturas, espaços de trabalho e sua estrutura de grupo de gerenciamento.

1. Selecione a assinatura ou grupo de gerenciamento para o qual você deseja gerenciar a postura de conformidade regulatória. É recomendável selecionar o escopo mais alto para o qual o padrão é aplicável para que os dados de conformidade sejam agregados e acompanhados para todos os recursos aninhados. 

1. Na seção padrões regulatórios do setor & (versão prévia), você verá que o Azure CIS 1.1.0 pode ser atualizado para novo conteúdo. Clique em **Atualizar agora**. 

1. Opcionalmente, clique em **adicionar mais padrões** para abrir a página **Adicionar padrões de conformidade regulatória** . Lá, você pode pesquisar manualmente para os pacotes do **Azure cis 1.1.0 (novo)** e para outros padrões de conformidade, como **NIST SP 800-53 R4**, **Swift CSP CSCF-V2020**, **UKO e UK NHS**e **Canadá PBMM**.
    
    ![Adicionando pacotes regulatórios ao painel de conformidade regulatória da central de segurança do Azure](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Na barra lateral da central de segurança, selecione **conformidade regulatória** para abrir o painel conformidade regulatória. 
    * O Azure CIS 1.1.0 (novo) agora aparece em sua lista de padrões de regulamentação & setor. 
    * A exibição *estática* original da sua conformidade do Azure cis 1.1.0 também permanecerá junto com ele. Ele pode ser removido automaticamente no futuro.

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.


    [![painel de conformidade regulatória mostrando o Azure CIS antigo e novo](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu:

* Como **atualizar os padrões** mostrados em seu painel de conformidade regulatória para os novos pacotes *dinâmicos*
* Como **adicionar pacotes de conformidade** para monitorar sua conformidade com padrões adicionais. 

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [Painel de conformidade regulatória da central de segurança](security-center-compliance-dashboard.md)
- [Trabalhando com políticas de segurança](tutorial-security-policy.md)
- [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) -saiba como usar as recomendações na central de segurança do Azure para ajudar a proteger os recursos do Azure.
- [Perguntas frequentes da central de segurança do Azure](security-center-faq.md) -Obtenha respostas para perguntas frequentes sobre como usar a central de segurança.