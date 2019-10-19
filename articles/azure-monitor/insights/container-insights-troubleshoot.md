---
title: Como solucionar problemas de Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode solucionar problemas e resolver questões com Azure Monitor para contêineres.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/27/2018
ms.openlocfilehash: ec75f607f707405d6a5bea98deb784f4306c04f1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555365"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Solução de problemas Azure Monitor para contêineres

Ao configurar o monitoramento do seu cluster AKS (serviço kubernetes do Azure) com Azure Monitor para contêineres, você pode encontrar um problema impedindo a coleta de dados ou o status de relatórios. Este artigo fornece detalhes sobre alguns problemas comuns e etapas de solução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de atualização ou integração
Ao habilitar Azure Monitor para contêineres ou atualizar um cluster para dar suporte à coleta de métricas, você pode receber um erro semelhante ao seguinte: *a identidade do usuário < cliente > ' com ID de objeto ' < ObjectID do usuário > ' não tem autorização para executar ação ' Microsoft. Authorization/roleAssignments/Write ' sobre escopo*

Durante a integração ou o processo de atualização, é tentada a concessão da atribuição de função de **Editor de métricas de monitoramento** no recurso de cluster. O usuário que inicia o processo para habilitar a Azure Monitor para contêineres ou a atualização para dar suporte à coleção de métricas deve ter acesso à permissão **Microsoft. Authorization/roleAssignments/Write** no escopo de recurso de cluster AKs. Somente os membros das funções internas do **proprietário** e do **administrador de acesso do usuário** recebem acesso a essa permissão. Se suas políticas de segurança exigirem a atribuição de permissões de nível granular, recomendamos que você exiba [funções personalizadas](../../role-based-access-control/custom-roles.md) e atribua-as aos usuários que precisam dela. 

Você também pode conceder manualmente essa função da portal do Azure executando as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, digite **kubernetes**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure kubernetes**.
3. Na lista de clusters kubernetes, selecione um na lista.
2. No menu à esquerda, clique em **controle de acesso (iam)** .
3. Selecione **+ Adicionar** para adicionar uma atribuição de função e selecione a função de **Editor de métricas de monitoramento** e, na caixa **selecionar** , digite **AKs** para filtrar os resultados em apenas as entidades de serviço de clusters definidas na assinatura. Selecione aquele na lista específica para esse cluster.
4. Selecione **salvar** para concluir a atribuição da função. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor para contêineres está habilitado, mas não está relatando nenhuma informação
Se Azure Monitor para contêineres for habilitado e configurado com êxito, mas você não puder exibir informações de status ou nenhum resultado for retornado de uma consulta de log, você diagnosticará o problema seguindo estas etapas: 

1. Verifique o status do agente executando o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve se parecer com o seguinte, que indica que foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o status da implantação com a versão *06072018* do agente ou posterior usando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve se parecer com o exemplo a seguir, que indica que foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o status do pod para verificar se ele está em execução usando o comando: `kubectl get pods --namespace=kube-system`

    A saída deve ser semelhante ao exemplo a seguir com um status de *em execução* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os logs do agente. Quando o agente em contêiner é implantado, ele executa uma verificação rápida executando comandos OMI e exibe a versão do agente e do provedor. 

5. Para verificar se o agente foi implantado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    O status deve ser semelhante ao exemplo a seguir:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela a seguir resume os erros conhecidos que você pode encontrar ao usar Azure Monitor para contêineres.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro `No data for selected filters`  | Pode levar algum tempo para estabelecer o fluxo de dados de monitoramento para clusters recém-criados. Aguarde pelo menos 10 a 15 minutos para que os dados sejam exibidos para o cluster. |   
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do serviço Kubenetes do Azure está configurando para monitoramento de integridade e desempenho, uma conexão é estabelecida entre o cluster e o espaço de trabalho do Azure Log Analytics. Um espaço de trabalho Log Analytics é usado para armazenar todos os dados de monitoramento para o cluster. Esse erro pode ocorrer quando seu espaço de trabalho Log Analytics foi excluído. Verifique se o espaço de trabalho foi excluído e, se ele foi, será necessário reabilitar o monitoramento do cluster com Azure Monitor para contêineres e especificar um novo espaço de trabalho ou criar um existente. Para reabilitar, você precisará [desabilitar](container-insights-optout.md) o monitoramento do cluster e [habilitar](container-insights-enable-new-cluster.md) Azure monitor para contêineres novamente. |  
| `Error retrieving data` depois de adicionar Azure Monitor para contêineres por meio do AZ AKs CLI | Ao habilitar o monitoramento usando `az aks cli`, os Azure Monitor para contêineres podem não ser implantados corretamente. Verifique se a solução está implantada. Para fazer isso, vá para o espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **soluções** no painel à esquerda. Para resolver esse problema, será necessário reimplantar a solução seguindo as instruções sobre [como implantar Azure monitor para contêineres](container-insights-onboard.md) |  

Para ajudar a diagnosticar o problema, fornecemos um script de solução de problemas disponível [aqui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Passos seguintes

Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar Azure Monitor para contêineres, consulte [exibir a integridade do serviço kubernetes do Azure](container-insights-analyze.md).