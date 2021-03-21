---
title: Criar e gerir tempos de execução de integração
description: Este artigo explica os passos para criar e gerir os Tempos de Integração em Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980750"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Criar e gerir um tempo de integração auto-hospedado

Este artigo descreve como criar e gerir um tempo de integração auto-hospedado (SHIR) que permite digitalizar fontes de dados em Azure Purview.

> [!NOTE]
> O tempo de execução da integração do Purview não pode ser partilhado com um Azure Synapse Analytics ou um Tempo de Integração da Fábrica de Dados Azure na mesma máquina. Tem de ser instalado numa máquina separada.

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

1. Na página inicial do Purview Studio, selecione **Management Center** a partir do painel de navegação à esquerda.

2. Em **Fontes e digitalização** no painel esquerdo, selecione **os tempos de integração** e, em seguida, selecione **+ Novo**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Clique no IR.":::

3. Na página de configuração do tempo de execução da **Integração,** selecione **Self-Hosted** para criar um Self-Hosted IR e, em seguida, selecione **Continue**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Criar novo SHIR.":::

4. Insira um nome para o seu IR e selecione Criar.

5. Na página de **definições de tempo de execução de integração,** siga os passos por baixo da secção **de configuração manual.** Você terá que baixar o tempo de execução de integração do site de descarregamento para um VM ou máquina onde pretende executá-lo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="obter chave":::

   - Copiar e colar a chave de autenticação.

   - Descarregue o tempo de execução de integração auto-hospedado a partir do Tempo de Execução da [Integração](https://www.microsoft.com/download/details.aspx?id=39717) da Microsoft numa máquina local do Windows. Execute o instalador.

   - Na página **'Runtime' de Integração de Registo (Self-hosted),** cole uma das duas teclas que guardou anteriormente e selecione **Registar.**

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="chave de entrada.":::

   - Na página de nó de novo número **de integração (auto-hospedada),** selecione **Finish**.

6. Depois de o tempo de integração auto-hospedado ser registado com sucesso, vê a seguinte janela:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registado com sucesso.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Gerir um tempo de integração auto-hospedado

Pode editar um tempo de integração auto-hospedado navegando para **os tempos de integração** no **centro de Gestão,** selecionando o IR e clicando em editar. Pode agora atualizar a descrição, copiar a chave ou regenerar novas teclas.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="editar detalhes do IR.":::

Pode eliminar um tempo de integração auto-hospedado navegando para **os tempos de integração** no centro de Gestão, selecionando o IR e clicando em **Delete**. Uma vez que um IR é eliminado, quaisquer exames em curso que dependam dele falharão.

## <a name="next-steps"></a>Passos seguintes

[Como as análises detetam ativos eliminados](concept-detect-deleted-assets.md)
