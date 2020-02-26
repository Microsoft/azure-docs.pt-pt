---
title: 'Tutorial: Criar e testar um portal NAT - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway NAT usando o Azure CLI e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 26a427baddc99fa702b638c36b5378750364c849
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587027"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Tutorial: Criar um portal NAT utilizando o Azure CLI e testar o serviço NAT

Neste tutorial, você vai criar uma porta de entrada NAT para fornecer conectividade de saída para máquinas virtuais em Azure. Para testar o portal NAT, você implanta uma máquina virtual de origem e destino. Vai testar a porta de entrada na NAT fazendo ligações de saída a um endereço IP público. Estas ligações virão da fonte para a máquina virtual de destino. Este tutorial implanta fonte e destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para simplicidade.

>[!NOTE] 
>A Rede Virtual Azure NAT está disponível como pré-visualização pública neste momento e disponível num conjunto limitado de [regiões.](./nat-overview.md#region-availability) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter mais informações.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode completar este tutorial utilizando a Azure Cloud Shell ou executar os respetivos comandos localmente.  Se ainda não usou a Azure Cloud Shell, deve [inscrever-se agora.](https://shell.azure.com)

Se optar por executar estes comandos localmente, tem de instalar o CLI.  Este tutorial requer que esteja a executar uma versão da versão Azure CLI 2.0.71 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o Portal naT

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet pública, precisa de um ou mais endereços IP públicos para o portal NAT. Use a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Pode utilizar um ou mais recursos públicos de endereçoip, prefixos IP públicos ou ambos com gateway NAT. Vamos adicionar um recurso público de prefixo IP a este cenário para demonstrar.   Utilize a criação de [prefixo público-ip](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) da rede Az para criar um recurso público de prefixo IP denominado **myPublicIPprefixsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool ip público e um prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Mude o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie um portal global Azure NAT com a [rede Az nat gateway criar](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) o nome **myNATgateway**. O comando utiliza tanto o endereço IP público **myPublicIP** como o IP público **prefixo myPublicIPprefix**. O comando também altera o tempo de inatividade para 10 minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Neste ponto, o portal NAT está funcional e tudo o que falta é configurar quais as subredes de uma rede virtual que devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Prepare a fonte para o tráfego de saída

Vamos guiá-lo através de um ambiente de teste completo. Vai montar um teste utilizando ferramentas de código aberto para verificar o portal NAT. Começaremos com a fonte, que usará o portal NAT que criamos anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configurar rede virtual para fonte

Antes de implementar um VM e testar o seu portal NAT, precisamos de criar a rede virtual.

Criar uma rede virtual chamada **myVnetsource** com uma subnet chamada **mySubnetsource** no **myResourceGroupNAT** utilizando a [rede az Microsoft Azure Virtual Network criar](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0.0/16**. A sub-rede dentro da rede virtual é **de 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configure o serviço NAT para subnet de origem

Configure a sub-rede de origem **mySubnetsource** em rede virtual **myVnetsource** para usar um recurso de gateway NAT específico **myNATgateway** com [a az rede Microsoft Azure Virtual Network atualização](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Este comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para destinos da Internet está agora a usar o serviço NAT.  Não é necessário configurar um UDR.

Antes de testarmos o portal NAT, precisamos de criar uma Fonte VM.  Atribuiremos um recurso público de endereço IP como um IP público de nível de exemplo para aceder a este VM a partir do exterior. Este endereço só é utilizado para aceder ao teste.  Vamos demonstrar como o serviço NAT tem precedência sobre outras opções de saída.

Você também poderia criar este VM sem um IP público e criar outro VM para usar como uma caixa de salto sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para fonte VM

Criamos um IP público para ser usado para aceder à fonte VM. Utilize a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPsourceVM** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Criar um NSG para fonte VM

Como os endereços IP públicos standard são "seguros por padrão", precisamos de criar um NSG que permita o acesso à entrada para acesso ssh.  O serviço Azure NAT está consciente. Este NSG não será usado para sair quando o gateway NAT estiver configurado na mesma sub-rede. Use a [rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSGsource** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor ponto final sSH na fonte VM

Criamos uma regra no NSG para o acesso ssh à fonte vm. Use a [regra nsg da rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Esta regra será criada no NSG nomeado **myNSGsource** no grupo de recursos **myResourceGroupNAT**.

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

### <a name="create-nic-for-source-vm"></a>Criar NIC para fonte VM

Criar uma interface de rede com a [rede az nic criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público e ao grupo de segurança da rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Criar uma fonte VM

Crie a máquina virtual com [az vm criar.](/cli/azure/vm#az-vm-create)  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Enquanto o comando regressará imediatamente, pode levar alguns minutos para o VM ser implantado.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para tráfego de saída

Vamos agora criar um destino para o tráfego de saída traduzido pelo serviço NAT para permitir que o teste.

### <a name="configure-virtual-network-for-destination"></a>Configure rede virtual para destino

 Precisamos criar uma rede virtual onde estará a máquina virtual de destino.  Estes comandos são os mesmos passos que para a fonte VM com pequenas alterações para expor o ponto final do destino.

Criar uma rede virtual chamada **myVnetdestination** com uma subnet chamada **mySubnetdestination** no **myResourceGroupNAT** utilizando a [rede az Microsoft Azure Virtual Network criar](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0.0/16**. A sub-rede dentro da rede virtual é **de 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para destino VM

Criamos um IP público para ser usado para aceder à fonte VM. Utilize a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso público de endereço IP chamado **myPublicIPdestinationVM** no **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Criar um NSG para destino VM

Os endereços IP públicos padrão são 'seguros por padrão', você precisará criar um NSG para permitir o acesso de entrada para ssh. O serviço Azure NAT está consciente. Este NSG não será usado para sair quando o gateway NAT estiver configurado na mesma sub-rede. Use a [rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSGdestination** no **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Expor ponto final do SSH no destino VM

Criamos uma regra no NSG para acesso ssh ao destino vm. Use a [regra nsg da rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh**. Esta regra será criada no NSG denominado **myNSGdestination** no grupo de recursos **myResourceGroupNAT**.

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

### <a name="expose-http-endpoint-on-destination-vm"></a>Expor o ponto final http no destino VM

Criamos uma regra no NSG para o acesso http ao destino vm. Use a regra nsg da [rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **http** no NSG nomeado **myNSGdestination** no **myResourceGroupNAT**.

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

Criar uma interface de rede com a [rede az nic criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público **myPublicIPdestinationVM** e ao grupo de segurança de rede **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Criar um destino VM

Crie a máquina virtual com [az vm criar.](/cli/azure/vm#az-vm-create)  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Enquanto o comando regressará imediatamente, pode levar alguns minutos para o VM ser implantado.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Prepare um servidor web e teste a carga útil no destino VM

Primeiro precisamos descobrir o endereço IP do destino VM.  Para obter o endereço IP público do destino VM, use a [rede az public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo em passos subsequentes. Indique que esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Inscreva-se no destino VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Nuvem Azure](https://shell.azure.com) Shell no seu navegador. Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual. 

```bash
ssh <ip-address-destination>
```

Copie e cole os seguintes comandos depois de ter assinado.  

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

Estes comandos atualizarão a sua máquina virtual, instalarão nginx e criarão um ficheiro 100-KBytes. Este ficheiro será recuperado da fonte VM utilizando o serviço NAT.

Feche a sessão sSH com o destino VM.

## <a name="prepare-test-on-source-vm"></a>Preparar teste na fonte VM

Primeiro, precisamos descobrir o endereço IP da fonte VM.  Para obter o endereço IP público da fonte VM, use a [rede az public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo em passos subsequentes. Indique que esta é a máquina virtual de origem.

### <a name="sign-in-to-source-vm"></a>Inscreva-se na fonte VM

Mais uma vez, as credenciais SSH são armazenadas na Cloud Shell. Abra um novo separador para [Azure Cloud Shell](https://shell.azure.com) no seu navegador.  Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual. 

```bash
ssh <ip-address-source>
```

Copie e cole os seguintes comandos para preparar o teste do serviço NAT.

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

Este comando irá atualizar a sua máquina virtual, instalar ir, instalar [hey](https://github.com/rakyll/hey) do GitHub e atualizar o seu ambiente de concha.

Está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Enquanto entra no VM de origem, pode usar **caracol** e **hey** para gerar pedidos para o endereço IP de destino.

Use caracóis para recuperar o ficheiro 100-KBytes.  Substitua **\<ip-address-destination>** no exemplo abaixo com o endereço IP de destino que copiou anteriormente.  O parâmetro **de saída** indica que o ficheiro recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Também pode gerar uma série de pedidos usando **hey**. Mais uma vez, substitua **\<ip-address-destination>** com o endereço IP de destino que copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Este comando gerará 100 pedidos, 10 simultaneamente, com um tempo de 30 segundos. A ligação TCP não será reutilizada.  Cada pedido recuperará 100 Kbytes.  No final da corrida, **o hey** irá reportar algumas estatísticas sobre como o serviço NAT fez bem.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [comando az group eliminar](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no interior.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um portal NAT, criou uma Fonte e destino VM, e depois testou o portal NAT.

Reveja as métricas no Monitor Azure para ver o seu serviço NAT a funcionar. Diagnosticar problemas como a exaustão de recursos das portas SNAT disponíveis.  A exaustão de recursos das portas SNAT é facilmente abordada adicionando recursos de endereços IP públicos adicionais ou recursos públicos de prefixo IP ou ambos.

- Conheça a [Rede Virtual NAT](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Arranque rápido para a implementação do [recurso de gateway NAT utilizando](./quickstart-create-nat-gateway-portal.md)o portal Azure .
- [Forneça feedback sobre a Pré-Visualização Pública](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

