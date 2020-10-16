---
title: Configuração do armazenamento
description: Explica opções de configuração de armazenamento de serviços de dados ativadas Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c420652a6385be2cade9723c20cff7c32a4a60b0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127238"
---
# <a name="storage-configuration"></a>Configuração do Armazenamento

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Conceitos de armazenamento de Kubernetes

Kubernetes fornece uma camada de abstração de infraestrutura sobre a pilha de tecnologia de virtualização subjacente (opcional) e hardware. A forma como a Kubernetes abstrata o armazenamento fora é através **[das Aulas de Armazenamento.](https://kubernetes.io/docs/concepts/storage/storage-classes/)** No momento do fornecimento de uma vagem, pode especificar-se uma classe de armazenamento para cada volume. No momento em que a cápsula é alocada, o **[provisionador](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** de classe de armazenamento é chamado a providenciar o armazenamento e, em seguida, um **[volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** é criado nesse armazenamento provisionado e, em seguida, a vagem é montada ao volume persistente por uma **[reivindicação persistente de volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

A Kubernetes fornece uma forma de os fornecedores de infraestruturas de armazenamento ligarem os condutores (também chamados "Addons") que estendem a Kubernetes. Os addons de armazenamento devem estar em conformidade com a **[norma da Interface de Armazenamento do Contentor](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Existem dezenas de addons que podem ser encontrados nesta lista não definitiva **[de motoristas de CSI](https://kubernetes-csi.github.io/docs/drivers.html)**. O controlador CSI que utiliza dependerá de fatores como se estiver a executar um serviço Kubernetes gerido na nuvem ou qual o fornecedor OEM que está a usar para o seu hardware.

Pode ver quais as aulas de armazenamento configuradas no seu cluster Kubernetes executando este comando:

``` terminal
kubectl get storageclass
```

Saída de exemplo de um cluster Azure Kubernetes Service (AKS):

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Você pode obter detalhes sobre uma classe de armazenamento executando este comando:

``` terminal
kubectl describe storageclass\<storage class name>
```

Exemplo:

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Pode ver os volumes persistentes atualmente previstos e as reivindicações de volume persistentes executando os seguintes comandos:

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Exemplo de mostrar volumes persistentes:

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Exemplo de alegações persistentes de volume:

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Fatores a ter em conta na escolha da sua configuração de armazenamento

A seleção da classe de armazenamento certa é muito importante para a resiliência e desempenho dos dados. Escolher a classe de armazenamento errada pode colocar os seus dados em risco de perda total de dados em caso de falha de hardware ou pode resultar num desempenho menos ideal.

Existem geralmente dois tipos de armazenamento:

- **Armazenamento local** - armazenamento que é a provisionado em discos rígidos locais em um dado nó. Este tipo de armazenamento pode ser ideal em termos de desempenho, mas requer especificamente a conceção para redundância de dados replicando os dados em vários nós.
- **Armazenamento remoto e compartilhado** - armazenamento que é abastado em algum dispositivo de armazenamento remoto - por exemplo, um serviço de armazenamento de SAN, NAS ou cloud como EBS ou Azure Files. Este tipo de armazenamento geralmente fornece redundância de dados automaticamente, mas geralmente não é tão rápido quanto o armazenamento local pode ser.

> [!NOTE]
> Por enquanto, se estiver a utilizar o NFS, tem de definir o modo de permitir que oRunAsRoot seja verdadeiro no seu ficheiro de perfil de implementação antes de implementar o controlador de dados Azure Arc.

### <a name="data-controller-storage-configuration"></a>Configuração de armazenamento de controlador de dados

Alguns serviços em Azure Arc para serviços de dados dependem de serem configurados para utilizar armazenamento remoto e compartilhado porque os serviços não têm a capacidade de replicar os dados. Estes serviços encontram-se na recolha de cápsulas de tratamento de dados:

|**Serviço**|**Reclamações persistentes de volume**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**AfluxoDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Exemplo SQL controlador**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Serviço API controlador**|`<namespace>/data-controller`|

No momento em que o controlador de dados é a provisionado, a classe de armazenamento a utilizar para cada um destes volumes persistentes é especificada através da passagem da classe --armazenamento / -sc parâmetro para o `azdata arc dc create` comando ou definindo as classes de armazenamento no control.jsno ficheiro de modelo de implantação que é usado.

Os modelos de implementação fornecidos fora da caixa têm uma classe de armazenamento padrão especificada que é apropriada para o ambiente alvo, mas pode ser ultrapassada no tempo de implementação. Consulte os passos detalhados para [alterar o perfil de implementação](create-data-controller.md) para alterar a configuração da classe de armazenamento para as cápsulas do controlador de dados no momento de implementação.

Se definir a classe de armazenamento usando a classe de armazenamento / -sc parâmetro a classe de armazenamento será usada tanto para as classes de registo como para armazenamento de dados. Se definir as classes de armazenamento no ficheiro do modelo de implementação, pode especificar diferentes classes de armazenamento para registos e dados.

Fatores importantes a ter em conta na escolha de uma classe de armazenamento para as cápsulas de tratamento de dados:

- Deve **must** utilizar uma classe de armazenamento remota e partilhada para garantir a durabilidade dos dados e para que, se uma vagem ou nó morrer, quando a cápsula é trazida de volta, pode voltar a ligar-se ao volume persistente.
- Os dados que estão a ser escritos para o controlador SQL, métricas DB e logs DB são tipicamente de volume bastante baixo e não são sensíveis à latência, pelo que o armazenamento de desempenho ultrarrápido não é crítico. Se tiver utilizadores que usam frequentemente as interfaces Grafana e Kibana e tem um grande número de casos de base de dados, então os seus utilizadores poderão beneficiar de um armazenamento mais rápido.
- A capacidade de armazenamento necessária é variável com o número de casos de base de dados que implementou porque os registos e métricas são recolhidos para cada instância da base de dados. Os dados são retidos nos registos e métricas DB durante 2 semanas antes de serem purgados. 
- Mudar a colocação de posto de classe de armazenamento é muito difícil, não documentado e não suportado. Certifique-se de que escolhe a classe de armazenamento corretamente no momento de implantação.

> [!NOTE]
> Se não for especificada nenhuma classe de armazenamento, a classe de armazenamento predefinida será utilizada. Pode haver apenas uma classe de armazenamento padrão por cluster Kubernetes. Pode [alterar a classe de armazenamento predefinido](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Configuração de armazenamento de casos de dados de base de dado

Cada instância da base de dados tem dados, registos e volumes persistentes de backup. As classes de armazenamento para estes volumes persistentes podem ser especificadas no momento da implementação. Se não for especificada nenhuma classe de armazenamento, a classe de armazenamento predefinida será utilizada.

Ao criar uma instância utilizando `azdata arc sql mi create` ou `azdata arc postgres server create` comandos, existem dois parâmetros que podem ser usados para definir as classes de armazenamento:

> [!NOTE]
> Alguns destes parâmetros estão em desenvolvimento e ficarão disponíveis `azdata arc sql mi create` `azdata arc postgres server create` nos próximos lançamentos.

|Nome do parâmetro, nome curto|Utilizado para|
|---|---|
|`--storage-class-data`, `-scd`|Usado para especificar a classe de armazenamento para todos os ficheiros de dados, incluindo ficheiros de registo de transações|
|`--storage-class-logs`, `-scl`|Usado para especificar a classe de armazenamento para todos os ficheiros de registo|
|`--storage-class-data-logs`, `-scdl`|Usado para especificar a classe de armazenamento para os ficheiros de registo de transações de base de dados. **Nota: Ainda não está disponível.**|
|`--storage-class-backups`, `-scb`|Usado para especificar a classe de armazenamento para todos os ficheiros de reserva. **Nota: Ainda não está disponível.**|

A tabela abaixo lista os caminhos dentro do recipiente Azure SQL Managed Instance que é mapeado para o volume persistente de dados e registos:

|Nome do parâmetro, nome curto|Caminho dentro do recipiente mssql-miaa|Descrição|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Contém diretórios para a instalação de mssql e outros processos do sistema. O diretório mssql contém dados predefinidos (incluindo registos de transações), registo de erros & diretórios de backup|
|`--storage-class-logs`, `-scl`|/var/log|Contém diretórios que armazenam a saída da consola (stderr, stdout), outras informações de registo de processos dentro do contentor|

A tabela abaixo lista os caminhos dentro do recipiente de instância PostgreSQL que é mapeado para o volume persistente para dados e registos:

|Nome do parâmetro, nome curto|Caminho dentro do recipiente postgres|Descrição|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Contém dados e diretórios de registo para a instalação de postgres|
|`--storage-class-logs`, `-scl`|/var/log|Contém diretórios que armazenam a saída da consola (stderr, stdout), outras informações de registo de processos dentro do contentor|

Cada instância da base de dados terá um volume persistente separado para ficheiros de dados, registos e backups. Isto significa que haverá separação do E/S para cada um destes tipos de ficheiros sujeitos à forma como o provisionador de volume irá providenciar armazenamento. Cada caso de base de dados tem as suas próprias reivindicações de volume persistentes e volumes persistentes.

Se existirem várias bases de dados numa determinada caixa de dados, todas as bases de dados utilizarão a mesma reivindicação persistente de volume, volume persistente e classe de armazenamento. Todas as cópias de segurança - tanto as cópias de segurança diferenciais como as cópias de segurança completas utilizarão a mesma reivindicação persistente de volume e volume persistente. Os pedidos de volume persistentes para as cápsulas de instância da base de dados são apresentados abaixo:

|**Instância**|**Reclamações persistentes de volume**|
|---|---|
|**Instância Gerida do SQL no Azure**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Base de dados Azure para instância pós-SQL**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Hiperescala Azure PostgresqL**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Ordinal varia de 0 a W onde W é o número de trabalhadores)*|

Fatores importantes a ter em conta na escolha de uma classe de armazenamento para as cápsulas de caso de base de dados:

- As instâncias da base de dados podem ser implantadas num único padrão de vagem ou num padrão de vagem múltipla. Um exemplo de um único padrão de vagem é uma instância de desenvolvimento de exemplo gerido Azure SQL ou um caso de preço geral de preço Azure SQL gerido. Um exemplo de um padrão de vagem múltipla é um caso de gestão de preços críticos de negócio altamente disponíveis Azure SQL. (Nota: os níveis de preços estão em desenvolvimento e ainda não estão disponíveis para os clientes.)  As instâncias de base de dados implementadas com o padrão de vagem única **devem** utilizar uma classe de armazenamento remota e partilhada para garantir a durabilidade dos dados e para que, se uma vagem ou nó morrer, quando a cápsula é trazida de volta, pode ligar-se novamente ao volume persistente. Em contraste, um exemplo gerido pelo Azure SQL altamente disponível utiliza grupos always on availability para replicar os dados de um caso para outro, sincronizado ou assíncronamente. Especialmente no caso em que os dados são replicados sincronizadamente, há sempre várias cópias dos dados - tipicamente 3 cópias. Por isso, é possível utilizar aulas de armazenamento local ou remotas, partilhadas para dados e ficheiros de registo. No caso de utilização do armazenamento local, os dados ainda são preservados mesmo no caso de uma vagem, nó ou hardware de armazenamento falhados. Dada esta flexibilidade, poderá optar por utilizar o armazenamento local para um melhor desempenho.
- O desempenho da base de dados é em grande parte uma função da produção de E/S de um determinado dispositivo de armazenamento. Se a sua base de dados for leituras pesadas ou escritas pesadas, então deve escolher uma classe de armazenamento que tenha hardware por baixo que seja projetado para esse tipo de carga de trabalho. Por exemplo, se a sua base de dados for usada principalmente para escritas, poderá escolher o armazenamento local com RAID 0. Se a sua base de dados for utilizada principalmente para leituras de uma pequena quantidade de "dados quentes", mas existe um grande volume global de armazenamento de dados frios, então pode escolher um dispositivo SAN capaz de armazenamento hierárquico. Escolher a classe de armazenamento certa não é muito diferente de escolher o tipo de armazenamento que usaria para qualquer base de dados.
- Se estiver a utilizar um provisionador local de volume de armazenamento, deve certificar-se de que os volumes locais que estão previstos para dados, registos e cópias de segurança estão cada um a aterrar em diferentes dispositivos de armazenamento subjacentes para evitar a contenção na E/S do disco. O SO também deve estar num volume montado num ou num disco separado. Esta é essencialmente a mesma orientação que seria seguida para uma instância de base de dados sobre hardware físico.
- Uma vez que todas as bases de dados de um dado caso partilham uma reclamação persistente de volume e volume persistente, certifique-se de não apresentar casos de bases de dados ocupados na mesma caixa de dados. Se possível, separe as bases de dados ocupadas nas suas próprias situações de base de dados para evitar a contenção de E/S. Além disso, utilize a etiqueta de nó direcionada para as instâncias da base de dados terrestres em nós separados, de modo a distribuir o tráfego geral de E/S através de múltiplos nós. Se estiver a utilizar a virtualização, não se esqueça de considerar a distribuição do tráfego de E/O não só ao nível do nó, mas também à atividade combinada de E/S que acontece por todos os VMs de nó num dado hospedeiro físico.

## <a name="estimating-storage-requirements"></a>Estimativa dos requisitos de armazenamento
Cada cápsula que contém dados imponentes utiliza dois volumes persistentes nesta versão - um volume persistente para dados e outro volume persistente para registos. O quadro abaixo lista o número de volumes persistentes necessários para um único controlador de dados, exemplo gerido Azure SQL, Base de Dados Azure para instância pós-SQL e instância de HiperEssqL Azure PostgreSQL:

|Tipo de Recurso|Número de cápsulas imponentes|Número exigido de volumes persistentes|
|---|---|---|
|Controlador de Dados|4 ( `control` , , , , `controldb` `logsdb` `metricsdb` )|4 * 2 = 8|
|Instância Gerida do Azure SQL|1|2|
|Base de Dados Azure para instância pós-SQL|1| 2|
|Hiperescala Azure PostgresqL|1 + W (W = número de trabalhadores)|2 * (1 + W)|

O quadro a seguir indica o número total de volume persistente necessário para a implantação da amostra:

|Tipo de Recurso|Número de instâncias|Número exigido de volumes persistentes|
|---|---|---|
|Controlador de Dados|1|4 * 2 = 8|
|Instância Gerida do Azure SQL|5|5 * 2 = 10|
|Base de Dados Azure para instância pós-SQL|5| 5 * 2 = 10|
|Hiperescala Azure PostgresqL|2 (Número de trabalhadores = 4 por exemplo)|2 * 2 * (1 + 4) = 20|
|***Número total de volumes persistentes***||8 + 10 + 10 + 20 = 48|

Este cálculo pode ser usado para planear o armazenamento do seu cluster Kubernetes com base no provisionador de armazenamento ou ambiente. Por exemplo, se o provisão de armazenamento local for utilizado para um cluster Kubernetes com 5 nós, então para a colocação da amostra acima de cada nó requer pelo menos armazenamento para 10 volumes persistentes. Da mesma forma, ao a provisionar um cluster Azure Kubernetes Service (AKS) com 5 nós escolhendo um tamanho VM apropriado para o conjunto de nós de modo que 10 discos de dados podem ser ligados é fundamental. Mais detalhes sobre como dimensionar os nós para necessidades de armazenamento para nós AKS podem ser encontrados [aqui.](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)

## <a name="choosing-the-right-storage-class"></a>Escolher a classe de armazenamento certa

### <a name="on-premises-and-edge-sites"></a>Locais e locais de borda

A Microsoft e os seus parceiros OEM, OS e Kubernetes estão a trabalhar num programa de certificação para serviços de dados do Azure Arc. Este programa fornecerá aos clientes resultados de testes comparáveis a partir de um kit de ferramentas de teste de certificação. Os testes avaliarão a compatibilidade do recurso, os resultados dos testes de stress e o desempenho e escalabilidade. Cada um destes resultados de teste indicará o SISTEMA utilizado, a distribuição de Kubernetes utilizada, a HW utilizada, o addon CSI utilizado e as classes de armazenamento utilizadas. Isto ajudará os clientes a escolher a melhor classe de armazenamento, os sistemas operativos, a distribuição de Kubernetes e a HW para os seus requisitos. Mais informações sobre este programa e os resultados iniciais dos testes serão fornecidos mais perto dos serviços de dados da Azure Arc.

#### <a name="public-cloud-managed-kubernetes-services"></a>Nuvem pública, serviços geridos de Kubernetes

Para serviços públicos baseados em nuvem, geridos kubernetes podemos fazer as seguintes recomendações:

|Serviço público de nuvem|Recomendação|
|---|---|
|**Azure Kubernetes Service (AKS)**|O Azure Kubernetes Service (AKS) tem dois tipos de armazenamento - Ficheiros Azure e Discos Geridos Azure. Cada tipo de armazenamento tem dois níveis de preços/desempenho - standard (HDD) e premium (SSD). Assim, as quatro classes de armazenamento fornecidas em AKS são `azurefile` (nível padrão Azure Files), `azurefile-premium` (nível premium Azure Files), `default` (nível padrão Azure Disks) e `managed-premium` (nível premium Azure Disks). A classe de armazenamento predefinida é `default` (nível padrão de Discos Azure). Existem **[diferenças](https://azure.microsoft.com/en-us/pricing/details/storage/)** de preços substanciais entre os tipos e os níveis que devem ser contabilizados na sua decisão. Para cargas de trabalho de produção com requisitos de alto desempenho, recomendamos a utilização `managed-premium` para todas as classes de armazenamento. Para cargas de trabalho dev/teste, provas de conceito, etc. onde o custo é uma consideração, então `azurefile` é a opção menos dispendiosa. Todas as quatro opções podem ser usadas para situações que requerem armazenamento remoto e partilhado, uma vez que são todos dispositivos de armazenamento ligados à rede em Azure. Leia mais sobre [o Armazenamento AKS.](../../aks/concepts-storage.md)|
|**Elastic Kubernetes Service (EKS) do AWS**| O Serviço Elástico Kubernetes da Amazon tem uma classe primária de armazenamento - baseada no [controlador de armazenamento EBS CSI](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Isto é recomendado para cargas de trabalho de produção. Existe um novo controlador de armazenamento - [o controlador de armazenamento EFS CSI](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) - que pode ser adicionado a um cluster EKS, mas está atualmente numa fase beta e sujeito a alterações. Embora a AWS diga que este controlador de armazenamento é suportado para produção, não recomendamos a sua utilização porque ainda está em fase beta e sujeita a alterações. A classe de armazenamento EBS é o padrão e é chamada `gp2` . Leia mais sobre [o armazenamento EKS.](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)|
|**Google Kubernetes Engine (GKE)**|O Google Kubernetes Engine (GKE) tem apenas uma classe de armazenamento chamada `standard` que é usada para discos [persistentes GCE](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Sendo o único, é também o padrão. Embora exista um [provisionador de volume estático local](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) para O GKE que pode usar com SSDs ligados diretamente, não recomendamos a sua utilização, uma vez que não é mantida ou suportada pela Google. Leia mais sobre [o armazenamento de GKE.](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)
