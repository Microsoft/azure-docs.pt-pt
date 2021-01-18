---
title: Conecte os dados do Azure Sentinel para a | Microsoft Docs
description: Saiba como utilizar o conector de dados Beyond Security beSECURE para puxar os registos beSECURE no Azure Sentinel. Ver dados do BESECURE nos livros de trabalho, criar alertas e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541172"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Ligue o seu beSECURE beyond Security ao Azure Sentinel

> [!IMPORTANT]
> O conector Beyond Security beSECURE está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector Beyond Security beSECURE permite-lhe ligar facilmente todos os registos da sua solução de segurança beSECURE com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre beSECURE e Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Configurar e ligar o BESECURE

o beSECURE pode integrar-se e exportar registos diretamente para o Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione **Beyond Security beSECURE (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga os passos abaixo para configurar a sua solução BESECURE para enviar resultados de digitalização, estado de digitalização e registos de pistas de auditoria para Azure Sentinel.

    **Aceda ao menu Integração:**
    1. Clique na opção menu 'Mais'

    1. Selecione Servidor

    1. Selecione Integração

    1. Ativar o Azure Sentinel

    **Fornecer configurações do BeSECURE com Azure Sentinel:**

      Copie os valores de *ID* e *Chave Primária* do Espaço de Trabalho a partir da página do conector Azure Sentinel, cole-os na configuração beSECURE e clique em **Modificar**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID do espaço de trabalho e chave primária}":::

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** na secção **PersonalLogs,** numa ou mais das seguintes tabelas:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Para consultar os registos beSECURE em regras de análise, consultas de caça, investigações ou em qualquer outro lugar em Azure Sentinel, insira um dos nomes de tabela acima no topo da janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade
Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o BESECURE ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
