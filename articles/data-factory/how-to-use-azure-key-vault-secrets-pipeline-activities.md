---
title: Utilizar segredos do Azure Key Vault em atividades de pipeline
description: Aprenda a obter credenciais armazenadas do cofre chave Azure e use-as durante as corridas de gasodutos da fábrica de dados.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200117"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Utilizar segredos do Azure Key Vault em atividades de pipeline

Você pode armazenar credenciais ou valores secretos em um Cofre chave Azure e usá-los durante a execução do gasoduto para passar para as suas atividades.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade gerida pela fábrica de dados.  Saiba como funciona a partir da [identidade gerida para data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) e certifique-se de que a sua fábrica de dados tem uma associada.

## <a name="steps"></a>Passos

1. Abra as propriedades da sua fábrica de dados e copie o valor de ID de identidade gerida.

    ![Valor de Identidade Gerido](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Abra as políticas de acesso ao cofre chave e adicione as permissões de identidade geridas para obter e listar segredos.

    ![Políticas de acesso ao cofre chave](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Políticas de acesso ao cofre chave](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Clique em **Adicionar**e, em seguida, clique em **Guardar**.

3. Navegue para o seu segredo do Cofre chave e copie o Identificador Secreto.

    ![Identificador Secreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Tome nota do seu URI secreto que pretende obter durante a sua execução de pipeline de fábrica de dados.

4. No seu pipeline Data Factory, adicione uma nova atividade Web e configure-a da seguinte forma.  

    |Propriedade  |Valor  |
    |---------|---------|
    |Saída segura     |Verdadeiro         |
    |do IdP     |[O seu valor de URI secreto]?api-version=7.0         |
    |Método     |GET         |
    |Autenticação     |MSI         |
    |Recurso        |https://vault.azure.net       |

    ![Atividade web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Você deve adicionar **?api-version=7.0** ao fim do seu URI secreto.  

    > [!CAUTION]
    > Desdefinir a opção Saída Segura como verdadeira para evitar que o valor secreto seja registado em texto simples.  Quaisquer outras atividades que consumam este valor devem ter a sua opção De Entrada Segura definida como verdadeira.

5. Para utilizar o valor noutra atividade, utilize a seguinte expressão ** @activityde código ('Web1').output.value**.

    ![Expressão de código](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar o Azure Key Vault para armazenar credenciais para lojas de dados e cálculos, consulte [as credenciais da Loja no Cofre chave Azure](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
