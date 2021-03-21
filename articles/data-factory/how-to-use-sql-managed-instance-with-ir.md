---
title: Use a exemplo gerida do Azure SQL com serviços de integração de servidores Azure-SQL (SSIS) na Azure Data Factory
description: Saiba como utilizar o Azure SQL Managed Instance com serviços de integração de servidores SQL (SSIS) na Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 62bd5d2e70d3a66998907305fecee4dcc87cdb23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451732"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Utilize a Azure SQL Managed Instance com serviços de integração de servidores SQL (SSIS) na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Agora pode mover os seus projetos, pacotes e cargas de trabalho dos Seus Serviços de Integração de Servidor SQL (SSIS). Implementar, executar e gerir projetos e pacotes SSIS na Base de Dados Azure SQL ou na SQL Managed Instance com ferramentas familiares como o SQL Server Management Studio (SSMS). Este artigo destaca as seguintes áreas específicas ao utilizar a Azure SQL Managed Instance com o tempo de integração Azure-SSIS (IR):

- [Fornecimento de um Azure-SSIS IR com catálogo SSIS (SSISDB) hospedado pela Azure SQL Managed Instance](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Execute pacotes SSIS por Azure SQL Trabalho de Agente de Instância Gerido](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Limpe os registos SSISDB por Azure SQL Trabalho de Agente de Instância Gerido](#clean-up-ssisdb-logs)
- [Falha do Azure-SSIS IR com Azure SQL Gestd instance](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrar no local cargas de trabalho SSIS para SSIS em ADF com Azure SQL Instância gerida como destino de carga de trabalho de base de dados](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Provision Azure-SSIS IR com SSISDB hospedado por Azure SQL Gerenciada Instância

### <a name="prerequisites"></a>Pré-requisitos

1. [Ativar o Azure Ative Directory (Azure AD) em Azure SQL Managed Instance,](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)ao escolher a autenticação do Azure Ative Directory.

1. Escolha como ligar a SQL Managed Instance, sobre o ponto final privado ou sobre o ponto final público:

    - Sobre o ponto final privado (preferencial)

        1. Escolha a rede virtual para a Azure-SSIS IR para aderir:
            - Dentro da mesma rede virtual que o caso gerido, com **diferentes sub-redes.**
            - Dentro de uma rede virtual diferente da instância gerida, através do espremiamento de rede virtual (que se limita à mesma região devido a restrições de peering Global VNet) ou uma ligação da rede virtual à rede virtual.

            Para obter mais informações sobre a conectividade sql Managed Instance, consulte [Conecte a sua aplicação a Azure SQL Managed Instance](/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Configure a rede virtual.](#configure-virtual-network)

    - Sobre o ponto final público

        Azure SQL Managed Instances pode fornecer conectividade sobre [os pontos finais públicos](../azure-sql/managed-instance/public-endpoint-configure.md). Os requisitos de entrada e saída devem ser cumpridos para permitir o tráfego entre a SQL Managed Instance e a Azure-SSIS IR:

        - quando o Azure-SSIS IR não está dentro de uma rede virtual (preferencial)

            **Requisito de entrada de SQL Managed Instance,** para permitir o tráfego de entrada a partir de Azure-SSIS IR.

            | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino |
            |---|---|---|---|---|
            |TCP|Tag de serviço Azure Cloud|*|VirtualNetwork|3342|

            Para obter mais informações, consulte [Permitir o tráfego de ponto final público no grupo de segurança da rede.](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)

        - quando o Azure-SSIS IR dentro de uma rede virtual

            Há um cenário especial quando o SQL Managed Instance está numa região que o Azure-SSIS IR não suporta, o Azure-SSIS IR está dentro de uma rede virtual sem o olhar da VNet devido à limitação global do peering VNet. Neste cenário, **o Azure-SSIS IR dentro de uma rede virtual** liga a SQL Managed Instance ao ponto final **público.** Utilize abaixo as regras do Grupo de Segurança da Rede (NSG) para permitir o tráfego entre a SQL Managed Instance e a Azure-SSIS IR:

            1. **Requisito de entrada de SQL Managed Instance,** para permitir o tráfego de entrada a partir de Azure-SSIS IR.

                | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|Endereço IP estático do Azure-SSIS IR <br> Para mais informações, consulte [Bring Your Own Public IP for Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Requisito de saída da Azure-SSIS IR,** para permitir o tráfego de saída para a SQL Managed Instance.

                | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Sql Managed Instance endereço IP público](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Configurar uma rede virtual

1. **Permissão do utilizador**. O utilizador que criar o Azure-SSIS IR deve ter a atribuição de [funções](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso Azure Data Factory com uma das opções abaixo:

    - Utilize a função de contribuinte de rede incorporada. Esta função vem com a permissão _Microsoft.Network/ \*_ que tem um âmbito muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a necessária _microsoft.network/virtualNetworks/ \* /join/action_ permission. Se também pretender trazer os seus próprios endereços IP públicos para Azure-SSIS IR ao mesmo tempo que o une a uma rede virtual do Azure Resource Manager, também inclua _microsoft.Network/publicIPAddresses/*/join/action_ permission in the role.

1. **Rede virtual**.

    1. Certifique-se de que o grupo de recursos da rede virtual pode criar e eliminar certos recursos de rede Azure.

        O Azure-SSIS IR precisa de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
        - Um equilibrador de carga Azure, com o nome *\<Guid> -azurebatch-cloudserviceloadanceancer*
        - Um grupo de segurança de rede, com o nome * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público Azure, com o nome -azurebatch-cloudservicepublicip

        Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu Azure-SSIS IR parar. Para evitar que o seu Azure-SSIS IR pare, não reutilize estes recursos de rede nos seus outros recursos.

    1. Certifique-se de que não tem [nenhum bloqueio](../azure-resource-manager/management/lock-resources.md) de recursos no grupo de recursos/subscrição a que pertence a rede virtual. Se configurar um bloqueio apenas de leitura/apagar, iniciar e parar o seu Azure-SSIS IR falhará, ou deixará de responder.

    1. Certifique-se de que não tem uma política Azure que impeça a criação dos seguintes recursos no âmbito do grupo de recursos/subscrição a que a rede virtual pertence:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Permitir o tráfego na regra do Grupo de Segurança da Rede (NSG), para permitir o tráfego entre a SQL Managed Instance e a Azure-SSIS IR, e o tráfego necessário pela Azure-SSIS IR.
        1. **Requisito de entrada de SQL Managed Instance,** para permitir o tráfego de entrada a partir de Azure-SSIS IR.

            | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Se a sua política de ligação ao servidor SQL Database estiver definida como **Proxy** em vez de **Redirecionar**, apenas é necessária a porta 1433.|

        1. **Requisito de saída da Azure-SSIS IR,** para permitir o tráfego de saída para a SQL Managed Instance, e outro tráfego necessário pela Azure-SSIS IR.

        | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Permitir o tráfego de saída para a SQL Managed Instance. Se a política de ligação for definida como **Proxy** em vez de **Redirecionar,** apenas a porta 1433 é necessária. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do seu Azure-SSIS IR na rede virtual utilizam esta porta para aceder aos serviços Azure, como o Azure Storage e o Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós do seu Azure-SSIS IR na rede virtual utilizam esta porta para descarregar uma lista de revogação de certificados da internet. Se bloquear este tráfego, poderá experimentar uma degradação de desempenho quando iniciar o IR e perder capacidade de verificar a lista de revogação de certificados para utilização do certificado. Se pretender reduzir ainda mais o destino a determinados FQDNs, consulte a [Utilização Azure ExpressRoute ou a Rota Definida pelo Utilizador (UDR)](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Esta regra só é necessária quando pretender executar o pacote SSIS armazenado em Ficheiros Azure. |
        |||||||

        1. **Requisito de entrada da Azure-SSIS IR,** para permitir o tráfego necessário pela Azure-SSIS IR.

        | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se aderir ao IR a uma rede virtual do Gestor de Recursos) <br/><br/>10100, 20100, 30100 (se juntar o IR a uma rede virtual clássica)| O serviço Data Factory utiliza estas portas para comunicar com os nós do seu Azure-SSIS IR na rede virtual. <br/><br/> Quer crie ou não um NSG de nível sub-rede, a Data Factory configura sempre um NSG ao nível dos cartões de interface de rede (NICs) ligados às máquinas virtuais que acolhem o Azure-SSIS IR. Apenas o tráfego de entrada a partir de endereços IP da Data Factory nas portas especificadas é permitido por esse NSG de nível NIC. Mesmo que abra estas portas ao tráfego de internet ao nível da sub-rede, o tráfego de endereços IP que não são endereços IP da Data Factory está bloqueado ao nível do NIC. |
        | TCP | Serra CorpNet | * | VirtualNetwork | 3389 | (Opcional) Esta regra só é necessária quando o apoiante da Microsoft pede ao cliente para abrir para uma resolução avançada de problemas, e pode ser fechada logo após a resolução de problemas. A tag de serviço **CorpNetSaw** permite apenas estações de trabalho de acesso segura na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto. E esta etiqueta de serviço não pode ser selecionada a partir do portal e só está disponível através do Azure PowerShell ou do Azure CLI. <br/><br/> No nível NIC NSG, a porta 3389 está aberta por defeito e permitimos que controle a porta 3389 no nível de sub-rede NSG, enquanto a Azure-SSIS IR não permitiu a saída da porta 3389 por defeito na regra de firewall do Windows firewall em cada nó DE IDS para proteção. |
        |||||||

    1. Consulte [a configuração da rede virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) para mais informações:
        - Se trouxer os seus próprios endereços IP públicos para o Azure-SSIS IR
        - Se utilizar o seu próprio servidor Do Sistema de Nome de Domínio (DNS)
        - Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR)
        - Se utilizar O Azure-SSIS IR personalizado

### <a name="provision-azure-ssis-integration-runtime"></a>Provision Azure-SSIS IntegrationTime

1. Selecione SQL Managed Instance ponto final privado ou ponto final público.

    Ao [forer a aplicação Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) em Azure portal/ADF, na página SQL Settings, utilize **o ponto final privado** da SQL Managed Instance ou o ponto **final público** ao criar o catálogo SSIS (SSISDB).

    O nome do anfitrião do ponto final público vem no formato <mi_name>.public.<dns_zone>.database.windows.net e que a porta utilizada para a ligação é 3342.  

    ![O Screenshot mostra a configuração de tempo de integração com o catálogo Create S I S selecionado e o ponto final do servidor de base de dados do Catálogo inserido.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Selecione a autenticação AD AD quando for aplicável.

    ![catálogo-público-ponto](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Para obter mais informações sobre como ativar a autenticação Azure AD, consulte [Enable Azure AD em Azure SQL Managed Instance](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Junte-se ao Azure-SSIS IR na rede virtual quando se aplicar.

    Na página de definição avançada, selecione a Rede Virtual e a sub-rede para se juntar.
    
    Quando estiver dentro da mesma rede virtual que o SQL Managed Instance, escolha uma **sub-rede diferente** da SQL Managed Instance. 

    Para obter mais informações sobre como juntar o Azure-SSIS IR numa rede virtual, consulte [Junte-se a uma rede virtual de integração Azure-SSIS.](join-azure-ssis-integration-runtime-virtual-network.md)

    ![O Screenshot mostra a configuração de tempo de execução de integração Definições avançadas, onde pode selecionar uma rede virtual para o seu tempo de execução para se juntar.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Para obter mais informações sobre como criar um Azure-SSIS IR, consulte [Criar um tempo de integração Azure-SSIS na Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Limpe os registos SSISDB

A política de retenção de registos SSISDB é definida por propriedades abaixo em [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Quando o valor é VERDADEIRO, os detalhes de funcionamento e as mensagens de funcionamento com mais de RETENTION_WINDOW (dias) são eliminados do catálogo. Quando o valor é FALSO, todos os detalhes de operação e mensagens de funcionamento são armazenados no catálogo. Nota: um trabalho do SQL Server executa a limpeza da operação.

- RETENTION_WINDOW

    O número de dias em que os detalhes da operação e as mensagens de operação são armazenados no catálogo. Quando o valor é -1, a janela de retenção é infinita. Nota: Se não for desejada uma limpeza, desate OPERATION_CLEANUP_ENABLED para FALSO.

Para remover os registos SSISDB que estão fora da janela de retenção definida pelo administrador, pode acionar o procedimento armazenado `[internal].[cleanup_server_retention_window_exclusive]` . Opcionalmente, pode agendar a execução de trabalho de agente de instância gerida sql para desencadear o procedimento armazenado.

## <a name="next-steps"></a>Passos seguintes

- [Execute pacotes SSIS por Azure SQL Trabalho de Agente de Instância Gerido](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Criar continuidade de negócios e recuperação de desastres (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrar cargas de trabalho SSIS no local para ssis em ADF](scenario-ssis-migration-overview.md)