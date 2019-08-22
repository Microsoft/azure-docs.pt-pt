---
title: Monitorar serviços e aplicativos do Azure usando o Grafana
description: Azure Monitor de rota e dados de Application Insights para que você possa exibi-los em Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: b9a9d0a16a31d06d0d4edc1b6f0617a5771b179e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872831"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorar seus serviços do Azure no Grafana
Agora você pode monitorar os serviços e aplicativos do Azure do [Grafana](https://grafana.com/) usando o [plug-in Azure monitor Data Source](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plug-in coleta dados de desempenho do aplicativo coletados por Azure Monitor, incluindo vários logs e métricas. Você pode exibir esses dados em seu painel do Grafana.

Use as etapas a seguir para configurar um servidor Grafana e criar painéis para métricas e logs de Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Configurar um servidor Grafana

### <a name="set-up-grafana-locally"></a>Configurar o Grafana localmente
Para configurar um servidor Grafana local, [Baixe e instale o Grafana em seu ambiente local](https://grafana.com/grafana/download). Para usar a integração de Azure Monitor do plug-in, instale o Grafana versão 5,3 ou superior.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configurar o Grafana no Azure por meio do Azure Marketplace
1. Acesse o Azure Marketplace e escolha Grafana by Grafana Labs.

2. Preencha os nomes e os detalhes. Crie um novo grupo de recursos. Acompanhe os valores escolhidos para o nome de usuário da VM, a senha da VM e a senha de administrador do servidor Grafana.  

3. Escolha o tamanho da VM e uma conta de armazenamento.

4. Defina as definições de configuração de rede.

5. Exiba o resumo e selecione **criar** depois de aceitar os termos de uso.

6. Após a conclusão da implantação, selecione **ir para o grupo de recursos**. Você verá uma lista de recursos recém-criados.

    ![Objetos do grupo de recursos Grafana](media/grafana-plugin/grafana1.png)

    Se você selecionar o grupo de segurança de rede (*grafana-NSG* , nesse caso), poderá ver que a porta 3000 é usada para acessar o grafana Server.

7. Obtenha o endereço IP público do seu servidor Grafana-volte para a lista de recursos e selecione **endereço IP público**.

## <a name="sign-in-to-grafana"></a>Entrar no Grafana

1. Usando o endereço IP do seu servidor, abra a página de logon *em\<http://endereço\>IP: 3000* ou  *\<DnsName >\:3000* em seu navegador. Embora 3000 seja a porta padrão, observe que você pode ter selecionado uma porta diferente durante a instalação. Você deve ver uma página de logon para o servidor Grafana que criou.

    ![Tela de logon do Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Entre com o *administrador* de nome de usuário e a senha de administrador do servidor Grafana que você criou anteriormente. Se você estiver usando uma instalação local, a senha padrão será *admin*, e você deverá ser solicitado a alterá-la em seu primeiro logon.

## <a name="configure-data-source-plugin"></a>Configurar plug-in de fonte de dados

Uma vez conectado com êxito, você verá que o plug-in de fonte de dados Azure Monitor já está incluído.

![Grafana inclui plug-in Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selecione **Adicionar fonte de dados** para adicionar e configurar a fonte de dados de Azure monitor.

2. Escolha um nome para a fonte de dados e selecione **Azure monitor** como o tipo no menu suspenso.

3. Criar uma entidade de serviço-Grafana usa uma entidade de serviço Azure Active Directory para se conectar a Azure Monitor APIs e coletar dados. Você deve criar ou usar uma entidade de serviço existente para gerenciar o acesso aos recursos do Azure.
    * Consulte [estas instruções](../../azure-resource-manager/resource-group-create-service-principal-portal.md) para criar uma entidade de serviço. Copie e salve sua ID de locatário (ID do diretório), ID do cliente (ID do aplicativo) e segredo do cliente (valor da chave do aplicativo).
    * Consulte [atribuir aplicativo à função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) para atribuir a função leitor ao aplicativo Azure Active Directory na assinatura, grupo de recursos ou recurso que você deseja monitorar. 
    A API de Log Analytics requer a [função leitor de log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), que inclui as permissões da função leitor e adiciona a ela.

4. Forneça os detalhes de conexão para as APIs que você gostaria de usar. Você pode se conectar a todos ou a alguns deles. 
    * Se você se conectar a métricas e logs em Azure Monitor, poderá reutilizar as mesmas credenciais selecionando os **mesmos detalhes que Azure monitor API**.
    * Ao configurar o plug-in, você pode indicar qual nuvem do Azure você deseja que o plug-in monitore (público, governo dos EUA do Azure, Azure Alemanha ou Azure China).
    * Se você usar Application Insights, também poderá incluir sua API de Application Insights e a ID do aplicativo para coletar métricas baseadas em Application Insights. Para obter mais informações, consulte [obtendo sua chave de API e a ID do aplicativo](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Alguns campos de fonte de dados são nomeados de forma diferente das configurações correlacionadas do Azure:
        > * A ID do locatário é a ID do diretório do Azure
        > * A ID do cliente é a ID do aplicativo Azure Active Directory
        > * O segredo do cliente é o valor da chave do aplicativo Azure Active Directory

5. Se você usar Application Insights, também poderá incluir sua API de Application Insights e a ID do aplicativo para coletar métricas baseadas em Application Insights. Para obter mais informações, consulte [obtendo sua chave de API e a ID do aplicativo](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Selecione **salvar**e o Grafana testará as credenciais para cada API. Você deverá ver uma mensagem semelhante à seguinte.  
    ![Configuração da fonte de dados Grafana aprovada](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Criar um painel do Grafana

1. Vá para a página inicial do Grafana e selecione **novo painel**.

2. No novo painel, selecione o **grafo**. Você pode experimentar outras opções de gráfico, mas este artigo usa o *grafo* como exemplo.

3. Um gráfico em branco é exibido no seu painel. Clique no título do painel e selecione **Editar** para inserir os detalhes dos dados que você deseja plotar neste gráfico de gráfico.
    ![Grafana novo grafo](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecione a fonte de dados Azure Monitor que você configurou.
   * Coletando métricas de Azure Monitor-selecione **Azure monitor** na lista suspensa serviço. Uma lista de seletores é mostrada, onde você pode selecionar os recursos e a métrica a serem monitorados neste gráfico. Para coletar métricas de uma VM, use o namespace **Microsoft. Compute/VirtualMachines**. Depois de selecionar as VMs e as métricas, você pode começar a exibir seus dados no painel.
     ![Configuração do grafo Grafana para Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Coletando dados de log de Azure Monitor-selecione **log Analytics do Azure** na lista suspensa serviço. Selecione o espaço de trabalho que você gostaria de consultar e definiu o texto da consulta. Você pode copiar aqui qualquer consulta de log que já tenha ou criar uma nova. Conforme você digita em sua consulta, o IntelliSense aparecerá e sugerirá opções de preenchimento automático. Selecione o tipo de visualização, **tabela**de **série temporal** e execute a consulta.
    
     > [!NOTE]
     >
     > A consulta padrão fornecida com o plug-in usa duas macros: "$ __timeFilter () e $ __interval. 
     > Essas macros permitem que o Grafana Calcule dinamicamente o intervalo de tempo e o detalhamento de tempo, ao ampliar parte de um gráfico. Você pode remover essas macros e usar um filtro de tempo padrão, como *TimeGenerated > atrás (1h)* , mas isso significa que o grafo não ofereceria suporte ao recurso de zoom.
    
     ![Configuração do grafo Grafana para o Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Veja a seguir um painel simples com dois gráficos. Aquele à esquerda mostra o percentual de CPU de duas VMs. O gráfico à direita mostra as transações em uma conta de armazenamento do Azure dividida pelo tipo de API de transação.
    ![Exemplo de dois gráficos do Grafana](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorar suas métricas personalizadas no mesmo servidor Grafana

Você também pode instalar o Telegraf e o InfluxDB para coletar e plotar as métricas personalizadas e baseadas em agente mesma instância de Grafana. Há muitos plug-ins de fonte de dados que você pode usar para trazer essas métricas juntas em um painel.

Você também pode reutilizar essa configuração para incluir métricas do seu servidor Prometheus. Use o plug-in de fonte de dados Prometheus na Galeria de plug-ins do Grafana.

Aqui estão bons artigos de referência sobre como usar Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorar as métricas do sistema com a pilha de TIQUEs no Ubuntu 16, 4](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Uma solução de monitoramento para hosts do Docker, contêineres e serviços em contêineres](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Aqui está uma imagem de um painel Grafana completo que tem métricas de Azure Monitor e Application Insights.
![Métricas de exemplo do Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Recursos avançados do Grafana

### <a name="variables"></a>Variáveis
Alguns valores de consulta podem ser selecionados por meio de listas suspensas da interface do usuário e atualizados na consulta. Considere a seguinte consulta como exemplo:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Você pode configurar uma variável que listará todos os valores de **solução** disponíveis e, em seguida, atualizar sua consulta para usá-lo.
Para criar uma nova variável, clique no botão Configurações do painel na área superior direita, selecione **variáveis**e, em seguida, **novo**.
Na página variável, defina a fonte de dados e a consulta a serem executadas para obter a lista de valores.
![Grafana configurar variável](./media/grafana-plugin/grafana-configure-variable-dark.png)

Depois de criada, ajuste a consulta para usar os valores selecionados e seus gráficos responderão de forma adequada:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana usar variáveis](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Criar listas de reprodução de Dashboard

Um dos muitos recursos úteis do Grafana é a lista de reprodução do painel. Você pode criar vários painéis e adicioná-los a uma lista de reprodução Configurando um intervalo para cada painel a ser mostrado. Selecione **reproduzir** para ver os painéis passarem pelo ciclo. Talvez você queira exibi-los em um monitor de parede grande para fornecer um quadro de status para seu grupo.

![Exemplo de playlist Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você configurou um ambiente Grafana no Azure, você será cobrado quando as VMs estiverem sendo executadas se você as estiver usando ou não. Para evitar incorrer em encargos adicionais, limpe o grupo de recursos criado neste artigo.

1. No menu à esquerda no portal do Azure, clique em grupos de **recursos** e, em seguida, clique em **Grafana**.
2. Na página do grupo de recursos, clique em **excluir**, digite **Grafana** na caixa de texto e clique em **excluir**.

## <a name="next-steps"></a>Passos Seguintes
* [Visão geral de métricas de Azure Monitor](data-platform.md)

