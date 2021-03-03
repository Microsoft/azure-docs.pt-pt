---
title: Ligue os dados da Sophos Cloud Optix ao | do Azure Sentinel Microsoft Docs
description: Aprenda a usar o conector Sophos Cloud Optix para puxar <PRODUCT NAME> troncos no Azure Sentinel. Ver <PRODUCT NAME> dados em livros, criar alertas e melhorar a investigação.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700932"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Ligue o seu Sophos Cloud Optix ao Azure Sentinel

> [!IMPORTANT]
> O conector Sophos Cloud Optix está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector Sophos Cloud Optix permite-lhe ligar facilmente todos os seus registos de solução de segurança Sophos Cloud Optix com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação.  Esta capacidade dá-lhe mais informações sobre a postura de segurança e conformidade na nuvem da sua organização e melhora as suas capacidades de operação de segurança na nuvem. A integração entre Sophos Cloud Optix e Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Configure e ligue Sophos Cloud Optix

Sophos Cloud Optix pode integrar e exportar troncos diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione **Sophos Cloud Optix (Preview)**.

1. Selecione **Abrir a página do conector**.

1. Copie e guarde o **ID** do Espaço de Trabalho e **a Chave Primária** da página do conector.

1. Siga as instruções da Sophos para [integrar com o Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (a partir do terceiro passo).

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos** sob a secção **Registos Personalizados,** na tabela *SophosCloudOptix_CL.*

Para consultar os dados sophos Cloud Optix, `SophosCloudOptix_CL` introduza na janela de consulta. Consulte o separador **passos seguintes** na página do conector e na [documentação de Sophos,](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)para obter algumas amostras de consulta úteis.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar Sophos Cloud Optix ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
