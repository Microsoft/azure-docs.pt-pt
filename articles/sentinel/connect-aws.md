---
title: Ligue o AWS CloudTrail ao Azure Sentinel ! Microsoft Docs
description: Utilize o conector AWS para delegar o acesso do Azure Sentinel aos registos de recursos AWS, criando uma relação de confiança entre a AWS CloudTrail e a Sentinela.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: a7405824d2477d2d39c45a56ae545e58a090c321
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436611"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Ligue o Azure Sentinel ao AWS CloudTrail

Utilize o conector AWS para transmitir os seus eventos de gestão AWS CloudTrail para o Azure Sentinel. Este processo de conexão delega acesso para Azure Sentinel aos seus registos de recursos AWS, criando uma relação de confiança entre a AWS CloudTrail e a Azure Sentinel. Isto é realizado em AWS criando um papel que dá permissão ao Azure Sentinel para aceder aos seus registos AWS.

> [!NOTE]
> A AWS CloudTrail tem [limitações incorporadas na](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) sua API LookupEvents. Não permite mais do que duas transações por segundo (TPS) por conta, e cada consulta pode devolver um máximo de 50 registos. Consequentemente, se um único inquilino gerar constantemente mais de 100 registos por segundo numa região, resultarão atrasos e atrasos na ingestão de dados.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter permissão para escrever no espaço de trabalho do Azure Sentinel.

> [!NOTE]
> Azure Sentinel recolhe eventos de gestão CloudTrail de todas as regiões. Recomenda-se que não transmita eventos de uma região para outra.

## <a name="connect-aws"></a>Ligar o AWS 


1. No Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione a linha **Amazon Web Services** na tabela e no painel AWS à direita, clique na página do **conector Open**.

1. Siga as instruções em **Configuração** utilizando os seguintes passos.
 
1.  Na sua consola Amazon Web Services, em **Segurança, Identidade & Compliance**, selecione **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Escolha **Funções** e **selecione Criar função.**

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Escolha **outra conta AWS.** No campo ID da **conta,** insira o **ID da Conta da Microsoft** (**123412341234)** que pode ser encontrado na página de conector AWS no portal Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Certifique-se de que **o ID externo** é selecionado e, em seguida, e introduza o ID Externo (Workspace ID) que pode ser encontrado na página do conector AWS no portal Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Ao **abrigo da política de permissões attach** selecione **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Introduza uma Etiqueta (Opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Em seguida, insira um **nome role** e selecione o botão **de função Criar.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na lista Roles, escolha o papel que criou.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copiar o **Papel ARN**. No portal Azure Sentinel, no ecrã do conector amazon Web Services, cole-o no **Role para adicionar** campo e clique em **Adicionar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para utilizar o esquema relevante no Log Analytics para eventos AWS, procure **por AWSCloudTrail**.

    > [!IMPORTANT]
    > A partir de 1 de dezembro de 2020, o campo **AwsRequestId** foi substituído pelo campo **AwsRequestId_** (note o sublinhado adicionado). Os dados no antigo campo **AwsRequestId** serão preservados até ao final do período de retenção de dados especificado do cliente.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o AWS CloudTrail ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
