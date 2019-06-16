---
title: Tarefas de gestão do serviço de nuvem comuns | Documentos da Microsoft
description: Saiba como gerir serviços Cloud no portal do Azure. Estes exemplos utilizam o portal do Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: d3d1ae759f0f3fa5edd417da61f1fa50b5d9cde7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61433967"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Gerir serviços Cloud no portal do Azure
Na **serviços Cloud** área do portal do Azure, pode:

* Atualize uma função de serviço ou uma implementação.
* Promova uma implementação faseada para produção.
* Ligar recursos ao seu serviço cloud, para que pode ver as dependências de recursos e dimensionar os recursos em conjunto.
* Elimine um serviço cloud ou uma implementação.

Para obter mais informações sobre como dimensionar o serviço de nuvem, consulte [configurar o dimensionamento automático para um serviço cloud no portal do](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Atualizar uma função de serviço na nuvem ou implementação
Se precisar de atualizar o código de aplicação do serviço em nuvem, utilize **atualizar** no painel de serviço cloud. Pode atualizar uma função única ou todas as funções. Para atualizar, pode carregar um novo pacote de serviço ou o ficheiro de configuração do serviço.

1. Na [portal do Azure][Azure portal], selecione o serviço em nuvem que pretende atualizar. Este passo é aberto o painel de instância do serviço cloud.

2. No painel, selecione **atualização**.

    ![Botão de atualização](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implementação com um novo ficheiro de pacote de serviço (. cspkg) e o ficheiro de configuração de serviço (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcionalmente, atualize a conta de armazenamento e a etiqueta de implementação.

5. Se quaisquer funções tem apenas uma instância de função, selecione o **implementar mesmo que uma ou mais funções contenham uma única instância** caixa de verificação para ativar a atualização continuar.

    Azure pode garantir a disponibilidade do serviço de 99,95 por cento apenas durante uma atualização de serviço em nuvem se cada função tem, pelo menos, duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processa os pedidos de cliente enquanto o outro está atualizado.

6. Selecione o **iniciar implementação** caixa de verificação para aplicar a atualização depois de terminar o carregamento do pacote.

7. Selecione **OK** para começar a atualizar o serviço.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Trocar implementações para promover uma implementação faseada para produção
Quando optar por implementar uma nova versão do serviço cloud, estágio e testar a nova versão no ambiente de teste do serviço na nuvem. Uso **trocar** para alternar os URLs através do qual as duas implementações são resolvidas e promovem uma nova versão para produção.

Pode trocar implementações a partir da **serviços Cloud** página ou dashboard.

1. Na [portal do Azure][Azure portal], selecione o serviço em nuvem que pretende atualizar. Este passo é aberto o painel de instância do serviço cloud.

2. No painel, selecione **trocar**.

    ![Botão de comutação de serviços cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Abre o pedido de confirmação seguinte:

    ![Comutação de serviços cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implementação, selecione **OK** para trocar as implementações.

    A troca de implementação acontece rapidamente porque a única alteração é os endereços IP virtuais (VIPs) para as implementações.

    Para reduzir os custos de computação, pode eliminar a implementação de teste depois de verificar se a implementação de produção está a funcionar conforme esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre a troca de implementações

**Quais são os pré-requisitos para a troca de implementações?**

Existem dois pré-requisitos chave para uma troca de implementação com êxito:

- Se pretender utilizar um endereço IP estático para o bloco de produção, tem de reservar um para o bloco de teste. Caso contrário, a troca falha.

- Tem de executar todas as instâncias de suas funções antes de realizar a troca. Pode verificar o estado das suas instâncias do **descrição geral** painel do portal do Azure. Em alternativa, pode utilizar o [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) comando no Windows PowerShell.

Tenha em atenção que as atualizações do SO convidado e o serviço de recuperação operações também podem causar trocas de implementação efetuar a ativação. Para obter mais informações, consulte [problemas de implementação de serviço de nuvem de resolução de problemas](cloud-services-troubleshoot-deployment-problems.md).

**Uma alternância de incorrer em períodos de indisponibilidade para a minha aplicação? Como posso manipulá-lo?**

Conforme descrito na secção anterior, uma alternância de implementação é normalmente rápida porque é apenas uma alteração de configuração no balanceador de carga do Azure. Em alguns casos, pode demorar 10 ou mais segundos e o resultado em falhas de conexão transitórias. Para limitar o impacto para os seus clientes, considere implementar [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Eliminar as implementações e um serviço em nuvem
Antes de poder eliminar um serviço em nuvem, tem de eliminar cada implementação existente.

Para reduzir os custos de computação, pode eliminar a implementação de teste depois de verificar se a implementação de produção está a funcionar conforme esperado. São faturados os custos de computação para as instâncias de função implantada, que são parados.

Utilize o procedimento seguinte para eliminar uma implementação ou o seu serviço cloud.

1. Na [portal do Azure][Azure portal], selecione o serviço em nuvem que pretende eliminar. Este passo é aberto o painel de instância do serviço cloud.

2. No painel, selecione **eliminar**.

    ![Botão de eliminar serviços cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Para eliminar o serviço em nuvem inteira, selecione o **Cloud service e as respetivas implementações** caixa de verificação. Ou pode escolher entre o **implementação de produção** ou o **implementação faseada** caixa de verificação.

    ![Eliminação de serviços cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Selecione **eliminar** na parte inferior.

5. Para eliminar o serviço em nuvem, selecione **serviço de cloud de eliminação**. Em seguida, no prompt de confirmação, selecione **Sim**.

> [!NOTE]
> Quando um serviço em nuvem é eliminado e monitorização verboso estiver configurado, tem de eliminar os dados manualmente da sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, veja [introdução à monitorização do serviço de nuvem](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Encontrar mais informações sobre implementações falhadas
O **descrição geral** painel tem uma barra de status na parte superior. Quando seleciona a barra, um novo painel abre e apresenta as informações de erro. Se a implementação não contém quaisquer erros, o painel de informações está em branco.

![Descrição geral dos serviços de cloud](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos Seguintes
* [Configuração geral do seu serviço cloud](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurar uma [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).
