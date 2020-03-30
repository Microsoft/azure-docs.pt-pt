---
title: Recomendações de armazenamento de dados & - Centro de Segurança Azure
description: Este documento aborda recomendações no Azure Security Center que o ajudam a proteger os seus dados e o serviço Azure SQL e a manter-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552870"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteja os serviços de dados e armazenamento do Azure
Quando o Azure Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Este artigo explica a página de Segurança de **Dados** da secção de segurança de recursos do Centro de Segurança.

Para obter uma lista completa das recomendações que poderá ver nesta página, consulte [recomendações](recommendations-reference.md#recs-datastorage)de Dados e Armazenamento .


## <a name="view-your-data-security-information"></a>Consulte as informações de segurança dos dados

1. Na secção de higiene de **segurança do Recurso,** clique em **Dados e recursos de armazenamento.**

    ![Recursos de armazenamento de dados &](./media/security-center-monitoring/click-data.png)

    A página de **segurança de Dados** abre com recomendações para os recursos de dados.

    [![Recursos de Dados](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Nesta página, pode:

    * Clique no separador **Overview** lista todas as recomendações de recursos de dados a remediar. 
    * Clique em cada separador e veja as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre encriptação de armazenamento, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Remediar uma recomendação sobre um recurso de dados

1. De qualquer um dos separadores de recursos, clique num recurso. A página de informação abre a lista das recomendações a remediar.

    ![Informação sobre recursos](./media/security-center-monitoring/sql-recommendations.png)

2. Clique numa recomendação. A página de Recomendação abre e exibe os passos de **Reparação** para implementar a recomendação.

   ![Passos de reparação](./media/security-center-monitoring/remediate1.png)

3. Clique em **Tomar medidas.** A página de definições de recursos aparece.

    ![Ativar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga os **passos de reparação** e clique em **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes tópicos:

* [Lista completa de referência das recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)