---
title: 'Quickstart: Criar uma conta Azure Purview no portal Azure (pré-visualização)'
description: Este Quickstart descreve como criar uma conta Azure Purview e configurar permissões para começar a usá-la.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 00b504c7bcf51a69d03fb1294de4f52ef35ed163
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555997"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Quickstart: Criar uma conta Azure Purview no portal Azure

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Neste arranque rápido, cria-se uma conta Azure Purview.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Seu próprio [inquilino do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="configure-your-subscription"></a>Configurar a sua subscrição

Se necessário, siga estes passos para configurar a sua subscrição para permitir que o Azure Purview seja executado na sua subscrição:

   1. No portal Azure, procure e selecione **Subscrições.**

   1. A partir da lista de subscrições, selecione a subscrição que pretende utilizar. É necessária autorização de acesso administrativo para a subscrição.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot mostrando como selecionar uma subscrição no portal Azure.":::

   1. Para a sua subscrição, selecione **Fornecedores de Recursos.** No painel de fornecedores de **recursos,** procure e registe os três fornecedores de recursos: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se não estiverem registados, registe-o selecionando **Registo**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot mostrando como registar o fornecedor de recursos microsoft dot Azure Purview no portal Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Criar uma instância de conta Azure Purview

1. Vá à página **de contas do Purview** no portal Azure e, em seguida, selecione **Adicionar** para criar uma nova conta Azure Purview. Em alternativa, pode ir ao mercado procurar **contas de provisão** e selecionar **Criar.** Note que pode adicionar apenas uma conta Azure Purview de cada vez.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Screenshot mostrando como criar uma conta Azure Purview no portal Azure.":::

1. No separador **Básico,** faça o seguinte:
    1. Selecione um **grupo de recursos**.
    1. Insira um **nome de conta Purview** para o seu catálogo. Espaços e símbolos não são permitidos.
    1. Escolha uma  **localização** e, em seguida, selecione **Seguinte: Configuração**.
1. No **separador Configuração,** selecione o tamanho da **Plataforma** desejada - os valores permitidos são 4 unidades de capacidade (CU) e 16 CU. Selecione **Seguinte: Tags**.
1. No separador **Tags,** pode adicionar opcionalmente uma ou mais etiquetas. Estas tags são para uso apenas no portal Azure, e não em Azure Purview.
1. Selecione **& criar** e, em seguida, selecione **Criar**. Leva alguns minutos para completar a criação. A nova instância da conta Azure Purview, recém-criada, aparece na lista na sua página **de contas Purview.**
1. Quando o novo provisionamento de conta estiver completo, **selecione Vá para o recurso**.

1. Selecione **a conta de competência de lançamento**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot da seleção para lançar o catálogo de conta Azure Purview.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Adicione um principal de segurança a uma função de data plane

Antes de você ou a sua equipa poder começar a usar o Azure Purview, um ou mais princípios de segurança devem ser adicionados a uma das funções pré-definidas do Data Plane: **Purview Data Reader**, **Purview Data Curator** ou **Purview Data Source Administrator**. Para obter mais informações sobre as permissões do Catálogo de Dados do Azure Purview, consulte [as permissões do Catálogo.](catalog-permissions.md)

Para adicionar um principal de segurança à função de plano de dados **do Curador de Dados da Purview** numa conta Azure Purview:

1. Aceda à página [**de contas do Purview**](https://aka.ms/purviewportal) no portal Azure.

1. Selecione a conta Azure Purview que pretende modificar.

1. Na página da **conta '',',** selecione o **separador Access control (IAM)**

1. Clique **+ Adicionar**

Se ao clicar em Adicionar vê duas escolhas que mostram ambas marcadas (desativadas) então isto significa que não tem as permissões certas para adicionar alguém a uma função de plano de dados na conta Azure Purview. Tem de encontrar um Proprietário, Administrador de Acesso ao Utilizador ou outra pessoa com autoridade de atribuição de funções na sua conta Azure Purview. Pode procurar as pessoas certas selecionando o separador **de atribuições de Função** e, em seguida, deslocar-se para baixo para procurar o Proprietário ou Administrador de Acesso ao Utilizador e contactar essas pessoas.

1. Selecione **Adicionar atribuição de função**.

1. Para o tipo de função no **Papel de Curador de Dados de Purview** ou **função de administrador de fonte de dados** de purview dependendo do que o principal de serviço vai ser usado (por favor consulte [permissões de catálogo](catalog-permissions.md) para mais detalhes).

1. Para **atribuir acesso para** deixar o padrão, **utilizador, grupo ou principal de serviço**.

1. Para **Selecionar insira** o nome do utilizador, do grupo Azure Ative Directory ou do principal de serviço que pretende atribuir e, em seguida, clique no seu nome no painel de resultados.

1. Clique em **Guardar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar desta conta Azure Purview, elimine-a com os seguintes passos:

1. Aceda à página **de contas do Purview** no portal Azure.

2. Selecione a conta Azure Purview que criou no início deste arranque rápido. Selecione **Eliminar**, insira o nome da conta e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Purview.

Avance para o próximo artigo para saber como permitir que os utilizadores acedam à sua Conta Azure Purview. 

> [!div class="nextstepaction"]
> [Adicione utilizadores à sua Conta Azure Purview](catalog-permissions.md)
