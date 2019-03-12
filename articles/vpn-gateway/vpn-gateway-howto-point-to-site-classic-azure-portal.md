---
title: 'Ligar um computador a uma rede virtual com o ponto a Site e autenticação de certificado: Clássico de Portal do Azure | Documentos da Microsoft'
description: Crie uma ligação de gateway de VPN Ponto a Site clássica utilizando o portal do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: e71999e9c5b118fbf31d0d735d03cddb321b0065
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773423"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurar uma ligação de ponto a Site através da autenticação de certificado (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra-lhe como criar uma VNet com uma ligação ponto a Site. Criar esta Vnet com o modelo de implementação clássica com o portal do Azure. Esta configuração utiliza certificados para autenticar o cliente da ligação, seja autoassinado ou emitido pela AC. Também pode criar esta configuração com uma ferramenta de implementação diferente ou um modelo ao utilizar as opções que são descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Utilizar um gateway VPN ponto a Site (P2S) para criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. Ligações de VPN ponto a Site são úteis quando pretende ligar a VNet a partir de uma localização remota. Quando tem apenas alguns clientes que precisam de ligar a uma VNet, uma VPN de P2S é uma solução útil para utilizar em vez de uma VPN de Site a Site. É estabelecida uma ligação VPN P2S ao iniciá-la a partir do computador cliente.

> [!IMPORTANT]
> O modelo de implementação clássico suporta apenas clientes VPN do Windows e utiliza SSTP (Secure Socket Tunneling Protocol), um protocolo VPN baseado em SSL. Para suportar clientes VPN não Windows, tem de criar a VNet com o modelo de implementação do Resource Manager. O modelo de implementação Resource Manager suporta a VPN IKEv2, além de SSTP. Para obter mais informações, veja [Sobre ligações P2S](point-to-site-about.md).
>
>

![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

As ligações de autenticação de certificado de ponto a Site requerem os seguintes pré-requisitos:

* Um gateway de VPN Dinâmico.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Esta chave é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente VPN nativo que já se encontra no sistema operativo com as informações necessárias para ligar à VNet.

As ligações ponto a Site não necessitam de um dispositivo VPN ou uma local do endereço IP destinado ao público. A ligação VPN é criada através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição. 

Para obter mais informações sobre ligações ponto a Site, consulte [Point-to-Site FAQ](#point-to-site-faq).

### <a name="example-settings"></a>Definições de exemplo

Utilize os seguintes valores para criar um ambiente de teste ou consultá-estes valores para compreender melhor os exemplos neste artigo:

- **Criar rede virtual definições (clássicas)**
   - **Nome**: Enter *VNet1*.
   - **Espaço de endereços**: Enter *192.168.0.0/16*. Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet, como apresentado no diagrama.
   - **Nome da sub-rede**: Enter *FrontEnd*.
   - **Intervalo de endereços da sub-rede**: Enter *192.168.1.0/24*.
   - **Subscrição**: Selecione uma subscrição na lista de subscrições disponíveis.
   - **Grupo de recursos**: Introduza *TestRG*. Selecione **criar novo**, se o grupo de recursos não existe.
   - **Localização**: Selecione **E.U.A. Leste** da lista.

 - **Definições de ligação de VPN**
   - **Tipo de ligação**: Selecione **Point-to-site**.
   - **Espaço de endereços de cliente**: Enter *172.16.201.0/24*. Os clientes VPN que se ligam à VNet através desta ligação ponto a Site recebem um endereço IP a partir do agrupamento especificado.

- **Configurações de sub-rede do gateway**
   - **Nome**: Autofilled com *GatewaySubnet*.
   - **Intervalo de endereços**: Enter *192.168.200.0/24*. 

- **Definições de configuração do gateway**:
   - **Tamanho**: Selecione o SKU de gateway que pretende utilizar.
   - **Tipo de encaminhamento**: Selecione **dinâmica**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Criar uma rede virtual e um gateway de VPN

Antes de começar, certifique-se de que tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Criar rede virtual

Se ainda não tiver uma rede virtual (VNet), crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo:

1. Inicie sessão para o [portal do Azure](http://portal.azure.com) e selecione **criar um recurso**. O **New** é aberta a página. 

2. Na **pesquisar no marketplace** , insira *rede virtual* e selecione **rede Virtual** da lista devolvida. O **rede Virtual** é aberta a página.

3. Partir do **selecionar um modelo de implementação** , selecione **clássico**e, em seguida, selecione **criar**. O **criar rede virtual** é aberta a página.

4. Na página **Criar rede virtual**, configure as definições da VNet. Nesta página, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

   ![Página Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Selecione o **subscrição** que pretende utilizar na lista pendente.

6. Selecione um existente **grupo de recursos**. Em alternativa, crie um novo grupo de recursos, selecionando **criar novo** e introduzir um nome. Se estiver a criar um novo grupo de recursos, dê um nome de acordo com os valores de configuração planeados. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Selecione um **localização** para a sua VNet. Esta definição determina a localização geográfica dos recursos que implementar nesta vnet.

8. Selecione **criar** para criar a VNet. Partir do **notificações** página, verá um **implementação em curso** mensagem.

8. Depois de sua rede virtual foi criada, a mensagem no **notificações** página muda para **implementação concluída com êxito**. Selecione **afixar ao dashboard** se quiser localizar facilmente a sua VNet no dashboard. 

10. Adicione um servidor DNS (opcional). Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS para a resolução de nomes. O endereço IP do servidor DNS especificado deve ser o endereço de um servidor DNS que possa resolver os nomes dos recursos na sua VNet.

    Para adicionar um servidor DNS, selecione **servidores DNS** da página da VNet. Em seguida, introduza o endereço IP do servidor DNS que pretende utilizar e selecione **guardar**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Parte 2: Criar uma sub-rede de gateway e um gateway de encaminhamento dinâmico

Neste passo, vai criar uma sub-rede de gateway e um gateway de encaminhamento dinâmico. O portal do Azure para o modelo de implementação clássico, vai criar a sub-rede do gateway e o gateway através das mesmas páginas de configuração. Utilize a sub-rede do gateway para apenas os serviços do gateway. Nunca implemente nada diretamente na sub-rede de gateway (como VMs ou outros serviços).

1. No portal do Azure, navegue para a rede virtual para o qual pretende criar um gateway.

2. Na página da sua rede virtual, selecione **descrição geral**e, no **ligações VPN** secção, selecione **Gateway**.

  ![Selecione para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na página **Nova Ligação VPN**, selecione **Ponto a site**.

  ![Tipo de ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Para **espaço de endereços de cliente**, adicione o intervalo de endereços IP do qual os clientes VPN recebem um endereço IP ao ligar. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local onde estabelecer ligação, ou com a VNet a que se liga a. Pode substituir o intervalo de autofilled com o intervalo de endereços IP privados que pretende utilizar. Este exemplo mostra o intervalo de autofilled. 

  ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecione **criar gateway de imediato**e, em seguida, selecione **configuração de gateway opcional** para abrir o **configuração do Gateway** página.

  ![Selecione a configuração do gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Partir do **configuração do Gateway** página, selecione **sub-rede** para adicionar a sub-rede de gateway. É possível criar uma sub-rede de gateway tão pequena como/29. No entanto, recomendamos que crie uma sub-rede maior que inclua mais endereços selecionando pelo menos/28 ou/27. Isso permitirá que endereços suficientes acomodar possíveis configurações adicionais que talvez deseje no futuro. Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. Associar um grupo de segurança de rede para esta sub-rede pode fazer com que o gateway de VPN não funcione conforme esperado. Selecione **OK** para guardar esta definição.

  ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Selecione o **Tamanho** do gateway. O tamanho é o SKU de gateway para o gateway de rede virtual. SKU no portal do Azure, a predefinição é **predefinição**. Para obter mais informações sobre os SKUs de gateway, consulte [definições do gateway de VPN sobre](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Selecione **OK** quando terminar a configuração desta página.

  ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Sobre o **nova ligação VPN** página, selecione **OK** na parte inferior da página para começar a criar o gateway de rede virtual. Um gateway VPN pode demorar até 45 minutos a concluir, consoante o SKU de gateway que selecionar.
 
## <a name="generatecerts"></a>Criar certificados

O Azure utiliza certificados para autenticar os clientes VPN em VPNs ponto a Site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública, em seguida, é considerada *fidedigna*. Certificados de cliente tem de ser gerados a partir do certificado de raiz fidedigna e, em seguida, instalados em cada computador cliente no arquivo de certificados ' Certificates-Current User\Personal\Certificates '. O certificado é utilizado para autenticar o cliente para estabelecer a ligação à VNet. 

Se utilizar certificados autoassinados, tem de ser criadas com parâmetros específicos. Pode criar um certificado autoassinado com as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md), ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante seguir os passos nestas instruções, quando utilizar certificados de raiz autoassinados e gerar certificados de cliente a partir do certificado de raiz autoassinado. Caso contrário, os certificados que criar não serão compatíveis com ligações de P2S e irá receber um erro de ligação.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Obter a chave pública (. cer) para o certificado de raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Carregar o ficheiro .cer do certificado de raiz

Depois do gateway tiver sido criado, carregue o ficheiro. cer (que contém as informações da chave públicas) para um certificado de raiz fidedigna para o servidor do Azure. Não carregue a chave privada do certificado de raiz. Depois de carregar o certificado, o Azure utiliza-a para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Mais tarde, pode carregar ficheiros de certificado de raiz fidedigna adicionais (até 20), se necessário.  

1. Sobre o **ligações VPN** secção da página para a sua VNet, selecione o gráfico de clientes para abrir o **Point-to-site VPN ligação** página.

  ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Sobre o **Point-to-site VPN ligação** página, selecione **gerir certificado** para abrir o **certificados** página.

  ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Sobre o **certificados** página, selecione **carregar** para abrir o **carregar certificado** página.

    ![Página Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Selecione a imagem de pasta para procurar o ficheiro. cer. Selecione o ficheiro, em seguida, selecione **OK**. O certificado carregado aparece no **certificados** página.

  ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Configurar o cliente

Para ligar a uma VNet com uma VPN ponto a Site, cada cliente tem de instalar um pacote para configurar o cliente VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para ligar à rede virtual.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operativos cliente suportados, consulte a [ligaçõesdePoint-to-Site FAQ](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Gerar e instalar um pacote de configuração de cliente VPN

1. No portal do Azure, no **descrição geral** página para a sua VNet, no **ligações VPN**, selecione o gráfico de cliente para abrir o **Point-to-site VPN ligação** página.

2. Partir do **Point-to-site VPN ligação** , selecione o pacote de transferência que corresponde ao sistema operativo cliente onde está instalado:

  * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
  * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.

  ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Depois de gera o pacote, transferi-lo e, em seguida, instale-o no seu computador cliente. Se vir um pop-up SmartScreen, selecione **mais informações**, em seguida, selecione **executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.

### <a name="install-a-client-certificate"></a>Instalar um certificado de cliente

Para criar uma ligação P2S a partir de um computador cliente diferente daquele usado para gerar os certificados de cliente, instale um certificado de cliente. Ao instalar um certificado de cliente, terá da palavra-passe que foi criada quando o certificado de cliente foi exportado. Normalmente, pode instalar o certificado, basta fazer duplo clique. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).


## <a name="connect-to-your-vnet"></a>Ligar à VNet

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente a partir do qual está a ligar.
>
>

1. Para ligar a VNet, no computador cliente, navegue para **ligações VPN** no portal do Azure e localize a ligação de VPN que criou. A ligação VPN tenha o mesmo nome que a rede virtual. Selecione **Ligar**. Se for apresentada uma mensagem de pop-up sobre o certificado, selecione **continuar** para utilizar privilégios elevados.

2. Sobre o **conexão** página de estado, selecione **Connect** para iniciar a ligação. Se vir a **Select Certificate** ecrã, certifique-se de que o certificado de cliente apresentado é a correta. Se não estiver, selecione o certificado correto na lista pendente e, em seguida, selecione **OK**.

3. Se a ligação tiver êxito, verá uma **ligado** notificação.


### <a name="troubleshooting-p2s-connections"></a>Resolução de problemas com ligações P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN

1. Certifique-se de que a ligação VPN está ativa. Abra uma linha de comandos elevada no computador cliente e execute **ipconfig/all**.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser semelhantes a este exemplo:

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connect-to-a-virtual-machine"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz já não podem autenticar e ligar. Para esses clientes para se autenticar e ligar novamente, tem de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno pelo Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, consulte a carregar o ficheiro. cer do certificado de raiz.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Sobre o **ligações VPN** secção da página para a sua VNet, selecione o gráfico de clientes para abrir o **Point-to-site VPN ligação** página.

   ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Sobre o **Point-to-site VPN ligação** página, selecione **gerir certificado** para abrir o **certificados** página.

   ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Sobre o **certificados** página, selecione as reticências junto ao certificado que pretende remover, em seguida, selecione **eliminar**.

   ![Eliminar certificado de raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Se necessário, pode revogar um certificado de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Este método é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, consulte [como: Obtenha o Thumbprint de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova sua espaços, para que seja uma cadeia contínua.
3. Navegue para a rede virtual clássica. Selecione **Point-to-site VPN ligação**, em seguida, selecione **gerir certificado** para abrir o **certificados** página.
4. Selecione **lista de revogação** para abrir o **lista de revogação** página. 
5. Selecione **adicionar certificado** para abrir o **adicionar certificado à lista de revogação** página.
6. Na **Thumbprint**, cole o thumbprint do certificado como uma linha contínua de texto, sem espaços. Selecione **OK** para concluir.

Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentarem ligar com este certificado recebem uma mensagem a indicar que o certificado já não é válido.

## <a name="point-to-site-faq"></a>FAQ ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Passos Seguintes

- Depois da ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). 

- Para compreender melhor o funcionamento em rede e máquinas virtuais do Linux, veja [descrição geral da rede do Azure e a VM do Linux](../virtual-machines/linux/network-overview.md).

- Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
