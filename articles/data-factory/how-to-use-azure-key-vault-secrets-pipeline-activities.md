---
title: Utilizar segredos do Azure Key Vault em atividades de pipeline
description: Aprenda a obter credenciais armazenadas a partir do cofre da chave Azure e use-as durante o gasoduto da fábrica de dados.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 1766705e73afab5d15cdb5aa2c5bb1487ad3d7c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013892"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Utilizar segredos do Azure Key Vault em atividades de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Você pode armazenar credenciais ou valores secretos em um Cofre de Chave Azure e usá-los durante a execução do gasoduto para passar para as suas atividades.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade gerida pela fábrica de dados.  Saiba como funciona a partir da [identidade gerida para a Data Factory](./data-factory-service-identity.md) e certifique-se de que a sua fábrica de dados tem uma associada.

## <a name="steps"></a>Passos

1. Abra as propriedades da sua fábrica de dados e copie o valor de ID de aplicação de identidade gerida.

    ![Valor de Identidade Gerido](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Abra as principais políticas de acesso ao cofre e adicione as permissões de identidade geridas aos segredos get e List.

    ![Screenshot que mostra a página "Políticas de acesso" com a ação "Adicionar Política de Acesso" realçada.](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Principais políticas de acesso ao cofre](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Clique **em Adicionar** e, em seguida, clique em **Guardar**.

3. Navegue para o seu segredo do Cofre chave e copie o Identificador Secreto.

    ![Identificador Secreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Tome nota do seu URI secreto que pretende obter durante o seu oleoduto de fábrica de dados.

4. No seu pipeline Data Factory, adicione uma nova atividade web e configuure-a da seguinte forma.  

    |Propriedade  |Valor  |
    |---------|---------|
    |Saída Segura     |Verdadeiro         |
    |URL     |[O seu valor URI secreto]?api-versão=7.0         |
    |Método     |GET         |
    |Autenticação     |MSI         |
    |Recurso        |https://vault.azure.net       |

    ![Atividade web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Tem de adicionar **?api-versão=7.0** ao fim do seu URI secreto.  

    > [!CAUTION]
    > Descreva a opção Saída Segura de forma verdadeira para evitar que o valor secreto seja registado em texto simples.  Quaisquer outras atividades que consumam este valor devem ter a sua opção de Entrada Segura definida como verdadeira.

5. Para utilizar o valor noutra atividade, utilize a seguinte expressão de código **@activity ('Web1').output.value**.

    ![Expressão de código](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar o Azure Key Vault para armazenar credenciais para lojas de dados e computas, consulte [credenciais da Loja no Cofre da Chave Azure](./store-credentials-in-key-vault.md)