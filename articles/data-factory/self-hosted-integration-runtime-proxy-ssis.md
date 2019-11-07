---
title: Configurar o tempo de execução de integração auto-hospedado como um proxy para SSIS no Azure Data Factory
description: Saiba como configurar o Integration Runtime auto-hospedado como um proxy para Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 178628db11b95fbd345e94111ebf15809da3fc35
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684294"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Configurar o IR auto-hospedado como um proxy para Azure-SSIS IR no ADF
Este artigo descreve como executar pacotes do SQL Server Integration Services (SSIS) em Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF) com o IR auto-hospedado configurado como um proxy.  Esse recurso permite que você acesse dados localmente sem [ingressar seu Azure-SSIS ir em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Isso é útil quando sua rede corporativa tem uma política de configuração/restritiva muito complexa para você injetar sua Azure-SSIS IR.

Esse recurso dividirá o pacote que contém uma tarefa de fluxo de dados com a fonte de dados local em duas tarefas de preparo: a primeira em execução no seu IR hospedado primeiro moverá os dados da fonte de dados local para uma área de preparação no armazenamento de BLOBs do Azure, enquanto o segundo em execução na sua Azure-SSIS IR moverá os dados da área de preparo para o destino de dados pretendido.

Esse recurso também fornece outros benefícios/recursos, pois permite que você provisione seu IR de hospedagem interna em regiões que ainda não têm suporte pelo Azure-SSIS IR, permitem o endereço IP estático público do seu IR hospedado no firewall de suas fontes de dados, etc.

## <a name="prepare-self-hosted-ir"></a>Preparar IR auto-hospedado
Para usar esse recurso, primeiro você precisará criar um ADF e provisionar seu Azure-SSIS IR sob ele, se ainda não tiver feito isso, seguindo o artigo [como provisionar um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Em seguida, você precisará provisionar seu IR via hospedagem interna no mesmo ADF em que o Azure-SSIS IR é provisionado seguindo o artigo [como criar um ir para o auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Por fim, você precisará baixar e instalar a versão mais recente do IR auto-hospedado, bem como os drivers adicionais e o tempo de execução, em seu computador local/VM (máquina virtual) do Azure da seguinte maneira:
- Baixe e instale a versão mais recente do IR auto-hospedado [aqui](https://www.microsoft.com/download/details.aspx?id=39717).
- Se você usar conectores OLEDB em seus pacotes, baixe e instale os drivers OLEDB relevantes no mesmo computador em que o IR auto-hospedado é instalado, caso ainda não tenha feito isso.  Se você usar a versão anterior do driver OLEDB para SQL Server (SQLNCLI), poderá baixar a versão de 64 bits [aqui](https://www.microsoft.com/download/details.aspx?id=50402).  Se você usar a versão mais recente do driver OLEDB para SQL Server (MSOLEDBSQL), poderá baixar a versão de 64 bits [aqui](https://www.microsoft.com/download/details.aspx?id=56730).  Se você usar drivers OLEDB para outros sistemas de banco de dados, como PostgreSQL, MySQL, Oracle, etc., você poderá baixar a versão de 64 bits de seus respectivos sites.
- Baixe e instale o tempo C++ de execução do Visual (VC) no mesmo computador em que o ir auto-hospedado está instalado, se você ainda não tiver feito isso.  Você pode baixar a versão de 64 bits [aqui](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo
Crie um serviço vinculado do armazenamento de BLOBs do Azure no mesmo ADF em que o Azure-SSIS IR for provisionado, se você ainda não tiver feito isso, seguindo o artigo [como criar um serviço vinculado do ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Verifique o seguinte:
- O **armazenamento de BLOBs do Azure** está selecionado para **armazenamento de dados**
- **AutoResolveIntegrationRuntime** é selecionado para **conectar via Integration Runtime**
- A **chave de conta**/**URI de SAS**/entidade de **serviço** está selecionada para o método de **autenticação**

![Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Configurar Azure-SSIS IR com o IR auto-hospedado como um proxy
Tendo preparado seu serviço vinculado de IR e armazenamento de BLOBs do Azure autohospedado para preparo, agora você pode configurar seu Azure-SSIS IR novo/existente com o IR hospedado internamente como um proxy no portal/aplicativo do ADF.  Se o Azure-SSIS IR existente estiver em execução, interrompa-o antes de fazer isso e, em seguida, reinicie-o depois.

Na página **Configurações avançadas** , marque a caixa de seleção **Configurar o Integration Runtime autohospedado como um proxy para sua Azure-SSIS Integration Runtime** , selecione o serviço vinculado de ir e o armazenamento de BLOBs auto-hospedados para preparo e especifique um blob nome do contêiner para **caminho de preparo** , se desejar.

![Configurar Azure-SSIS IR com o IR auto-hospedado como um proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Habilitar pacotes SSIS para se conectar por proxy
Usando a extensão mais recente do SSDT com projetos do SSIS para Visual Studio que pode ser baixado [aqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como um instalador autônomo que pode ser baixado [aqui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), você pode encontrar uma nova propriedade **CONNECTBYPROXY** que foi adicionada em OleDb/ Gerenciadores de conexões de arquivos simples.  

Ao criar novos pacotes que contenham tarefas de fluxo de dados com fontes de arquivos OLEDB/simples para acessar bancos de dados/arquivos locais, você pode habilitar essa propriedade definindo-a como **true** no painel de propriedades de gerenciadores de conexões relevantes.

![Habilitar Propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Você também pode habilitar essa propriedade ao executar pacotes existentes sem precisar alterá-los manualmente um por um.  Há duas opções:
- Abrindo, recriando e reimplantando o projeto que contém os pacotes com o SSDT mais recente a ser executado no seu Azure-SSIS IR: a propriedade pode ser habilitada definindo-a como **true** para os gerenciadores de conexões relevantes que aparecem na **conexão Guia gerentes** da janela pop-up executar pacote ao executar pacotes do SSMS.

  ![Habilitar ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A propriedade também pode ser habilitada definindo-a como **true** para os gerenciadores de conexões relevantes que aparecem na guia **gerenciadores de conexões** da [atividade executar pacote SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) ao executar pacotes em pipelines do ADF.
  
  ![Habilitar ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Reimplantando o projeto que contém os pacotes a serem executados em seu IR do SSIS: a propriedade pode ser habilitada fornecendo seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e definindo-a como **true** como uma substituição de propriedade na guia **avançado** da janela pop-up executar pacote ao executar pacotes do SSMS.

  ![Habilitar ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A propriedade também pode ser habilitada fornecendo seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e definindo-a como **true** como uma substituição de propriedade na guia **substituições de propriedade** da [atividade executar pacote SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) ao executar pacotes em pipelines do ADF.
  
  ![Habilitar ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Depurar a primeira e a segunda tarefas de preparo
Em seu IR auto-hospedado, você pode encontrar os logs de tempo de execução na pasta `C:\ProgramData\SSISTelemetry` e os logs de execução das primeiras tarefas de preparo na pasta `C:\ProgramData\SSISTelemetry\ExecutionLog`.  Os logs de execução de segundo tarefas de preparo podem ser encontrados nos caminhos do SSISDB ou do log especificado, dependendo de você armazenar seus pacotes no SSISDB ou no sistema de arquivos/compartilhamentos de arquivos/arquivos do Azure, respectivamente.  As IDs exclusivas das primeiras tarefas de preparo também podem ser encontradas nos logs de execução de segundo tarefas de preparo, por exemplo, 

![ID exclusiva da primeira tarefa de preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Cobrança da primeira e segunda tarefa de preparo
As primeiras tarefas de preparo em execução no IR auto-hospedado serão cobradas separadamente da mesma maneira que as atividades de movimentação de dados em execução no IR via hospedagem interna são cobradas conforme especificado no artigo [preço do pipeline de dados do ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

As segunda tarefas de preparo em execução no seu Azure-SSIS IR não serão cobradas separadamente, mas suas Azure-SSIS IR em execução serão cobradas conforme especificado no artigo sobre [preços de Azure-SSIS ir](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Limitações atuais

- No momento, somente os gerenciadores de conexões de arquivo simples/OleDb e OLEDB/fontes de arquivo simples têm suporte. 
- Somente os serviços vinculados do armazenamento de BLOBs do Azure configurados com **chave de conta**/**URI de SAS**/autenticação de entidade de **serviço** atualmente têm suporte
- Somente o IR de hospedagem interna provisionado no mesmo ADF em que o Azure-SSIS IR é provisionado tem suporte no momento.
- Não há suporte para o uso de parâmetros/variáveis do SSIS nas propriedades de fontes de arquivo OLEDB/Flat e gerenciadores de conexões.

## <a name="next-steps"></a>Passos seguintes
Depois de configurar o IR auto-hospedado como um proxy para seu Azure-SSIS IR, você pode implantar e executar seus pacotes para acessar dados localmente como atividades executar pacote SSIS em pipelines do ADF, consulte [executar pacotes SSIS como atividades executar pacote SSIS em pipelines do ADF ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).