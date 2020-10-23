---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "91024898"
---
1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Criar um recurso**.

   ![Recurso](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. No campo **'Pesquisar no mercado',** **digite gateway de rede local**e, em seguida, prima **Enter** para pesquisar. Isto irá devolver uma lista de resultados. Clique em **Gateway de rede Local** e, em seguida, clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Criar um endereço IP de gateway de rede local":::

3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

   - **Nome:** Especifique um nome para seu objeto de gateway de rede local.
   - **Ponto final:** Selecione o tipo de ponto final para o dispositivo VPN no local - **endereço IP** ou **FQDN (Nome de domínio totalmente qualificado)**.
      - **Endereço IP**: Se tiver um endereço IP público estático atribuído ao seu fornecedor de serviços de Internet para o seu dispositivo VPN, selecione a opção de endereço IP e preencha o endereço IP como mostrado no exemplo. Este é o endereço IP público do dispositivo VPN a que pretende que a porta de entrada Azure VPN se conecte. Se não tiver o endereço IP no momento, pode usar os valores mostrados no exemplo, mas precisará voltar atrás e substituir o endereço IP do marcador de posição pelo endereço IP público do seu dispositivo VPN. Caso contrário, o Azure não será capaz de se ligar.
      - **FQDN**: Se tiver um endereço IP público dinâmico que pode ser alterado após determinado período de tempo, normalmente determinado pelo seu fornecedor de serviços de Internet, pode utilizar um nome DNS constante com um serviço DNS Dinâmico para apontar para o seu endereço IP público atual do seu dispositivo VPN. O seu gateway Azure VPN resolverá o FQDN para determinar o endereço IP público para se ligar. Uma imagem abaixo mostra um exemplo de utilização de FQDN em vez de endereço IP.
   - O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. *Se pretender ligar ao seu site no local utilize os seus próprios valores, não os valores mostrados no exemplo*.
   - **Configurar definições de BGP:** utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
   - **Subscrição:** Verifique se é apresentada a subscrição correta.
   - **Grupo de Recursos:** Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
   - **Localização:** A localização é a mesma **da Região** em outros cenários. Selecione a localização em que este objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

   Esta é a mesma página, mas com fQDN em destaque:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Criar um endereço IP de gateway de rede local":::

   > [!NOTE]
   >
   > * A Azure VPN suporta apenas um endereço IPv4 para cada FQDN. Se o nome de domínio resolver vários endereços IP, o Azure VPN Gateway utilizará o primeiro endereço IP devolvido pelos servidores DNS. Para eliminar a incerteza, recomendamos que o seu FQDN resolva sempre um único endereço IPv4. O IPv6 não é apoiado.
   > * A Azure VPN Gateway mantém uma cache DNS renovada a cada 5 minutos. O portal tenta resolver as FQDNs apenas para túneis desligados. A reposição do gateway também irá desencadear a resolução FQDN.
   >

4. Quando terminar de especificar os valores, selecione o botão **Criar** na parte inferior da página para criar o gateway de rede local.
