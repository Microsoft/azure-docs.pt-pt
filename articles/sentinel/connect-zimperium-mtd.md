---
title: Ligue a Zimperium Mobile Threat Defense ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar a Zimperium Mobile Threat Defense ao Azure Sentinel.
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: a11d4602882973a24e09c62c12a9dc1dcdc8246d
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501254"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Ligue a sua Defesa de Ameaça Móvel zimperium ao Azure Sentinel


> [!IMPORTANT]
> O conector de dados de Defesa de Ameaças Móveis zimperium em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector de defesa de ameaças móveis zimperium dá-lhe a capacidade de ligar o registo de ameaça zimperium com o Azure Sentinel para ver dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre o panorama de ameaça móvel da sua organização e aumenta as suas capacidades de operação de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configure e ligue a Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense pode integrar e exportar registos diretamente para **Azure Sentinel.**

1. No portal Azure Sentinel, clique em conectores de dados e selecione **Zimperium Mobile Threat Defense**.
2. Selecione página do **conector Aberto**.
3. Siga as instruções na página do conector de defesa de **ameaças móveis zimperium,** resumida da seguinte forma.
 1. No zConsole, clique em **Gerir** na barra de navegação.
 2. Clique no separador **Integrações.**
 3. Clique no botão Relatório de **Ameaças** e, em seguida, no botão **Adicionar Integrações.**
 4. Crie a Integração selecionando o **Microsoft Azure Sentinel** a partir das integrações disponíveis e introduza id no espaço de trabalho e chave primária para ligar ao Azure Sentinel.
 5. A opção de selecionar um nível de filtro para os dados de ameaça para empurrar para O Sentinel também está disponível. 

4. Para mais informações, consulte o portal de apoio ao [cliente zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics em CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Para utilizar o esquema relevante em Log Analytics para a Zimperium Mobile Threat Defense, procure ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a ligar a Zimperium Mobile Threat Defense ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)

- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

Para saber mais sobre zimperium, consulte o seguinte:

- [Zimperium](https://zimperium.com)

- [Blog de Segurança Móvel zimperium](https://blog.zimperium.com)

- [Portal de Apoio ao Cliente zimperium](https://support.zimperium.com)

