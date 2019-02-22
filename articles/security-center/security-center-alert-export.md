---
title: Exportar registos de alerta para uma área de trabalho do Log Analytics do Azure | Documentos da Microsoft
description: Este artigo explica como exportar os alertas que foram gerados pelo centro de segurança do Azure, uma área de trabalho do Log Analytics.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: 2bc67ce5-ec3a-49df-afc3-b4bad5d8ce21
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/13/2019
ms.author: monhaber
ms.openlocfilehash: 12b8b376a0ff149cbfafc7fe69dd8da636280de8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653714"
---
# <a name="export-azure-security-center-alerts"></a>Exportar alertas do Centro de Segurança do Azure
Este artigo explica como exportar os alertas gerados pelo centro de segurança do Azure para uma localização que pode ser utilizada por outras ferramentas.

Alguns dos clientes do Centro de segurança expressaram a necessidade de consumir os dados gerados pelo centro de segurança de um local central, que contém todos os dados agregados e processados (por exemplo, os alertas de deteção de ameaças, recomendações de políticas de segurança, cobertura, Proteja a pontuação, etc.). Isto permite que os dados ser analisado e processado. Em alternativa, também pode ser outra forma de consumir programaticamente os dados, em vez de utilizar o portal do Centro de segurança.

Uma vez que as áreas de trabalho do Log Analytics do Azure são utilizadas para armazenar e processar os dados brutos coletados de VMs, o **exportar para o Log Analytics** funcionalidade fornece a solução para essa necessidade, por transmissão em fluxo os dados para uma área de trabalho definidos pelo utilizador.

## <a name="prerequisites"></a>Pré-requisitos
Precisa de uma área de trabalho do Log Analytics. Recomendamos que utilize a área de trabalho do Log Analytics mesmo que tiver definido nas definições de recolha de dados. ao definir um para a recolha de dados. [Área de trabalho do log Analytics para a recolha de dados](security-center-enable-data-collection.md)


## <a name="export-the-alerts"></a>Exportar os alertas
1. Clique em **política de segurança**.
1. Na linha da subscrição que pretende utilizar, clique em **editar definições de**.
  ![Editar definições](./media/security-center-alert-export/edit_settings.png "editar definições")
1. Clique em **recolha de dados**.
1. Desloque para baixo até **Centro de segurança de Store enriquecida dados** e clique na opção de alternar para **no**.

   ![Opção de dados de Store](./media/security-center-alert-export/store_data_option.png "política de segurança")
1. Selecione a área de trabalho que pretende exportar os alertas para.

    > [!NOTE]
    > Não é possível exportar alertas de registo para a área de trabalho do Centro de segurança padrão. Conforme mencionado na [pré-requisitos](#Prerequisites), tem de utilizar uma área de trabalho do Log Analytics.

1. Clique em **guardar** (na parte superior do ecrã).