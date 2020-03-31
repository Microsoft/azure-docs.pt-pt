---
title: Visualizar dados do Azure Data Explorer usando kibana
description: Neste artigo, aprende-se a configurar o Azure Data Explorer como fonte de dados para a Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065601"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualizar dados do Azure Data Explorer em Kibana com o conector de código aberto K2Bridge

O K2Bridge (Ponte Kibana-Kusto) permite-lhe utilizar o Azure Data Explorer como fonte de dados e visualizar esses dados em Kibana. K2Bridge é uma aplicação contentorizada [de código aberto](https://github.com/microsoft/K2Bridge) que funciona como um proxy entre uma instância de Kibana e um cluster Azure Data Explorer. Este artigo descreve como usar o K2Bridge para criar essa ligação.

K2Bridge traduz consultas de Kibana para A Linguagem Consulta de Kusto (KQL) e envia os resultados do Azure Data Explorer de volta para Kibana. 

   ![gráfico](media/k2bridge/k2bridge-chart.png)

K2Bridge suporta o separador Discover da Kibana, onde pode:
* Pesquisar e explorar os dados
* Filtrar os resultados
* Adicionar ou remover campos na grelha de resultados
* Ver conteúdo de gravação
* Economize e partilhe pesquisas

A imagem abaixo mostra uma instância de Kibana ligada ao Azure Data Explorer por K2Bridge. A experiência do utilizador em Kibana é inalterada.

   [![Página de Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

Antes de visualizar os dados do Azure Data Explorer em Kibana, tenha o seguinte pronto:

* [Helm V3,](https://github.com/helm/helm#install)o gestor de pacotes Kubernetes
* O cluster Azure Kubernetes Service (AKS) ou qualquer outro cluster Kubernetes (versão 1.14 para a versão 1.16 foram testados e verificados). Se precisar de um cluster AKS, consulte Implementar um cluster AKS [utilizando o Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) ou [utilizando o portal Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Um [cluster Azure Data Explorer,](create-cluster-database-portal.md)incluindo:
    * Url do cluster Azure Data Explorer 
    * O nome da base de dados
    
* Um principal de serviço da Azure AD autorizado a visualizar dados no Azure Data Explorer, incluindo:
    * O ID do Cliente 
    * O Segredo do Cliente

    Recomenda-se um diretor de serviço com permissão 'Viewer'. É desencorajado usar permissões mais altas.

    * [Detete as permissões de vista do cluster para o diretor](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)de serviço da AD Azure .

    Para obter mais informações sobre o diretor de serviço da Azure AD, consulte [Create a Azure AD service principal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Executar K2Bridge no Serviço Azure Kubernetes (AKS)

Por predefinição, o gráfico helm da K2Bridges refere uma imagem publicamente disponível localizada no Registo de Contentores da Microsoft (MCR). O MCR não requer credenciais e funciona fora da caixa.

1. Descarregue as tabelas de Helm necessárias.

1. Adicione a dependência de pesquisa elástica ao Leme. 
    A razão para a dependência de pesquisa elástica é que o K2Bridge usa uma pequena instância de pesquisa elástica interna para atender pedidos relacionados com metadados (tais como padrões de índice e consultas guardadas). Nenhum dado de negócio é guardado neste caso interno, e pode ser considerado como um detalhe de implementação. 

    1. Para adicionar a dependência de pesquisa elástica ao Leme:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Para obter o gráfico K2Bridge sob o diretório de gráficos do repositório GitHub:
        1. Clone o repositório do [GitHub.](https://github.com/microsoft/K2Bridge)
        1. Vá ao diretório de repositório de raiz k2Bridges.
        1. Execução:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Implementar K2Bridge:

    1. Detete as variáveis com os valores corretos para o seu ambiente:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Opcional) Ativar a telemetria de insights de aplicação Azure. 
        Se esta for a primeira vez que está a usar insights de aplicação do Azure, deve [primeiro criar um recurso Desinformação](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)de Aplicação . Terá de [copiar a chave](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) de instrumentação para uma variável: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Instale o gráfico K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        Na [Configuração](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) pode encontrar o conjunto completo de opções de configuração.

    1. A saída de comando sugere que o próximo comando helm a correr para implantar Kibana. Opcionalmente, executar:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Utilize o encaminhamento do porto para aceder a Kibana no local anfitrião: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Ligue-se a Kibana http://127.0.0.1:5601navegando até .

    1. Expor Kibana aos utilizadores finais. Existem vários métodos para o fazer. O método que usa depende em grande parte do seu caso de utilização.

        Por exemplo:

        Exponha o serviço como um serviço LoadBalancer. Para isso, adicione `--set service.type=LoadBalancer` o parâmetro ao comando de instalação de instalação k2Bridge Helm[(acima).](#install-k2bridge-chart)        
    
        Em seguida, execute:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        A saída deve parecer: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Em seguida, pode utilizar o EXTERNO-IP gerado que aparece, e usá-lo `<EXTERNAL-IP>:5601`para aceder a Kibana abrindo um navegador para .

1. Configure padrões de índice para aceder aos seus dados:  
Num novo caso de Kibana:
     1. Abra kibana.
     1. Navegue para a Gestão.
     1. Selecione **Padrões de Índice**. 
     1. Criar um padrão de índice.
O nome do índice deve corresponder exatamente ao nome da tabela ou nome da função, sem um asterisco. Pode copiar a linha relevante da lista.

> [!Note]
> Para funcionar com outros fornecedores kubernetes, altere `values.yaml` o armazenamento ElasticsearchClassName para se adaptar ao sugerido pelo fornecedor.

## <a name="visualize-data"></a>Visualizar os dados

Quando o Azure Data Explorer estiver configurado como uma fonte de dados para a Kibana, pode utilizar a Kibana para explorar os dados. 

1. Em Kibana, no menu esquerdo, selecione o **separador Descobrir.**

1. A partir da lista de abandono à esquerda, selecione um padrão de índice (neste caso, uma tabela Azure Data Explorer), que define a fonte de dados que pretende explorar.
    
   ![Selecione um padrão de índice](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Se os seus dados dispõem de um campo de filtro de tempo, pode especificar o intervalo de tempo. Na parte superior direita da página, delineie um filtro de tempo. Por padrão, o Discover mostra dados dos últimos 15 minutos.

   ![Filtro de tempo](media/k2bridge/k2bridge-time-filter.png)
    
1. A tabela de resultados mostra os primeiros 500 registos. Pode expandir um documento para examinar os seus dados de campo em formatoJ ou tabela.

   ![Expandir um recorde](media/k2bridge/k2bridge-expand-record.png)

1. Por predefinição, a tabela de resultados inclui colunas para o documento _source e o campo de tempo (se existir). Pode escolher colunas específicas a adicionar à tabela de resultados selecionando **adicionar** ao lado do nome de campo na barra lateral esquerda.

   ![Colunas específicas](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na barra de consulta, pode pesquisar os dados por:
    * Inserindo um termo de pesquisa
    * Usando a sintaxe lucene consulta. 
    Por exemplo:
        * Procure "erro" para encontrar todos os registos que contenham este valor. 
        * Procurar o "status: 200", para obter todos os registos com um valor de estado de 200. 
    * Utilização de operadores lógicos (E, OU, NÃO)
    * Usando wildcards (asterisco " \* ou ponto de interrogação "?") Por exemplo:
        * A consulta `"destination_city: L*"` corresponderá aos registos em que o valor da cidade de destino começa com "l" (K2Bridge não é sensível a casos).

    ![Executar consulta](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > Em [Pesquisa,](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)você pode encontrar mais regras de pesquisa e lógica.

1. Para filtrar os resultados da sua pesquisa, utilize a lista de **campo** na barra lateral direita da página. 
    A lista de campo é onde pode ver:
    * Os cinco melhores valores para o campo
    * O número de registos que contêm o campo
    * A percentagem de registos que contêm cada valor. 
    
    >[!Tip]
    > Utilize o ícone (+) de ampliação para encontrar todos os registos que tenham um valor específico.
    
    ![Lista de campos](media/k2bridge/k2bridge-field-list.png)
   
    Também pode filtrar os resultados utilizando o ícone (+) de amplificador na vista de formato da tabela de resultados de cada registo na tabela de resultados.
    
     ![Lista de tabelas](media/k2bridge/k2bridge-table-list.png)
    
1. Selecione para **Guardar** ou **Partilhar** a sua pesquisa.

     ![Salvar a pesquisa](media/k2bridge/k2bridge-save-search.png)
