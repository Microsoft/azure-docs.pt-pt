---
title: Deprecação de métricas de disco no portal Azure Microsoft Docs
description: Saiba quais as métricas do disco que foram depreciadas e como atualizar os seus alertas métricos para utilizar novas métricas.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299806"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Deprecação de métricas de disco no portal Azure

As métricas relacionadas com o disco depreciadas serão em breve removidas do portal Azure. Uma nova versão de cada métrica depreciada está disponível para que possa utilizar. Este artigo mostra quais as métricas novas e como atualizar os seus alertas métricos para usá-los.

## <a name="list-of-new-metrics"></a>Lista de novas métricas

Esta tabela mapeia cada métrica depreciada à sua nova métrica correspondente. 

|Métrica depreciada|Nova métrica (substituição)|
|----|----|
|QD do disco de dados (depreciado)|Profundidade da fila do disco de dados (pré-visualização)|
|Data Disk Read Bytes/Sec (Depreciado)|Data Disk Read Bytes/Sec (Pré-visualização)|
|Operações de leitura de disco de dados/Sec (Depreciado)|Data Disk Ler Operações/Sec (Pré-visualização)|
|Data Disk Write Bytes/Sec (Depreciado)|Data Disk Write Bytes/Sec (Pré-visualização)|
|Operações de Escrita de Disco de Dados/Sec (Depreciado)|Operações de escrita de disco de dados/sec (pré-visualização)|
|Os QD (Deprecated)|Profundidade da fila dos SOs (pré-visualização)|
|OS Ler Bytes/Sec (Depreciado)|OS Ler Bytes/Sec (Pré-visualização)|
|OS Read Operations/Sec (Depreciado)|OS Ler Operações/Sec (Pré-visualização)|
|OS Write Bytes/Sec (Depreciado)|OS Write Bytes/Sec (Pré-visualização)|
|Operações de Escrita OS/Sec (Depreciado)|Operações de Escrita osS/Sec (Pré-visualização)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrar métricas nos seus alertas métricos

Atualize os seus alertas métricos para utilizar novas métricas.

1. No portal Azure, procure **Alertas.** Em seguida, na secção **Serviços,** escolha **Alertas**.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na página **Alertas,** escolha o botão 'Gerir regras de **alerta'.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Na lista de drop-down do **grupo Resource,** selecione a caixa de verificação de **Máquinas Virtuais** e na lista de drop-down do **tipo Signal,** selecione a caixa de verificação **Métricas.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na lista de métricas, identifique as condições relacionadas com os discos. Clique no nome da regra. 

   O nome aparece como uma hiperligação na coluna **nome** da tabela.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Na secção **Condições** da página de gestão do **Regimento,** clique no estado do alerta. 

   A condição aparece como uma hiperligação.  

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   A página lógica do **sinal configurar** aparece e as definições da condição aparecem na secção lógica de **alerta** dessa página.

6. Faça um registo destas definições, pois desaparecerão quando remover a métrica depreciada.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Considere capturar estas definições numa imagem ou num ficheiro de texto. 

7. Clique no link **de seleção de volta para sinal.**

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na página lógica do **sinal Configurar,** escolha a métrica de substituição adequada (nova métrica). Use a [tabela](#update-metrics) que aparece anteriormente neste artigo para identificar o nome da nova métrica.

   > [!TIP] 
   > Comece a escrever na barra de pesquisa para reduzir a lista de nomes métricos. 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Escolha o botão **Done.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Comprometa as suas alterações escolhendo o botão **Guardar.** 

    > [!div class="mx-imgBorder"]
    > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/save-new-metric.png)






