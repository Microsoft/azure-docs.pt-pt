---
title: Tarefas comuns de gerenciamento de serviço de nuvem | Microsoft Docs
description: Saiba como gerenciar serviços de nuvem no portal do Azure. Esses exemplos usam o portal do Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 185bb9d03bec042a5c8e9223616b40eba6629e36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360910"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Gerenciar serviços de nuvem no portal do Azure
Na área **serviços de nuvem** da portal do Azure, você pode:

* Atualizar uma função de serviço ou uma implantação.
* Promova uma implantação em etapas para a produção.
* Vincule recursos ao seu serviço de nuvem para que você possa ver as dependências de recursos e dimensionar os recursos juntos.
* Excluir um serviço de nuvem ou uma implantação.

Para obter mais informações sobre como dimensionar seu serviço de nuvem, consulte [Configurar o dimensionamento automático para um serviço de nuvem no portal](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Atualizar uma função ou implantação de serviço de nuvem
Se você precisar atualizar o código do aplicativo para seu serviço de nuvem, use **Atualizar** na folha do serviço de nuvem. Você pode atualizar uma única função ou todas as funções. Para atualizar, você pode carregar um novo pacote de serviço ou arquivo de configuração de serviço.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja atualizar. Essa etapa abre a folha instância do serviço de nuvem.

2. Na folha, selecione **Atualizar**.

    ![Botão de atualização](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implantação com um novo arquivo de pacote de serviço (. cspkg) e arquivo de configuração de serviço (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcionalmente, atualize a conta de armazenamento e o rótulo de implantação.

5. Se qualquer função tiver apenas uma instância de função, marque a caixa de seleção **implantar mesmo se uma ou mais funções contiverem uma única instância** para habilitar a atualização para continuar.

    O Azure pode garantir apenas 99,95% de disponibilidade de serviço durante uma atualização de serviço de nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processa solicitações de cliente enquanto a outra é atualizada.

6. Marque a caixa de seleção **Iniciar implantação** para aplicar a atualização depois que o carregamento do pacote for concluído.

7. Selecione **OK** para iniciar a atualização do serviço.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Permutar implantações para promover uma implantação em etapas para produção
Quando você decidir implantar uma nova versão de um serviço de nuvem, prepare e teste sua nova versão no ambiente de preparo do serviço de nuvem. Use **swap** para alternar as URLs pelas quais as duas implantações são endereçadas e promover uma nova versão para produção.

Você pode alternar as implantações da página de **serviços de nuvem** ou do painel.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja atualizar. Essa etapa abre a folha instância do serviço de nuvem.

2. Na folha, selecione **alternar**.

    ![Botão de permuta dos serviços de nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O prompt de confirmação a seguir é aberto:

    ![Troca de serviços de nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implantação, selecione **OK** para trocar as implantações.

    A troca de implantação ocorre rapidamente porque a única coisa que é alterada são os VIPs (endereços IP virtuais) para as implantações.

    Para economizar custos de computação, você pode excluir a implantação de preparo depois de verificar se sua implantação de produção está funcionando conforme o esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre a troca de implantações

**Quais são os pré-requisitos para a troca de implantações?**

Há dois pré-requisitos principais para uma troca de implantação bem-sucedida:

- Se você quiser usar um endereço IP estático para o slot de produção, deverá reservar um para o slot de preparo também. Caso contrário, a permuta falhará.

- Todas as instâncias de suas funções devem estar em execução antes que você possa executar a permuta. Você pode verificar o status de suas instâncias na folha **visão geral** do portal do Azure. Como alternativa, você pode usar o comando [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) no Windows PowerShell.

Observe que as atualizações de SO convidado e as operações de recuperação de serviço também podem causar falha nas trocas de implantação. Para obter mais informações, consulte [solucionar problemas de implantação do serviço de nuvem](cloud-services-troubleshoot-deployment-problems.md).

**Uma troca incorre em tempo de inatividade para meu aplicativo? Como devo tratá-lo?**

Conforme descrito na seção anterior, uma troca de implantação é normalmente rápida porque é apenas uma alteração de configuração no Azure Load Balancer. Em alguns casos, pode levar 10 ou mais segundos e resultar em falhas de conexão transitórias. Para limitar o impacto aos seus clientes, considere implementar a [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Excluir implantações e um serviço de nuvem
Para poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo depois de verificar se sua implantação de produção está funcionando conforme o esperado. Você é cobrado por custos de computação para instâncias de função implantadas que são interrompidas.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja excluir. Essa etapa abre a folha instância do serviço de nuvem.

2. Na folha, selecione **excluir**.

    ![Botão de exclusão de serviços de nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Para excluir o serviço de nuvem inteiro, marque a caixa de seleção **serviço de nuvem e suas implantações** . Ou você pode escolher a caixa de seleção **implantação de produção** ou **implantação de preparo** .

    ![Exclusão de serviços de nuvem](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Selecione **excluir** na parte inferior.

5. Para excluir o serviço de nuvem, selecione **excluir serviço de nuvem**. Em seguida, no prompt de confirmação, selecione **Sim**.

> [!NOTE]
> Quando um serviço de nuvem é excluído e o monitoramento detalhado é configurado, você deve excluir os dados manualmente de sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [introdução ao monitoramento do serviço de nuvem](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Encontre mais informações sobre implantações com falha
A folha de **visão geral** tem uma barra de status na parte superior. Quando você seleciona a barra, uma nova folha é aberta e exibe as informações de erro. Se a implantação não contiver erros, a folha informações ficará em branco.

![Visão geral dos serviços de nuvem](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).



