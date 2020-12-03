---
title: Criar e gerir tempos de execução de integração
description: Este artigo explica os passos para criar e gerir os Tempos de Integração em Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553655"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Criar e gerir um tempo de integração auto-hospedado

Este artigo descreve como criar e gerir um tempo de integração auto-hospedado (SHIR) para ajudar na digitalização de fontes de dados.

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

1. Na página inicial do Purview Studio, selecione **Management Center** a partir do painel de navegação à esquerda.

2. Em **Fontes e digitalização** no painel esquerdo, selecione **os tempos de integração** e, em seguida, selecione **+ Novo**.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="clique em IR.":::

3. Na página de configuração do tempo de execução da **Integração,** selecione **Self-Hosted** para criar um Self-Hosted IR e, em seguida, selecione **Continue**.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="criar novo SHIR.":::

4. Insira um nome para o seu IR e selecione Criar.

5. Na página de **definições de tempo de execução de integração,** siga os passos por baixo da secção **de configuração manual.** Você terá que baixar o tempo de execução de integração do site de descarregamento para um VM ou máquina onde pretende executá-lo.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="obter chave":::

    a. Copiar e colar a chave de autenticação.
        
    b. Descarregue o tempo de execução de integração auto-hospedado a partir do Tempo de Integração da [Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) numa máquina local do Windows. Execute o instalador.
        
    c. Na página **'Runtime' de Integração de Registo (Self-hosted),** cole uma das 2 teclas que guardou anteriormente e selecione **Registar.**

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="chave de entrada.":::

    d. Na página de nó de novo número **de integração (auto-hospedada),** selecione **Finish**.

6. Depois de o tempo de integração auto-hospedado ser registado com sucesso, vê a seguinte janela:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registado com sucesso.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Gerir um tempo de integração auto-hospedado

Pode editar um tempo de integração auto-hospedado navegando para **os tempos de integração** no **centro de Gestão,** selecionando o IR e clicando em editar. Pode agora atualizar a descrição, copiar a chave ou regenerar novas teclas.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="editar detalhes do IR.":::

Pode eliminar um tempo de integração auto-hospedado navegando para **os tempos de integração** no centro de Gestão, selecionando o IR e clicando em **Delete**. Uma vez que um IR é eliminado, quaisquer exames em curso que dependam dele falharão.

## <a name="next-steps"></a>Passos seguintes

* [Como as análises detetam ativos eliminados](concept-detect-deleted-assets.md)
