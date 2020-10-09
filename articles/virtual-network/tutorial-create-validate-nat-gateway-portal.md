---
title: 'Tutorial: Criar e testar um PORTAL NAT Gateway - Azure'
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um NAT Gateway usando o portal Azure e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84417842"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Tutorial: Criar um GATEWAY NAT utilizando o portal Azure e testar o serviço NAT

Neste tutorial, você vai criar uma porta de entrada NAT para fornecer conectividade de saída para máquinas virtuais em Azure. Para testar o gateway NAT, você implanta uma máquina virtual de origem e destino. Você vai testar o gateway NAT fazendo ligações de saída para um endereço IP público da fonte para a máquina virtual de destino.  Este tutorial implementa a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para a simplicidade.

Se preferir, pode fazer estes passos utilizando o [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) ou [o Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) em vez do portal.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a fonte para o tráfego de saída

Vamos guiá-lo através da configuração de um ambiente de teste completo e da execução dos próprios testes nos próximos passos. Começaremos com a fonte, que usará o recurso de gateway NAT que criamos em etapas posteriores.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Antes de implementar um VM e poder utilizar o seu gateway NAT, precisamos de criar o grupo de recursos e a rede virtual.

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Criar máquina virtual de origem

Vamos agora criar um VM para usar o serviço NAT. Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM. O serviço NAT está consciente da direção do fluxo e substituirá o destino de Internet predefinido na sua sub-rede. O endereço IP público do VM não será utilizado para ligações de saída.

Para testar o gateway NAT, atribuiremos um recurso de endereço IP público como um IP público de nível de instância para aceder a este VM a partir do exterior. Este endereço é utilizado apenas para aceder ao mesmo para o teste.  Vamos demonstrar como o serviço NAT tem precedência sobre outras opções de saída.

Você também poderia criar este VM sem um IP público e criar outro VM para usar como uma caixa de salto sem um IP público como um exercício.

1. No lado superior esquerdo do portal, selecione **Criar um recurso**  >  **Compute**  >  **Ubuntu Server 18.04 LTS**, ou procurar **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Na **Criação de uma máquina virtual,** insira ou selecione os seguintes valores no separador **Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: insira **myVMsource**.
   - **Detalhes da**  >  instância **Região** > selecione **East US 2**.
   - Conta de **administrador**  >  **Introdução de autenticação**: Selecione **Palavra-passe**.
   - **Conta de administrador** > Introduza o **nome de utilizador,** **palavra-passe**e confirme as informações **de senha.**
   - **Regras portuárias**  >  de entrada **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Regras portuárias**  >  de entrada **Selecione portas de entrada**: Selecione **SSH (22)**
   - Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.

3. No **separador Networking** certifique-se de que estão selecionados:
   - **Rede virtual**: **myVnetsource**
   - **Sub-rede**: **mySubnetsource**
   - **IP público** > Selecione **Criar novo**.  Na janela **de endereço IP público,** insira **o myPublicIPsourceVM** no campo **Nome.** Selecione **Standard** para o **SKU**. Deixe o resto à predefinição e clique **em OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Selecione portas de entrada**: Confirme que **o SSH** está selecionado.

4. No separador **Gestão,** em **Monitorização,** desemote **os diagnósticos boot** para **desligar**.

5. Selecione **Rever + criar**.

6. Reveja as definições e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o NAT Gateway

Você pode usar um ou mais recursos de endereço IP público, prefixos IP públicos, ou ambos com gateway NAT. Adicionaremos um recurso IP público, prefixo IP público e um recurso de gateway NAT.

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool IP público e prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Altere o tempo de inatividade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No lado superior esquerdo do portal, selecione **Criar um**endereço IP público em rede de recursos ou procurar endereço  >  **Networking**  >  **Public IP address** **IP público** na pesquisa do Marketplace. 

2. Para **criar o endereço IP público,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Standard**.
    | Nome | Insira **o myPublicIPsource**. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **myResourceGroupNAT**. |
    | Localização | Selecione **E.U.A. Leste 2**.|

3. Deixe o resto das predefinições e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione **Criar um**  >  prefixo IP público em rede**de**recursos ou  >  **Public IP prefix**procurar **prefixo IP público** na pesquisa do Marketplace.

2. Na **Criação de um prefixo IP público,** introduza ou selecione os seguintes valores no separador **Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**>
   - **Detalhes da**  >  instância **Nome**: **insira myPpofixsource**.
   - **Detalhes da**  >  instância **Região**: Selecione **Leste US 2**.
   - **Detalhes da**  >  instância **Tamanho do prefixo**: Selecione **/31 (2 endereços)**

3. Deixe o resto as predefinições e selecione **Review + create**.

4. Reveja as definições e, em seguida, **selecione Criar**.


### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

1. No lado superior esquerdo do portal, selecione **Criar um**gateway NAT  >  **de rede de**  >  **NAT gateway**recursos, ou procurar por **gateway NAT** na pesquisa do Marketplace.

2. No **Portal de Tradução de Endereços de Rede (NAT),** introduza ou selecione os seguintes valores no separador **Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da**  >  instância **Nome do gateway NAT**: **insira myNATgateway**.
   - **Detalhes da**  >  instância **Região**: Selecione **Leste US 2**.
   - **Detalhes da**  >  instância **Intervalo de tempo inativo (minutos):** insira **10**.
   - Selecione o **separador IP público** ou selecione **Seguinte: IP público**.

3. No **separador IP público,** introduza ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIPsource**.
   - **Prefixos IP públicos**: Selecione **myPubfixsource**.
   - Selecione o separador **Sub-rede** ou selecione **Seguinte: Sub-rede**.

4. No separador **Sub-rede,** introduza ou selecione os seguintes valores:
   - **Rede Virtual**: Selecione **myResourceGroupNAT**  >  **myVnetsource**.
   - **Nome da sub-rede**: Selecione a caixa ao lado **do mySubnetsource**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, **selecione Criar**.

Todo o tráfego de saída para destinos de Internet está agora a usar o serviço NAT.  Não é necessário configurar uma UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para tráfego de saída

Vamos agora criar um destino para o tráfego de saída traduzido pelo serviço NAT para permitir testá-lo.


## <a name="virtual-network-and-parameters-for-destination"></a>Rede virtual e parâmetros para o destino

Antes de implementar um VM para o destino, precisamos criar uma rede virtual onde a máquina virtual de destino possa residir. Seguem-se os mesmos passos que para a origem VM com algumas pequenas alterações para expor o ponto final do destino.

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Criar máquina virtual de destino

1. No lado superior esquerdo do portal, selecione **Criar um recurso**  >  **Compute**  >  **Ubuntu Server 18.04 LTS**, ou procurar **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Na **Criação de uma máquina virtual,** insira ou selecione os seguintes valores no separador **Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: insira **a myVMdestination**.
   - **Detalhes da**  >  instância **Região** > selecione **East US 2**.
   - Conta de **administrador**  >  **Introdução de autenticação**: Selecione **Palavra-passe**.
   - **Conta de administrador** > Introduza o **nome de utilizador,** **palavra-passe**e confirme as informações **de senha.**
   - **Regras portuárias**  >  de entrada **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Regras portuárias**  >  de entrada **Selecione portas de entrada**: Selecione **SSH (22)** e **HTTP (80)**.
   - Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.

3. No **separador Networking** certifique-se de que estão selecionados:
   - **Rede virtual**: **myVnetdestination**
   - **Sub-rede**: **mySubnetdestination**
   - **IP público** > Selecione **Criar novo**.  Na janela **de endereço IP público,** insira **o myPublicIPdestinationVM** no campo **Nome.** Selecione **Standard** para **SKU**. Deixe o resto à predefinição e clique **em OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Selecione portas de entrada**: Confirme **que SSH** e **HTTP** estão selecionados.

4. No separador **Gestão,** em **Monitorização,** desemote **os diagnósticos boot** para **desligar**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Prepare um servidor web e teste a carga útil no VM de destino

Primeiro precisamos descobrir o endereço IP do destino VM. 

1. No lado esquerdo do portal, selecione **grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMdestination**.
4. Em **Visão Geral,** copie o valor **do endereço IP público** e cole-o em bloco de notas para que possa utilizá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Inscreva-se no destino VM

Abra uma [Azure Cloud Shell](https://shell.azure.com) no seu navegador. Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Copie e cole os seguintes comandos depois de ter iniciado sessão.  

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

Estes comandos irão atualizar a sua máquina virtual, instalar nginx e criar um ficheiro de 100 KBytes. Este ficheiro será recuperado a partir da fonte VM utilizando o serviço NAT.

Feche a sessão SSH com o destino VM.

## <a name="prepare-test-on-source-vm"></a>Preparar teste na fonte VM

Primeiro, temos de descobrir o endereço IP da fonte VM.

1. No lado esquerdo do portal, selecione **grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMsource**.
4. Em **Visão Geral,** copie o valor **do endereço IP público** e cole-o em bloco de notas para que possa utilizá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Iniciar sessão na fonte VM

Abra um novo separador para [Azure Cloud Shell](https://shell.azure.com) no seu navegador.  Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Copiar e colar os seguintes comandos para preparar o teste do serviço NAT.

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

Este comando gerará 100 pedidos, 10 simultaneamente, com um intervalo de tempo de 30 segundos, e sem reutilizar a ligação TCP.  Cada pedido recuperará 100 Kbytes.  No final da corrida, **vai** relatar algumas estatísticas sobre como o serviço NAT se saiu bem.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, o gateway NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway NAT e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma porta de entrada NAT, criou uma VM de origem e destino, e depois testou o gateway NAT.

Reveja as métricas no Azure Monitor para ver o seu serviço NAT a funcionar. Diagnosticar problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é facilmente abordado adicionando recursos adicionais de endereços IP públicos ou recursos prefixos IP públicos ou ambos.

- Saiba mais sobre [a Rede Virtual NAT](./nat-overview.md)
- Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
- Quickstart para implantar [recurso de gateway NAT utilizando O Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart para implantar [recurso de gateway NAT utilizando Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart para implantar [recurso de gateway NAT utilizando o portal Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

