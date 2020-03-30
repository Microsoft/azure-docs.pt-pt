---
title: Monitor Azure serviços e aplicações usando grafana
description: Dados do Route Azure Monitor e da Aplicação Insights para que possa vê-los em Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672213"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorize os seus serviços Azure em Grafana
Agora pode monitorizar os serviços e aplicações do Azure a partir da [Grafana](https://grafana.com/) utilizando o plugin de fonte de [dados do Monitor Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plugin recolhe dados de desempenho da aplicação recolhidos pelo Azure Monitor, incluindo vários registos e métricas. Em seguida, pode apresentar estes dados no seu painel grafana.

Utilize os seguintes passos para configurar um servidor Grafana e construir dashboards para métricas e registos do Monitor Azure.

## <a name="set-up-a-grafana-server"></a>Criar um servidor Grafana

### <a name="set-up-grafana-locally"></a>Instale Grafana localmente
Para configurar um servidor grafana local, [descarregue e instale grafana no seu ambiente local](https://grafana.com/grafana/download). Para utilizar a integração do Plugin's Azure Monitor, instale a versão Grafana 5.3 ou superior.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Instale grafana em Azure através do Mercado Azure
1. Vá ao Azure Marketplace e escolha Grafana pela Grafana Labs.

2. Preencha os nomes e detalhes. Crie um novo grupo de recursos. Acompanhe os valores que escolhe para o nome de utilizador VM, senha VM e senha de administração do servidor Grafana.  

3. Escolha o tamanho vm e uma conta de armazenamento.

4. Configure as definições de configuração da rede.

5. Consulte o resumo e selecione **Criar** depois de aceitar os termos de utilização.

6. Depois de concluída a implementação, selecione **Go to Resource Group**. Vê uma lista de recursos recém-criados.

    ![Objetos do grupo de recursos de Grafana](media/grafana-plugin/grafana1.png)

    Se selecionar o grupo de segurança da rede *(grafana-nsg* neste caso), pode ver que a porta 3000 é usada para aceder ao servidor Grafana.

7. Obtenha o endereço IP público do seu servidor Grafana - volte à lista de recursos e selecione **endereço IP público**.

## <a name="sign-in-to-grafana"></a>Inscreva-se em Grafana

1. Utilizando o endereço IP do seu servidor, abra a página de Login no *endereço IP\<\>http:// :3000* ou o * \<DNSName>\:3000* no seu navegador. Enquanto 3000 é a porta padrão, note que pode ter selecionado uma porta diferente durante a configuração. Devia ver uma página de login para o servidor Grafana que construíu.

    ![Tela de login grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Inscreva-se com o *administrador* do nome do utilizador e a senha de administração do servidor Grafana que criou anteriormente. Se estiver a usar uma configuração local, a senha padrão seria *administradora*, e seria-lhe pedido que a alterasse no seu primeiro login.

## <a name="configure-data-source-plugin"></a>Configure plugin de fonte de dados

Uma vez iniciado o registo com sucesso, deve ver se o plugin de origem de dados do Monitor Azure já está incluído.

![Grafana inclui plugin Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. **Selecione Adicionar fonte** de dados para adicionar e configurar a fonte de dados do Monitor Azure.

2. Escolha um nome para a fonte de dados e selecione **O Monitor Azure** como o tipo a partir da queda.

3. Criar um diretor de serviço - grafana usa um serviço de Diretório Ativo Azure para ligar a APIs do Monitor Azure e recolher dados. Deve criar, ou utilizar um diretor de serviço existente, para gerir o acesso aos seus recursos Azure.
    * Consulte [estas instruções](../../azure-resource-manager/resource-group-create-service-principal-portal.md) para criar um diretor de serviço. Copie e guarde o ID do seu inquilino (ID do Diretório), ID do cliente (ID da aplicação) e segredo do cliente (valor chave de aplicação).
    * Consulte [a aplicação Atribuir a função de](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) atribuir a função de Leitor à aplicação Azure Ative Directory na subscrição, grupo de recursos ou recursos que pretende monitorizar. 
    O Log Analytics API requer o papel de Leitor de [Log Analytics,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)que inclui as permissões do papel do Leitor e adiciona-lhe.

4. Forneça os detalhes da ligação às APIs que gostaria de utilizar. Pode ligar-se a todos ou a alguns deles. 
    * Se ligar a ambas as métricas e registos no Monitor Azure, pode reutilizar as mesmas credenciais selecionando os mesmos detalhes que a **API do Monitor Azure**.
    * Ao configurar o plugin, pode indicar qual Azure Cloud gostaria que o plugin monitorizasse (Governo Público, Azure, Azure Germany ou Azure China).
    * Se utilizar insights de aplicação, também pode incluir a sua API de Insights de Aplicação e ID de aplicação para recolher métricas baseadas em Insights de Aplicação. Para mais informações, consulte [Obter a sua chave API e ID de aplicação](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Alguns campos de origem de dados são nomeados de forma diferente das suas definições de Azure correlacionadas:
        > * ID do inquilino é o ID do Diretório Azure
        > * ID do cliente é o ID de aplicação de diretório ativo Azure
        > * Client Secret é o valor-chave da aplicação de diretório ativo Azure

5. Se utilizar insights de aplicação, também pode incluir a sua API de Insights de Aplicação e ID de aplicação para recolher métricas baseadas em Insights de Aplicação. Para mais informações, consulte [Obter a sua chave API e ID de aplicação](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Selecione **Guardar**, e Grafana irá testar as credenciais para cada API. Devia ver uma mensagem semelhante à seguinte.  
    ![Fonte de dados de Grafana aprovada](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Construa um dashboard grafana

1. Vá à página inicial da Grafana e selecione **New Dashboard**.

2. No novo painel de instrumentos, selecione o **Gráfico**. Pode experimentar outras opções de gráficos, mas este artigo usa o *Graph* como exemplo.

3. Um gráfico em branco aparece no seu painel. Clique no título do painel e **selecione Editar** para introduzir os detalhes dos dados que pretende traçar neste gráfico.
    ![Grafana novo gráfico](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecione a fonte de dados do Monitor Azure que configura.
   * Recolha de métricas do Monitor Azure - selecione **O Monitor Azure** no dropdown do serviço. Aparece uma lista de selecionadores, onde pode selecionar os recursos e a métrica para monitorizar neste gráfico. Para recolher métricas de um VM, utilize o espaço de nome **Microsoft.Compute/VirtualMachines**. Depois de selecionado seleções de VMs e métricas, pode começar a visualizar os seus dados no painel de instrumentos.
     ![Grafana graph config para Monitor Azure](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Recolha de dados de registo do Azure Monitor - **selecione Azure Log Analytics** no dropdown do serviço. Selecione o espaço de trabalho que deseja consultar e definir o texto de consulta. Pode copiar aqui qualquer consulta de log que já tenha ou criar uma nova. À medida que escreve na sua consulta, o IntelliSense aparecerá e sugerirá opções autocompletas. Selecione o tipo de visualização, **Tabela**da **série de tempo,** e execute a consulta.
    
     > [!NOTE]
     >
     > A consulta padrão fornecida com o plugin usa duas macros: "$__timeFilter() e $__interval. 
     > Estas macros permitem que Grafana calcule dinamicamente a faixa de tempo e o grão de tempo, quando você zoom em parte de um gráfico. Pode remover estas macros e utilizar um filtro de tempo padrão, como o *TimeGenerated > (1h),* mas isso significa que o gráfico não suportaria o zoom na função.
    
     ![Grafana graph config para Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Segue-se um simples dashboard com dois gráficos. O da esquerda mostra a percentagem de CPU de dois VMs. O gráfico à direita mostra as transações numa conta de Armazenamento Azure discriminada pelo tipo API transação.
    ![Grafana Dois Gráficos Exemplo](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorize as suas métricas personalizadas no mesmo servidor Grafana

Também pode instalar a Telegraf e a InfluxDB para recolher e traçar métricas personalizadas e baseadas em agentes na mesma instância grafana. Existem muitos plugins de fonte de dados que pode usar para juntar estas métricas num dashboard.

Também pode reutilizar esta configuração para incluir métricas do seu servidor Prometheus. Utilize o plugin de fonte de dados da Prometheus na galeria plugin da Grafana.

Aqui estão bons artigos de referência sobre como usar Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorizar as métricas do sistema com a pilha tick em Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Uma solução de monitorização para anfitriões, contentores e serviços contentorizados do Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Aqui está uma imagem de um painel completo da Grafana que tem métricas do Azure Monitor e da Application Insights.
![Grafana Exemplo Métricas](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Características avançadas de Grafana

### <a name="variables"></a>Variáveis
Alguns valores de consulta podem ser selecionados através de downdowns de UI, e atualizados na consulta. Considere a seguinte consulta como um exemplo:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Pode configurar uma variável que listará todos os valores disponíveis da **Solução** e, em seguida, atualizar a sua consulta para usá-la.
Para criar uma nova variável, clique no botão Definições do painel na área superior direita, selecione **Variáveis,** e depois **Nova**.
Na página variável, defina a fonte de dados e a consulta a executar de forma a obter a lista de valores.
![Grafana configurar variável](./media/grafana-plugin/grafana-configure-variable-dark.png)

Uma vez criado, ajuste a consulta para utilizar o(s) valor selecionado e os seus gráficos responderão em conformidade:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana usam variáveis](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Criar listas de reprodução de dashboard

Uma das muitas funcionalidades úteis de Grafana é a lista de reprodução do painel de instrumentos. Pode criar vários dashboards e adicioná-los a uma lista de reprodução configurando um intervalo para cada painel de instrumentos mostrar. Selecione **Reproduzir** para ver o ciclo dos tabliers. É melhor exibi-los num grande monitor de parede para fornecer um quadro de estado para o seu grupo.

![Exemplo de lista de grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se configurar um ambiente Grafana em Azure, é cobrado quando os VMs estão a funcionar, quer esteja a usá-los ou não. Para evitar incorrer em taxas adicionais, limpe o grupo de recursos criado neste artigo.

1. A partir do menu à esquerda no portal Azure, clique em **grupos de recursos** e, em seguida, clique em **Grafana**.
2. Na página do grupo de recursos, clique em **Eliminar**, digite **Grafana** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
* [Visão geral das Métricas do Monitor Azure](data-platform.md)

