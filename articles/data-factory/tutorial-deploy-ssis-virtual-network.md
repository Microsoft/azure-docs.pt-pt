---
title: Tutorial para configurar um tempo de integração Azure-SSIS para aderir a uma rede virtual
description: Aprenda a aderir a um tempo de funcionação de integração Azure-SSIS para uma rede virtual Azure.
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
ms.openlocfilehash: 3dd2be9a9f618f19ae71de8b19115013896b10cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195558"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configure um tempo de integração de serviços de integração de servidores Azure-SQL (SSIS) para aderir a uma rede virtual

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial fornece passos básicos para a utilização do portal Azure para configurar um tempo de execução de integração do Servidor Azure-SQL (SSIS) para aderir a uma rede virtual.

Os passos incluem:

- Configure uma rede virtual.
- Junte-se ao IR Azure-SSIS a uma rede virtual do portal Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos

- Tempo de execução de **integração Azure-SSIS.** Se não tiver um tempo de execução de integração Azure-SSIS, forneça um tempo de execução de [integração Azure-SSIS na Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) antes do início.

- **Autorização do utilizador**. O utilizador que criar o IR Azure-SSIS deve ter a atribuição de [funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso Azure Data Factory com uma das opções abaixo:

    - Utilize a função de Colaborador de Rede incorporada. Este papel vem com a permissão _Microsoft.Network/,\* _ que tem um âmbito muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a necessária _Microsoft.Network/virtualNetworks/\*/join/action_ permission. Se também pretende trazer os seus próprios endereços IP públicos para o Ir Azure-SSIS ao mesmo tempo que o junta a uma rede virtual do Gestor de Recursos Do Azure, por favor, inclua também a _Microsoft.Network/publicIPAddresss/*/join/action_ permission no papel.

- **Rede virtual.**

    - Se não tiver uma rede virtual, [crie uma rede virtual utilizando o portal Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Certifique-se de que o grupo de recursos da rede virtual pode criar e eliminar certos recursos da rede Azure.
    
        O IR Azure-SSIS necessita de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
        - Um equilibrador de carga Azure, com o nome * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Um grupo de segurança de\<rede, com o nome * Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público azure, com o nome -azurebatch-cloudservicepublicip
    
        Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu IR Azure-SSIS parar. Para evitar que o seu IR Azure-SSIS pare, não reutilize estes recursos de rede nos seus outros recursos.

    - Certifique-se de que não tem [nenhum bloqueio](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) de recursos no grupo/subscrição de recursos a que pertence a rede virtual. Se configurar um bloqueio de leitura/exclusão, iniciar e parar o seu IR Azure-SSIS falhará, ou deixará de responder.

    - Certifique-se de que não tem uma atribuição de Política Azure que impeça a criação dos seguintes recursos ao abrigo do grupo/subscrição de recursos a que pertence a rede virtual:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Abaixo os cenários de **configuração** da rede não são cobertos neste tutorial:
    - Se trouxer os seus próprios endereços IP públicos para o IR Azure-SSIS.
    - Se utilizar o seu próprio servidor do Sistema de Nomede Domínio (DNS).
    - Se utilizar um grupo de segurança de rede (NSG) na sub-rede.
    - Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR).
    - Se utilizar o Azure-SSIS IR personalizado.
    
    Para mais informações, verifique a [configuração da rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

## <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Utilize o portal Azure para configurar uma rede virtual antes de tentar aderir a um IR Azure-SSIS.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores web suportam a UI da Fábrica de Dados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Mais serviços**. Filtrar e selecionar **redes virtuais**.

1. Filtre e selecione a sua rede virtual na lista.

1. Na página de **rede Virtual,** selecione **Propriedades**.

1. Selecione o botão de cópia para **ID DE RECURSOS** para copiar o ID de recurso para a rede virtual para a área de reparação. Guarde o ID da pasta no OneNote ou num ficheiro.

1. No menu esquerdo, selecione **Subnets**.

    - Certifique-se de que a sub-rede selecionada tem espaço de endereço disponível suficiente para a utilização do IR Azure-SSIS. Deixe os endereços IP disponíveis durante pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP dentro de cada subnet. Estes endereços não podem ser usados. Os primeiros e últimos endereços IP das subnets estão reservados para conformidade com o protocolo, e mais três endereços são utilizados para os serviços Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Não selecione a GatewaySubnet para implantar um IR Azure-SSIS. É dedicado a portais de rede virtuais.
    - Não utilize uma sub-rede que seja exclusivamente ocupada por outros serviços Azure (por exemplo, sqL Database gerido por exemplo, App Service, e assim por diante).

1. Verifique se o fornecedor de Lote Azure está registado na subscrição Azure que possui a rede virtual. Ou registe o fornecedor do Lote Azure. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada para o Lote Azure. (Se criar o IR Azure-SSIS no portal data Factory, o fornecedor de Lotes Azure está automaticamente registado para si.)

   1. No portal Azure, no menu esquerdo, selecione **Subscrições**.

   1. Selecione a sua subscrição.

   1. À esquerda, selecione **fornecedores**de Recursos , e confirme que a **Microsoft.Batch** é um fornecedor registado.

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir o **Microsoft.Batch** na lista, para o registar, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Junte-se ao IR Azure-SSIS a uma rede virtual

Depois de configurar a sua rede virtual Do Gestor de Recursos Azure ou a rede virtual clássica, pode juntar-se ao IR Azure-SSIS à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores web suportam a UI da Fábrica de Dados.

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione fábricas de **dados.** Se não vir **as fábricas de dados** no menu, selecione Mais **serviços,** e depois na secção **INTELLIGENCE + ANALYTICS,** selecione **Fábricas**de Dados .

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione a sua fábrica de dados com o IR Azure-SSIS na lista. Veja a página inicial da sua fábrica de dados. Selecione o azulejo **monitor de & autor.** Vê a UI da Fábrica de Dados num separador.

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na UI da Fábrica de Dados, mude para o separador **Editar,** selecione **Ligações**e mude para o separador Tempos de **Integração.**

   ![Separador "Tempos de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o seu IR Azure-SSIS estiver em execução, na lista de Tempos de **Integração,** na coluna **Ações,** selecione o botão **Stop** para o seu IR Azure-SSIS. Não pode editar o seu IR Azure-SSIS até o parar.

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **De Integração Runtimes,** na coluna **Ações,** selecione o botão **Editar** para o seu IR Azure-SSIS.

   ![Editar o tempo de execução da integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração do tempo de integração, avance através das **definições gerais** e das **definições SQL** selecionando o botão **Seguinte.**

1. Na secção **Definições Avançadas:**
   1. Selecione o Select a VNet para o seu Tempo de **Integração Azure-SSIS para aderir, permitir que a ADF crie certos recursos de rede e, opcionalmente, traga** a sua própria caixa de verificação de endereços IP públicos estáticos.

   1. Para **Subscrição**, selecione a subscrição Azure que tem a sua rede virtual.

   1. Para **localização**, é selecionada a mesma localização do seu tempo de execução de integração.

   1. Para **Tipo**, selecione o tipo da sua rede virtual: classic ou Gestor de Recursos Azure. Recomendamos que selecione uma rede virtual do Gestor de Recursos Azure, porque as redes virtuais clássicas serão depreciadas em breve.

   1. Para **Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo utilizado para o seu servidor de base de dados Azure SQL com pontos finais de serviço de rede virtual ou instância gerida com ponto final privado para hospedar o SSISDB. Ou deve ser a mesma ligada à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

   1. Para **nome sub-rede,** selecione o nome da subnet para a sua rede virtual. Deve ser o mesmo utilizado para o seu servidor de base de dados Azure SQL com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para a sua instância gerida com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer subrede para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

   1. Selecione **validação VNet**. Se a validação for bem sucedida, selecione **Continue**.

   ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na secção **Resumo,** reveja todas as definições para o seu IR Azure-SSIS. Em seguida, selecione **Atualizar**.

1. Inicie o seu IR Azure-SSIS selecionando o botão **Iniciar** na coluna **Ações** para o seu IR Azure-SSIS. Leva cerca de 20 a 30 minutos para iniciar o IR Azure-SSIS que se junta a uma rede virtual.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [a adesão do Azure-SSIS IR a uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)
