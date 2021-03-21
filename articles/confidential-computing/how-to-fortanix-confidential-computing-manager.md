---
title: Fortanix Confidential Computing Manager numa aplicação gerida pela Azure
description: Saiba como implementar o Fortanix Confidential Computing Manager (CCM) numa aplicação gerida no portal Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563426"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix Confidential Computing Manager numa aplicação gerida pela Azure

Este artigo mostra-lhe como implementar uma aplicação que é gerida pelo Fortanix Confidential Computing Manager no portal Azure.

Fortanix é um fornecedor de software de terceiros com produtos e serviços construídos em cima da infraestrutura Azure. Existem outros fornecedores de terceiros que oferecem serviços de computação confidencial semelhantes na Azure.

> [!NOTE]
>Os produtos referenciados neste documento não estão sob o controlo da Microsoft. A Microsoft está a fornecer-lhe estas informações apenas como uma conveniência, e a referência a estes produtos não Microsoft não implica o endosso da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

- Um registo privado do Docker para empurrar imagens de aplicações convertidas.
- Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Implementar um Gestor de Computação Confidencial através de uma aplicação gerida pelo Azure

1. Aceda ao [Portal do Azure](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Portal Azure.":::

2. Na Barra de Pesquisa, procure "Fortanix Confidential Computing Manager" e encontrará a listagem marketplace para O CcM Fortanix. Selecione **Fortanix Confidential Computing Manager em Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Listagem de mercado.":::

3. Abre a página na qual cria a aplicação gerida pelo CCM. selecionar **Criar**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Criar Aplicação.":::

4. Preencha todos os campos necessários.
   1. Na secção Detalhes de Aplicação Gerida, o campo **Grupo de Recursos Geridos** terá um valor predefinido que o utilizador pode modificar se necessário.
   2. No campo da **Região,** selecione **a Austrália Oriental**, Austrália **Sudeste**, **Leste dos EUA**, Oeste DOS EUA **2**, **Europa Ocidental**, Europa **do Norte,** **Canadá Central,** **Canadá Leste** ou **Leste EUAP.**

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Campos Necessários":::

   Selecione **Review + criar** para criar a aplicação gerida pelo Fortanix CCM.

5. Reveja os detalhes e assim que a validação passar, selecione o **eu concordo com os termos e condições acima da** caixa de verificação e, em seguida, selecione **Criar** para criar a aplicação gerida.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Rever detalhes.":::

6. A implantação do CcM Fortanix iniciar-se-á e notificará que a implantação está em curso.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Progresso de implantação.":::

7. Quando a implementação estiver concluída, selecione Ir ao botão **de recursos** para ir à página "Visão Geral" da aplicação gerida pela CCM para inscrever o nó de computação.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Screenshot que mostra uma implementação bem sucedida no portal Azure.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Screenshot que mostra uma visão geral do recurso de computação confidencial no portal Azure.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Inscreva o nó computativo no Fortanix CCM

1. Selecione **Confidential Computing Manager** a partir do menu de navegação à esquerda. Faça login no CcM Fortanix e crie uma conta como vê na **Figura 9**.

    Para obter mais detalhes sobre como se inscrever, faça login e crie uma conta em CCM consulte a [CCM Getting Started](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Screenshot que mostra o login do Fortanix Confidential Computing Manager.":::
    
2. Para obter o Token de junção da Consola de Gestão CCM, selecione primeiro o botão **NODE de INSCRIÇÃO.** Em seguida, na janela NODE DE INSCRIÇÃO, selecione o botão **COPY** para copiar o token de junção.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Screenshot que mostra obter o símbolo de junção.":::

3. Agora para inscrever um agente de nó, selecione o **separador Agente de Nó de Computação Confidencial** e selecione **Adicionar** para adicionar um agente de nó CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Screenshot que mostra a adição do agente de nó.":::

4.  No formulário de agente de nó CCM, preencha todos os campos necessários. Cole o símbolo de união que copiou no Passo 2 em **Join Token**. Selecione **Review + submeter-se** para confirmar.

    Para obter mais informações sobre como inscrever um nó de computação CCM, consulte [o Nó de Cálculo de Inscrição](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Screenshot que mostra a inscrição do nó de computação.":::
    
5. Após a validação passar, **selecione Submeter-se** para completar a criação do agente de nó.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Screenshot que mostra que o agente de nó é criado.":::

6. Para verificar o estado de implementação, vá ao **separador 'Vista Geral'** e selecione o link **do grupo de recursos gerido.**

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Screenshot que mostra o nó está matriculado.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Screenshot que mostra a verificação do estado de implementação.":::

7. Agora vai notar que o estado de implantação ainda está em andamento e levará alguns minutos para que o agente de nó seja matriculado com sucesso.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Screenshot que mostra a implantação em andamento.":::

8. Quando a inscrição do agente de nó é bem sucedida, o estado muda para "Bem sucedido".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Screenshot que mostra a implementação conseguiu.":::

9. Agora, na aplicação gerida pela CCM, vá às páginas Compute Nodes e note que o nó está em estado **Ativo** e matriculado com sucesso.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Screenshot que mostra o nó matriculado com sucesso.":::

## <a name="clean-up-resources"></a>Limpar os recursos

O utilizador também pode eliminar um agente de nó CCM da página Confidencial do Agente de Nó de Computação. Para eliminar o agente de nó, selecione o agente de nó e selecione o botão **Eliminar** na barra superior.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Screenshot que mostra apagar o agente de nó.":::

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você inscreveu um nó usando uma aplicação gerida Azure para O Gestor de Computação Confidencial da Fortanix. A inscrição no nó permite converter a sua imagem de aplicação para funcionar em cima de uma máquina virtual de computação confidencial. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, consulte [Soluções em Máquinas Virtuais.](virtual-machine-solutions.md)

Para saber mais sobre as ofertas confidenciais de computação da Azure, consulte [a computação confidencial do Azure.](overview.md)

Saiba como completar tarefas semelhantes utilizando outras ofertas de terceiros em Azure, como [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) e [Scone.](https://sconedocs.github.io)

