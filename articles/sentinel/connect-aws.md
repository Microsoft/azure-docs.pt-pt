---
title: Ligue os dados de AWS da Symantec para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados da Symantec AWS ao sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673976"
---
# <a name="connect-azure-sentinel-to-aws"></a>Ligar o Sentinel do Azure ao AWS

Utilize o conector do AWS para transmitir todos os seus eventos de CloudTrail do AWS para o Azure sentinela. Este processo de ligação delega o acesso para sentinela do Azure para os registos de recursos do AWS, criar uma relação de confiança entre o CloudTrail do AWS e sentinela do Azure. Isso é feito no AWS através da criação de uma função que dá permissão ao Azure sentinela para aceder aos seus registos AWS.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter permissão de escrita na área de trabalho sentinela do Azure.

## <a name="connect-aws"></a>Ligar AWS 
 
1.  Na Amazon Web Services consola, em **Security, Identity & Compliance**, clique em **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Escolher **funções** e clique em **criar função**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Escolha **conta AWS outro.** Na **ID da conta** , insira o **ID da conta Microsoft** (**123412341234**) que pode ser encontrada na página de conector do AWS no portal do Azure sentinela.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Certifique-se **necessitam de ID externo** está selecionada e, em seguida e introduza o ID externo (ID de área de trabalho) que podem ser encontradas na página de conector do AWS no portal do Azure sentinela.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  Sob **anexar política de permissões** selecionar **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Introduza uma etiqueta (opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Em seguida, introduza um **nome da função** e clique nas **criar função** botão.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  Na lista de funções, escolha a função que criou.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Cópia a **ARN de função** e cole-o para o **função para adicionar** campo no Portal do Azure sentinela.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Para utilizar o esquema relevante no Log Analytics para eventos do AWS, procure **AWSCloudTrail**.



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o CloudTrail do AWS para o Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

