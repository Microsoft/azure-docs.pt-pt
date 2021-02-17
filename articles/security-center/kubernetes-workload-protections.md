---
title: Proteções de carga de trabalho para as suas cargas de trabalho Kubernetes
description: Saiba como usar o conjunto de recomendações de segurança da carga de trabalho do Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: f024457ed9f20e6bab0852a53ab6a60ac85ed25a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546266"
---
# <a name="protect-your-kubernetes-workloads"></a>Proteger cargas de trabalho do Kubernetes

Esta página descreve como usar o conjunto de recomendações de segurança do Azure Security Center dedicadas à proteção da carga de trabalho de Kubernetes.

Saiba mais sobre estas funcionalidades na [proteção da carga de trabalho as melhores práticas utilizando o controlo de admissão de Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

O Security Center oferece mais funcionalidades de segurança para contentores se ativar o Azure Defender. Especificamente:

- Verifique os seus registos de contentores para obter vulnerabilidades com [o Azure Defender para registos de contentores](defender-for-container-registries-introduction.md)
- Obtenha alertas de deteção de ameaças em tempo real para os seus clusters K8s [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Para obter uma lista de *todas as* recomendações de segurança que possam aparecer para os clusters e nós de Kubernetes, consulte a secção de [cálculo](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.



## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Administrador de proprietário** ou **segurança** para editar uma atribuição<br>**Leitor** para ver as recomendações|
|Requisitos ambientais:|Kubernetes v1.14 (ou superior) é necessário<br>Sem recurso PodSecurityPolicy (modelo antigo da PSP) nos clusters<br>Os nómadas do Windows não são suportados|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="set-up-your-workload-protection"></a>Configurar a sua proteção de carga de trabalho

O Azure Security Center inclui um conjunto de recomendações que estão disponíveis quando instalou o **addon Azure Policy para Kubernetes.**

### <a name="step-1-deploy-the-add-on"></a>Passo 1: Implementar o add-on

Para configurar as recomendações, instale o  **addon Azure Policy para Kubernetes**. 

- Pode implementar automaticamente este addon como explicado no [Enable auto provisioning de extensões](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions). Quando o fornecimento automático para o addon é definido como "on", a extensão é ativada por padrão em todos os clusters existentes e futuros (que satisfazem os requisitos de instalação adicionais).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Utilizar a ferramenta de provisionamento automático do Security Center para instalar o complemento de política para Kubernetes":::

- Para implementar manualmente o addon:

    1. Na página de recomendações, procure a recomendação "**Azure Policy add-on for Kubernetes deve ser instalado e ativado nos seus clusters**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Recomendação **Azure Policy add-on for Kubernetes deve ser instalado e ativado nos seus clusters**":::

        > [!TIP]
        > A recomendação está incluída em cinco controlos de segurança diferentes e não importa qual selecione no passo seguinte.

    1. A partir de qualquer um dos controlos de segurança, selecione a recomendação para ver os recursos em que pode instalar o add-on.
    1. Selecione o cluster relevante e **Remedia.**

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="A página de detalhes da recomendação para o addon da Política de Azure para Kubernetes deve ser instalada e ativada nos seus clusters**":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Passo 2: Ver e configurar o pacote de 13 recomendações

1. Aproximadamente 30 minutos após o fim da instalação adicional, o Centro de Segurança mostra o estado de saúde dos clusters para as seguintes recomendações, cada uma no controlo de segurança relevante, conforme mostrado:

    > [!TIP]
    > Algumas recomendações têm parâmetros que devem ser personalizados através da Azure Policy para usá-las de forma eficaz. Por exemplo, para beneficiar da recomendação As **imagens do Contentor devem ser implementadas apenas a partir de registos fidedignos,** terá de definir os seus registos fidedignos.
    > 
    > Se não introduzir os parâmetros necessários para as recomendações que requerem configuração, as suas cargas de trabalho serão mostradas como pouco saudáveis.

    | Nome da recomendação                                                         | Controlo de segurança                         | Configuração necessária |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | CpU do contentor e limites de memória devem ser aplicados                          | Proteja as aplicações contra o ataque do DDoS | Não                     |
    | Recipientes privilegiados devem ser evitados                                     | Gerir acessos e permissões            | Não                     |
    | O sistema de ficheiros de raiz imutável (apenas para leitura) deve ser aplicado para os contentores     | Gerir acessos e permissões            | Não                     |
    | Contentor com escalada de privilégio deve ser evitado                       | Gerir acessos e permissões            | Não                     |
    | Os recipientes de funcionamento como utilizador de raiz devem ser evitados                           | Gerir acessos e permissões            | Não                     |
    | Os recipientes que partilham espaços sensíveis de nome de hospedeiro devem ser evitados              | Gerir acessos e permissões            | Não                     |
    | Capacidades linux menos privilegiadas devem ser aplicadas para contentores       | Gerir acessos e permissões            | **Sim**                |
    | O uso dos suportes de volume pod HostPath deve ser restringido a uma lista conhecida    | Gerir acessos e permissões            | **Sim**                |
    | Os contentores devem ouvir apenas as portas permitidas                              | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Os serviços devem ouvir apenas os portos permitidos                                | Restringir o acesso não autorizado à rede     | **Sim**                |
    | A utilização da rede de acolhimento e das portas deve ser restringida                     | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Sobrevaor ou desativação de contentores O perfil appArmor deve ser restringido | Remediar configurações de segurança        | **Sim**                |
    | As imagens dos contentores devem ser implantadas apenas a partir de registos fidedignos            | Remediar vulnerabilidades                | **Sim**                |
    |||


1. Para as recomendações com parâmetros devem ser personalizadas, desagram os parâmetros:

    1. A partir do menu do Security Center, selecione **a política de segurança.**
    1. Selecione a subscrição relevante.
    1. A partir da secção **de política de padrão do Centro de Segurança,** selecione **Ver a política eficaz**.
    1. Selecione "ASC Predefinido".
    1. Abra o **separador Parâmetros** e modifique os valores conforme necessário.
    1. Selecione **Review + guardar**.
    1. Selecione **Guardar**.


1. Para impor qualquer uma das recomendações, 

    1. Abra a página de detalhes da recomendação e selecione **Deny**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Negar opção para parâmetro da Política Azure":::

        Isto abrirá o painel onde definirá o âmbito. 

    1. Quando definir o âmbito, selecione **Alterar para negar**.

1. Para ver quais recomendações se aplicam aos seus clusters:

    1. A página de inventário de [ativos](asset-inventory.md) do Open Security Center e use o filtro de tipo de recurso para **os serviços kubernetes.**

    1. Selecione um cluster para investigar e rever as recomendações disponíveis para o mesmo. 

1. Ao visualizar uma recomendação do conjunto de proteção da carga de trabalho, verá o número de cápsulas afetadas ("componentes Kubernetes") listados ao lado do cluster. Para obter uma lista das cápsulas específicas, selecione o cluster e, em seguida, **selecione Ação**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Visualização das cápsulas afetadas para uma recomendação de K8s"::: 

1. Para testar a execução, utilize as duas implementações de Kubernetes abaixo:

    - Uma delas é uma implantação saudável, em conformidade com o pacote de recomendações de proteção da carga de trabalho.
    - A outra destina-se a uma implantação pouco saudável, incompatível com *qualquer* uma das recomendações.

    Desloque o exemplo .yaml files as-is, ou use-os como referência para remediar a sua própria carga de trabalho (passo VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Exemplo de implementação saudável .yaml

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Exemplo de implementação pouco saudável .yaml

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a proteção da carga de trabalho da Kubernetes. 

Para outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações do Centro de Segurança para o cálculo](recommendations-reference.md#recs-compute)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de hospedeiro de contentores](alerts-reference.md#alerts-containerhost)