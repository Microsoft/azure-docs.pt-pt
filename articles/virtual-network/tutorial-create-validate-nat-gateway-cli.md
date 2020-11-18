---
title: Criar e testar uma porta de entrada NAT - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway NAT usando o CLI Azure e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0ec054d55432ad2680314b4ff91a067d37b629d4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734335"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Tutorial: Criar uma porta de entrada NAT usando O CLI Azure e testar o serviço NAT

Neste tutorial, você vai criar uma porta de entrada NAT para fornecer conectividade de saída para máquinas virtuais em Azure. Para testar o gateway NAT, você implanta uma máquina virtual de origem e destino. Você vai testar o gateway NAT fazendo ligações de saída para um endereço IP público. Estas ligações virão da fonte para a máquina virtual de destino. Este tutorial implementa a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para a simplicidade.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.71 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Criar o NAT Gateway

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet pública, precisa de um ou mais endereços IP públicos para o gateway NAT. Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público, prefixos IP públicos ou ambos com gateway NAT. Vamos adicionar um recurso de prefixo IP público a este cenário para demonstrar.   Utilize [o prefixo ip da rede az](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) para criar um recurso de prefixo IP público chamado **myPublicIPprefixsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool IP público e prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Altere o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie uma porta de entrada Azure NAT global com [a az network nat gateway criar](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) chamado **myNATgateway**. O comando utiliza o endereço IP público **myPublicIP** e o prefixo ip público **myPpo.** O comando também muda o tempo de inatividade para 10 minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

Neste momento, o gateway NAT está funcional e tudo o que falta é configurar quais as sub-redes de uma rede virtual que devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a fonte para o tráfego de saída

Vamos guiá-lo através de um ambiente de teste completo. Irá configurar um teste utilizando ferramentas de código aberto para verificar o gateway NAT. Começaremos com a fonte, que usará o portal NAT que criamos anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configure a rede virtual para a origem

Antes de implementar um VM e poder testar o seu gateway NAT, precisamos de criar a rede virtual.

Criar uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mySubnetsource** no **myResourceGroupNAT** utilizando [a rede az Microsoft Azure Virtual Network criar](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Configure o serviço NAT para a sub-rede de origem

Configure a sub-rede de origem **mySubnetsource** na rede virtual **myVnetsource** para utilizar um recurso de gateway específico **myNATgateway** com [a az network Microsoft Azure Virtual Network atualização de sub-rede](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Este comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Todo o tráfego de saída para destinos de Internet está agora a usar o serviço NAT.  Não é necessário configurar uma UDR.

Antes de testarmos o portal NAT, precisamos de criar uma fonte VM.  Atribuiremos um recurso de endereço IP público como um IP público de nível de instância para aceder a este VM a partir do exterior. Este endereço é utilizado apenas para aceder ao mesmo para o teste.  Vamos demonstrar como o serviço NAT tem precedência sobre outras opções de saída.

Você também poderia criar este VM sem um IP público e criar outro VM para usar como uma caixa de salto sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para origem VM

Criamos um IP público para ser usado para aceder à fonte VM. Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsourceVM** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Criar um NSG para vm de origem

Como os endereços IP públicos standard são "seguros por padrão", precisamos de criar um NSG para permitir o acesso à entrada para acesso à SSH.  O serviço Azure NAT está consciente da direção do fluxo. Este NSG não será utilizado para a saída uma vez que o gateway NAT esteja configurado na mesma sub-rede. Utilize [a rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSGsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto final do SSH na fonte VM

Criamos uma regra no NSG para acesso SSH à fonte vm. Utilize [a regra nsg de rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Esta regra será criada no NSG nomeado **myNSGsource** no grupo de recursos **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Criar NIC para vm de origem

Crie uma interface de rede com [o nic de rede Az criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público e ao grupo de segurança da rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Criar um VM de origem

Crie a máquina virtual com [a criação de az vm](/cli/azure/vm#az-vm-create).  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Embora o comando regresse imediatamente, pode levar alguns minutos para o VM ser acionado.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para tráfego de saída

Vamos agora criar um destino para o tráfego de saída traduzido pelo serviço NAT para permitir testá-lo.

### <a name="configure-virtual-network-for-destination"></a>Configurar rede virtual para destino

 Precisamos criar uma rede virtual onde estará a máquina virtual de destino.  Estes comandos são os mesmos passos que para a fonte VM com pequenas alterações para expor o ponto final do destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede chamada **mySubnetdestination** no **myResourceGroupNAT** usando [a rede az Microsoft Azure Virtual Network criar](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para destino VM

Criamos um IP público para ser usado para aceder à fonte VM. Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** in **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Criar um NSG para destino VM

Os endereços IP públicos padrão são "seguros por padrão", terá de criar um NSG para permitir o acesso à entrada para o ssh. O serviço Azure NAT está consciente da direção do fluxo. Este NSG não será utilizado para a saída uma vez que o gateway NAT esteja configurado na mesma sub-rede. Utilize [a rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Expor o ponto final do SSH no destino VM

Criamos uma regra no NSG para acesso SSH ao destino vm. Utilize [a regra nsg de rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Esta regra será criada no NSG nomeado **myNSGdestination** no grupo de recursos **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Expor http ponto final no destino VM

Criamos uma regra no NSG para acesso HTTP ao destino vm. Utilize [a regra nsg de rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG nomeada **http** na NSG chamada **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para destino VM

Crie uma interface de rede com [o az network nic criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público **myPublicIPdestinationVM** e ao grupo de segurança de rede **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Criar um VM de destino

Crie a máquina virtual com [a criação de az vm](/cli/azure/vm#az-vm-create).  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Embora o comando regresse imediatamente, pode levar alguns minutos para o VM ser acionado.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Prepare um servidor web e teste a carga útil no VM de destino

Primeiro precisamos descobrir o endereço IP do destino VM.  Para obter o endereço IP público do destino VM, utilize [o show público-ip da rede Az](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Inscreva-se no destino VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Azure Cloud Shell](https://shell.azure.com) no seu navegador. Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual. 

```bash
ssh <ip-address-destination>
```

Copie e cole os seguintes comandos assim que se inscrever.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Estes comandos irão atualizar a sua máquina virtual, instalar nginx e criar um ficheiro de 100 KBytes. Este ficheiro será recuperado a partir da fonte VM utilizando o serviço NAT.

Feche a sessão SSH com o destino VM.

## <a name="prepare-test-on-source-vm"></a>Preparar teste na fonte VM

Primeiro, temos de descobrir o endereço IP da fonte VM.  Para obter o endereço IP público da origem VM, utilize [o programa público-ip da rede Az](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de origem.

### <a name="sign-in-to-source-vm"></a>Inscreva-se na fonte VM

Mais uma vez, as credenciais SSH são armazenadas na Cloud Shell. Abra um novo separador para [Azure Cloud Shell](https://shell.azure.com) no seu navegador.  Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual. 

```bash
ssh <ip-address-source>
```

Copiar e colar os seguintes comandos para preparar o teste do serviço NAT.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Este comando irá atualizar a sua máquina virtual, instalar-se em movimento, instalar [hey](https://github.com/rakyll/hey) do GitHub e atualizar o ambiente da sua concha.

Está agora pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Ao iniciar sessão na origem VM, pode utilizar **curl** e **hey** para gerar pedidos para o endereço IP de destino.

Use caracóis para recuperar o ficheiro 100-KBytes.  Substitua **\<ip-address-destination>** no exemplo abaixo pelo endereço IP de destino que já copiou anteriormente.  O parâmetro **de saída** indica que o ficheiro recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Também pode gerar uma série de pedidos usando **hey**. Mais uma vez, **\<ip-address-destination>** substitua o endereço IP de destino que já copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Este comando gerará 100 pedidos, 10 simultaneamente, com um intervalo de tempo de 30 segundos. A ligação TCP não será reutilizada.  Cada pedido recuperará 100 Kbytes.  No final da corrida, **vai** relatar algumas estatísticas sobre como o serviço NAT se saiu bem.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma porta de entrada NAT, criou uma VM de origem e destino, e depois testou o gateway NAT.

Reveja as métricas no Azure Monitor para ver o seu serviço NAT a funcionar. Diagnosticar problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é facilmente abordado adicionando recursos adicionais de endereços IP públicos ou recursos prefixos IP públicos ou ambos.

- Saiba mais sobre [a Rede Virtual NAT](./nat-overview.md)
- Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
- Quickstart para implantar [recurso de gateway NAT utilizando O Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart para implantar [recurso de gateway NAT utilizando Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart para implantar [recurso de gateway NAT utilizando o portal Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

