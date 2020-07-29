---
title: Recomendações de armazenamento de dados & - Azure Security Center
description: Este documento aborda recomendações no Azure Security Center que o ajudam a proteger os seus dados e o serviço Azure SQL e manter-se em conformidade com as políticas de segurança.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552870"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteja os serviços de dados e armazenamento da Azure
Quando o Azure Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiem através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Este artigo explica a página de Segurança de **Dados** da secção de segurança de recursos do Centro de Segurança.

Para obter uma lista completa das recomendações que pode ver nesta página, consulte as [recomendações de Dados e Armazenamento.](recommendations-reference.md#recs-datastorage)


## <a name="view-your-data-security-information"></a>Ver as suas informações de segurança de dados

1. Na secção **de higiene de segurança de recursos,** clique em **Dados e recursos de armazenamento.**

    ![Dados & recursos de armazenamento](./media/security-center-monitoring/click-data.png)

    A página **de segurança data** abre com recomendações para recursos de dados.

    [![Recursos de Dados](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Nesta página, pode:

    * Clique no **separador 'Vista Geral'** lista todas as recomendações de recursos de dados a serem remediadas. 
    * Clique em cada separador e veja as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre encriptação de armazenamento, consulte [a encriptação do Azure Storage para obter dados em repouso](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Remediar uma recomendação sobre um recurso de dados

1. A partir de qualquer um dos separadores de recursos, clique num recurso. A página de informação abre a listar as recomendações a serem remediadas.

    ![Informação sobre recursos](./media/security-center-monitoring/sql-recommendations.png)

2. Clique numa recomendação. A página recomendação abre e apresenta as **medidas de reparação** para implementar a recomendação.

   ![Medidas de reparação](./media/security-center-monitoring/remediate1.png)

3. Clique **em Tomar medidas.** A página de definições de recursos aparece.

    ![Ativar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga os **passos de remediação** e clique em **Guardar**.


## <a name="next-steps"></a>Próximos passos

Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes tópicos:

* [Lista completa de recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)