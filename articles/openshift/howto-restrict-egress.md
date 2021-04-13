---
title: Restringir o tráfego de saídas num cluster Azure Red Hat OpenShift (ARO)
description: Saiba quais as portas e endereços necessários para controlar o tráfego de saídas em Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368735"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Controle o tráfego de saída para o seu cluster Azure Red Hat OpenShift (ARO) (pré-visualização)

Este artigo fornece os detalhes necessários que lhe permitem proteger o tráfego de saída do seu cluster Azure Red Hat OpenShift (ARO). Contém os requisitos de cluster para uma implantação ARO básica, e mais requisitos para o chapéu vermelho opcional e componentes de terceiros. No final, será dado um [exemplo](#private-aro-cluster-setup) sobre como configurar estes requisitos com a Azure Firewall. Tenha em mente que pode aplicar estas informações ao Azure Firewall ou a qualquer método ou aparelho de restrição de saída.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que estás a criar um novo aglomerado. Se precisar de um cluster ARO básico, consulte o arranque rápido da [ARO](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster).

> [!IMPORTANT]
> As funcionalidades de pré-visualização ARO estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "conforme" e "conforme disponível", e estão excluídas dos contratos de nível de serviço e garantia limitada. As pré-visualizações da ARO são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço.

## <a name="minimum-required-fqdn--application-rules"></a>Regras mínimas exigidas de FQDN / aplicação

Esta lista baseia-se na lista de FQDNs encontrados nos docs OpenShift aqui: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Destino FQDN | Porta | Utilização |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | Obrigatório para a instalação, utilizado pelo cluster. Isto é usado pelo cluster para descarregar as imagens do contentor da plataforma. |
| **`registry.redhat.io`** | **HTTPS:443** | Obrigatório para addons principais. Isto é usado pelo cluster para descarregar componentes principais, tais como ferramentas dev, addons baseados no operador e Chapéu Vermelho fornecido imagens de recipientes.
| **`mirror.openshift.com`** | **HTTPS:443** | Isto é necessário no ambiente VDI ou no seu portátil para aceder a conteúdos e imagens de instalação espelhados. É necessário no cluster descarregar assinaturas de lançamento da plataforma para saber que imagens tirar do quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Requerido pelo cluster para verificar se existem atualizações disponíveis antes de descarregar as assinaturas de imagem. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Registo privado interno dos operadores da ARO.  Necessário se não permitir os pontos finais de serviço Microsoft.ContainerRegistry nas suas sub-redes. |
| **`management.azure.com`** | **HTTPS:443** | Isto é usado pelo cluster para aceder a Azure APIs. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Isto é utilizado pelo cluster para autenticação a Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa da ARO possa monitorizar o cluster(s) do cliente. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa da ARO possa monitorizar o cluster(s) do cliente. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa da ARO possa monitorizar o cluster(s) do cliente. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa da ARO possa monitorizar o cluster(s) do cliente. |

> [!NOTE] 
> Para muitos clientes que expõem *.blob, *.table e outros espaços de endereços grandes criam uma potencial preocupação com a exfiltração de dados. Você pode querer considerar a utilização do [OpenShift Egress Firewall](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) para proteger as aplicações implementadas no cluster de chegar a estes destinos e usar o Azure Private Link para necessidades específicas de aplicações.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Lista completa de FQDNs necessários e opcionais

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>PRIMEIRO GRUPO: INSTALAR E DESCARREGAR PACOTES E FERRAMENTAS

- **`quay.io`**: Obrigatório para a instalação, utilizado pelo cluster. Isto é usado pelo cluster para descarregar as imagens do contentor da plataforma.
- **`registry.redhat.io`**: Obrigatório para complementos principais. Isto é usado pelo cluster para descarregar componentes principais, tais como ferramentas dev, addons baseados no operador ou Chapéu Vermelho forneceu imagens de contentores como o nosso middleware, a Imagem Base Universal...
- **`sso.redhat.com`**: Este é necessário no ambiente VDI ou no seu portátil para ligar a cloud.redhat.com. Este é o site onde podemos descarregar o segredo de puxar, e usar algumas das soluções SaaS que oferecemos na Red Hat para facilitar a monitorização das suas subscrições, inventário de cluster, relatórios de chargeback, entre outras coisas.
- **`openshift.org`**: Este é necessário no ambiente VDI ou no seu portátil para se conectar para descarregar imagens RH CoreOS, mas em Azure são colhidos no mercado, não há necessidade de descarregar imagens de SO.

---

### <a name="second-group-telemetry"></a>SEGUNDO GRUPO: TELEMETRIA

Toda esta secção pode ser optada, mas antes que saibamos como, por favor, verifique o que é: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: Utilize no seu ambiente VDI ou portátil.
- **`api.access.redhat.com`**: Utilize no seu ambiente VDI ou portátil.
- **`infogw.api.openshift.com`**: Utilize no seu ambiente VDI ou portátil.
- **`https://cloud.redhat.com/api/ingress`**: Utilizar no cluster para o operador de insights que se integra com o aaS Red Hat Insights.
Na Plataforma de Contentores OpenShift, os clientes podem optar por não reportar informações sobre saúde e utilização. No entanto, os clusters conectados permitem que a Red Hat reaja mais rapidamente aos problemas e apoie melhor os nossos clientes, e entenda melhor como os produtos atualizam os clusters. Consulte os detalhes aqui: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>TERCEIRO GRUPO: CLOUD APIs

- **`management.azure.com`**: Isto é utilizado pelo cluster para aceder às APIs do Azure.

---

### <a name="fourth-group-other-openshift-requirements"></a>QUARTO GRUPO: OUTROS REQUISITOS DE ABERTURA

- **`mirror.openshift.com`**: Este é necessário no ambiente VDI ou no seu portátil para aceder a conteúdos e imagens de instalação espelhados e necessário no cluster para descarregar assinaturas de lançamento da plataforma, utilizadas pelo cluster para saber que imagens retirar de quay.io.
- **`storage.googleapis.com/openshift-release`**: Site alternativo para descarregar assinaturas de lançamento da plataforma, usadas pelo cluster para saber que imagens tirar de quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (OU URL ARO EQUIVALENTE): Ao permitir a lista de domínios, este é o uso na sua rede corporativa para alcançar aplicações implementadas em OpenShift ou para aceder à consola OpenShift.
- **`api.openshift.com`**: Requerido pelo cluster verificar se existem atualizações disponíveis antes de descarregar as assinaturas de imagem.
- **`registry.access.redhat.com`**: O acesso ao registo é necessário no seu ambiente VDI ou portátil para descarregar imagens dev quando utilizar a ferramenta ODO CLI. (Esta ferramenta CLI é uma ferramenta CLI alternativa para desenvolvedores que não estão familiarizados com kubernetes). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>QUINTO GRUPO: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: Este é utilizado pelo cluster para autenticação a azul.
- **`gcs.prod.monitoring.core.windows.net`**: Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa ARO possa monitorizar o cluster(s) do cliente.
- **`*.blob.core.windows.net`**: Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa ARO possa monitorizar o cluster(s) do cliente.
- **`*.servicebus.windows.net`**: Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa ARO possa monitorizar o cluster(s) do cliente.
- **`*.table.core.windows.net`**: Isto é utilizado para o Microsoft Geneva Monitoring para que a equipa ARO possa monitorizar o cluster(s) do cliente.

## <a name="aro-integrations"></a>Integrações da ARO

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores

Existem duas opções para fornecer acesso ao Azure Monitor para contentores, pode permitir o Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **ou** fornecer acesso às regras de FQDN/Aplicação necessárias.  Aqui estão as [instruções](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) sobre como adicionar o Azure Monitor ao seu cluster ARO existente.

#### <a name="required-network-rules"></a>Regras de rede exigidas

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Este ponto final é utilizado para enviar dados de métricas e registos para Azure Monitor e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias

São necessárias as seguintes regras de FQDN/aplicação para os clusters ARO que tenham o Monitor Azure para contentores habilitados:

| FQDN                                    | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Este ponto final é utilizado para métricas e telemetria de monitorização utilizando o Monitor Azure. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Este ponto final é utilizado pelo Azure Monitor para ingerir dados de análise de registo. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Este ponto final é utilizado pelo omsagent, que é utilizado para autenticar o serviço de análise de registos. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Este ponto final é utilizado para enviar dados de métricas para o Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Configuração do cluster ARO privado
O objetivo é garantir o cluster ARO, encaminhando o tráfego da Egress através de uma Firewall Azure
### <a name="before"></a>Antes:
![Antes](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Depois:
![Depois](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Criar um cluster do ARO privado

### <a name="set-up-vars-for-your-environment"></a>Configurar VARS para o seu ambiente
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Adicione duas sub-redes vazias à sua rede virtual
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Desativar as políticas de rede para o Serviço de Ligação Privada na sua rede virtual e sub-redes. Esta é uma exigência para que o serviço ARO aceda e gere o cluster.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Criar uma sub-rede de firewall
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Criar um VM de hospedeiro de salto
### <a name="create-a-jump-subnet"></a>Criar uma sub-rede de salto
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Criar um VM de hospedeiro de salto
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Criar um cluster do Azure Red Hat OpenShift
### <a name="get-a-red-hat-pull-secret-optional"></a>Obtenha um segredo de puxar o chapéu vermelho (opcional)

Um segredo de puxar o chapéu vermelho permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com outros conteúdos. Este passo é opcional, mas recomendado.

1. **[Vá ao seu portal de clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça login.**

   Terá de iniciar sessão na sua conta Red Hat ou criar uma nova conta Red Hat com o seu email de negócios e aceitar os termos e condições.

2. **Clique em Baixar O segredo de puxar.**

Mantenha o ficheiro guardado `pull-secret.txt` em algum lugar seguro - será usado em cada criação de cluster.

Ao executar o `az aro create` comando, pode fazer referência ao seu segredo de puxar utilizando o `--pull-secret @pull-secret.txt` parâmetro. Execute `az aro create` a partir do diretório onde guardou o seu `pull-secret.txt` ficheiro. Caso contrário, `@pull-secret.txt` `@<path-to-my-pull-secret-file` substitua-o por .

Se estiver a copiar o seu segredo de puxar ou a fazê-lo referenciar noutros scripts, o seu segredo de puxar deve ser formatado como uma cadeia JSON válida.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Criar uma Firewall Azure

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Atualizar instalar extensão Azure Firewall
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Criar firewall Azure e configurar IP Config
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Capture IPs de Firewall Azure para uma utilização posterior
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Criar uma tabela de UDR e encaminhamento para firewall Azure
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Adicionar regras de aplicação para firewall Azure
Regra para OpenShift para funcionar com base nesta [lista](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall):
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Regras opcionais para imagens Docker:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Subnetas ARO associadas à FW
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Teste a configuração a partir da Jumpbox
Estes passos só funcionam se adicionares regras para as imagens do Docker. 
### <a name="configure-the-jumpbox"></a>Configure a caixa de salto
Inicie sessão num VM de caixa de salto e `azure-cli` `oc-cli` instale, e `jq` utils. Para a instalação de openshift-cli, verifique o portal do cliente red hat.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Inicie sessão no cluster ARO
Credenciais de agrupamento de listas:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Obtenha um ponto final do servidor API:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Descarregue o CLI oc para a caixa de salto
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Faça login `oc login` utilizando:
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Executar CentOS para testar conectividade externa
Criar uma cápsula
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Uma vez que a cápsula esteja em funcionamento, exec-lo e testar a conectividade externa.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Aceda à consola web do cluster privado

### <a name="set-up-ssh-forwards-commands"></a>Configurar comandos de avançados ssh

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Modifique o ficheiro etc. anfitriões na sua máquina local
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Use sshuttle como outra opção

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Limpar os recursos

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```