---
title: Use um equilibrador de carga pública
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar um equilibrador de carga pública com um SKU Standard para expor os seus serviços com o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5da7f2a11be7562313b709a8af72ccd709165cfa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000866"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Utilize um balanceador de carga padrão público no serviço Azure Kubernetes (AKS)

O Azure Load Balancer encontra-se no L4 do modelo Open Systems Interconnection (OSI) que suporta cenários de entrada e saída. Distribui fluxos de entrada que chegam à extremidade dianteira do balançador de carga para as instâncias da piscina de backend.

Um Balanceador de Carga **público** quando integrado com aKS serve dois propósitos:

1. Para fornecer ligações de saída aos nós de cluster dentro da rede virtual AKS. Alcança este objetivo traduzindo o endereço IP privado dos nós para um endereço IP público que faz parte do seu *Pool Outbound*.
2. Para fornecer acesso a aplicações através dos serviços de tipo Kubernetes `LoadBalancer` . Com ele, pode facilmente escalar as suas aplicações e criar serviços altamente disponíveis.

Um equilibrador de carga **interno (ou privado)** é utilizado onde apenas os IPs privados são permitidos como frontend. Os balançadores de carga internos são utilizados para carregar o tráfego de equilíbrio dentro de uma rede virtual. Um frontend de balançador de carga também pode ser acedido a partir de uma rede no local num cenário híbrido.

Este documento abrange a integração com o balanceador de carga pública. Para a integração interna do balanceador de carga, consulte a documentação do [balançador interno de carga AKS](internal-lb.md).

## <a name="before-you-begin"></a>Before you begin

O Azure Load Balancer está disponível em dois SKUs - *Básico* e *Standard*. Por padrão, *o Standard* SKU é utilizado quando cria um cluster AKS. Utilize o *SKU Standard* para ter acesso a funcionalidades adicionais, como uma piscina de backend maior, [**várias piscinas de nó,**](use-multiple-node-pools.md)e [**Zonas de Disponibilidade.**](availability-zones.md) É o Balancer de Carga recomendado SKU para AKS.

Para obter mais informações sobre os SKUs *básicos* e *padrão,* consulte [a comparação SKU do balanceador de carga Azure][azure-lb-comparison].

Este artigo pressupõe que você tem um cluster AKS com o *Balancer de* carga Standard SKU Azure e anda através de como usar e configurar algumas das capacidades e características do equilibrador de carga. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Se preferir não aproveitar o Balançador de Carga Azure para fornecer ligação de saída e, em vez disso, ter o seu próprio gateway, firewall ou proxy para o efeito, pode saltar a criação da piscina de saída do balanceador de carga e do respetivo FRONTend IP utilizando o [**tipo de saída como UserDefinedRout (UDR)**](egress-outboundtype.md). O tipo de saída define o método de saída para um cluster e não escreve: balanceador de carga.

## <a name="use-the-public-standard-load-balancer"></a>Utilize o equilibrador de carga padrão público

Depois de criar um cluster AKS com Outbound Type: Load Balancer (predefinição), o cluster está pronto a utilizar o equilibrador de carga para expor também os serviços.

Para isso pode criar um serviço público de tipo `LoadBalancer` como mostrado no exemplo seguinte. Comece por criar um manifesto de serviço chamado `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Implemente o manifesto de serviço público utilizando [o kubectl apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

O Azure Load Balancer será configurado com um novo IP público que irá fazer frente a este novo serviço. Uma vez que o Azure Load Balancer pode ter vários IPs frontend, cada novo serviço implantado receberá um novo IP dedicado para ser acedido de forma única.

Pode confirmar que o seu serviço foi criado e o equilibrador de carga é configurado executando, por exemplo:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Quando vê os dados de serviço, o endereço IP público criado para este serviço no equilibrador de carga é mostrado na coluna *EXTERNAL-IP.* Pode levar um minuto ou dois para que o endereço IP mude de *\<pending\>* um endereço IP público real, como mostrado no exemplo acima.

## <a name="configure-the-public-standard-load-balancer"></a>Configure o balanceador de carga padrão público

Ao utilizar o balanceador de carga público Standard SKU, há um conjunto de opções que podem ser personalizadas no momento da criação ou atualizando o cluster. Estas opções permitem-lhe personalizar o Balanceador de Carga para atender às suas necessidades de carga de trabalho e devem ser revistas em conformidade. Com o balanceador de carga Standard pode:

* Definir ou escalar o número de IPs de saída geridos
* Traga os seus próprios [IPs de saída personalizados ou prefixo IP de saída](#provide-your-own-outbound-public-ips-or-prefixes)
* Personalize o número de portas de saída atribuídas a cada nó do cluster
* Configurar a definição de tempo limite para ligações ociosas

> [!IMPORTANT]
> Apenas uma opção IP de saída (IPs geridos, traga o seu próprio IP ou PREfixo IP) pode ser usada num dado momento.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Escalar o número de IPs públicos geridos

O Azure Load Balancer fornece conectividade de saída a partir de uma rede virtual, além de entrada. As regras de saída simplificam a configuração da tradução de endereços de rede de saída do Standard Load Balancer.

Como todas as regras do Balanceador de Carga, as regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada:

***IPs frontend + parâmetros + backend pool** _

Uma regra de saída configura o NAT de saída para todas as máquinas virtuais identificadas pela piscina de backend para serem traduzidas para o frontend. E os parâmetros fornecem um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

Embora uma regra de saída possa ser usada apenas com um único endereço IP público, as regras de saída facilitam o fardo de configuração para a escala de NAT de saída. Você pode usar vários endereços IP para planear cenários de grande escala e você pode usar regras de saída para mitigar padrões propensos à exaustão SNAT. Cada endereço IP adicional fornecido por um frontend fornece portas efémeras de 64k para o Balancer de Carga usar como portas SNAT. 

Ao utilizar um equilibrador de carga SKU _Standard* com IPs públicos de saída geridos, que são criados por padrão, pode escalar o número de IPs públicos geridos de saída utilizando o **`load-balancer-managed-ip-count`** parâmetro.

Para atualizar um cluster existente, executar o seguinte comando. Este parâmetro também pode ser definido no intervalo para ter múltiplos IPs públicos geridos.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos geridos de saída para *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Também pode utilizar o **`load-balancer-managed-ip-count`** parâmetro para definir o número inicial de IPs públicos geridos ao criar o seu cluster, fixando o parâmetro e **`--load-balancer-managed-outbound-ip-count`** definindo-o para o valor pretendido. O número predefinido de IPs públicos geridos é 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Forneça os seus próprios IPs ou prefixos públicos de saída

Quando utiliza um balanceador de carga *Standard* SKU, por predefinição, o cluster AKS cria automaticamente um IP público no grupo de recursos de infraestrutura gerido pela AKS e atribui-o ao pool de saída do balanceador de carga.

Um IP público criado pela AKS é considerado um recurso gerido pela AKS. Isto significa que o ciclo de vida desse IP público destina-se a ser gerido pela AKS e não requer nenhuma ação do utilizador diretamente sobre o recurso IP público. Em alternativa, pode atribuir o seu próprio prefixo IP público personalizado ou ip público no tempo de criação do cluster. Os seus IPs personalizados também podem ser atualizados sobre as propriedades do balanceador de carga existentes.

Requisitos para a utilização do seu próprio IP público ou prefixo:

- Os endereços IP públicos personalizados devem ser criados e propriedade do utilizador. Os endereços IP públicos geridos criados pela AKS não podem ser reutilizados como um IP personalizado, pois pode causar conflitos de gestão.
- Deve garantir que a identidade do cluster AKS (Service Principal ou Identidade Gerida) tem permissões para aceder ao IP de saída. De acordo com a [lista de permissões ip públicas necessárias.](kubernetes-service-principal.md#networking)
- Certifique-se de que cumpre os [requisitos e constrangimentos necessários](../virtual-network/public-ip-address-prefix.md#constraints) para configurar iPs de saída ou prefixos IP de saída.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Atualize o cluster com o seu próprio IP público de saída

Utilize o comando [de exibição público-ip da rede az][az-network-public-ip-show] para listar os IDs dos seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra o ID para o ip público *myPublicIP* no grupo de recursos *myResourceGroup.*

Utilize o `az aks update` comando com o parâmetro para atualizar o seu cluster com os seus **`load-balancer-outbound-ips`** IPs públicos.

O exemplo a seguir utiliza o `load-balancer-outbound-ips` parâmetro com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Atualize o cluster com o seu próprio prefixo IP público de saída

Também pode utilizar prefixos IP públicos para saídas com o seu balanceador de carga *SKU Standard.* O exemplo a seguir utiliza o comando [de programa de prefixo ip da rede az][az-network-public-ip-prefix-show] para listar os IDs dos seus prefixos IP públicos:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra o ID para o prefixo IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup.*

O exemplo a seguir utiliza o parâmetro *de saída-ip-prefixos de carregamento-balanceador* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Crie o cluster com o seu próprio IP público ou prefixos

Pode desejar trazer os seus próprios endereços IP ou prefixos IP para saídas no tempo de criação de clusters para suportar cenários como adicionar pontos finais de saídas a uma lista de autorizações. Anexar os mesmos parâmetros acima mostrados ao seu passo de criação de cluster para definir os seus próprios iPs públicos e prefixos IP no início do ciclo de vida de um cluster.

Utilize os *az aks criar* comando com o parâmetro *de saída ips de saída de equilíbrio de carga* para criar um novo cluster com os seus IPs públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Utilize o *az aks criar* comando com o parâmetro *de pré-prefixos de saída ip-out para* criar um novo cluster com os seus prefixos IP públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Configure os portos de saída atribuídos

> [!IMPORTANT]
> Se tiver aplicações no seu cluster que se espera que estabeleçam um grande número de ligações a um pequeno conjunto de destinos, por exemplo. muitas instâncias frontend conectando-se a um SQL DB, você tem um cenário muito suscetível de encontrar a exaustão do Porto SNAT (ficar sem portas para ligar). Para estes cenários é altamente recomendado aumentar as portas de saída atribuídas e os IPs frontend de saída no equilibrador de carga. O aumento deve considerar que um (1) endereço IP adicional adiciona 64k portas adicionais para distribuir por todos os nós de cluster.


Salvo especificação em contrário, a AKS utilizará o valor predefinido das portas de saída atribuídas que o Balanceador de Carga Padrão define ao configurar. Este valor é **nulo** na API AKS ou **0** na API SLB, como mostrado pelo comando abaixo:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Os comandos anteriores listarão a regra de saída para o seu equilibrador de carga, por exemplo:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Esta saída não significa que você tem 0 portas, mas em vez disso você está aproveitando a atribuição automática de porta de saída com base no tamanho da [piscina de backend][azure-lb-outbound-preallocatedports], por isso, por exemplo, se um cluster tem 50 ou menos nós, 1024 portas para cada nó são atribuídas, à medida que você aumenta o número de nós a partir daí você gradualmente obterá menos portas por nó.


Para definir ou aumentar o número de portas de saída atribuídas, pode seguir o exemplo abaixo:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Este exemplo dar-lhe-ia 4000 portas de saída atribuídas para cada nó no meu cluster, e com 7 IPs teria *4000 portas por nó * 100 nós = 400 k de portas totais < = 448k portas totais = 7 IPs * portas de 64k por IP*. Isto permitir-lhe-ia escalar com segurança para 100 nós e ter uma operação de atualização predefinido. É fundamental atribuir portas suficientes para os nós adicionais necessários para a atualização e outras operações. AKS predefine um nó tampão para upgrade, neste exemplo isto requer 4000 portas livres em qualquer momento. Se utilizar [valores maxSurge,](upgrade-cluster.md#customize-node-surge-upgrade)multiplique as portas de saída por nó pelo seu valor máximoSurge.

Para ultrapassar com segurança 100 nós, teria de adicionar mais IPs.


> [!IMPORTANT]
> Tem de [calcular a sua quota necessária e verificar os requisitos][requirements] antes de personalizar os *OutboundPorts atribuídos* para evitar problemas de conectividade ou de escala.

Também pode utilizar os **`load-balancer-outbound-ports`** parâmetros ao criar um cluster, mas também deve especificar qualquer **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** um, ou **`load-balancer-outbound-ip-prefixes`** também.  Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Configure o tempo limite de marcha lenta ocioso do balançador de carga

Quando os recursos portuários SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes libertem as portas SNAT. O Balancer de Carga recupera as portas SNAT quando o fluxo se fecha e o balançador de carga configurado pela AKS utiliza um tempo limite de 30 minutos para recuperar as portas SNAT dos fluxos inativos.
Também pode utilizar o transporte (por exemplo, **`TCP keepalives`** ) ou para refrescar um fluxo **`application-layer keepalives`** inativo e repor este tempo de inatividade, se necessário. Pode configurar este intervalo seguindo o exemplo abaixo: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Se você espera ter numerosas ligações de curta duração, e nenhuma conexão que seja longa e que possa ter longos períodos de inatividade, como alavancar `kubectl proxy` ou considerar usar um baixo valor de tempo `kubectl port-forward` limite, como 4 minutos. Além disso, quando se utilizam keepalives TCP, é suficiente para os ativar de um lado da ligação. Por exemplo, é suficiente para permitir que eles no lado do servidor apenas para redefinir o temporizador inativo do fluxo e não é necessário para ambos os lados iniciar keepalives TCP. Existem conceitos semelhantes para a camada de aplicação, incluindo configurações de servidor de clientes de base de dados. Verifique o lado do servidor para saber quais as opções existentes para manter a aplicação específica.

> [!IMPORTANT]
> O AKS permite o Reset TCP on ocioso por predefinição e recomenda que mantenha esta configuração e a aproveite para um comportamento de aplicação mais previsível nos seus cenários.
> O TCP RST só é enviado durante a ligação TCP no estado estabelecido. Leia mais sobre o assunto [aqui](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Requisitos para personalizar portas de saída atribuídas e tempo limite de marcha lenta

- O valor que especifica para *os Portos Outbound atribuídos* também deve ser um múltiplo de 8.
- Você deve ter capacidade IP de saída suficiente com base no número de seus VMs de nó e portas de saída alocadas necessárias. Para validar tem capacidade IP de saída suficiente, utilize a seguinte fórmula: 
 
*saídaIPs* \* 64.000 \> *nodeVMs* \* *desejados Portos De Saída.*
 
Por exemplo, se tiver 3 *nodeVMs*, e 50.000 *Portos De Saída De Saída desejados,* precisa de ter pelo menos 3 *Blocos de Saída*. Recomenda-se que incorpore capacidade ip adicional de saída para além do que precisa. Além disso, você deve ter em conta o autoescalador do cluster e a possibilidade de atualizações de piscina de nó ao calcular a capacidade ip de saída. Para o autoescalador do cluster, reveja a contagem de nós corrente e a contagem máxima do nó e utilize o valor mais elevado. Para a atualização, contabiliza um VM adicional de nó para cada piscina de nó que permite a atualização.

- Ao configurar *o IdleTimeoutInMinutes* para um valor diferente do padrão de 30 minutos, considere quanto tempo as suas cargas de trabalho precisarão de uma ligação de saída. Considere também o valor de tempo limite padrão para um balanceador de carga *Standard* SKU usado fora de AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflita com mais precisão a sua carga de trabalho específica da AKS pode ajudar a diminuir a exaustão do SNAT causada pela ligação que já não é utilizada.

> [!WARNING]
> Alterar os valores para *AlocedOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o seu balançador de carga e não deve ser feito de ânimo leve, sem compreender as compensações e os padrões de ligação da sua aplicação, consulte [a secção de resolução de problemas do SNAT abaixo][troubleshoot-snat] e reveja as regras de saída do [Balancer de Carga][azure-lb-outbound-rules-overview] e as [ligações de saída em Azure][azure-lb-outbound-connections] antes de atualizar estes valores para entender completamente o impacto das suas alterações.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Restringir o tráfego de entrada a gamas IP específicas

O manifesto a seguir utiliza *loadBalancerSourceRanges* para especificar uma nova gama IP para tráfego externo de entrada:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> A entrada, o tráfego externo flui do equilibrador de carga para a rede virtual para o seu cluster AKS. A rede virtual tem um Grupo de Segurança de Rede (NSG) que permite todo o tráfego de entrada a partir do balançador de carga. Este NSG utiliza uma etiqueta de [serviço][service-tags] do tipo *LoadBalancer* para permitir o tráfego do equilibrador de carga.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Mantenha o IP do cliente em ligações de entrada

Por padrão, um serviço de tipo `LoadBalancer` [em Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) e em AKS não persistirá o endereço IP do cliente na ligação ao pod. O IP de origem no pacote que é entregue na cápsula será o IP privado do nó. Para manter o endereço IP do cliente, tem de estar definido `service.spec.externalTrafficPolicy` `local` na definição de serviço. O manifesto que se segue mostra um exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Personalizações adicionais através de anotações de Kubernetes

Abaixo está uma lista de anotações suportadas para serviços Kubernetes com `LoadBalancer` tipo, estas anotações aplicam-se apenas aos fluxos **INBOUND:**

| Anotação | Valor | Descrição
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` ou `false`                     | Especificar se o balançador de carga deve ser interno. É indefinitivo para o público, se não definido.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nome da sub-rede                    | Especificar a que sub-rede o balançador interno de carga deve estar ligado. Está a falhar na sub-rede configurada em ficheiro de configuração de nuvem se não estiver definida.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nome da etiqueta DNS em IPs públicos   | Especifique o nome da etiqueta DNS para o serviço **público.** Se estiver definido para cadeias vazias, a entrada DNS no IP Público não será utilizada.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` ou `false`                     | Especifique que o serviço deve ser exposto usando uma regra de segurança Azure que pode ser partilhada com outro serviço, comercializando a especificidade das regras para um aumento do número de serviços que podem ser expostos. Esta anotação baseia-se na funcionalidade Azure [Aumentada Security Rules](../virtual-network/network-security-groups-overview.md#augmented-security-rules) dos grupos de Segurança da Rede. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nome do grupo de recursos            | Especificar o grupo de recursos de IPs públicos do balanceador de carga que não estão no mesmo grupo de recursos que a infraestrutura de cluster (grupo de recursos de nó).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista de etiquetas de serviço permitidas          | Especifique uma lista de etiquetas de [serviço permitidas separadas][service-tags] por vírgula.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Intervalos de tempo inativos da TCP em minutos          | Especifique a hora, em minutos, para que os intervalos de inatividade da ligação TCP ocorram no balançador de carga. O padrão e o valor mínimo são 4. O valor máximo é de 30. Deve ser um inteiro.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | `enableTcpReset`Desativar para SLB


## <a name="troubleshooting-snat"></a>SNAT de resolução de problemas

Se sabe que está a iniciar muitas ligações TCP ou UDP de saída para o mesmo endereço IP de destino e porta, e observa ligações de saída falhadas ou é aconselhado por suporte a que está a esgotar as portas SNAT (portas efémeras pré-locadas utilizadas pela PAT), tem várias opções gerais de mitigação. Reveja estas opções e decida o que está disponível e o melhor para o seu cenário. É possível que um ou mais possam ajudar a gerir este cenário. Para obter informações detalhadas, reveja o [Guia de Resolução de Problemas de Ligações de Saída](../load-balancer/troubleshoot-outbound-connection.md).

Frequentemente, a causa principal da exaustão do SNAT é um anti-padrão para a forma como a conectividade de saída é estabelecida, gerida ou configurável dos seus valores padrão. Leia esta secção atentamente.

### <a name="steps"></a>Passos
1. Verifique se as suas ligações permanecem inativas durante muito tempo e confie no tempo limite de marcha lenta padrão para libertar essa porta. Se assim for, o tempo limite de 30 min pode ter de ser reduzido para o seu cenário.
2. Investigue como a sua aplicação está a criar conectividade de saída (por exemplo, revisão de código ou captura de pacotes).
3. Determine se esta atividade é um comportamento esperado ou se a aplicação está a portar-se mal. Utilize [métricas](../load-balancer/load-balancer-standard-diagnostics.md) e [registos](../load-balancer/load-balancer-monitor-log.md) no Azure Monitor para fundamentar as suas descobertas. Utilize a categoria "Falhado" para a métrica de ligações SNAT, por exemplo.
4. Avalie se os [padrões adequados são seguidos.](#design-patterns)
5. Avaliar se o esgotamento da porta SNAT deve ser atenuado com [endereços IP adicionais de saída + portas de saída adicionais atribuídas](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Padrões de estrutura
Aproveite sempre que possível a reutilização da ligação e a ligação. Estes padrões evitarão problemas de exaustão de recursos e resultarão em comportamentos previsíveis. Os primitivos para estes padrões podem ser encontrados em muitas bibliotecas e estruturas de desenvolvimento.

- Os pedidos atómicos (um pedido por ligação) geralmente não são uma boa escolha de design. Tal escala de limites anti-padrão, reduz o desempenho e diminui a fiabilidade. Em vez disso, reutilizar as ligações HTTP/S para reduzir o número de ligações e portas SNAT associadas. A escala de aplicação aumentará e o desempenho melhorará devido à redução dos apertos de mão, sobrecarga e custo de operação criptográfico ao utilizar o TLS.
- Se estiver a utilizar DNS de cluster/personalizado, ou servidores a montante personalizados no coreDNS têm em mente que o DNS pode introduzir muitos fluxos individuais em volume quando o cliente não está a caching o resultado dos resolvers DNS. Certifique-se de personalizar o coreDNS primeiro em vez de usar servidores DNS personalizados e definir um bom valor de caching.
- Os fluxos de UDP (por exemplo, pesquisas de DNS) alocam portas SNAT durante o tempo limite de marcha lenta. Quanto mais tempo o tempo de pressão, maior a pressão sobre as portas SNAT. Utilize um tempo curto de marcha lenta (por exemplo, 4 minutos).
Utilize piscinas de ligação para moldar o seu volume de ligação.
- Nunca abandone silenciosamente um fluxo de TCP e confie nos temporizadores TCP para limpar o fluxo. Se não deixar a TCP fechar explicitamente a ligação, o estado permanece alocado em sistemas intermédios e pontos finais e torna as portas SNAT indisponíveis para outras ligações. Este padrão pode desencadear falhas de aplicação e exaustão SNAT.
- Não altere os valores de temporizador de proximidade de nível de OS sem conhecimento especializado do impacto. Enquanto a stack TCP vai recuperar, o desempenho da sua aplicação pode ser negativamente afetado quando os pontos finais de uma ligação têm expectativas desajustadas. Querer mudar os temporizadores é geralmente um sinal de um problema de design subjacente. Reveja as recomendações.


O exemplo acima atualiza a regra para permitir apenas o tráfego externo de entrada a partir da gama *MY_EXTERNAL_IP_RANGE.* Se substituir *MY_EXTERNAL_IP_RANGE* pelo endereço IP da sub-rede interna, o tráfego é restrito apenas aos IPs internos do cluster. Isto não permitirá que clientes de fora do seu cluster Kubernetes acedam ao equilibrador de carga.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passando de um balanceador de carga SKU básico para SKU padrão

Se tiver um cluster existente com o Balanceador de Carga Básico SKU, existem diferenças comportamentais importantes a notar ao migrar para usar um cluster com o Balancer de Carga SKU Padrão.

Por exemplo, fazer implantações azuis/verdes para migrar clusters é uma prática comum dado que o `load-balancer-sku` tipo de cluster só pode ser definido no cluster criar tempo. No entanto, os balanceadores *básicos de* carga SKU utilizam endereços *IP Básicos SKU,* que não são compatíveis com os Balanceadores de Carga *SKU padrão,* uma vez que requerem endereços *IP Standard SKU.* Ao migrar clusters para atualizar os SKUs do Balancer de Carga, será necessário um novo endereço IP com um endereço IP compatível SKU.

Para mais considerações sobre como migrar aglomerados, visite a [nossa documentação sobre considerações migratórias](aks-migration.md) para ver uma lista de tópicos importantes a ter em conta na migração. As limitações abaixo também são diferenças comportamentais importantes a notar ao usar balanceadores de carga padrão SKU em AKS.

## <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS que suportam um equilibrador de carga com o *SKU padrão:*

* Pelo menos um prefixo IP ou IP público é necessário para permitir o tráfego de saída do cluster AKS. O prefixo IP ou IP público também é necessário para manter a conectividade entre o plano de controlo e os nós do agente e para manter a compatibilidade com as versões anteriores da AKS. Tem as seguintes opções para especificar iPs públicos ou prefixos IP com um balanceador de carga *SKU standard:*
    * Forneça os seus próprios iPs públicos.
    * Forneça os seus próprios prefixos IP públicos.
    * Especifique um número até 100 para permitir que o cluster AKS crie muitos IPs públicos *Standard* SKU no mesmo grupo de recursos criado como o cluster AKS, que é geralmente nomeado com *MC_* no início. AKS atribui o IP público ao *balanceador de* carga Standard SKU. Por padrão, um IP público será automaticamente criado no mesmo grupo de recursos que o cluster AKS, se não for especificado nenhum IP público, prefixo IP público ou número de IPs. Também deve permitir endereços públicos e evitar criar qualquer Política de Azure que proíba a criação de IP.
* Um IP público criado pela AKS não pode ser reutilizado como um endereço IP público. Todos os endereços IP personalizados devem ser criados e geridos pelo utilizador.
* A definição do balanceador de carga SKU só pode ser feita quando se cria um cluster AKS. Não é possível alterar o balanceador de carga SKU depois de ter sido criado um cluster AKS.
* Só pode utilizar um tipo de balanceador de carga SKU (Básico ou Standard) num único cluster.
* *Padrão* Os balançadores de carga SKU suportam apenas endereços IP *Standard* SKU.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços da Kubernetes na documentação dos [serviços kubernetes.][kubernetes-services]

Saiba mais sobre a utilização do Balançador de Carga Interna para o tráfego de entrada na [documentação do balançador interno de carga da AKS](internal-lb.md).

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
