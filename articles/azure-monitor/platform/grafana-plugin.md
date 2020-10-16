---
title: Monitor Azure serviços e aplicações usando Grafana
description: Route Azure Monitor e Application Insights dados para que possa vê-los em Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 23bba091628eee767fbf292a8a8d772ffab674cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073464"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorize os seus serviços Azure em Grafana
Agora pode monitorizar os serviços e aplicações da Azure a partir de [Grafana](https://grafana.com/) utilizando o [plugin de fonte de dados Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plugin recolhe dados de desempenho da aplicação recolhidos pelo Azure Monitor, incluindo vários registos e métricas. Em seguida, pode apresentar estes dados no seu painel grafana.

Utilize os seguintes passos para configurar um servidor Grafana e construir dashboards para métricas e registos a partir do Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Configurar um servidor Grafana

### <a name="set-up-grafana-locally"></a>Configurar Grafana localmente
Para configurar um servidor Grafana local, [descarregue e instale Grafana no seu ambiente local.](https://grafana.com/grafana/download) Para utilizar a integração do Azure Monitor do plugin, instale a versão Grafana 5.3 ou superior.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Instale Grafana em Azure através do Azure Marketplace
1. Vá ao Azure Marketplace e escolha Grafana pela Grafana Labs.

2. Preencha os nomes e detalhes. Criar um novo grupo de recursos. Acompanhe os valores que escolhe para o nome de utilizador VM, palavra-passe VM e senha de administração do servidor Grafana.  

3. Escolha o tamanho VM e uma conta de armazenamento.

4. Configure as definições de configuração da rede.

5. Ver o resumo e selecione **Criar** depois de aceitar os termos de utilização.

6. Depois de concluída a implementação, selecione **Ir para o Grupo de Recursos**. Vê-se uma lista de recursos recém-criados.

    ![Objetos de grupo de recursos grafana](media/grafana-plugin/grafana1.png)

    Se selecionar o grupo de segurança da rede *(grafana-nsg* neste caso), pode ver que a porta 3000 é usada para aceder ao servidor Grafana.

7. Obtenha o endereço IP público do seu servidor Grafana - volte à lista de recursos e selecione **o endereço IP público**.

## <a name="sign-in-to-grafana"></a>Inscreva-se em Grafana

1. Utilizando o endereço IP do seu servidor, abra a página de Início de Sessão * \<IP address\> http://:3000* ou os * \<DNSName> \: 3000* no seu navegador. Enquanto 3000 é a porta predefinida, note que pode ter selecionado uma porta diferente durante a configuração. Devia ver uma página de login para o servidor Grafana que construiu.

    ![Tela de login de Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Faça o *s.admin* com o nome de utilizador e a palavra-passe de administração do servidor Grafana que criou anteriormente. Se estiver a utilizar uma configuração local, a palavra-passe padrão seria *administrador,* e seria solicitado que a alterasse no seu primeiro login.

## <a name="configure-data-source-plugin"></a>Configurar plugin de fonte de dados

Uma vez iniciado o login com sucesso, deve ver se o plugin de fonte de dados do Azure Monitor já está incluído.

![Grafana inclui plugin Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. **Selecione Adicionar fonte de dados** para adicionar e configurar a fonte de dados do Monitor Azure.

2. Escolha um nome para a fonte de dados e selecione **O Monitor Azure** como o tipo a partir do dropdown.

3. Criar um principal de serviço - Grafana usa um diretor de serviço Azure Ative Directory para ligar às APIs do Monitor Azure e recolher dados. Deve criar, ou utilizar um principal de serviço existente, para gerir o acesso aos seus recursos Azure.
    * Consulte [estas instruções](../../active-directory/develop/howto-create-service-principal-portal.md) para criar um principal de serviço. Copie e guarde o seu ID do inquilino (ID de diretório), ID do cliente (ID de aplicação) e segredo do cliente (valor chave de aplicação).
    * Consulte [a aplicação de atribuir a função](../../active-directory/develop/howto-create-service-principal-portal.md) de atribuir a função Reader à aplicação Azure Ative Directory na subscrição, grupo de recursos ou recursos que pretende monitorizar. 
    A API do Log Analytics requer a [função De Leitor de Log Analytics,](../../role-based-access-control/built-in-roles.md#log-analytics-reader)que inclui as permissões da função Reader e acrescenta-lhe.

4. Forneça os detalhes da ligação às APIs que gostaria de usar. Pode ligar-se a todos ou a alguns deles. 
    * Se ligar a ambas as métricas e registos no Azure Monitor, pode reutilizar as mesmas credenciais selecionando os **mesmos detalhes que a API do Monitor Azure**.
    * Ao configurar o plugin, pode indicar qual a Nuvem Azure que gostaria que o plugin monitorizasse (Public, Azure US Government, Azure Germany ou Azure China).
    * Se utilizar o Application Insights, também pode incluir a API dos Seus Insights de Aplicação e o ID de aplicação para recolher métricas baseadas em Insights de Aplicação. Para obter mais informações, consulte [obter a sua chave API e iD de aplicação.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)

        > [!NOTE]
        > Alguns campos de origem de dados são nomeados de forma diferente das suas definições de Azure correlacionadas:
        > * ID do inquilino é o ID do Diretório Azure
        > * ID do cliente é o ID do Diretório Ativo Azure
        > * A Client Secret é o valor-chave da aplicação do Diretório Ativo Azure

5. Se utilizar o Application Insights, também pode incluir a API dos Seus Insights de Aplicação e o ID de aplicação para recolher métricas baseadas em Insights de Aplicação. Para obter mais informações, consulte [obter a sua chave API e iD de aplicação.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)

6. **Selecione Save**, e Grafana irá testar as credenciais para cada API. Deve ver uma mensagem semelhante à seguinte.  
    ![Grafana fonte de dados config aprovada](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Construa um tablier Grafana

1. Vá à página inicial de Grafana e selecione **New Dashboard**.

2. No novo painel de instrumentos, selecione o **Gráfico**. Pode experimentar outras opções de gráfico, mas este artigo usa *o Graph* como exemplo.

3. Um gráfico em branco aparece no seu painel. Clique no título do painel e **selecione Editar** para introduzir os detalhes dos dados que pretende traçar neste gráfico gráfico.
    ![Grafana novo gráfico](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecione a fonte de dados do Monitor Azure que configuraste.
   * Recolha de métricas do Monitor Azure - selecione **Azure Monitor** no dropdown de serviço. Aparece uma lista de selecionadores onde é possível selecionar os recursos e a métrica para monitorizar neste gráfico. Para recolher métricas de um VM, utilize o espaço de nome **Microsoft.Compute/VirtualMachines**. Uma vez selecionados VMs e métricas, pode começar a visualizar os seus dados no painel de instrumentos.
     ![Grafana config gráfico para Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Recolhendo dados de registo do Azure Monitor - selecione **Azure Log Analytics** no dropdown de serviço. Selecione o espaço de trabalho que pretende consultar e definir o texto de consulta. Pode copiar aqui qualquer consulta de registo que já tenha ou criar uma nova. À medida que escreve na sua consulta, o IntelliSense aparecerá e sugerirá opções autocompletas. Selecione o tipo de visualização, **tabela série de tempo** **Table**, e executar a consulta.
    
     > [!NOTE]
     >
     > A consulta padrão fornecida com o plugin usa duas macros: "$__timeFilter() e $__interval. 
     > Estas macros permitem que Grafana calcule dinamicamente o intervalo de tempo e o grão de tempo, quando se faz zoom em parte de um gráfico. Pode remover estas macros e utilizar um filtro de tempo padrão, como *o TimeGenerated >(1h),* mas isso significa que o gráfico não suportaria o zoom em funcionalidade.
    
     ![Grafana config para Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Segue-se um simples painel de instrumentos com duas tabelas. O da esquerda mostra a percentagem de CPU de dois VMs. O gráfico à direita mostra as transações numa conta de Armazenamento Azure desfeita pelo tipo API de transação.
    ![Exemplo de Grafana Two Charts](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorize as suas métricas personalizadas no mesmo servidor Grafana

Também pode instalar a Telegraf e a InfluxDB para recolher e traçar métricas personalizadas e baseadas em agentes, na mesma instância de Grafana. Existem muitos plugins de fonte de dados que você pode usar para juntar estas métricas num dashboard.

Também pode reutilizar esta configuração para incluir métricas do seu servidor Prometheus. Utilize o plugin de fonte de dados Prometheus na galeria plugin da Grafana.

Aqui estão bons artigos de referência sobre como usar Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorizar as métricas do sistema com a stack tick em Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Uma solução de monitorização para anfitriões, contentores e serviços contentorizados do Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Aqui está uma imagem de um dashboard grafana completo que tem métricas do Azure Monitor e Application Insights.
![Métricas de Exemplo Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Recursos avançados de Grafana

### <a name="variables"></a>Variáveis
Alguns valores de consulta podem ser selecionados através de dropdowns de UI e atualizados na consulta. Considere a seguinte consulta como um exemplo:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Pode configurar uma variável que irá listar todos os valores de **solução** disponíveis e, em seguida, atualizar a sua consulta para usá-la.
Para criar uma nova variável, clique no botão Definições do painel na área superior direita, selecione **Variáveis**e, em seguida, **New**.
Na página variável, defina a fonte de dados e a consulta a correr de forma a obter a lista de valores.
![Grafana configurar variável](./media/grafana-plugin/grafana-configure-variable-dark.png)

Uma vez criado, ajuste a consulta para utilizar o valor(s) selecionado e os seus gráficos responderão em conformidade:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana usa variáveis](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Criar listas de reprodução de dashboard

Uma das muitas funcionalidades úteis de Grafana é a lista de reprodução do painel de instrumentos. Pode criar vários dashboards e adicioná-los a uma lista de reprodução configurando um intervalo para cada dashboard mostrar. Selecione **Reproduzir** para ver o ciclo dos dashboards. É melhor exibi-las num monitor de parede grande para fornecer uma placa de estado para o seu grupo.

![Exemplo de lista de reprodução de Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se configurar um ambiente Grafana no Azure, é cobrado quando os VMs estão a funcionar quer esteja a usá-los ou não. Para evitar incorrer em taxas adicionais, limpe o grupo de recursos criado neste artigo.

1. A partir do menu à esquerda no portal Azure, clique em **grupos de recursos** e, em seguida, clique em **Grafana**.
2. Na sua página de grupo de recursos, clique em **Eliminar**, **digite Grafana** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
* [Visão geral das métricas do monitor Azure](data-platform.md)
