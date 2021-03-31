---
title: Implemente uma porta de entrada auto-hospedada para Kubernetes | Microsoft Docs
description: Saiba como implementar um componente de gateway auto-hospedado da Azure API Management para Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89500135"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Implementar um gateway autoalojado no Kubernetes

Este artigo descreve os passos para a implantação do componente de gateway auto-hospedado da Azure API Management para um cluster Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
- Crie um cluster Kubernetes.
   > [!TIP]
   > [Os agrupamentos de nó único](https://kubernetes.io/docs/setup/#learning-environment) funcionam bem para fins de desenvolvimento e avaliação. Utilize clusters de vários nós [certificados da Kubernetes](https://kubernetes.io/partners/#conformance) no local ou na nuvem para cargas de trabalho de produção.
- [Fornecimento de um recurso de gateway auto-hospedado na sua instância de Gestão da API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implementar no Kubernetes

1. Selecione **Gateways** em **Implementação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que pretende implantar.
3. Selecione **Implantação**.
4. Um token de acesso na caixa de texto **Token** foi gerado automaticamente para si, com base nos valores de **validade** e **segredo** padrão. Se necessário, escolha valores em ambos os controlos para gerar um novo token.
5. Selecione o separador **Kubernetes** nos **scripts de Implementação**.
6. Selecione o link de ficheiro **\<gateway-name\> .yml** e descarregue o ficheiro YAML.
7. Selecione o ícone de **cópia** no canto inferior direito da caixa de texto **Implementar** para guardar os `kubectl` comandos para a área de transferência.
8. Cole comandos para a janela do terminal (ou comando). O primeiro comando cria um segredo de Kubernetes que contém o símbolo de acesso gerado no passo 4. O segundo comando aplica o ficheiro de configuração descarregado no passo 6 para o cluster Kubernetes e espera que o ficheiro esteja no diretório atual.
9. Executar os comandos para criar os objetos Kubernetes necessários no [espaço de nome padrão](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e iniciar pods de gateway auto-hospedados a partir da imagem do [recipiente](https://aka.ms/apim/sputnik/dhub) descarregado do Registo de Contentores da Microsoft.
10. Executar o seguinte comando para verificar se a implantação foi bem sucedida. Note que pode levar algum tempo para que todos os objetos sejam criados e para que as cápsulas se inicializem.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Executar o seguinte comando para verificar se o serviço foi criado com sucesso. Note que os seus IPs e portas de serviço serão diferentes.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Volte ao portal Azure e selecione **Overview**.
13. Confirme que **o Estado** mostra uma marca de verificação verde, seguida de uma contagem de nó que corresponde ao número de réplicas especificadas no ficheiro YAML. Este estado significa que as cápsulas de gateway auto-hospedadas estão a comunicar com sucesso com o serviço de Gestão da API e têm um "batimento cardíaco" regular.

    ![Estado do gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Executar o <code>kubectl logs deployment/<gateway-name></code> comando para visualizar registos de uma cápsula selecionada aleatoriamente se houver mais de um.
> Corra <code>kubectl logs -h</code> para um conjunto completo de opções de comando, tais como como visualizar registos para uma vagem ou recipiente específico.

## <a name="production-deployment-considerations"></a>Considerações de implantação da produção

### <a name="access-token"></a>Ficha de acesso
Sem um token de acesso válido, um gateway auto-hospedado não pode aceder e descarregar dados de configuração a partir do ponto final do serviço de Gestão API associado. O token de acesso pode ser válido por um máximo de 30 dias. Deve ser regenerado e o cluster configurado com um token fresco, manualmente ou através da automatização antes de expirar.

Quando estiver a automatizar a atualização de fichas, use [esta operação de API](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) de gestão para gerar um novo token. Para obter informações sobre a gestão dos segredos de Kubernetes, consulte o [site da Kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret)

### <a name="namespace"></a>Espaço de Nomes
Os espaços [de nomes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) de Kubernetes ajudam a dividir um único cluster entre várias equipas, projetos ou aplicações. Os espaços de nome fornecem uma margem para recursos e nomes. Podem ser associados a uma quota de recursos e a políticas de controlo de acessos.

O portal Azure fornece comandos para criar recursos de gateway auto-hospedados no espaço de nome **padrão.** Este espaço de nome é criado automaticamente, existe em todos os clusters, e não pode ser eliminado.
Considere [criar e implantar](https://kubernetesbyexample.com/ns/) uma porta de entrada auto-hospedada num espaço de nome separado em produção.

### <a name="number-of-replicas"></a>Número de réplicas
O número mínimo de réplicas adequadas para a produção é dois.

Por padrão, um gateway auto-hospedado é implantado com uma [estratégia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)de implementação **rollingUpdate** . Reveja os valores predefinidos e considere explicitamente definir os campos [maxUn disponível](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [maxSurge,](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) especialmente quando estiver a usar uma contagem de réplicas elevada.

### <a name="container-resources"></a>Recursos de contentores
Por predefinição, o ficheiro YAML fornecido no portal Azure não especifica pedidos de recursos de contentores.

É impossível prever e recomendar de forma fiável a quantidade de CPU por contentor e recursos de memória e o número de réplicas necessárias para suportar uma carga de trabalho específica. Muitos fatores estão em jogo, tais como:

- Hardware específico em que o cluster está a funcionar.
- Presença e tipo de virtualização.
- Número e taxa de ligações ao cliente simultâneas.
- Taxa de pedido.
- Tipo e número de políticas configuradas.
- O tamanho da carga útil e se as cargas são tamponadas ou transmitidas em streaming.
- Latência do serviço de backend.

Recomendamos a definição de pedidos de recursos para dois núcleos e 2 GiB como ponto de partida. Efetue um teste de carga e escalone para cima/para fora ou para baixo/em com base nos resultados.

### <a name="container-image-tag"></a>Etiqueta de imagem do recipiente
O ficheiro YAML fornecido no portal Azure utiliza a **etiqueta mais recente.** Esta etiqueta refere-se sempre à versão mais recente da imagem do contentor de gateway auto-hospedado.

Considere usar uma etiqueta de versão específica em produção para evitar o upgrade não intencional para uma versão mais recente.

Você pode [baixar uma lista completa de tags disponíveis](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Política do DNS
A resolução do nome DNS desempenha um papel fundamental na capacidade de um gateway auto-hospedado de se conectar às dependências em Azure e enviar chamadas API para serviços de backend.

O ficheiro YAML fornecido no portal Azure aplica a política padrão [clusterFirst.](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) Esta política faz com que os pedidos de resolução de nomes não resolvidos pelo cluster DNS sejam encaminhados para o servidor DNS a montante que é herdado do nó.

Para saber mais sobre a resolução de nomes em Kubernetes, consulte o [site da Kubernetes.](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) Considere personalizar a [política de DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou [a configuração de DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) como apropriado para a sua configuração.

### <a name="external-traffic-policy"></a>Política de tráfego externo
O ficheiro YAML fornecido no portal Azure define `externalTrafficPolicy` o campo no objeto de [Serviço](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) para `Local` . Isto preserva o endereço IP do chamador (acessível no contexto do [pedido)](api-management-policy-expressions.md#ContextVariables)e desativa o equilíbrio da carga do nó transversal, eliminando o lúpulo de rede causado por ele. Esteja ciente de que esta configuração pode causar distribuição assimétrica do tráfego em implantações com um número desigual de cápsulas de gateway por nó.

### <a name="custom-domain-names-and-ssl-certificates"></a>Nomes de domínio personalizados e certificados SSL

Se utilizar nomes de domínio personalizados para os pontos finais da API Management, especialmente se utilizar um nome de domínio personalizado para o ponto final de Gestão, poderá ter de atualizar o valor do `config.service.endpoint` ficheiro **\<gateway-name\> .yaml** para substituir o nome de domínio predefinido pelo nome de domínio personalizado. Certifique-se de que o ponto final de Gestão pode ser acedido a partir do casulo do gateway auto-hospedado no cluster Kubernetes.

Neste cenário, se o certificado SSL utilizado pelo ponto final de Gestão não for assinado por um certificado de CA bem conhecido, deve certificar-se de que o certificado de CA é fidedigno pelo casulo do gateway auto-hospedado.

### <a name="configuration-backup"></a>Backup de configuração
Para conhecer o comportamento de gateway auto-hospedado na presença de uma interrupção temporária da conectividade Azure, consulte a [visão geral do gateway auto-hospedado](self-hosted-gateway-overview.md#connectivity-to-azure).

Configure um volume de armazenamento local para o recipiente de gateway auto-hospedado, para que possa persistir uma cópia de cópia de segurança da configuração mais recente descarregada. Se a conectividade estiver baixa, o volume de armazenamento pode utilizar a cópia de reserva no reinício. O caminho de montagem de volume deve ser <code>/apim/config</code> . Veja um exemplo no [GitHub.](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)
Para saber mais sobre o armazenamento em Kubernetes, consulte o site da [Kubernetes.](https://kubernetes.io/docs/concepts/storage/volumes/)

### <a name="local-logs-and-metrics"></a>Troncos e métricas locais
O gateway auto-hospedado envia telemetria para [Azure Monitor](api-management-howto-use-azure-monitor.md) e [Azure Application Insights](api-management-howto-app-insights.md) de acordo com as definições de configuração no serviço de Gestão API associado.
Quando [a conectividade com Azure](self-hosted-gateway-overview.md#connectivity-to-azure) é temporariamente perdida, o fluxo de telemetria para Azure é interrompido e os dados são perdidos durante a interrupção.
Considere [a criação de monitorização local](how-to-configure-local-metrics-logs.md) para garantir a capacidade de observar o tráfego da API e evitar a perda de telemetria durante as interrupções de conectividade Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a visão geral do gateway auto-hospedado.](self-hosted-gateway-overview.md)
