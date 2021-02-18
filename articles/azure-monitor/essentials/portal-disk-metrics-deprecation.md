---
title: Depreciação das métricas do disco no portal Azure | Microsoft Docs
description: Saiba quais as métricas do disco que foram depreciadas e como atualizar os seus alertas métricos para usar novas métricas.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 623d9385b9ae6b13c8964f655fb973fe67a0918a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616522"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Depreciação das métricas do disco no portal Azure

As métricas precítadas relacionadas com o disco serão em breve removidas do portal Azure. Uma nova versão de cada métrica precedida está disponível para você usar. Este artigo mostra-lhe quais as métricas novas e como atualizar os seus alertas métricos para usá-las.

## <a name="list-of-new-metrics"></a>Lista de novas métricas

Esta tabela mapeia cada métrica precída para a sua nova métrica correspondente. 

|Métrica preprecada|Nova métrica (substituição)|
|----|----|
|QD do disco de dados (precotado)|Profundidade da fila do disco de dados (pré-visualização)|
|Data Disk Read Bytes/Sec (Deprecado)|Data Disk Read Bytes/Sec (Pré-visualização)|
|Operações de leitura de discos de dados/seg (depreciadas)|Operações de leitura de discos de dados/seg (pré-visualização)|
|Data Disk Write Bytes/Sec (Deprecado)|Data Disk Write Bytes/Sec (Pré-visualização)|
|Operações de escrita de discos de dados/seg (depreciadas)|Operações de escrita de discos de dados/seg (pré-visualização)|
|OS QD (Precotado)|Profundidade da fila do OS (pré-visualização)|
|Os Ler Bytes/Sec (Deprecado)|Os Ler Bytes/Sec (Pré-visualização)|
|Operações de leitura de OS/Sec (Depreciadas)|Operações de leitura de OS/Seg (Pré-visualização)|
|Os Write Bytes/Sec (Deprecado)|Os Write Bytes/Sec (Pré-visualização)|
|Operações de escrita de OS/Sec (Depreciadas)|Operações de escrita de OS/Seg (Pré-visualização)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrar métricas nos seus alertas métricos

Atualize os seus alertas métricos para utilizar novas métricas.

1. No portal Azure, procure **alertas.** Em seguida, na secção **Serviços,** escolha **Alertas.**

   > [!div class="mx-imgBorder"]
   > ![Serviço de alerta](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na página **Alertas,** escolha o botão **'Gerir as Regras de Alerta'.** 

   > [!div class="mx-imgBorder"]
   > ![Gere regras do alerta](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Na lista de down-down do **grupo de recursos,** selecione a caixa de verificação **das Máquinas Virtuais** e, na lista de down-down do tipo **Signal,** selecione a caixa de verificação **Métricas.** 

   > [!div class="mx-imgBorder"]
   > ![Alertas de filtro](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na lista de métricas, identificar as condições que se relacionam com os discos. Clique no nome da regra. 

   O nome aparece como uma hiperligação na coluna **Nome** da tabela.

   > [!div class="mx-imgBorder"]
   > ![Encontre condições de disco](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Na secção **Condições** da página **de gestão do Regimento,** clique no estado do alerta. 

   A condição aparece como uma hiperligação.  

   > [!div class="mx-imgBorder"]
   > ![Ajustar condições](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Aparece a página **lógica de sinal de configuração** e as definições da condição aparecem na secção lógica **alerta** dessa página.

6. Faça um registo destas definições pois desaparecerão quando remover a métrica depreciada.

   > [!div class="mx-imgBorder"]
   > ![Regras de condição](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Considere capturar estas definições numa imagem ou num ficheiro de texto. 

7. Clique no link **de seleção de sinais de volta.**

   > [!div class="mx-imgBorder"]
   > ![De volta à seleção de sinais](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na página lógica do **sinal configurado,** escolha a métrica de substituição adequada (nova métrica). Utilize a [tabela](#update-metrics) que aparece anteriormente neste artigo para identificar o nome da nova métrica.

   > [!TIP] 
   > Comece a escrever na barra de pesquisa para reduzir a lista de nomes métricos. 

   > [!div class="mx-imgBorder"]
   > ![Escolha nova métrica](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Escolha o botão **'Fazer'.** 

   > [!div class="mx-imgBorder"]
   > ![Definir nova métrica](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Comprometa as suas alterações escolhendo o botão **Guardar.** 

    > [!div class="mx-imgBorder"]
    > ![Salvar nova métrica](./media/portal-disk-metrics-deprecation/save-new-metric.png)






