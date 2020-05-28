---
title: Utilize a base de dados Azure SQL Managed Instance com serviços de integração de servidores Azure-SQL (SSIS) na Fábrica de Dados Azure
description: Saiba como utilizar a Instância Gerida pela Base de Dados Azure SQL com os Serviços de Integração de Servidores SQL (SSIS) na Fábrica de Dados Azure.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: cd07bf86852d608a6d872f4c6b973b0a81b2a1c3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015289"
---
# <a name="use-azure-sql-database-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Utilize a instância gerida pela base de dados Azure SQL com serviços de integração de servidores SQL (SSIS) na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Agora pode mover os seus projetos, pacotes e cargas de trabalho dos seus Serviços de Integração de Servidores SQL (SSIS) para a nuvem Azure. Implementar, executar e gerir projetos e pacotes SSIS na Base de Dados Azure SQL ou Na Base de Dados SQL Managed Instance com ferramentas familiares como O Estúdio de Gestão de Servidores SQL (SSMS). Este artigo destaca as seguintes áreas específicas ao utilizar a Instância gerida pela Base de Dados Azure SQL com o tempo de execução de integração Azure-SSIS (IR):

- [Provisão de um IR Azure-SSIS com catálogo SSIS (SSISDB) hospedado pela Azure SQL Database gerido](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Executar pacotes SSIS por Azure SQL Gerido Agente de Instância](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Limpar os registos SSISDB por Azure SQL Managed Instance Agent](#clean-up-ssisdb-logs)
- [Falha no IR azure-SSIS com instância gerida pela Base de Dados Azure SQL](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-database-managed-instance)
- [Migrar no local cargas de trabalho Do SSIS para o SSIS em ADF com base de dados Azure SQL gerida como destino de carga de trabalho de base de dados](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Provision Azure-SSIS IR com SSISDB hospedado pela Azure SQL Managed Instance

### <a name="prerequisites"></a>Pré-requisitos

1. Ativar o [Diretório Ativo Azure (Azure AD) na Base de Dados Azure SQL gerida,](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)ao escolher a autenticação de Diretório Ativo Azure.

1. Escolha como ligar a instância gerida pela SQL, sobre o ponto final privado ou sobre o ponto final público:

    - Sobre o ponto final privado (preferido)

        1. Escolha a rede virtual para o IR Azure-SSIS para aderir:
            - Dentro da mesma rede virtual que a SQL geria a instância, com **subnet diferente**.
            - Dentro de uma rede virtual diferente da instância gerida pela SQL, através de um peering de rede virtual (que está limitado à mesma região devido a restrições de peering Global VNet) ou uma ligação da rede virtual à rede virtual.

            Para obter mais informações sobre a conectividade de instância gerida pela SQL, consulte Connect a sua aplicação para a instância gerida pela Base de [Dados Azure SQL](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Configurar a rede virtual.](#configure-virtual-network)

    - Sobre o ponto final público

        As instâncias geridas pela Base de Dados Azure SQL podem fornecer conectividade em relação aos [pontos finais públicos](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure). Os requisitos de entrada e saída devem satisfazer-se para permitir o tráfego entre a instância gerida pela SQL e o IR Azure-SSIS:

        - quando o Ir Azure-SSIS não está dentro de uma rede virtual (preferida)

            Requisito de entrada de instância gerida pela **SQL,** para permitir o tráfego de entrada a partir do Ir Azure-SSIS.

            | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino |
            |---|---|---|---|---|
            |TCP|Etiqueta de serviço Azure Cloud|*|VirtualNetwork|3342|

            Para mais informações, consulte Permitir o [tráfego de pontos finais públicos no grupo de segurança da rede](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure#allow-public-endpoint-traffic-on-the-network-security-group).

        - quando o Azure-SSIS IR dentro de uma rede virtual

            Há um cenário especial quando a instância gerida pela SQL está numa região que o Azure-SSIS IR não suporta, o Azure-SSIS IR está dentro de uma rede virtual sem que a VNet olhe devido à limitação de peering Global VNet. Neste cenário, **o Azure-SSIS IR dentro de uma rede virtual** liga a instância gerida pela SQL sobre o ponto final **público**. Utilize abaixo as regras do Grupo de Segurança da Rede (NSG) para permitir o tráfego entre a instância gerida pela SQL e o IR Azure-SSIS:

            1. Requisito de entrada de instância gerida pela **SQL,** para permitir o tráfego de entrada a partir do Ir Azure-SSIS.

                | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|Endereço IP estático do IR Azure-SSIS <br> Para mais detalhes, consulte [Bring Your Own Public IP for Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Requisito de saída do Ir Azure-SSIS,** para permitir o tráfego de saída para a instância gerida pela SQL.

                | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Endereço IP do ponto final gerido pela SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-find-management-endpoint-ip-address)|3342|

### <a name="configure-virtual-network"></a>Configurar uma rede virtual

1. **Autorização do utilizador**. O utilizador que criar o IR Azure-SSIS deve ter a atribuição de [funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso Azure Data Factory com uma das opções abaixo:

    - Utilize a função de Colaborador de Rede incorporada. Este papel vem com a permissão _Microsoft.Network/, \* _ que tem um âmbito muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a necessária _Microsoft.Network/virtualNetworks/ \* /join/action_ permission. Se também pretende trazer os seus próprios endereços IP públicos para o Ir Azure-SSIS ao mesmo tempo que o junta a uma rede virtual do Gestor de Recursos Do Azure, também inclui a _Microsoft.Network/publicIPAddresss/*/join/action_ permission no papel.

1. **Rede virtual.**

    1. Certifique-se de que o grupo de recursos da rede virtual pode criar e eliminar certos recursos da rede Azure.

        O IR Azure-SSIS necessita de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
        - Um equilibrador de carga Azure, com o nome * \<Guid> -azurebatch-cloudserviceloadbalancer*
        - Um grupo de segurança de rede, com o nome * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público azure, com o nome -azurebatch-cloudservicepublicip

        Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu IR Azure-SSIS parar. Para evitar que o seu IR Azure-SSIS pare, não reutilize estes recursos de rede nos seus outros recursos.

    1. Certifique-se de que não tem [nenhum bloqueio](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) de recursos no grupo/subscrição de recursos a que pertence a rede virtual. Se configurar um bloqueio de leitura/exclusão, iniciar e parar o seu IR Azure-SSIS falhará, ou deixará de responder.

    1. Certifique-se de que não tem uma política Azure que impeça a criação dos seguintes recursos ao abrigo do grupo de recursos/subscrição a que pertence a rede virtual:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Permitir a regra do Grupo de Segurança da Rede (NSG), permitir o tráfego entre a instância gerida pela SQL e o IR Azure-SSIS, e o tráfego necessário pelo IR Azure-SSIS.
        1. Requisito de entrada de instância gerida pela **SQL,** para permitir o tráfego de entrada a partir do Ir Azure-SSIS.

            | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Se a sua política de ligação ao servidor sQL Database estiver definida para **Proxy** em vez de **Redirecionar,** apenas é necessária a porta 1433.|

        1. **Requisito de saída do IR Azure-SSIS,** para permitir o tráfego de saída para a instância gerida pela SQL, e outros tráfegos necessários pelo IR Azure-SSIS.

        | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Permitir o tráfego de saída para a instância gerida pela SQL. Se a política de ligação for definida para **Proxy** em vez de **Redirecionar,** apenas é necessária a porta 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do seu Ir Azure-SSIS na rede virtual utilizam este porto para aceder aos serviços Azure, como o Azure Storage e o Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós do seu Ir Azure-SSIS na rede virtual utilizam esta porta para descarregar uma lista de revogação de certificados a partir da internet. Se bloquear este tráfego, poderá sentir uma desvalorização de desempenho quando iniciar o IR e perder capacidade para verificar a lista de revogação do certificado para uso do certificado. Se pretender reduzir ainda mais o destino a certas FQDNs, consulte o [Use Azure ExpressRoute ou a User Defined Route (UDR)](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#route).|
        | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Esta regra só é necessária quando pretender executar o pacote SSIS armazenado em Ficheiros Azure. |
        |||||||

        1. **Requisito de entrada do Ir Azure-SSIS,** para permitir o tráfego necessário pelo IR Azure-SSIS.

        | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | Gestão de BatchNode | * | VirtualNetwork | 29876, 29877 (se aderir ao IR a uma rede virtual do Gestor de Recursos) <br/><br/>10100, 20100, 30100 (se aderir ao IR a uma rede virtual clássica)| O serviço Data Factory utiliza estas portas para comunicar com os nódosos do seu Ir Azure-SSIS na rede virtual. <br/><br/> Quer crie ou não um NSG de nível de sub-rede, a Data Factory configura sempre um NSG ao nível dos cartões de interface de rede (NICs) ligados às máquinas virtuais que acolhem o IR Azure-SSIS. Apenas o tráfego de entrada a partir de endereços IP da Fábrica de Dados nas portas especificadas é permitido por esse NSG de nível NIC. Mesmo que abra estas portas ao tráfego de internet ao nível da subnet, o tráfego a partir de endereços IP que não são endereços IP da Fábrica de Dados está bloqueado ao nível nic. |
        | TCP | Corpnetsaw | * | VirtualNetwork | 3389 | (Opcional) Esta regra só é necessária quando o apoiante da Microsoft pede ao cliente para abrir para resolução avançada de problemas, e pode ser fechado logo após resolução de problemas. A etiqueta de serviço **CorpNetSaw** permite apenas postos de trabalho de acesso seguros na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto. E esta etiqueta de serviço não pode ser selecionada a partir do portal e só está disponível via Azure PowerShell ou Azure CLI. <br/><br/> Ao nível NIC NSG, a porta 3389 está aberta por padrão e permitimos controlar a porta 3389 ao nível da sub-rede NSG, enquanto o Azure-SSIS IR desautorizado é a saída por defeito na regra da firewall das janelas em cada nó de IR para proteção. |
        |||||||

    1. Consulte a [configuração da rede virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) para obter mais informações:
        - Se trouxer os seus próprios endereços IP públicos para o IR Azure-SSIS
        - Se utilizar o seu próprio servidor do Sistema de Nomede Domínio (DNS)
        - Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR)
        - Se utilizar o Azure-SSIS IR personalizado

### <a name="provision-azure-ssis-integration-runtime"></a>Prazo de execução de integração Azure-SSIS

1. Selecione SQL gerido ponto final ou ponto final público.

    Ao fornecer o [acesso ao Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) na aplicação Portal/ADF do Portal Azure, na página SQL Settings, utilize o ponto **final** gerido pela SQL ou o ponto **final público** ao criar o catálogo SSIS (SSISDB).

    O nome do anfitrião do ponto final do público vem no formato <mi_name>.public.<dns_zone>.database.windows.net e que a porta utilizada para a ligação é 3342.  

    ![catálogo-ponto final público](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Selecione autenticação Azure AD quando se aplicar.

    ![catálogo-ponto final público](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Para obter mais informações sobre como ativar a autenticação da AD Azure, consulte [enable Azure AD na base de dados Azure SQL gerida](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Junte-se ao Ir Azure-SSIS na rede virtual quando se aplicar.

    Na página de definição avançada, selecione a Rede Virtual e a sub-rede para aderir.
    
    Quando dentro da mesma rede virtual que a SQL geria a instância, escolha uma **sub-rede diferente** da instância gerida pela SQL. 

    Para obter mais informações sobre como aderir ao Azure-SSIS IR numa rede virtual, consulte [Join a Azure-SSIS integration time to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md).

    ![rede de adesão virtual](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Para obter mais informações sobre como criar um IR Azure-SSIS, consulte [Create a Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Limpar os registos SSISDB

A política de retenção de registos SSISDB é definida por propriedades abaixo no [catálogo.catalog_properties:](https://docs.microsoft.com/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15)

- OPERATION_CLEANUP_ENABLED

    Quando o valor for VERDADEIRO, os detalhes da operação e as mensagens de funcionamento com mais de RETENTION_WINDOW (dias) são eliminados do catálogo. Quando o valor for FALSO, todos os detalhes da operação e mensagens de operação são armazenados no catálogo. Nota: um trabalho do SQL Server realiza a limpeza da operação.

- RETENTION_WINDOW

    O número de dias em que os detalhes da operação e as mensagens de operação estão armazenados no catálogo. Quando o valor é -1, a janela de retenção é infinita. Nota: Se não for desejada limpeza, coloque OPERATION_CLEANUP_ENABLED em FALSO.

Para remover os registos SSISDB que estão fora da janela de retenção definida pelo administrador, pode acionar o procedimento armazenado `[internal].[cleanup_server_retention_window_exclusive]` . Opcionalmente, pode agendar a execução de trabalho de agente de instância gerida pela SQL para desencadear o procedimento armazenado.

## <a name="next-steps"></a>Próximos passos

- [Executar pacotes SSIS por Azure SQL Gerido Agente de Instância](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Criar continuidade de negócios e recuperação de desastres (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrar no local cargas de trabalho do SSIS para o SSIS em ADF](scenario-ssis-migration-overview.md)
