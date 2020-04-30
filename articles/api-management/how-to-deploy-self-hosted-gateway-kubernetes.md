---
title: Implementar porta de entrada auto-hospedada para Kubernetes Microsoft Docs
description: Saiba como implementar a componente de gateway auto-hospedada da Azure API Management para kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205108"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Implementar porta de entrada auto-hospedada para Kubernetes

Este artigo descreve os passos para a implementação do componente de gateway auto-hospedado da Azure API Management para um cluster Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
- Crie um aglomerado de Kubernetes.
> [!TIP]
> [Os clusters de nó único](https://kubernetes.io/docs/setup/#learning-environment) funcionam bem para fins de desenvolvimento e avaliação. Utilize [kubernetes](https://kubernetes.io/partners/#conformance) Clusters multi-nó certificados no local ou na nuvem para cargas de trabalho de produção.
- [Fornecer um recurso de gateway auto-hospedado na sua instância de Gestão API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implementar no Kubernetes

1. Selecione **Gateways** a partir de baixo **da implantação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que pretende implementar.
3. Selecione **Implementação**.
4. Note que um token de acesso na caixa de texto **Token** foi autogerado para si usando os **valores-chave** de **Validade** e Segredo predefinidos. Se necessário, escolha os valores desejados em ambos os controlos para gerar um novo símbolo.
5. Selecione o separador **Kubernetes** sob **scripts de implantação**.
6. Clique no link de ficheiro **>.yml de nome de gateway<** e descarregue o ficheiro YAML.
7. Selecione o ícone **de cópia** localizado no canto inferior direito da caixa de texto **de implantação** para guardar os `kubectl` comandos para a área de recortes.
8. A pasta comanda a janela do terminal (ou comando). O primeiro comando cria um segredo kubernetes contendo ficha de acesso gerada no passo 4. O segundo comando aplica o ficheiro de configuração descarregado no passo 6 para o cluster Kubernetes e espera que o ficheiro esteja presente no diretório atual.
9. Execute os comandos para criar os objetos Kubernetes necessários no espaço de [nome padrão](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e inicie o 'gateway pod's) auto-hospedado a partir da imagem do [contentor](https://aka.ms/apim/sputnik/dhub) descarregada do Registo de Contentores da Microsoft.
10. Execute o comando mostrado bellow para verificar se a implantação foi bem sucedida . Note que pode levar algum tempo para que todos os objetos sejam criados e para que o pod(s) seja inicializa.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Execute o comando mostrado bellow para verificar se o serviço foi criado com sucesso. Note que os seus IPs de serviço e portas serão diferentes.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Volte ao portal Azure e selecione **visão geral.**
13. **O estado** que mostra um ícone de marca de verificação verde seguido pela contagem do nó que corresponde ao número de réplicas especificadas no ficheiro YAML confirma que as cápsulas de gateway auto-hospedadas estão a comunicar com sucesso com o serviço de Gestão API e têm um "batimento cardíaco" regular.

![estado de gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Execute <code>kubectl logs deployment/<gateway-name></code> o comando para visualizar os registos de uma cápsula selecionada aleatoriamente se houver mais de um.
> Execute <code>kubectl logs -h</code> para um conjunto completo de opções de comando, por exemplo, como visualizar os registos para uma cápsula ou recipiente específico.

## <a name="production-deployment-considerations"></a>Considerações de implantação de produção

### <a name="access-token"></a>Ficha de acesso
Sem um gateway de acesso válido, não é possível aceder e descarregar a configuração a partir do ponto final de dados de configuração do serviço de Gestão API associado. O token de acesso pode ser válido por um máximo de 30 dias. Deve ser regenerado e configurado o cluster com um token fresco manualmente ou através da automatização antes de expirar. Ao automatizar a atualização token, utilize esta [operação](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) de gestão da API para gerar um novo símbolo. Siga este [link](https://kubernetes.io/docs/concepts/configuration/secret) para obter informações sobre a gestão dos segredos da Kubernetes.

### <a name="namespace"></a>Espaço de nomes
Os espaços [de nome](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes ajudam a dividir um único cluster entre várias equipas, projetos ou aplicações. Os espaços de nome fornecem uma margem de manobra para recursos e nomes e podem ser associados a uma quota de recursos e políticas de controlo de acesso.
Os comandos fornecidos no portal Azure criam recursos de gateway auto-hospedados no espaço de **nome** padrão que é automaticamente criado, existe em todos os clusters, e não pode ser eliminado.
Considere [criar e implantar](https://kubernetesbyexample.com/ns/) porta de entrada auto-hospedada num espaço de nome separado na produção.

### <a name="number-of-replicas"></a>Número de réplicas
O número mínimo de réplicas adequadas na produção é de dois.

Por predefinição, o gateway auto-hospedado é implementado com uma [estratégia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)de implementação **rollingUpdate** . Reveja os valores predefinidos e considere explicitamente a definição de campos [**maxInine**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [**maxSurge,**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) especialmente quando utilizar uma alta contagem de réplicas.

### <a name="container-resources"></a>Recursos de contentores
Por predefinição, o ficheiro YAML fornecido no portal Azure não especifica os pedidos de recursos do contentor.

É impossível prever e recomendar de forma fiável a quantidade de CPU por contentor e recursos de memória e o número de réplicas necessárias para suportar uma carga de trabalho específica devido a muitos fatores em jogo, por exemplo:

- Hardware específico que o cluster está em execução
- Presença e tipo de virtualização
- Número e taxa de conexões de clientes simultâneos
- Taxa de pedido
- Tipo e número de políticas configuradas
- Tamanho da carga útil e se as cargas são tamponadas ou transmitidas
- Latência de serviço backend

Recomendamos definir o pedido de recursos para 2 núcleos e 2 GiB como ponto de partida, realizando um teste de carga e escalando para cima/para fora ou para baixo/em base dos resultados.

### <a name="container-image-tag"></a>Etiqueta de imagem de recipiente
O ficheiro YAML fornecido no portal Azure utiliza a **mais recente** etiqueta que refere sempre a versão mais recente da imagem de porta de entrada auto-hospedada.

Considere utilizar uma etiqueta de versão específica em produção para evitar o upgrade não intencional para uma versão mais recente.

Siga este [link](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) para ver a lista completa das etiquetas disponíveis.

### <a name="dns-policy"></a>Política do DNS
A resolução de nomes DNS desempenha um papel fundamental na capacidade auto-hospedada de gateway de se conectar às dependências em Azure e enviar chamadas da API para backend serviços.

O ficheiro YAML fornecido no portal Azure aplica a política padrão [**clusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) que faz com que os pedidos de resolução de nomes não resolvidos pelo cluster DNS sejam encaminhados para o servidor DNS a montante herdado do nó.

Siga este [link](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) para saber sobre a resolução de nomes em Kubernetes e considere personalizar a [política de DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou a[configuração](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) D NS conforme apropriado para a sua configuração.

### <a name="configuration-backup"></a>Backup de configuração
Siga este [link](self-hosted-gateway-overview.md#connectivity-to-azure) para aprender sobre o comportamento de gateway auto-hospedado na presença de uma falha temporária de conectividade Azure.
Configure o volume de armazenamento local para o recipiente de gateway auto-hospedado, para que possa persistir uma cópia de cópia de reserva da configuração mais recente descarregada e, se a conectividade estiver em baixo, use-o no momento do reinício. O caminho do <code>/apim/config</code>suporte de volume deve ser . Veja um exemplo [aqui.](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)
Para aprender sobre o armazenamento em Kubernetes siga este [link](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Registos e métricas locais
Gateway auto-hospedado envia telemetria para [Azure Monitor](api-management-howto-use-azure-monitor.md) e [Azure Application Insights](api-management-howto-app-insights.md) por configuração no serviço de Gestão API associado.
Quando a [conectividade com o Azure](self-hosted-gateway-overview.md#connectivity-to-azure) é temporariamente perdida, o fluxo de telemetria para Azure é interrompido e os dados perdem-se durante a interrupção.
Considere [a criação de uma monitorização local](how-to-configure-local-metrics-logs.md) para garantir a capacidade de observar o tráfego da API e evitar a perda de telemetria durante as interrupções de conectividade Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management](self-hosted-gateway-overview.md)