---
title: 'Tutorial: criar e testar um gateway NAT-CLI do Azure'
titlesuffix: Azure NAT service
description: Este tutorial mostra como criar um gateway NAT usando o CLI do Azure e testar o serviço NAT
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.custom: seodec18
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: f97284b3c629dbaab1b0cbb54b1f114342278da7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376491"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Tutorial: criar um gateway NAT usando CLI do Azure e testar o serviço NAT

Este tutorial mostra como usar o serviço NAT do Azure e criar um gateway NAT para fornecer conectividade de saída para máquinas virtuais no Azure. Para testar o gateway NAT, você implanta uma máquina virtual de origem e de destino.  Você testará o gateway NAT fazendo conexões de saída com um endereço IP público da origem para a máquina virtual de destino.  Este tutorial implanta a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para fins de simplicidade.

>[!NOTE] 
>O serviço NAT do Azure está disponível como visualização pública no momento e disponível em um conjunto limitado de [regiões](https://azure.microsoft.com/global-infrastructure/regions/). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms) para obter mais informações.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode concluir este tutorial usando o Azure cloud Shell ou executar os respectivos comandos localmente.  Se você nunca usou o Azure cloud Shell, faça [logon agora](https://shell.azure.com) para passar pela configuração inicial.

Se você optar por executar esses comandos localmente, precisará instalar a CLI.  Este tutorial requer que você esteja executando uma versão do CLI do Azure versão 2.0.71 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** no local **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o gateway NAT

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para acessar a Internet pública, você precisa de um ou mais endereços IP públicos para o gateway NAT. Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsource** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público ou um ou mais prefixos IP públicos ou ambos com o gateway NAT. Adicionaremos um recurso de prefixo de IP público a este cenário para demonstrar.   Use [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) para criar um recurso de prefixo de IP público chamado **myPublicIPprefixsource** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta seção fornece detalhes sobre como você pode criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway NAT:
  - Um pool IP público e o prefixo IP público a ser usado para fluxos de saída convertidos pelo recurso de gateway NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway de NAT global do Azure com [AZ Network NAT gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) chamado **myNATgateway** que usa o endereço IP público **myPublicIPsource** e o prefixo IP público **myPublicIPprefixsource** e altere o tempo limite de ociosidade para **10 minutos**.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Neste ponto, o gateway de NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-la.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a origem do tráfego de saída

Orientaremos você pela configuração de um ambiente de teste completo e a execução dos testes em si nas próximas etapas. Começaremos com a origem, que usará o recurso de gateway de NAT que criamos anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configurar a rede virtual para a origem

Antes de implantar uma VM e testar seu gateway NAT, precisamos criar a rede virtual.

Crie uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mysubnetname** no **myResourceGroupNAT** usando [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16** e a sub-rede na rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurar o serviço NAT para a sub-rede de origem

Já criamos o gateway NAT e agora configuramos a sub-rede de origem **Mysubnetname** na rede virtual **myVnetsource** para usar um recurso de gateway NAT específico **myNATsource** com [AZ Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Esse comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para os destinos da Internet agora está usando o serviço NAT.  Não é necessário configurar um UDR.

Antes que possamos testar o gateway NAT, precisamos criar uma VM de origem.  Atribuíremos um recurso de endereço IP público como um IP público em nível de instância para acessar essa VM de fora. Esse endereço é usado somente para acessá-lo para o teste.  Demonstraremos como o serviço NAT tem precedência sobre outras opções de saída.

Você também pode criar essa VM sem um IP público e criar outra VM para usar como um Jumpbox sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar um IP público para a VM de origem

Criamos um IP público a ser usado para acessar a VM de origem. Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsourceVM** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Criar um NSG para a VM de origem

Como os endereços IP públicos padrão são "seguros por padrão", precisamos criar um NSG para permitir o acesso de entrada para acesso SSH.  Como o serviço NAT reconhece a direção do fluxo, esse NSG não será usado para saída depois que o gateway NAT estiver configurado na mesma sub-rede. Use [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso do NSG chamado **myNSGsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto de extremidade SSH na VM de origem

Criamos uma regra no NSG para o acesso SSH à VM de origem. Use [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **SSH** no NSG chamado **myNSGsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Criar NIC para a VM de origem

Crie um adaptador de rede com [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) e associe com o endereço IP público e o grupo de segurança de rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Criar uma VM de origem

Crie a máquina virtual com [AZ VM Create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Enquanto o comando retornará imediatamente, pode levar alguns minutos para que a VM seja implantada.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para o tráfego de saída

Agora, criaremos um destino para o tráfego de saída convertido pelo serviço NAT para permitir que você o teste.

### <a name="configure-virtual-network-for-destination"></a>Configurar a rede virtual para o destino

Antes de implantar uma VM e para o destino, também precisamos criar uma rede virtual na qual a máquina virtual de destino pode ser colocada.  Essas são as mesmas etapas para a VM de origem com algumas pequenas alterações para expor o ponto de extremidade de destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede denominada **mySubnetdestination** no **myResourceGroupNAT** usando [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16** e a sub-rede na rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Criar um IP público para a VM de destino

Criamos um IP público a ser usado para acessar a VM de origem. Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** em **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM
  --sku standard
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Criar um NSG para a VM de destino

Como os endereços IP públicos padrão são "seguros por padrão", precisamos criar um NSG para permitir o acesso de entrada para acesso SSH.  Como o serviço NAT reconhece a direção do fluxo, esse NSG não será usado para saída depois que o gateway NAT estiver configurado na mesma sub-rede. Use [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso do NSG chamado **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Expor o ponto de extremidade SSH na VM de destino

Criamos uma regra no NSG para o acesso SSH à VM de destino. Use [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **SSH** no NSG chamado **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Expor ponto de extremidade HTTP na VM de destino

Criamos uma regra no NSG para o acesso HTTP à VM de destino. Use [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **http** no NSG chamado **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para VM de destino

Crie uma interface de rede com [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) e associe com o endereço IP público **myPublicIPdestinationVM** e o grupo de segurança de rede **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM
    --network-security-group myNSGdestination
    --location westus
```

### <a name="create-a-destination-vm"></a>Criar uma VM de destino

Crie a máquina virtual com [AZ VM Create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    --location westus
```
Enquanto o comando retornará imediatamente, pode levar alguns minutos para que a VM seja implantada.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparar um servidor Web e testar a carga na VM de destino

Primeiro, precisamos descobrir o endereço IP da VM de destino.  Para obter o endereço IP público da VM de destino, use [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para que você possa usá-lo nas etapas subsequentes. Indique esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Entrar na VM de destino

As credenciais SSH devem ser armazenadas em seu Cloud Shell a partir da operação anterior.  Abra um [Azure cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para SSH para a máquina virtual. 

```bash
ssh <ip-address-destination>
```

Copie e cole os comandos a seguir depois de fazer logon.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Esses comandos atualizarão sua máquina virtual, instalarão o Nginx e criarão um arquivo 100 KBytes que você pode usar para recuperar da VM de origem usando o serviço NAT.

Feche a sessão SSH com a VM de destino.

## <a name="prepare-test-on-source-vm"></a>Preparar o teste na VM de origem

Primeiro, precisamos descobrir o endereço IP da VM de origem.  Para obter o endereço IP público da VM de origem, use [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para que você possa usá-lo nas etapas subsequentes. Indique que esta é a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Fazer logon na VM de origem

Novamente, as credenciais SSH são armazenadas no Cloud Shell. Abra uma nova guia para [Azure cloud Shell](https://shell.azure.com) em seu navegador.  Use o endereço IP recuperado na etapa anterior para SSH para a máquina virtual. 

```bash
ssh <ip-address-source>
```

Copie e cole os comandos a seguir para se preparar para testar o serviço NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Isso atualizará sua máquina virtual, instalará o go, instalará o [Ei](https://github.com/rakyll/hey) do GitHub e atualizará o ambiente do Shell.

Agora você está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar serviço NAT

Enquanto estiver conectado à VM de origem, você poderá usar a **ondulação** e o **Ei** para gerar solicitações para o endereço IP de destino.

Use a rotação para recuperar o arquivo 100 KBytes.  Substitua o **> de destino \<ip-address** no exemplo abaixo pelo endereço IP de destino que você copiou anteriormente.  O parâmetro **--output** indica que o arquivo recuperado será Descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Você também pode gerar uma série de solicitações usando o **Ei**. Novamente, substitua **\<ip-address-destination >** pelo endereço IP de destino que você copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Isso irá gerar 100 solicitações, 10 simultaneamente, com um tempo limite de 30 segundos e sem reutilizar a conexão TCP.  Cada solicitação recuperará 100 Kbytes.  No final da execução, **Ei** relatará algumas estatísticas sobre o quão bem o serviço NAT foi executado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [AZ Group Delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você criou um gateway NAT, criou uma VM de origem e de destino e, em seguida, testou o gateway NAT. Para saber mais sobre o serviço NAT do Azure, continue para outros tutoriais do serviço NAT do Azure.

Você também pode examinar as métricas em Azure Monitor para ver o serviço NAT operando e diagnosticar problemas como esgotamento de recursos de portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é facilmente resolvido com a adição de recursos de endereço IP público adicionais ou recursos de prefixo IP público ou ambos.

> [!div class="nextstepaction"]

