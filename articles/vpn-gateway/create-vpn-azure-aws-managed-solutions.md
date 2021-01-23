---
title: Criar uma VPN entre Azure e AWS utilizando soluções geridas
description: Como criar uma ligação VPN entre Azure e AWS utilizando soluções geridas, em vez de VMs ou aparelhos.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/22/2021
ms.author: ricmart
ms.openlocfilehash: a0655ce1d2e9939981bb4fd3280af80e359ea1e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737749"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Criar uma ligação VPN entre Azure e AWS utilizando soluções geridas

Pode estabelecer uma ligação entre a Azure e a AWS utilizando soluções geridas. Anteriormente, era-lhe exigido que usasse um aparelho ou VM agindo como resposta. Agora, você pode ligar a porta de entrada virtual AWS virtual ao Azure VPN Gateway diretamente sem ter que se preocupar em gerir recursos IaaS, como máquinas virtuais. Este artigo ajuda-o a criar uma ligação VPN entre a Azure e a AWS utilizando apenas soluções geridas.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagrama de arquitetura":::

## <a name="configure-azure"></a>Configurar Azure

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Configurar uma rede virtual. Para obter instruções, consulte o [Quickstart da Rede Virtual](../virtual-network/quick-create-portal.md).

Neste artigo são utilizados os seguintes valores de exemplo:

* **Grupo de Recursos:** rg-azure-aws
* **Região:** Leste dos EUA
* **Nome da rede virtual:** vnet-azure
* **Espaço de endereço IPv4:** 172.10.0.0/16
* **Nome da sub-rede:** sub-rede-01
* **Intervalo de endereços da sub-rede:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN

Crie uma porta VPN para a sua rede virtual. Para obter instruções, consulte [Tutorial: Criar e gerir um gateway VPN](tutorial-create-gateway-portal.md).

Neste artigo são utilizados os seguintes valores e configurações:

* **Nome gateway:** vpn-azure-aws
* **Região:** Leste dos EUA
* **Tipo de gateway:** VPN
* **Tipo de VPN:** baseado na rota
* **SKU:** VpnGw1
* **Geração:** Geração 1
* **Rede virtual:** Deve ser o VNet para o que quer criar o portal.
* **Intervalo de endereço da sub-rede Gateway:** 172.10.0.0/27
* **Endereço IP público:** Criar novo
* **Nome do endereço IP público:** pip-vpn-azure-aws
* **Ativar o modo ativo:** Desativar
* **Configure BGP:** Desativar

Exemplo:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Resumo do gateway de rede virtual":::

## <a name="configure-aws"></a>Configure AWS

1. Criar a Nuvem Privada Virtual (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Criar informações VPC":::

1. Criar uma sub-rede dentro do VPC (rede virtual).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Criar a sub-rede":::

1. Crie uma porta de entrada para o cliente que aponta para o endereço IP público do Azure VPN Gateway. O **Customer Gateway** é um recurso AWS que contém informações para a AWS sobre o dispositivo de gateway do cliente, que neste caso é o Gateway Azure VPN.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Criar porta de entrada de clientes":::

1. Crie o portal virtual privado.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Criar porta de entrada privada virtual":::

1. Fixe a porta de entrada privada virtual ao VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Ligue o VPG ao VPC":::

1. Selecione o VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Ligar":::

1. Crie uma ligação VPN site-to-site.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Criar ligação VPN":::

1. Desloca a opção de encaminhamento para **Estática** e aponte para o prefixo subnet-01 do Azure **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Definição de uma rota estática":::

1. Depois de preencher as opções, **Crie** a ligação.

1. Descarregue o ficheiro de configuração. Para descarregar a configuração correta, altere o Fornecedor, Plataforma e Software para **Genérico,** uma vez que o Azure não é uma opção válida.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Configuração de descarregamento":::

1. O ficheiro de configuração contém a Chave Pré-Partilhada e o Endereço IP público para cada um dos dois túneis IPsec criados pela AWS.

   **Túnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Túnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Configuração do túnel 1":::

   **Túnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Túnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Configuração do túnel 2":::

1. Depois de criados os túneis, verá algo semelhante a este exemplo.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Detalhes de conexão AWS VPN":::

## <a name="create-local-network-gateway"></a>Criar porta de rede local

Em Azure, o gateway de rede local é um recurso Azure que normalmente representa uma localização no local. É preenchido com informações usadas para ligar ao dispositivo VPN no local. No entanto, nesta configuração, o gateway de rede local é criado e povoado com a informação de ligação virtual de gateway privado AWS. Para obter mais informações sobre as portas de rede locais do [Azure, consulte as definições do Gateway Azure VPN](vpn-gateway-about-vpn-gateway-settings.md#lng).

Crie uma porta de entrada de rede local em Azure. Para etapas, consulte [Criar um gateway de rede local.](tutorial-site-to-site-portal.md#LocalNetworkGateway)

Especificar os seguintes valores:

* **Nome:** No exemplo, usamos lng-azure-aws.
* **Ponto final:** Endereço IP
* **Endereço IP:** O endereço IP público a partir do gateway virtual privado AWS e do prefixo VPC CIDR. Pode encontrar o endereço IP público no ficheiro de configuração que descarregou anteriormente.

A AWS cria dois túneis IPsec para fins de alta disponibilidade. O exemplo a seguir mostra o endereço IP público do Túnel IPsec #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Gateway de rede local":::

## <a name="create-vpn-connection"></a>Criar ligação VPN

Nesta secção, cria-se a ligação VPN entre o gateway de rede virtual Azure e o gateway AWS.

1. Crie a ligação Azure. Para obter passos para criar uma ligação, consulte [Criar uma ligação VPN](tutorial-site-to-site-portal.md#CreateConnection).

   No exemplo seguinte, a tecla Shared foi obtida a partir do ficheiro de configuração que descarregou anteriormente. Neste exemplo, utilizamos os valores para o Túnel IPsec #1 criado pela AWS e descrito no ficheiro de configuração.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Objeto de conexão azul":::

1. Veja a ligação. Após alguns minutos, a ligação é estabelecida.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Conexão de trabalho":::

1. Verifique se o túnel AWS IPsec #1 está **UP**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Verifique se o túnel AWS é UP":::

1. Editar a tabela de rotas associada ao VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Editar a rota":::

1. Adicione a rota para a sub-rede Azure. Esta rota percorrerá o portal VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Salvar a configuração da rota":::

## <a name="configure-second-connection"></a>Configurar segunda ligação

Nesta secção, cria-se uma segunda ligação para garantir uma elevada disponibilidade.

1. Crie outra porta de entrada de rede local que aponte para o endereço IP público do túnel IPsec #2 na AWS. Esta é a porta de entrada.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Criar o portal de rede local":::

1. Crie a segunda ligação VPN de Azure a AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Criar a ligação de gateway de rede local em espera":::

1. Veja as ligações de gateway Azure VPN.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Estado de ligação azul":::

1. Veja as ligações AWS. Neste exemplo, pode ver-se que as ligações estão agora estabelecidas.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Estado de ligação AWS":::

## <a name="to-test-connections"></a>Para testar ligações

1. Adicione uma **porta de entrada** de internet ao VPC na AWS. O portal de internet é uma ligação lógica entre uma VPN Amazon e a Internet. Este recurso permite-lhe ligar através do VM de teste a partir do IP público AWS através da Internet. Este recurso não é necessário para a ligação VPN. Só estamos a usá-lo para testar.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Criar o portal da Internet":::

1. **Selecione Anexar ao VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Anexação da Porta de Internet ao VPC":::

1. Selecione um VPC e **anexe o portal de internet**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Anexar o portal":::

1. Crie uma rota para permitir ligações a **0.0.0.0/0** (Internet) através do portal de internet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Configure a rota através do portal":::

1. Em Azure, a rota é criada automaticamente. Pode consultar a rota a partir do Azure VM selecionando **VM > Networking > Interface de rede > rotas eficazes**. Vê 2 rotas, 1 rota por ligação.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Verifique as rotas eficazes":::

1. Pode testar isto a partir de um Linux VM em Azure. O resultado será semelhante ao seguinte exemplo.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Visão geral do Azure da Linux VM":::

1. Também pode testar isto a partir de um Linux VM em AWS. O resultado será semelhante ao seguinte exemplo.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Visão geral da AWS da Linux VM":::

1. Teste a conectividade a partir do Azure VM.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Teste de ping de Azure":::

1. Teste a conectividade a partir do VM AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Teste de ping da AWS":::

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o suporte da AWS para o IKEv2, consulte o [artigo da AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).
