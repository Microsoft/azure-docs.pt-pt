---
title: Ligue a CloudTrail a Azure Sentinel Microsoft Docs
description: Saiba como ligar os dados da AWS CloudTrail ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588659"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Ligue o Sentinela Azure ao AWS CloudTrail

Utilize o conector AWS para transmitir todos os seus eventos AWS CloudTrail para O Sentinel. Este processo de ligação delega o acesso do Azure Sentinel aos seus registos de recursos AWS, criando uma relação de confiança entre a AWS CloudTrail e o Azure Sentinel. Isto é realizado na AWS criando uma função que dá permissão ao Azure Sentinel para aceder aos seus registos AWS.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter permissão para escrever no espaço de trabalho do Azure Sentinel.

> [!NOTE]
> O Azure Sentinel recolhe eventos CloudTrail de todas as regiões. Recomenda-se que não transmita eventos de uma região para outra.

## <a name="connect-aws"></a>Ligar AWS 


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione a linha **Amazon Web Services** na tabela e no painel AWS à direita, clique na página do **conector Open**.

1. Siga as instruções sob **configuração** utilizando os seguintes passos.
 
1.  Na sua consola Amazon Web Services, sob **segurança, identidade & conformidade,** selecione **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Escolha **funções** e selecione **Criar o papel**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Escolha **outra conta AWS.** No campo ID da **conta,** introduza o ID da **conta microsoft** **(123412341234**) que pode ser encontrado na página do conector AWS no portal Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Certifique-se de **que o ID externo** é selecionado e, em seguida, e introduza o ID externo (ID do espaço de trabalho) que pode ser encontrado na página do conector AWS no portal Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Sob **a política de permissões anexas,** selecione **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Introduza uma etiqueta (Opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Em seguida, introduza um **nome de função** e selecione o botão **de função Criar.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na lista de Papéis, escolha o papel que criou.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copiar o **papel ARN**. No portal Azure Sentinel, no ecrã do conector Amazon Web Services, cola-o na **Função para adicionar** campo e clicar em **Adicionar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para utilizar o esquema relevante no Log Analytics para eventos AWS, procure a **AWSCloudTrail**.



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o AWS CloudTrail ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

