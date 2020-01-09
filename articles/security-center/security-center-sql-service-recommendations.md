---
title: Recomendações de armazenamento de & de dados – central de segurança do Azure
description: Este documento aborda as recomendações na central de segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure e manter a conformidade com as políticas de segurança.
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
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552870"
---
# <a name="protect-azure-data-and-storage-services"></a>Proteger serviços de armazenamento e dados do Azure
Quando a central de segurança do Azure identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a **página segurança de dados** da seção segurança de recursos da central de segurança.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de armazenamento e dados](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Exibir suas informações de segurança de dados

1. Na seção **higiene de segurança de recursos** , clique em **dados e recursos de armazenamento**.

    ![Recursos de dados e armazenamento](./media/security-center-monitoring/click-data.png)

    A página **segurança de dados** é aberta com recomendações para recursos de dados.

    [![recursos de dados](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Nessa página, você pode:

    * Clique na guia **visão geral** para listar todas as recomendações de recursos de dados a serem corrigidas. 
    * Clique em cada guia e exiba as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre criptografia de armazenamento, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Corrigir uma recomendação em um recurso de dados

1. Em qualquer uma das guias de recurso, clique em um recurso. A página informações é aberta, listando as recomendações a serem corrigidas.

    ![Informações do recurso](./media/security-center-monitoring/sql-recommendations.png)

2. Clique em uma recomendação. A página recomendação é aberta e exibe as **etapas de correção** para implementar a recomendação.

   ![Passos de remediação](./media/security-center-monitoring/remediate1.png)

3. Clique em **executar ação**. A página Configurações de recurso é exibida.

    ![Habilitar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga as **etapas de correção** e clique em **salvar**.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes tópicos:

* [Lista de referências completa das recomendações de segurança da central de segurança do Azure](recommendations-reference.md)
* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)