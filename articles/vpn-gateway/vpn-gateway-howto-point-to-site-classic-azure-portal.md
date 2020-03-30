---
title: 'Ligar um computador a uma rede virtual através de Ponto a Site e autenticação de certificado: Portal do Azure clássico | Microsoft Docs'
description: Crie uma ligação de gateway de VPN Ponto a Site clássica utilizando o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5d80cb2f2ed844126d1e9311151e6c53fcb11840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244579"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configure uma ligação Ponto-a-Local utilizando a autenticação de certificado (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra-lhe como criar um VNet com uma ligação Ponto-a-Local. Cria este Vnet com o modelo de implantação clássico utilizando o portal Azure. Esta configuração utiliza certificados para autenticar o cliente da ligação, seja autoassinado ou emitido pela AC. Também pode criar esta configuração com uma ferramenta ou modelo de implementação diferente utilizando opções descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Utiliza um gateway VPN Point-to-Site (P2S) para criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As ligações VPN ponto-a-local são úteis quando pretende ligar-se ao vNet a partir de uma localização remota. Quando tem apenas alguns clientes que precisam de se ligar a um VNet, um P2S VPN é uma solução útil para usar em vez de uma VPN site-to-site. É estabelecida uma ligação VPN P2S ao iniciá-la a partir do computador cliente.

> [!IMPORTANT]
> O modelo de implementação clássico suporta apenas clientes VPN do Windows e utiliza SSTP (Secure Socket Tunneling Protocol), um protocolo VPN baseado em SSL. Para suportar clientes VPN não Windows, tem de criar o seu VNet com o modelo de implementação do Gestor de Recursos. O modelo de implementação Resource Manager suporta a VPN IKEv2, além de SSTP. Para obter mais informações, veja [Sobre ligações P2S](point-to-site-about.md).
>
>

![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

As ligações de autenticação do certificado ponto ao local requerem os seguintes pré-requisitos:

* Um gateway de VPN Dinâmico.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Esta chave é considerada um certificado de confiança e é utilizada para autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente NATIVO VPN que já está no sistema operativo com as informações necessárias para ligar ao VNet.

As ligações ponto-a-local não requerem um dispositivo VPN ou um endereço IP virado para o público no local. A ligação VPN é criada através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição. 

Para obter mais informações sobre as ligações Ponto-a-Local, consulte [as FAQ ponto-a-site](#point-to-site-faq).

### <a name="example-settings"></a>Definições de exemplo

Utilize os seguintes valores para criar um ambiente de teste, ou consulte estes valores para entender melhor os exemplos deste artigo:

- **Criar configurações de rede virtual (clássica)**
   - **Nome**: Insira *VNet1*.
   - **Espaço de endereço**: Insira *192.168.0.0/16*. Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet, como apresentado no diagrama.
   - **Nome da sub-rede**: Enter *FrontEnd*.
   - Intervalo de **endereços de sub-rede**: Insira *192.168.1.0/24*.
   - **Subscrição**: Selecione uma subscrição da lista de subscrições disponíveis.
   - **Grupo de recursos**: Enter *TestRG*. Selecione **Criar novos,** se o grupo de recursos não existir.
   - **Localização**: Selecione **East US** da lista.

  - **Definições de ligação VPN**
    - Tipo de **ligação**: Selecione **Ponto-a-local**.
    - **Espaço de endereço do cliente**: Insira *172.16.201.0/24*. Os clientes VPN que se ligam ao VNet utilizando esta ligação Ponto-a-Local recebem um endereço IP da piscina especificada.

- **Definições de sub-rede de configuração gateway**
   - **Nome**: Auto-preenchido com *GatewaySubnet*.
   - Intervalo de **endereços**: Insira *192.168.200.0/24*. 

- **Definições de configuração do gateway:**
   - **Tamanho**: Selecione o portal SKU que pretende utilizar.
   - **Tipo de encaminhamento**: Selecione **Dynamic**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se tem uma assinatura Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: criar uma rede virtual

Se ainda não tem uma rede virtual (VNet), crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo:

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**. A **nova** página abre.

2. No campo **De pesquisa,** introduza a *rede virtual* e selecione a **rede Virtual** a partir da lista devolvida. A página **de rede Virtual** abre.

3. A partir da lista de modelos de **implementação,** selecione **Classic**, e, em seguida, **selecione Criar**. A página de **rede virtual Create** abre.

4. Na página **Criar rede virtual**, configure as definições da VNet. Nesta página, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

   ![Página Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Selecione a **Subscrição** que pretende utilizar na lista de lançamentos.

6. Selecione um **Grupo de Recursos**existente . Ou criar um novo grupo de recursos selecionando **Criar novo** e inserindo um nome. Se estiver a criar um novo grupo de recursos, nomeie o grupo de recursos de acordo com os valores de configuração planeados. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).

7. Selecione um **Local** para o seu VNet. Esta definição determina a localização geográfica dos recursos que utiliza para este VNet.

8. Selecione **Criar** para criar o VNet. Na página **Notificações,** verá uma mensagem **de implantação em curso.**

8. Após a criação da sua rede virtual, a mensagem na página **Notificações** altera-se à **Implementação.** Selecione **Pin para dashboard** se quiser encontrar facilmente o seu VNet no painel de instrumentos. 

10. Adicione um servidor DNS (opcional). Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS para a resolução de nomes. O endereço IP do servidor DNS especificado deve ser o endereço de um servidor DNS que possa resolver os nomes dos recursos na sua VNet.

    Para adicionar um servidor DNS, selecione **servidores DNS** a partir da sua página VNet. Em seguida, introduza o endereço IP do servidor DNS que pretende utilizar e selecione **Guardar**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Parte 2: Criar uma subnet de gateway e um gateway de encaminhamento dinâmico

Neste passo, cria-se uma subnet de gateway e um gateway de encaminhamento dinâmico. No portal Azure para o modelo de implementação clássico, cria-se a subnet gateway e o gateway através das mesmas páginas de configuração. Utilize a sub-rede de porta de entrada apenas para os serviços de gateway. Nunca implemente nada diretamente na sub-rede de gateway (como VMs ou outros serviços).

1. No portal Azure, vá à rede virtual para a qual pretende criar um portal.

2. Na página da sua rede virtual, selecione **Visão Geral**, e na secção de **ligações VPN,** selecione **Gateway**.

   ![Selecione para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na página **Nova Ligação VPN**, selecione **Ponto a site**.

   ![Tipo de ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Para o Espaço de Endereço do **Cliente,** adicione o intervalo de endereçoIP a partir do qual os clientes VPN recebem um endereço IP ao ligar. Utilize uma gama de endereços IP privado que não se sobreponha à localização no local a que se liga, ou à VNet a que se liga. Pode substituir a gama de endereços IP preenchidos automaticamente com a gama de endereços IP privado que pretende utilizar. Este exemplo mostra o alcance auto-preenchido. 

   ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecione **Criar o gateway imediatamente**, e, em seguida, selecione a **configuração opcional do gateway** para abrir a página de **configuração gateway.**

   ![Selecione a configuração opcional do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. A partir da página de **configuração gateway,** selecione **Subnet** para adicionar a sub-rede gateway. É possível criar uma sub-rede de porta tão pequena como /29. No entanto, recomendamos que crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Ao fazê-lo, permitirá endereços suficientes para acomodar possíveis configurações adicionais que poderá desejar no futuro. Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) à sub-rede do gateway. Associar um grupo de segurança de rede a esta subrede pode fazer com que o seu gateway VPN não funcione como esperado. Selecione **OK** para guardar esta definição.

   ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Selecione o **Tamanho** do gateway. O tamanho é o SKU de gateway para o gateway de rede virtual. No portal Azure, o SKU padrão é **Padrão**. Para mais informações sobre as SKUs de gateway, consulte as definições de [gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Selecione **OK** quando terminar de configurar esta página.

   ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na página **new VPN Connection,** selecione **OK** na parte inferior da página para começar a criar o seu portal de rede virtual. Um gateway VPN pode demorar até 45 minutos a ser concluído, dependendo do gateway SKU que selecionar.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Criar certificados

A Azure utiliza certificados para autenticar clientes VPN para VPNs ponto-a-site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada *de confiança.* Os certificados de cliente devem ser gerados a partir do certificado raiz de confiança e, em seguida, instalados em cada computador cliente na loja de certificados Certificados-Utilizador/Utilizador Corrente\Certificados Pessoais\Certificados. O certificado é utilizado para autenticar o cliente quando se conecta ao VNet. 

Se utilizar certificados auto-assinados, devem ser criados utilizando parâmetros específicos. Pode criar um certificado auto-assinado utilizando as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md), ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante seguir os passos nestas instruções quando utiliza certificados de raiz auto-assinados e gera certificados de cliente a partir do certificado de raiz auto-assinado. Caso contrário, os certificados que criar não serão compatíveis com ligações P2S e receberá um erro de ligação.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Adquirir a chave pública (.cer) para o certificado de raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Carregar o ficheiro .cer do certificado de raiz

Depois de criado o gateway, faça o upload do ficheiro .cer (que contém as informações da chave pública) para um certificado de raiz fidedigno para o servidor Azure. Não faça o upload da chave privada para o certificado de raiz. Depois de fazer o upload do certificado, o Azure usa-o para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado raiz de confiança. Mais tarde, pode fazer o upload de ficheiros adicionais de certificado de raiz fidedigno (até 20), se necessário.  

1. Na secção de **ligações VPN** da página para o seu VNet, selecione o gráfico dos clientes para abrir a página **de ligação VPN Ponto-a-site.**

   ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na página **de ligação VPN Ponto-a-site,** selecione **Gerir o certificado** para abrir a página **certificados.**

   ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na página **Certificados,** selecione **Upload** para abrir a página do **certificado de upload.**

    ![Página Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Selecione o gráfico da pasta para procurar o ficheiro .cer. Selecione o ficheiro e, em seguida, selecione **OK**. O certificado carregado aparece na página dos **Certificados.**

   ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Configurar o cliente

Para ligar a um VNet utilizando uma VPN Ponto-a-Site, cada cliente deve instalar um pacote para configurar o cliente VPN nativo do Windows. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para ligar à rede virtual.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para a lista de sistemas operativos do cliente suportados, consulte as [ligações Ponto-a-Local FAQ](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Gerar e instalar um pacote de configuração de cliente VPN

1. No portal Azure, na página **'Overview'** para o seu VNet, em **ligações VPN,** selecione o gráfico do cliente para abrir a página **de ligação VPN Ponto-a-site.**

2. A partir da página **de ligação VPN Point-to-site,** selecione o pacote de descarregamento que corresponde ao sistema operativo do cliente onde está instalado:

   * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.

   ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Depois de o pacote gerar, descarregue-o e instale-o no computador do seu cliente. Se vir um popup SmartScreen, selecione **Mais informações,** então selecione **Executar de qualquer forma**. Também pode guardar o pacote para instalar noutros computadores cliente.

### <a name="install-a-client-certificate"></a>Instalar um certificado de cliente

Para criar uma ligação P2S a partir de um computador cliente diferente daquele usado para gerar os certificados de cliente, instale um certificado de cliente. Quando instala um certificado de cliente, precisa da senha que foi criada quando o certificado de cliente foi exportado. Normalmente, pode instalar o certificado apenas clicando duas vezes. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).


## <a name="connect-to-your-vnet"></a>Ligar à VNet

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente a partir do qual está a ligar.
>
>

1. Para ligar ao seu VNet, no computador cliente, vá às **ligações VPN** no portal Azure e localize a ligação VPN que criou. A ligação VPN tem o mesmo nome que a sua rede virtual. Selecione **Ligar**. Se aparecer uma mensagem pop-up sobre o certificado, selecione **Continue** a utilizar privilégios elevados.

2. Na página de estado de **Ligação,** selecione **Ligar** para iniciar a ligação. Se vir o ecrã **Select Certificate,** verifique se o certificado de cliente apresentado é o correto. Caso contrário, selecione o certificado correto da lista de lançamentos e, em seguida, selecione **OK**.

3. Se a sua ligação for bem sucedida, verá uma notificação **Connected.**


### <a name="troubleshooting-p2s-connections"></a>Resolução de problemas com ligações P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN

1. Verifique se a sua ligação VPN está ativa. Abra um pedido de comando elevado no computador do seu cliente e execute **ipconfig/all**.
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

Pode adicionar e remover certificados de raiz fidedigna do Azure. Ao remover um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz já não podem autenticar e ligar. Para que esses clientes autentiquem e se conectem novamente, é necessário instalar um novo certificado de cliente gerado a partir de um certificado de raiz que é confiado pelo Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, consulte O upload do ficheiro root certificate .cer.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Na secção de **ligações VPN** da página para o seu VNet, selecione o gráfico dos clientes para abrir a página **de ligação VPN Ponto-a-site.**

   ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na página **de ligação VPN Ponto-a-site,** selecione **Gerir o certificado** para abrir a página **certificados.**

   ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na página **Certificados,** selecione a elipse junto ao certificado que pretende remover e, em seguida, **selecione Delete**.

   ![Eliminar certificado de raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Se necessário, pode revogar um certificado de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Este método difere da remoção de um certificado de raiz fidedigno. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para mais informações, consulte [Como: Recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie a informação para um editor de texto e remova os seus espaços de modo a que seja uma corda contínua.
3. Vá à rede virtual clássica. Selecione **a ligação VPN ponto-a-local**e, em seguida, selecione **'Gerir certificado'** para abrir a página **certificados.**
4. Selecione lista de **revogação** para abrir a lista de **revogação.** 
5. Selecione **Adicionar certificado** para abrir o certificado Adicionar à página da lista de **revogação.**
6. Na **impressão digital ,** colar a impressão digital do certificado como uma linha contínua de texto, sem espaços. Selecione **OK** para terminar.

Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentam ligar utilizando este certificado recebem uma mensagem a dizer que o certificado já não é válido.

## <a name="point-to-site-faq"></a>FAQ ponto-a-local

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes

- Depois de a sua ligação estar completa, pode adicionar máquinas virtuais às suas redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). 

- Para saber mais sobre networking e máquinas virtuais Linux, consulte a visão geral da [rede Azure e Linux VM](../virtual-machines/linux/network-overview.md).

- Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
