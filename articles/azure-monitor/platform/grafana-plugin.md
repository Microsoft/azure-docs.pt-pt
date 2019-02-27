---
title: Monitorizar serviços do Azure e as aplicações com o Grafana
description: Dados de rota do Azure Monitor e o Application Insights para que pode vê-los numa Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: f592cfdba940b95a9935c6b68f3294233d9fbf15
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888496"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorizar os serviços do Azure no Grafana
Agora pode monitorizar serviços do Azure e aplicações a partir [Grafana](https://grafana.com/) utilizando o [Plug-in de origem de dados do Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plug-in reúne dados de desempenho de aplicativo recolhidos pelo Monitor do Azure, incluindo vários registos e métricas. Em seguida, pode exibir esses dados no seu dashboard da Grafana.

O plug-in está atualmente em pré-visualização.

Utilize os seguintes passos para configurar um servidor o Grafana e criar dashboards para métricas e registos do Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Configurar um servidor o Grafana

### <a name="set-up-grafana-locally"></a>Configurar o Grafana localmente
Para configurar um servidor o Grafana local, [transfira e instale o Grafana no seu ambiente local](https://grafana.com/grafana/download). Para utilizar a integração do Azure Monitor o plug-in, instale o Grafana versão 5.3 ou superior.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configurar o Grafana no Azure através do Azure Marketplace
1. Ir para o Azure Marketplace e escolha o Grafana por laboratórios da Grafana.

2. Preencha os nomes e os detalhes. Crie um novo grupo de recursos. Mantenha um registo de valores que escolher para o nome de utilizador VM, a palavra-passe VM e a palavra-passe de administrador de servidor o Grafana.  

3. Escolha o tamanho da VM e uma conta de armazenamento.

4. Configure as definições de configuração de rede.

5. Ver o resumo e selecione **criar** depois de aceitar os termos de utilização.

6. Depois de concluída a implementação, selecione **vá para o grupo de recursos**. É apresentada uma lista de recursos criados recentemente.

    ![Objetos de grupo de recursos o Grafana](media/grafana-plugin/grafana1.png)

    Se selecionar o grupo de segurança de rede (*grafana-nsg* neste caso), pode ver que a porta 3000 é utilizada para aceder ao servidor da Grafana.

7. Obtenha o endereço IP público do seu servidor Grafana - voltar à lista de recursos e selecione **endereço IP público**.

## <a name="log-in-to-grafana"></a>Inicie sessão para o Grafana

1. Com o endereço IP do seu servidor, abra a página de início de sessão no *http://\<endereço IP\>: 3000* ou o  *\<DNSName >\:3000* no seu browser. Embora 3000 é a porta predefinida, tenha em atenção de que poderá ter selecionado uma porta diferente durante a configuração. Deverá ver uma página de início de sessão do servidor o Grafana que criou.

    ![Ecrã de início de sessão o Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Inicie sessão com o nome de utilizador *administrador* e a senha de administrador de servidor Grafana que criou anteriormente. Se estiver a utilizar uma instalação local, a palavra-passe predefinida seria *administrador*, e deve ser solicitada alterá-la no primeiro início de sessão.

## <a name="configure-data-source-plugin"></a>Configurar o plug-in de origem de dados

Depois de iniciado sessão com êxito, deverá ver que o plug-in da origem de dados do Azure Monitor já está incluído.

![O Grafana inclui o plug-in do Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selecione **Adicionar origem de dados** para adicionar e configurar a origem de dados do Azure Monitor.

2. Escolha um nome para a origem de dados e selecione **do Azure Monitor** como o tipo na lista pendente.

3. Criar um principal de serviço - o Grafana utiliza um principal de serviço do Azure Active Directory para ligar a APIs do Azure Monitor e recolher dados. Tem de criar ou utilizar um principal de serviço existente, para gerir o acesso aos recursos do Azure.
    * Ver [estas instruções](../../azure-resource-manager/resource-group-create-service-principal-portal.md) para criar um principal de serviço. Copie e guarde o seu inquilino ID (ID de diretório), o ID de cliente (ID de aplicação) e o segredo do cliente (valor da chave de aplicação).
    * Ver [atribua a aplicação à função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) para atribuir a função de leitor para a aplicação do Azure Active Directory da subscrição, grupo de recursos ou recurso que pretende monitorizar. 
    A API do Log Analytics requer o [função de leitor do Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), que inclui as permissões da função de leitor e adiciona a ela.

4. Forneça os detalhes de ligação para as APIs que pretende utilizar. Pode ligar-se a todos ou alguns deles. 
    * Se se conectar para métricas e registos no Azure Monitor, pode reutilizar as mesmas credenciais, selecionando **mesmo detalhes como a API do Azure Monitor**.
    * Ao configurar o plug-in, pode indicar que Cloud do Azure que pretende que o plug-in para o monitor (público, do Azure US Government, Azure Alemanha ou Azure China).
    * Se utilizar o Application Insights, também pode incluir o API do Application Insights e o ID de aplicação para recolher métricas do Application Insights com base. Para obter mais informações, consulte [obter a sua chave de API e o ID da aplicação](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Alguns campos de origem de dados são nomeados de forma diferente do que as definições do Azure correlacionadas:
        >     * ID do inquilino é o ID de diretório do Azure
        >     * ID de cliente é o ID de aplicação do Active Directory do Azure
        >     * Segredo do cliente é o valor da chave de aplicação do Active Directory do Azure

5. Se utilizar o Application Insights, também pode incluir o API do Application Insights e o ID de aplicação para recolher métricas do Application Insights com base. Para obter mais informações, consulte [obter a sua chave de API e o ID da aplicação](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Selecione **guardar**, e o Grafana testará as credenciais para cada API. Deverá ver uma mensagem semelhante ao seguinte.  
    ![Config aprovada da origem de dados do Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Criar um dashboard da Grafana

1. Vá para o Grafana Home page e selecione **novo Dashboard**.

2. No novo dashboard, selecione o **Graph**. Pode experimentar outras opções de criação de gráficos, mas este artigo usa *Graph* como exemplo.

3. Um gráfico em branco aparece no seu dashboard. Clique com o título do painel e selecione **editar** para introduzir os detalhes dos dados que quer representar neste gráfico de gráfico.
    ![O Grafana novo gráfico](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecione a origem de dados do Azure Monitor que configurou.
    * Azure Monitor a recolher métricas – selecione **do Azure Monitor** na lista pendente de serviço. Uma lista de seletores é exibida, onde pode selecionar os recursos e a métrica a monitorizar neste gráfico. Para recolher métricas de uma VM, utilize o espaço de nomes **Microsoft.Compute/VirtualMachines**. Assim que tiver selecionado as VMs e as métricas, pode começar a ver seus dados no dashboard.
    ![O Grafana gráfico config para o Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
    * Azure Monitor a recolher registos de dados – selecione **do Azure Log Analytics** na lista pendente de serviço. Selecione a área de trabalho que gostaria de consultar e definir o texto da consulta. Pode copiar, aqui, qualquer consulta de registo já tem ou crie um novo. À medida que escreve na sua consulta, o IntelliSense aparece e opções de preenchimento automático de sugerir. Selecione o tipo de visualização **série de tempo** **tabela**, e execute a consulta.
    
    > [!NOTE]
    >
    > A consulta predefinida fornecida com o plug-in utiliza duas macros: "__interval $__timeFilter() e $. 
    > Essas macros permitem o Grafana calcular dinamicamente o intervalo de tempo e o intervalo de tempo, quando aplica zoom parte de um gráfico. Pode remover essas macros e utilizar um filtro de hora padrão, como *TimeGenerated > há (1H)*, mas isso significa que o gráfico não suporte o zoom na funcionalidade.
    
    ![O Grafana gráfico config para o Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Segue-se um dashboard simple com dois gráficos. Um no lado esquerdo mostra a percentagem de CPU de duas VMs. O gráfico à direita mostra as transações numa conta de armazenamento do Azure, dividida por tipo de transação de API.
    ![O Grafana dois gráficos exemplo](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorizar as métricas personalizadas no mesmo servidor o Grafana

Também pode instalar Telegraf e InfluxDB para recolher e desenhar métricas personalizadas e baseado em agente mesma instância de Grafana. Existem muitos plugins de origem de dados que pode utilizar para reunir estas métricas num dashboard.

Também pode reutilizar esta configuração para incluir as métricas do seu servidor Prometheus. Utilize o plug-in da origem de dados do Prometheus na Galeria de plug-in da Grafana.

Seguem-se artigos de boa referência sobre como utilizar Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorizar as métricas do sistema com a pilha de escala no Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorizar as métricas de recurso do Docker com o Grafana, InfluxDB e Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Uma solução de monitorização para anfitriões do Docker, contentores e serviços em contentores](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Aqui está uma imagem de um dashboard da Grafana completa que tem as métricas do Azure Monitor e o Application Insights.
![Métricas de exemplo o Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Funcionalidades avançadas do Grafana

### <a name="variables"></a>Variáveis
Alguns valores de consulta podem ser selecionados por meio de menus suspensos da interface do Usuário e atualizados na consulta. Por exemplo, considere a seguinte consulta:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Pode configurar uma variável que listará todos disponíveis **solução** valores e, em seguida, atualize a consulta para utilizá-lo.
Para criar um clique de variável, novo botão de definições do dashboard na área superior direita, selecione **variáveis**e, em seguida **New**.
Na página de variável, defina a origem de dados e consulta de executar para obter a lista de valores.
![Variável de configurar o Grafana](./media/grafana-plugin/grafana-configure-variable-dark.png)

Depois de criado, ajustar a consulta para utilizar o valor ou valores selecionado e seus gráficos irão responder de acordo:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Variáveis de utilizar o Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Criar listas de reprodução do dashboard

Uma das várias funcionalidades da Grafana é a lista de reprodução do dashboard. Pode criar vários dashboards e adicioná-los para configurar um intervalo para cada dashboard mostrar uma lista de reprodução. Selecione **reproduzir** para ver os dashboards percorrer. Poderá exibi-los num monitor de parede grande para fornecer um quadro de estado para o seu grupo.

![Exemplo de lista de reprodução o Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se configurou um ambiente de Grafana no Azure, é cobrado quando as VMs estão em execução quer esteja a utilizá-los ou não. Para evitar incorrer em custos adicionais, limpe o grupo de recursos criado neste artigo.

1. No menu esquerdo no portal do Azure, clique em **grupos de recursos** e, em seguida, clique em **Grafana**.
2. Na página do grupo de recursos, clique em **elimine**, tipo **Grafana** na caixa de texto e, em seguida, clique **eliminar**.

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral das métricas de Monitor do Azure](../../azure-monitor/platform/data-collection.md)

