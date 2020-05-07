---
title: Implementar uma porta de entrada auto-hospedada para Kubernetes [ Microsoft Docs
description: Saiba como implementar um componente de gateway auto-hospedado da Azure API Management para kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 38cfab8a3b73eeef28249f53bd2f5c56e26b21a1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854106"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Implementar um gateway autoalojado no Kubernetes

Este artigo descreve os passos para a implementação do componente de gateway auto-hospedado da Azure API Management para um cluster Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
- Crie um aglomerado de Kubernetes.
   > [!TIP]
   > [Os clusters de nó único](https://kubernetes.io/docs/setup/#learning-environment) funcionam bem para fins de desenvolvimento e avaliação. Utilize [kubernetes](https://kubernetes.io/partners/#conformance) Clusters multi-nó certificados no local ou na nuvem para cargas de trabalho de produção.
- [Fornecer um recurso de gateway auto-hospedado na sua instância de Gestão API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implementar no Kubernetes

1. Selecione **Gateways** em **implementação e infraestrutura.**
2. Selecione o recurso de gateway auto-hospedado que pretende implementar.
3. Selecione **Implementação**.
4. Um símbolo de acesso na caixa de texto **Token** foi autogerado para si, com base nos **valores-chave** padrão **de Expiração** e Segredo. Se necessário, escolha valores em ambos os controlos para gerar um novo símbolo.
5. Selecione o separador **Kubernetes** em **scripts de implantação**.
6. Selecione o link de ficheiro **>.yml** de nome de gateway<e descarregue o ficheiro YAML.
7. Selecione o ícone de **cópia** no canto inferior `kubectl` direito da caixa de texto **de implantação** para guardar os comandos para a área de recortes.
8. A pasta comanda a janela do terminal (ou comando). O primeiro comando cria um segredo kubernetes que contém o símbolo de acesso gerado no passo 4. O segundo comando aplica o ficheiro de configuração descarregado no passo 6 para o cluster Kubernetes e espera que o ficheiro esteja no diretório atual.
9. Execute os comandos para criar os objetos Kubernetes necessários no espaço de [nome padrão](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e inicie cápsulas de gateway auto-hospedadas a partir da imagem do [recipiente](https://aka.ms/apim/sputnik/dhub) descarregada do Registo de Contentores da Microsoft.
10. Executar o seguinte comando para verificar se o destacamento foi bem sucedido. Note que pode levar algum tempo para que todos os objetos sejam criados e para que as cápsulas se ininicializem.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Executar o seguinte comando para verificar se o serviço foi criado com sucesso. Note que os seus IPs de serviço e portas serão diferentes.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Volte ao portal Azure e selecione **visão geral.**
13. Confirme que **o Estado** mostra uma marca de verificação verde, seguida de uma contagem de nós que corresponde ao número de réplicas especificadas no ficheiro YAML. Este estado significa que as cápsulas de gateway auto-hospedadas estão a comunicar com sucesso com o serviço de Gestão API e têm um "batimento cardíaco" regular.

    ![Estado do gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Execute <code>kubectl logs deployment/<gateway-name></code> o comando para visualizar os registos de uma cápsula selecionada aleatoriamente se houver mais de um.
> Procure <code>kubectl logs -h</code> um conjunto completo de opções de comando, como como visualizar os registos para uma cápsula ou recipiente específico.

## <a name="production-deployment-considerations"></a>Considerações de implantação de produção

### <a name="access-token"></a>Ficha de acesso
Sem um sinal de acesso válido, um gateway auto-hospedado não pode aceder e transferir dados de configuração a partir do ponto final do serviço de Gestão API associado. O token de acesso pode ser válido por um máximo de 30 dias. Deve ser regenerado e o cluster configurado com um token fresco, manualmente ou através da automatização antes de expirar. 

Quando estiver a automatizar a atualização de token, use [esta operação de gestão da API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) para gerar um novo símbolo. Para obter informações sobre a gestão dos segredos de Kubernetes, consulte o site da [Kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret)

### <a name="namespace"></a>Espaço de nomes
Os espaços [de nome](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes ajudam a dividir um único cluster entre várias equipas, projetos ou aplicações. Os espaços de nome fornecem uma margem para recursos e nomes. Podem ser associados a uma quota de recursos e a políticas de controlo de acesso.

O portal Azure fornece comandos para criar recursos de gateway auto-hospedados no espaço de nome **padrão.** Este espaço de nome é criado automaticamente, existe em todos os clusters, e não pode ser eliminado.
Considere [criar e implantar](https://kubernetesbyexample.com/ns/) uma porta de entrada auto-hospedada num espaço de nome separado na produção.

### <a name="number-of-replicas"></a>Número de réplicas
O número mínimo de réplicas adequadas para a produção é de dois.

Por padrão, um gateway auto-hospedado é implementado com uma [estratégia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)de implementação **RollingUpdate** . Reveja os valores predefinidos e considere explicitamente definir os campos [maxInine](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [maxSurge,](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) especialmente quando estiver a utilizar uma alta contagem de réplicas.

### <a name="container-resources"></a>Recursos de contentores
Por predefinição, o ficheiro YAML fornecido no portal Azure não especifica os pedidos de recursos do contentor.

É impossível prever e recomendar de forma fiável a quantidade de CPU por contentor e recursos de memória e o número de réplicas necessárias para suportar uma carga de trabalho específica. Muitos fatores estão em jogo, tais como:

- Hardware específico em que o cluster está a funcionar.
- Presença e tipo de virtualização.
- Número e taxa de ligações simultâneas ao cliente.
- Taxa de pedido.
- Tipo e número de políticas configuradas.
- Tamanho da carga útil e se as cargas são tamponadas ou transmitidas.
- Latência de serviço de backend.

Recomendamos definir pedidos de recursos para dois núcleos e 2 GiB como ponto de partida. Efetuar um teste de carga e escalar para cima/para fora ou para baixo/para dentro/com base nos resultados.

### <a name="container-image-tag"></a>Etiqueta de imagem de recipiente
O ficheiro YAML fornecido no portal Azure utiliza a **etiqueta mais recente.** Esta etiqueta refere sempre a versão mais recente da imagem de contentor de gateway auto-hospedada.

Considere utilizar uma etiqueta de versão específica em produção para evitar o upgrade não intencional para uma versão mais recente.

Pode [fazer o download de uma lista completa de tags disponíveis.](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)

### <a name="dns-policy"></a>Política do DNS
A resolução de nomes DNS desempenha um papel fundamental na capacidade de um gateway auto-hospedado de se ligar às dependências em Azure e enviar chamadas da API para apoiar os serviços.

O ficheiro YAML fornecido no portal Azure aplica a política padrão [clusterFirst.](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) Esta política faz com que os pedidos de resolução de nomes não resolvidos pelo cluster DNS sejam encaminhados para o servidor DNS a montante que é herdado do nó.

Para saber sobre a resolução de nomes em Kubernetes, consulte o site da [Kubernetes.](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) Considere personalizar a [política dNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou [a configuração DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) conforme apropriado para a sua configuração.

### <a name="configuration-backup"></a>Backup de configuração
Para aprender sobre o comportamento auto-hospedado na presença de uma interrupção temporária da conectividade Azure, consulte a visão geral do [gateway auto-hospedado.](self-hosted-gateway-overview.md#connectivity-to-azure)

Configure um volume de armazenamento local para o recipiente de gateway auto-hospedado, para que possa persistir uma cópia de reserva da configuração mais recente descarregada. Se a conectividade estiver em baixo, o volume de armazenamento pode utilizar a cópia de reserva no momento do reinício. O caminho do <code>/apim/config</code>suporte de volume deve ser . Veja um exemplo no [GitHub.](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)
Para saber mais sobre o armazenamento em Kubernetes, consulte o site da [Kubernetes.](https://kubernetes.io/docs/concepts/storage/volumes/)

### <a name="local-logs-and-metrics"></a>Registos e métricas locais
O gateway auto-hospedado envia telemetria para [O Monitor Azure](api-management-howto-use-azure-monitor.md) e Insights de [aplicação Azure de](api-management-howto-app-insights.md) acordo com as configurações de configuração no serviço de Gestão aPI associado.
Quando a [conectividade com o Azure](self-hosted-gateway-overview.md#connectivity-to-azure) é temporariamente perdida, o fluxo de telemetria para Azure é interrompido e os dados perdem-se durante a interrupção.
Considere [a criação de uma monitorização local](how-to-configure-local-metrics-logs.md) para garantir a capacidade de observar o tráfego de API e evitar a perda de telemetria durante as interrupções de conectividade Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a visão geral do [gateway auto-hospedado.](self-hosted-gateway-overview.md)
