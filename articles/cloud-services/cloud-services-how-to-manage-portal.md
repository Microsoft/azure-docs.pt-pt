---
title: Tarefas comuns de gestão do serviço na nuvem [ Microsoft Docs
description: Saiba como gerir os Serviços cloud no portal Azure. Estes exemplos utilizam o portal Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 185bb9d03bec042a5c8e9223616b40eba6629e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247530"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Gerir serviços de cloud no portal Azure
Na área de **Serviços cloud** do portal Azure, pode:

* Atualizar uma função de serviço ou uma implantação.
* Promover uma implantação encenada para a produção.
* Ligue os recursos ao seu serviço na nuvem para que possa ver as dependências de recursos e escalar os recursos em conjunto.
* Elimine um serviço de nuvem ou uma implantação.

Para obter mais informações sobre como escalar o seu serviço de cloud, consulte [configurar automaticamente para um serviço de nuvem no portal](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Atualizar uma função de serviço na nuvem ou implementação
Se necessitar de atualizar o código de aplicação para o seu serviço na nuvem, utilize o **Update** na lâmina de serviço cloud. Pode atualizar um único papel ou todos os papéis. Para atualizar, pode fazer o upload de um novo pacote de serviço ou ficheiro de configuração de serviço.

1. No [portal Azure,][Azure portal]selecione o serviço de cloud que pretende atualizar. Este passo abre a lâmina da instância de serviço na nuvem.

2. Na lâmina, selecione **Update**.

    ![Botão de atualização](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implementação com um novo ficheiro de pacote de serviço (.cspkg) e ficheiro de configuração de serviço (.cscfg).

    ![AtualizaçãoImplantação](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcionalmente, atualize a conta de armazenamento e a etiqueta de implantação.

5. Se alguma função tiver apenas uma instância de função, selecione o **Deploy mesmo que uma ou mais funções contenham uma** caixa de verificação de uma única instância para permitir que a atualização prossiga.

    O Azure pode garantir apenas 99,95% de disponibilidade de serviço durante uma atualização de serviço na nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Com duas instâncias de papel, uma máquina virtual processa pedidos de clientes enquanto a outra é atualizada.

6. Selecione a caixa de verificação de **implementação Iniciar** para aplicar a atualização depois de terminado o upload da embalagem.

7. Selecione **OK** para começar a atualizar o serviço.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Swap implantações para promover uma implantação faseada para a produção
Quando decidir lançar um novo lançamento de um serviço na nuvem, encenar e testar a sua nova versão no ambiente de paragem do serviço na nuvem. Use **o Swap** para mudar os URLs pelos quais as duas implementações são endereçadas e promover uma nova versão para a produção.

Pode trocar as implementações a partir da página **cloud Services** ou do dashboard.

1. No [portal Azure,][Azure portal]selecione o serviço de cloud que pretende atualizar. Este passo abre a lâmina da instância de serviço na nuvem.

2. Na lâmina, selecione **Trocar**.

    ![Botão de troca de serviços de nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Abre-se o seguinte pedido de confirmação:

    ![Troca de Serviços na Nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implementação, selecione **OK** para trocar as implementações.

    A troca de implantação acontece rapidamente porque a única coisa que muda são os endereços IP virtuais (VIPs) para as implementações.

    Para poupar custos de cálculo, pode eliminar a implementação da encenação depois de verificar se a sua implantação de produção está a funcionar como esperado.

### <a name="common-questions-about-swapping-deployments"></a>Questões comuns sobre a troca de implantações

**Quais são os pré-requisitos para a troca de destacamentos?**

Existem dois pré-requisitos fundamentais para uma troca de implantação bem sucedida:

- Se quiser utilizar um endereço IP estático para a sua ranhura de produção, deve reservar um para a sua ranhura de preparação também. Caso contrário, a troca falha.

- Todas as instâncias das suas funções devem estar a decorrer antes de poder realizar a troca. Pode verificar o estado das suas instâncias na lâmina **de visão geral** do portal Azure. Em alternativa, pode utilizar o comando [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) no Windows PowerShell.

Note que as atualizações de OS e as operações de cura do serviço também podem fazer com que os swaps de implantação falhem. Para mais informações, consulte problemas de implementação do serviço em [nuvem Troubleshoot](cloud-services-troubleshoot-deployment-problems.md).

**Uma troca incorre em tempo de inatividade para a minha candidatura? Como devo lidar com isto?**

Como descrito na secção anterior, uma troca de implantação é tipicamente rápida porque é apenas uma mudança de configuração no equilíbrio de carga Azure. Em alguns casos, pode levar 10 ou mais segundos e resultar em falhas de ligação transitórias. Para limitar o impacto aos seus clientes, considere implementar a lógica de [retry do cliente.](../best-practices-retry-general.md)

## <a name="delete-deployments-and-a-cloud-service"></a>Eliminar implementações e um serviço na nuvem
Antes de poder eliminar um serviço na nuvem, tem de eliminar cada implementação existente.

Para poupar custos de cálculo, pode eliminar a implementação da encenação depois de verificar se a sua implantação de produção está a funcionar como esperado. Você é cobrado para os custos de computação para casos de funções implementados que são parados.

Utilize o seguinte procedimento para eliminar uma implantação ou o seu serviço na nuvem.

1. No [portal Azure,][Azure portal]selecione o serviço de nuvem que pretende eliminar. Este passo abre a lâmina da instância de serviço na nuvem.

2. Na lâmina, selecione **Delete**.

    ![Serviços de nuvem Eliminar botão](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Para eliminar todo o serviço de cloud, selecione o serviço Cloud e a sua caixa de verificação de **implementações.** Ou pode escolher a **implantação** da Produção ou a caixa de verificação de **implantação de encenação.**

    ![Serviços de nuvem apagam](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. **Selecione Excluir** na parte inferior.

5. Para eliminar o serviço de nuvem, selecione Eliminar o **serviço de nuvem**. Em seguida, no pedido de confirmação, selecione **Sim**.

> [!NOTE]
> Quando um serviço de nuvem é eliminado e a monitorização verbosa é configurada, deve eliminar os dados manualmente da sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [Introdução à monitorização do serviço em nuvem](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Encontre mais informações sobre implementações falhadas
A lâmina **de visão geral** tem uma barra de estado na parte superior. Quando seleciona a barra, abre-se uma nova lâmina e exibe qualquer informação de erro. Se a implementação não contiver erros, a lâmina de informação fica em branco.

![Visão geral dos serviços de nuvem](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço](cloud-services-how-to-configure-portal.md)de nuvem.
* Aprenda a implementar um serviço de [nuvem.](cloud-services-how-to-create-deploy-portal.md)
* Configure um nome de [domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).



