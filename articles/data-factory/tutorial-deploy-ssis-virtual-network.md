---
title: Tutorial para configurar um tempo de integração Azure-SSIS para aderir a uma rede virtual
description: Aprenda a aderir a um tempo de integração Azure-SSIS para se juntar a uma rede virtual Azure.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: fc34c2422816f23c0c3eb8adf8a02b5e7ed3b4c0
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636991"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configure um tempo de integração de servidores Azure-SQL (SSIS) para aderir a uma rede virtual

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial fornece passos básicos para a utilização do portal Azure para configurar um tempo de integração de servidores (SSIS) Azure-SQL para aderir a uma rede virtual.

Os passos incluem:

- Configurar uma rede virtual.
- Junte-se ao Azure-SSIS IR a uma rede virtual do portal Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos

- **Tempo de integração Azure-SSIS** . Se não tiver um tempo de integração Azure-SSIS, [forneça um tempo de integração Azure-SSIS na Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) antes de começar.

- **Permissão do utilizador** . O utilizador que criar o Azure-SSIS IR deve ter a atribuição de [funções](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso Azure Data Factory com uma das opções abaixo:

    - Utilize a função de contribuinte de rede incorporada. Esta função vem com a permissão _Microsoft.Network/ \*_ que tem um âmbito muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a necessária _microsoft.network/virtualNetworks/ \* /join/action_ permission. Se também pretender trazer os seus próprios endereços IP públicos para Azure-SSIS IR ao juntar-se a uma rede virtual Azure Resource Manager, por favor inclua também _a Microsoft.Network/publicIPAddresses/*/join/action_ permission in the role.

- **Rede virtual** .

    - Se não tiver uma rede virtual, [crie uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    - Certifique-se de que o grupo de recursos da rede virtual pode criar e eliminar certos recursos de rede Azure.
    
        O Azure-SSIS IR precisa de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
        - Um equilibrador de carga Azure, com o nome *\<Guid> -azurebatch-cloudserviceloadanceancer*
        - Um grupo de segurança de rede, com o nome * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público Azure, com o nome -azurebatch-cloudservicepublicip
    
        Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu Azure-SSIS IR parar. Para evitar que o seu Azure-SSIS IR pare, não reutilize estes recursos de rede nos seus outros recursos.

    - Certifique-se de que não tem [nenhum bloqueio](../azure-resource-manager/management/lock-resources.md) de recursos no grupo de recursos/subscrição a que pertence a rede virtual. Se configurar um bloqueio apenas de leitura/apagar, iniciar e parar o seu Azure-SSIS IR falhará, ou deixará de responder.

    - Certifique-se de que não tem uma atribuição da Azure Policy que impeça a criação dos seguintes recursos no âmbito do grupo de recursos/subscrição a que a rede virtual pertence:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Abaixo os cenários **de configuração da rede** não são cobertos neste tutorial:
    - Se trouxer os seus próprios endereços IP públicos para o Azure-SSIS IR.
    - Se utilizar o seu próprio servidor Do Sistema de Nome de Domínio (DNS).
    - Se utilizar um grupo de segurança de rede (NSG) na sub-rede.
    - Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR).
    - Se utilizar Azure-SSIS IR personalizado.
    
    Para obter mais informações, verifique a [configuração da rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

## <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Utilize o portal Azure para configurar uma rede virtual antes de tentar aderir a um Azure-SSIS IR.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores da Web suportam a UI da Data Factory.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione **Mais serviços** . Filtrar e selecionar **redes virtuais.**

1. Filtre e selecione a sua rede virtual na lista.

1. Na página **de rede Virtual,** selecione **Propriedades.**

1. Selecione o botão de cópia para **O ID do RECURSO** para copiar o ID do recurso para a rede virtual para a área de transferência. Guarde o ID da área de transferência no OneNote ou num ficheiro.

1. No menu esquerdo, selecione **Subnets** .

    - Certifique-se de que a sub-rede selecionada tem espaço de endereço disponível suficiente para o Azure-SSIS IR utilizar. Deixe os endereços IP disponíveis pelo menos duas vezes o número do nó IV. O Azure reserva alguns endereços IP dentro de cada sub-rede. Estes endereços não podem ser usados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade do protocolo, e mais três endereços são utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Não selecione a GatewaySubnet para implementar um Azure-SSIS IR. É dedicado a portais de rede virtuais.
    - Não utilize uma sub-rede exclusivamente ocupada por outros serviços Azure (por exemplo, SQL Database SQL Managed Instance, App Service, e assim por diante).

1. Verifique se o fornecedor Azure Batch está registado na subscrição do Azure que possui a rede virtual. Ou registe o fornecedor Azure Batch. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada no Azure Batch. (Se criar o Azure-SSIS IR no portal Data Factory, o fornecedor Azure Batch está automaticamente registado para si.)

   1. No portal Azure, no menu esquerdo, selecione **Subscrições** .

   1. Selecione a sua subscrição.

   1. À esquerda, selecione **Fornecedores de Recursos** , e confirme que **Microsoft.Batch** é um fornecedor registado.

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **Microsoft.Batch** na lista, para registá-lo, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Junte-se ao Azure-SSIS IR a uma rede virtual

Depois de configurar a sua rede virtual Azure Resource Manager ou rede virtual clássica, pode juntar-se ao Azure-SSIS IR à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores da Web suportam a UI da Data Factory.

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Fábricas de Dados.** Se não vir **fábricas de Dados** no menu, selecione **Mais serviços** e, em seguida, na secção **INTELLIGENCE + ANALYTICS,** selecione **Fábricas de Dados** .

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione a sua fábrica de dados com o Azure-SSIS IR na lista. Vê a página inicial da sua fábrica de dados. Selecione o **azulejo do Monitor &** autor. Vê a UI da Data Factory num separador.

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na UI da Fábrica de Dados, mude para o **separador Editar,** selecione **Connections** e mude para o separador **Tempos de Execução de Integração.**

   ![Separador "Tempos de execução de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o seu Azure-SSIS IR estiver em execução, na lista **de Tempos de Execução de Integração,** na coluna **Ações,** selecione o botão **Stop** para o seu Azure-SSIS IR. Não pode editar o seu Azure-SSIS IR até que o pare.

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **de Tempos de Execução de Integração,** na coluna **Ações,** selecione o botão **Editar** para o seu Azure-SSIS IR.

   ![Editar o tempo de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração do tempo de execução da integração, avance através das **definições gerais** e **das definições SQL** selecionando o botão **Seguinte.**

1. Na secção **Definições Avançadas:**
   1. Selecione o **VNet para o seu tempo de execução de integração Azure-SSIS para aderir, permita que a ADF crie certos recursos de rede e, opcionalmente, traga a sua própria caixa de verificação de endereços IP públicos estáticos.**

   1. Para **Subscrição** , selecione a subscrição Azure que tem a sua rede virtual.

   1. Para **a localização,** é selecionado o mesmo local do seu tempo de execução de integração.

   1. Para **tipo,** selecione o tipo da sua rede virtual: classic ou Azure Resource Manager. Recomendamos que selecione uma rede virtual Azure Resource Manager, porque redes virtuais clássicas serão depreciadas em breve.

   1. Para **O Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo usado para a Base de Dados SQL com pontos finais de serviço de rede virtual ou SQL Managed Instance com ponto final privado para hospedar O SSISDB. Ou deve ser o mesmo ligado à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Para **nome da sub-rede,** selecione o nome da sub-rede para a sua rede virtual. Deve ser o mesmo usado para a SQL Datbase com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da utilizada para o seu SQL Managed Instance com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Selecione **validação VNet** . Se a validação for bem sucedida, **selecione Continue** .

   ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na secção **Resumo,** reveja todas as definições para o seu Azure-SSIS IR. Em seguida, selecione **Update** .

1. Inicie o seu Azure-SSIS IR selecionando o botão **Iniciar** na coluna **Ações** para o seu Azure-SSIS IR. Leva cerca de 20 a 30 minutos para iniciar o Azure-SSIS IR que une uma rede virtual.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [a adesão do Azure-SSIS IR a uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)