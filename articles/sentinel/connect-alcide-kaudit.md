---
title: Ligue os dados do Alcide kAudit ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Alcide kAudit ao Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: cf8da1d88529a823ff4399fb955c8a5e0abbd20e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038244"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Ligue o seu Alcide kAudit ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Alcide kAudit em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) ajuda-o a identificar comportamentos anómalos de Kubernetes e a focar-se em violações e incidentes de Kubernetes, reduzindo o tempo de deteção. Este artigo explica como ligar a sua solução Alcide kAudit ao Azure Sentinel. O conector de dados Alcide kAudit permite-lhe facilmente trazer os seus dados de registo kAudit para o Azure Sentinel, para que possa vê-lo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre Alcide kAudit e Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho.

## <a name="configure-and-connect-alcide-kaudit"></a>Configure e ligue Alcide kAudit

Alcide kAudit pode exportar troncos diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** no menu de navegação.

1. Selecione **Alcide kAudit** da galeria e, em seguida, clique no botão de página do **conector Open.**

1. Siga as instruções passo a passo fornecidas no Guia de [Instalação Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Quando solicitado o ID do Espaço de Trabalho e a Chave Primária, pode copiá-los a partir da página do conector de dados Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ID do espaço de trabalho e chave primária":::

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em Logs sob os **seguintes** tipos de dados em **CustomLogs**:

- **alcide_kaudit_detections_1_CL** - Deteções de Alcide kAudit 
- **alcide_kaudit_activity_1_CL** - Registos de atividades de Alcide kAudit
- **alcide_kaudit_selections_count_1_CL** - Alcide kAudit conta
- **alcide_kaudit_selections_details_1_CL** - Detalhes da atividade de Alcide kAudit

Para utilizar o esquema relevante em Logs para o Alcide kAudit, procure os tipos de dados acima mencionados.

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar Alcide kAudit a Azure Sentinel. Para tirar o máximo partido das capacidades incorporadas neste conector de dados, clique no separador **passos seguintes** na página do conector de dados. Lá você encontrará algumas consultas de amostras prontas para que você possa começar a encontrar informações úteis.

Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
