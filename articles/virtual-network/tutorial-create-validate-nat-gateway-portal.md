---
title: 'Tutorial: Criar e testar um PORTAL NAT Gateway - Azure'
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um NaT Gateway usando o portal Azure e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 3468dd72ff47c895ee4df6ed4308aabab92c33f1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216949"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Tutorial: Criar um Portal NAT utilizando o portal Azure e testar o serviço NAT

Neste tutorial, você vai criar uma porta de entrada NAT para fornecer conectividade de saída para máquinas virtuais em Azure. Para testar o portal NAT, você implanta uma máquina virtual de origem e destino. Você vai testar o gateway NAT fazendo ligações de saída a um endereço IP público da fonte para a máquina virtual de destino.  Este tutorial implanta fonte e destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para simplicidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Prepare a fonte para o tráfego de saída

Vamos guiá-lo através da configuração de um ambiente de teste completo e da execução dos testes em si nos próximos passos. Começaremos com a fonte, que usará o recurso de gateway NAT que criamos em etapas posteriores.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Antes de implementar um VM e poder usar o seu portal NAT, precisamos de criar o grupo de recursos e a rede virtual.

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<nome de grupo de recursos>**  | myResourceGroupNAT |
| **\<nome de rede virtual>** | myVNetsource          |
| **\<nome da região>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<sub-nome>**          | mySubnetsource        |
| **\<sub-endereço-gama>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Criar a fonte máquina virtual

Vamos agora criar um VM para usar o serviço NAT. Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM. O serviço NAT está consciente e substituirá o destino de Internet padrão na sua subnet. O endereço IP público da VM não será usado para ligações de saída.

Para testar o portal NAT, atribuiremos um recurso público de endereço IP como um IP público de nível de instância para aceder a este VM a partir do exterior. Este endereço só é utilizado para aceder ao teste.  Vamos demonstrar como o serviço NAT tem precedência sobre outras opções de saída.

Você também poderia criar este VM sem um IP público e criar outro VM para usar como uma caixa de salto sem um IP público como um exercício.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Ubuntu Server 18.04 LTS,** ou procurar **ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual,** introduza ou selecione os seguintes valores no separador **Basics:**
   - **Grupo de** **recursos** > de assinatura : Selecione **myResourceGroupNAT**.
   - **Detalhes** da ocorrência > **nome virtual**da máquina : introduza **myVMsource**.
   - **Detalhes da instância** > **Região** > selecione **East US 2**.
   - **Conta de administrador** > **Autenticação introduza**: Selecione **Password**.
   - **Conta administradora** > Introduza o nome de **utilizador,** **palavra-passe**e confirme informações de **senha.**
   - **Regras** de entrada > **portas de entrada públicas**: Selecione permitir portas **selecionadas**.
   - **Regras de entrada** > **Selecione portas de entrada :** Selecione **SSH (22)**
   - Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**.

3. No separador **de rede** certifique-se de que são selecionados os seguintes:
   - **Rede virtual**: **myVnetsource**
   - **Subnet**: **mySubnetsource**
   - **IP &gt público;** **Selecione Criar novo**.  Na janela **de endereços IP públicos Criar,** introduza o **myPublicIPsourceVM** no campo **Nome.** Selecione **Standard** para o **SKU**. Deixe o resto nas predefinições e clique **OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecione portas de entrada**: Confirme se está selecionado **o SSH.**

4. No separador **Gestão,** sob **monitorização,** desembare **os diagnósticos da bota** para **desligar**.

5. Selecione **Rever + criar**.

6. Reveja as definições e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o Portal naT

Pode utilizar um ou mais recursos públicos de endereçoip, prefixos IP públicos ou ambos com gateway NAT. Adicionaremos um recurso IP público, prefixo ip público, e um recurso de gateway NAT.

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool ip público e um prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Mude o tempo de inatividade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **networking** > **endereço IP público,** ou procurar endereço **IP público** na pesquisa do Marketplace. 

2. No **Endereço IP do Público,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Standard**.
    | Nome | **Insira o meu PublicIPsource**. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **myResourceGroupNAT**. |
    | Localização | Selecione **E.U.A. Leste 2**.|

3. Deixe o resto dos predefinições e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Networking** > **prefixo IP público,** ou procurar **prefixo IP público** na pesquisa do Marketplace.

2. Em **Criar um prefixo IP público,** introduza ou selecione os **seguintes valores** no separador Basics:
   - **Grupo de** **recursos** > de assinatura : Selecione **myResourceGroupNAT**>
   - **Detalhes** da > **Nome**: **introduza o meu PublicIPprefixsource**.
   - **Detalhes da instância** > **Região**: Selecione **East US 2**.
   - **Detalhes da > ** **tamanho prefixo**: Selecione **/31 (2 endereços)**

3. Deixe o resto os defeitos e selecione **Rever + criar**.

4. Reveja as definições e, em seguida, selecione **Criar**.


### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Networking** > **gateway NAT,** ou procurar gateway **NAT** na pesquisa do Marketplace.

2. Na **Create network address translation (NAT) gateway,** insira ou selecione os seguintes valores no separador **Basics:**
   - **Grupo de** **recursos** > de assinatura : Selecione **myResourceGroupNAT**.
   - **Detalhes da instância** > **nome de gateway NAT:** insira **myNATgateway**.
   - **Detalhes da instância** > **Região**: Selecione **East US 2**.
   - **Detalhes da > ** **tempo limite de inativação (minutos)** : insira **10**.
   - Selecione o **separador IP público** ou selecione **Seguinte: IP público**.

3. No **separador IP público,** introduza ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIPsource**.
   - **Prefixos IP públicos**: Selecione **myPublicIPprefixsource**.
   - Selecione o separador **Subnet** ou selecione **Seguinte: Subnet**.

4. No separador **Subnet,** introduza ou selecione os seguintes valores:
   - **Rede Virtual**: Selecione **myResourceGroupNAT** > **myVnetsource**.
   - **Nome da sub-rede**: Selecione a caixa ao lado do **mySubnetsource**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, selecione **Criar**.

Todo o tráfego de saída para destinos da Internet está agora a usar o serviço NAT.  Não é necessário configurar um UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para tráfego de saída

Vamos agora criar um destino para o tráfego de saída traduzido pelo serviço NAT para permitir que o teste.


## <a name="virtual-network-and-parameters-for-destination"></a>Rede virtual e parâmetros para destino

Antes de implementar um VM para o destino, precisamos de criar uma rede virtual onde a máquina virtual de destino possa residir. Seguem-se os mesmos passos que para a fonte VM com algumas pequenas alterações para expor o ponto final do destino.

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<nome de grupo de recursos>**  | myResourceGroupNAT |
| **\<nome de rede virtual>** | myVNetdestination          |
| **\<nome da região>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<sub-nome>**          | mySubnetdestination        |
| **\<sub-endereço-gama>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Criar máquina virtual de destino

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Ubuntu Server 18.04 LTS,** ou procurar **ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual,** introduza ou selecione os seguintes valores no separador **Basics:**
   - **Grupo de** **recursos** > de assinatura : Selecione **myResourceGroupNAT**.
   - **Detalhes da > ** **nome virtual**da máquina : insira **myVMdestination**.
   - **Detalhes da instância** > **Região** > selecione **East US 2**.
   - **Conta de administrador** > **Autenticação introduza**: Selecione **Password**.
   - **Conta administradora** > Introduza o nome de **utilizador,** **palavra-passe**e confirme informações de **senha.**
   - **Regras** de entrada > **portas de entrada públicas**: Selecione permitir portas **selecionadas**.
   - **As regras da porta de entrada** > **Selecione as portas de entrada:** Selecione **SSH (22)** e **HTTP (80)** .
   - Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**.

3. No separador **de rede** certifique-se de que são selecionados os seguintes:
   - **Rede virtual**: **myVnetdestination**
   - **Subnet**: **mySubnetdestination**
   - **IP &gt público;** **Selecione Criar novo**.  Na janela **de endereços IP públicos Criar,** insira o **myPublicIPdestinationVM** no campo **Nome.** Selecione **Standard** para **SKU**. Deixe o resto nas predefinições e clique **OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecione portas de entrada**: Confirme **SSH** e **HTTP** selecionados.

4. No separador **Gestão,** sob **monitorização,** desembare **os diagnósticos da bota** para **desligar**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, selecione **Criar**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Prepare um servidor web e teste a carga útil no destino VM

Primeiro precisamos descobrir o endereço IP do destino VM. 

1. No lado esquerdo do portal, selecione **Grupos de Recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMdestination**.
4. Em **resumo,** copie o valor do **endereço IP público** e cole no bloco de notas para que possa usá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo em passos subsequentes. Indique que esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Inscreva-se no destino VM

Abra uma [Nuvem Azure](https://shell.azure.com) Shell no seu navegador. Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Copie e cole os seguintes comandos assim que iniciar sessão.  

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

Primeiro, precisamos descobrir o endereço IP da fonte VM.

1. No lado esquerdo do portal, selecione **Grupos de Recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMsource**.
4. Em **resumo,** copie o valor do **endereço IP público** e cole no bloco de notas para que possa usá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo em passos subsequentes. Indique que esta é a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Iniciar sessão na Fonte VM

Abra um novo separador para [Azure Cloud Shell](https://shell.azure.com) no seu navegador.  Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual. 

```azurecli-interactive
ssh <username>@<ip-address-source>
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

Este comando gerará 100 pedidos, 10 simultaneamente, com um tempo de 30 segundos, e sem reutilizar a ligação TCP.  Cada pedido recuperará 100 Kbytes.  No final da corrida, **o hey** irá reportar algumas estatísticas sobre como o serviço NAT fez bem.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o portal NAT e todos os recursos conexos. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway NAT e, em seguida, **selecione Eliminar**.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um portal NAT, criou uma Fonte e destino VM, e depois testou o portal NAT.

Reveja as métricas no Monitor Azure para ver o seu serviço NAT a funcionar. Diagnosticar problemas como a exaustão de recursos das portas SNAT disponíveis.  A exaustão de recursos das portas SNAT é facilmente abordada adicionando recursos de endereços IP públicos adicionais ou recursos públicos de prefixo IP ou ambos.

- Conheça a [Rede Virtual NAT](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Arranque rápido para a implementação do [recurso de gateway NAT utilizando](./quickstart-create-nat-gateway-portal.md)o portal Azure .

> [!div class="nextstepaction"]

