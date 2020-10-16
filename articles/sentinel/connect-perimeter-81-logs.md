---
title: Ligue os dados do Perímetro 81 ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Perímetro 81 ao Azure Sentinel.
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
ms.openlocfilehash: 8c9a7a09d3085b1ec67bf29f142c6e7b205561ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021754"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Ligue o seu Perímetro 81 registos de atividades a Azure Sentinel

> [!IMPORTANT]
> O conector de dados do Perímetro 81 em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho [de registos de atividades do Perímetro 81](https://www.perimeter81.com/) ao Azure Sentinel. O conector de registos de atividade perimeter 81 permite-lhe facilmente trazer os dados do Perímetro 81 para o Azure Sentinel, para que possa vê-lo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Configure e ligue registos de atividades do Perímetro 81

Perímetro 81 Registos de atividade podem integrar e exportar registos diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** no menu de navegação.

1. Selecione Registos de atividade do **Perímetro 81** da galeria e, em seguida, clique no botão de página do **conector Open.**

1. A partir da página de conector de registos de atividades do Perímetro 81, copie o **ID** do Espaço de Trabalho e **a Chave Primária** e cole-os no Perímetro 81, conforme aqui [instruído.](https://support.perimeter81.com/hc/en-us/articles/360012680780)

1. Depois de completar as instruções, verá os tipos de dados ligados na página do conector Azure Sentinel.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos** em **Perimeter81_CL Desempaçamentos**  -  **Personalizados**.

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os registos de atividade do Perímetro 81 ao Azure Sentinel. Para tirar o máximo partido das capacidades incorporadas neste conector de dados, clique no separador **passos seguintes** na página do conector de dados. Lá você encontrará um livro pronto e algumas consultas de amostra para que você possa começar a encontrar informações úteis.

Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
