---
title: Tarefas comuns de gestão de serviços em nuvem | Microsoft Docs
description: Saiba como gerir os Serviços Cloud no portal Azure. Estes exemplos usam o portal Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a1b37ed1d15282224cc7de61ec6f8a98a4bbf732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610506"
---
# <a name="manage-cloud-services-classic-in-the-azure-portal"></a>Gerir serviços cloud (clássicos) no portal Azure

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Na área **de Serviços cloud** do portal Azure, pode:

* Atualize uma função de serviço ou uma implementação.
* Promover uma implantação encenada para a produção.
* Ligue os recursos ao seu serviço na nuvem para que possa ver as dependências de recursos e escalar os recursos em conjunto.
* Elimine um serviço de nuvem ou uma implantação.

Para obter mais informações sobre como escalar o seu serviço na nuvem, consulte [configurar a escala automática para um serviço de cloud no portal](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Atualizar uma função de serviço de nuvem ou implantação
Se precisar de atualizar o código de aplicação para o seu serviço na nuvem, utilize **a Atualização** na lâmina de serviço na nuvem. Pode atualizar uma única função ou todas as funções. Para atualizar, pode carregar um novo pacote de serviço ou ficheiro de configuração de serviço.

1. No [portal Azure,][Azure portal]selecione o serviço de cloud que pretende atualizar. Este passo abre a lâmina de placa de serviço de nuvem.

2. Na lâmina, selecione **Update**.

    ![Botão de atualização](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implementação com um novo ficheiro de pacote de serviço (.cspkg) e ficheiro de configuração de serviço (.cscfg).

    ![AtualizaçãoDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcionalmente, atualize a conta de armazenamento e a etiqueta de implantação.

5. Se alguma função tiver apenas uma instância de função, selecione o **Deploy mesmo que uma ou mais funções contenham uma** única caixa de verificação de instância para permitir que a atualização prossiga.

    O Azure pode garantir apenas 99,95% de disponibilidade de serviço durante uma atualização do serviço na nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processa os pedidos do cliente enquanto a outra é atualizada.

6. Selecione a caixa **de verificação de implementação Iniciar** para aplicar a atualização depois de ter terminado o upload da embalagem.

7. Selecione **OK** para começar a atualizar o serviço.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Swap de implementações para promover uma implantação encenada para a produção
Quando decidir implementar um novo lançamento de um serviço de cloud, encenar e testar o seu novo lançamento no seu ambiente de preparação de serviços na nuvem. Utilize **o Swap** para mudar os URLs através dos quais as duas implementações são endereçadas e promover uma nova versão para a produção.

Pode trocar as implementações da página **Cloud Services** ou do dashboard.

1. No [portal Azure,][Azure portal]selecione o serviço de cloud que pretende atualizar. Este passo abre a lâmina de placa de serviço de nuvem.

2. Na lâmina, selecione **Swap**.

    ![Botão de troca de serviços em nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O seguinte aviso de confirmação abre:

    ![Troca de serviços na nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implantação, selecione **OK** para trocar as implementações.

    A troca de implementação acontece rapidamente porque a única coisa que muda são os endereços IP virtuais (VIPs) para as implementações.

    Para poupar custos de cálculo, pode eliminar a colocação de produção depois de verificar se a sua implantação de produção está a funcionar como esperado.

### <a name="common-questions-about-swapping-deployments"></a>Questões comuns sobre a troca de implementações

**Quais são os pré-requisitos para a troca de implantações?**

Existem dois pré-requisitos fundamentais para uma troca de implementação bem sucedida:

- Se pretender utilizar um endereço IP estático para a sua ranhura de produção, também deve reservar um para a sua ranhura de preparação. Caso contrário, a troca falha.

- Todas as instâncias das suas funções devem estar a decorrer antes de poder efetuar a troca. Pode verificar o estado das suas instâncias na lâmina **geral** do portal Azure. Em alternativa, pode utilizar o comando [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole) no Windows PowerShell.

Note que as atualizações do SO do hóspede e as operações de cura do serviço também podem causar a falha de trocas de implementação. Para obter mais informações, consulte [problemas de implementação do serviço de nuvem troubleshoot](cloud-services-troubleshoot-deployment-problems.md).

**Uma troca incorre no tempo de inatividade para o meu pedido? Como devo lidar com isto?**

Como descrito na secção anterior, uma troca de implementação é tipicamente rápida porque é apenas uma alteração de configuração no equilibrador de carga Azure. Em alguns casos, pode levar 10 ou mais segundos e resultar em falhas de ligação transitórias. Para limitar o impacto aos seus clientes, considere implementar a lógica de [relagem](/azure/architecture/best-practices/transient-faults)do cliente.

## <a name="delete-deployments-and-a-cloud-service"></a>Eliminar implementações e um serviço de nuvem
Antes de eliminar um serviço de nuvem, tem de eliminar cada implementação existente.

Para poupar custos de cálculo, pode eliminar a colocação de produção depois de verificar se a sua implantação de produção está a funcionar como esperado. Você é cobrado para custos de cálculo para instâncias de papel implementadas que são parados.

Utilize o seguinte procedimento para eliminar uma implantação ou o seu serviço na nuvem.

1. No [portal Azure,][Azure portal]selecione o serviço de cloud que pretende eliminar. Este passo abre a lâmina de placa de serviço de nuvem.

2. Na lâmina, **selecione Delete**.

    ![Botão de eliminação de serviços na nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Para eliminar todo o serviço de nuvem, selecione o serviço Cloud e a sua caixa **de verificação de implementações.** Ou pode escolher a **implementação** da Produção ou a caixa **de verificação de implementação de encenação.**

    ![Serviços na Nuvem Eliminar](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. **Selecione Excluir** na parte inferior.

5. Para eliminar o serviço na nuvem, selecione **Eliminar o serviço de nuvem**. Em seguida, no pedido de confirmação, selecione **Sim**.

> [!NOTE]
> Quando um serviço de nuvem é eliminado e a monitorização verbosa é configurada, deve eliminar os dados manualmente da sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [Introdução à monitorização do serviço na nuvem.](cloud-services-how-to-monitor.md)


## <a name="find-more-information-about-failed-deployments"></a>Encontre mais informações sobre implementações falhadas
A **lâmina de visão geral** tem uma barra de estado no topo. Quando selecionar a barra, uma lâmina nova abre-se e apresenta qualquer informação de erro. Se a colocação não contiver erros, a lâmina de informação está em branco.

![Visão geral dos serviços em nuvem](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço na nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado.](cloud-services-custom-domain-name-portal.md)
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).