---
title: Gerir o acesso seguro aos recursos em VNets falados para clientes P2S
titleSuffix: Azure Virtual WAN
description: Este artigo ajuda-o a utilizar as regras Azure Virtual WAN e Azure Firewall para gerir o acesso seguro a redes virtuais para clientes VPN (ponto a local) do utilizador.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048268"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Gerir o acesso seguro aos recursos em VNets falados para clientes VPN de utilizador

Este artigo mostra-lhe como utilizar as regras e filtros Virtual WAN e Azure Firewall para gerir o acesso seguro às ligações aos seus recursos em Azure sobre ligações iKEv2 ou Open VPN. Esta configuração é útil se tiver utilizadores remotos para os quais pretende restringir o acesso aos recursos do Azure ou garantir os seus recursos em Azure.

Os passos deste artigo ajudam a criar a arquitetura no seguinte diagrama para permitir que os clientes VPN do utilizador acedam a um recurso específico (VM1) num VNet falado ligado ao centro virtual, mas não outros recursos (VM2). Use este exemplo de arquitetura como uma orientação básica.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagrama: Centro virtual seguro" :::

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Tem os valores disponíveis para a configuração de autenticação que pretende utilizar. Por exemplo, um servidor RADIUS, autenticação do Diretório Azure Ative ou [Certificados de Geração e Exportação](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Criar uma WAN Virtual

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definir parâmetros de configuração P2S

A configuração ponto-a-local (P2S) define os parâmetros para a ligação de clientes remotos. Esta secção ajuda-o a definir parâmetros de configuração P2S e, em seguida, a criar a configuração que será usada para o perfil do cliente VPN. As instruções que segue dependem do método de autenticação que pretende utilizar.

### <a name="authentication-methods"></a>Métodos de autenticação

Ao selecionar o método de autenticação, tem três opções. Cada método tem requisitos específicos. Selecione um dos seguintes métodos e, em seguida, complete os passos.

* **Autenticação do Diretório Ativo Azure:** Obter o seguinte:

   * O ID de **aplicação** da Aplicação Empresarial Azure VPN registrado no seu inquilino AZure AD.
   * O **Emitente.** Exemplo: `https://sts.windows.net/your-Directory-ID`.
   * O **inquilino da AD Azure.** Exemplo: `https://login.microsoftonline.com/your-Directory-ID`.

* **Autenticação baseada em raio:** Obtenha o IP do servidor Radius, o segredo do servidor Radius e as informações do certificado.

* **Certificados Azure:** Para esta configuração, são necessários certificados. É necessário gerar ou obter certificados. É necessário um certificado de cliente para cada cliente. Além disso, a informação do certificado de raiz (chave pública) precisa de ser carregada. Para obter mais informações sobre os certificados necessários, consulte [certificados de geração e exportação.](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Crie o hub e gateway

Nesta secção, você cria o hub virtual com uma porta de entrada ponto-a-local. Ao configurar, pode utilizar os seguintes valores de exemplo:

* **Espaço de endereço IP privado do hub:** 10.0.0.0/24
* **Piscina de endereços do cliente:** 10.5.0.0/16
* **Servidores DNS personalizados:** Pode listar até 5 Servidores DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Gerar ficheiros de configuração de clientes VPN

Nesta secção, gera e descarrega os ficheiros de perfil de configuração. Estes ficheiros são usados para configurar o cliente VPN nativo no computador cliente. Para obter informações sobre o conteúdo dos ficheiros de perfil do cliente, consulte [a configuração ponto-a-local - certificados](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Configurar clientes VPN

Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento para cada sistema operativo é diferente, siga as instruções que se aplicam ao seu sistema.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Ligue o VNet falado

Nesta secção, você anexa a rede virtual falada ao centro WAN virtual.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Criar máquinas virtuais

Nesta secção, cria-se dois VMs no seu VNet, VM1 e VM2. No diagrama da rede, utilizamos 10.18.0.4 e 10.18.0.5. Ao configurar os seus VMs, certifique-se de selecionar a rede virtual que criou (encontrada no separador 'Rede'). Para obter passos para criar um VM, consulte [Quickstart: Criar um VM](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Proteja o centro virtual

Um hub virtual padrão não tem políticas de segurança incorporadas para proteger os recursos em redes virtuais faladas. Um hub virtual seguro utiliza o Azure Firewall ou um fornecedor de terceiros para gerir o tráfego de entrada e saída para proteger os seus recursos em Azure.

Converta o hub num hub seguro utilizando o seguinte artigo: [Configurar a Azure Firewall num hub VIRTUAL WAN](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Criar regras para gerir e filtrar tráfego

Crie regras que ditam o comportamento do Azure Firewall. Ao proteger o hub, garantimos que todos os pacotes que entram no centro virtual estão sujeitos ao processamento de firewall antes de aceder aos seus recursos Azure.

Assim que completar estes passos, terá criado uma arquitetura que permite aos utilizadores VPN aceder ao VM com endereço IP privado 10.18.0.4, mas **NÃO** aceder ao VM com endereço IP privado 10.18.0.5

1. No portal Azure, navegue para **o Gestor de Firewall.**
1. Em Segurança, selecione **as políticas Azure Firewall**.
1. Selecione **Criar Azure Firewall Policy**.
1. De acordo com **os detalhes da Política,** escreva um nome e selecione a região onde o seu hub virtual está implantado.
1. Selecione **Seguinte: Definições DE DNS (pré-visualização)**.
1. Selecione **Seguinte: Regras**.
1. No **separador Regras,** **selecione Adicione uma coleção de regras**.
1. Forneça um nome para a coleção. Definir o tipo como **Rede**. Adicione um valor prioritário **100**.
1. Preencha o nome da regra, tipo de origem, fonte, protocolo, portas de destino e tipo de destino, como mostra o exemplo abaixo. Em seguida, **selecione adicionar**. Esta regra permite que qualquer endereço IP do pool de clientes VPN aceda ao VM com endereço IP privado 10.18.04, mas não qualquer outro recurso ligado ao centro virtual. Crie quaisquer regras que queira que se encaixem nas regras de arquitetura e permissões desejadas.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Regras da firewall" :::

1. Selecione **Seguinte: Inteligência de ameaça**.
1. Selecione **Seguinte: Hubs**.
1. No separador **Hubs,** **selecione Centros virtuais Associados**.
1. Selecione o hub virtual que criou anteriormente e, em seguida, **selecione Add**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

Pode levar 5 minutos ou mais para que este processo esteja concluído.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Tráfego de rota através da Firewall de Azure

Nesta secção, é necessário garantir que o tráfego é encaminhado através do Azure Firewall.

1. No portal, a partir do **Firewall Manager,** selecione **centros virtuais seguros**.
1. Selecione o hub virtual que criou.
1. Em **Definições**, selecione **configuração de segurança**.
1. Sob **tráfego privado**, selecione Enviar através do **Azure Firewall**.
1. Verifique se a ligação VNet e a ligação do Ramo o tráfego privado está protegido pela Azure Firewall.
1. Selecione **Guardar**.

## <a name="validate"></a><a name="validate"></a>Validação

Verifique a configuração do seu hub seguro.

1. Ligue-se ao **Hub Virtual Seguro** via VPN a partir do seu dispositivo cliente.
1. O endereço IP **10.18.0.4** do seu cliente. Devia ver uma resposta.
1. O endereço IP **10.18.0.5** do seu cliente. Não deve ver uma resposta.

### <a name="considerations"></a>Considerações

* Certifique-se de que a **Tabela de Rotas Eficazes** no centro virtual seguro tem o próximo salto para tráfego privado junto à firewall. Para aceder à Tabela de Rotas Eficazes, navegue para o seu recurso **Virtual Hub.** Em **Conectividade**, selecione **Encaminhamento** e, em seguida, selecione **Rotas Eficazes**. A partir daí, selecione a tabela **Rota Padrão.**
* Verifique se criou regras na secção [Criar Regras.](#create-rules) Se estes passos forem perdidos, as regras que criou não serão realmente associadas ao hub e a tabela de rotas e o fluxo de pacotes não utilizarão o Azure Firewall.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte o [VIRTUAL WAN FAQ](virtual-wan-faq.md).
* Para obter mais informações sobre o Azure Firewall, consulte o [Azure Firewall FAQ](../firewall/firewall-faq.yml).
