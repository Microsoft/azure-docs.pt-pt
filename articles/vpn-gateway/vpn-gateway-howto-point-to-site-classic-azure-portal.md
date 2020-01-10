---
title: 'Ligar um computador a uma rede virtual através de Ponto a Site e autenticação de certificado: Portal do Azure clássico | Microsoft Docs'
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
ms.openlocfilehash: 01327d24aebee02c3b14594c2b0b2f2f175211fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450815"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurar uma conexão ponto a site usando a autenticação de certificado (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra como criar uma VNet com uma conexão ponto a site. Você cria essa vnet com o modelo de implantação clássico usando o portal do Azure. Esta configuração utiliza certificados para autenticar o cliente da ligação, seja autoassinado ou emitido pela AC. Você também pode criar essa configuração com uma ferramenta ou modelo de implantação diferente usando as opções descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Você usa um gateway de VPN ponto a site (P2S) para criar uma conexão segura com sua rede virtual a partir de um computador cliente individual. As conexões VPN ponto a site são úteis quando você deseja se conectar à sua VNet de um local remoto. Quando você tem apenas alguns clientes que precisam se conectar a uma VNet, uma VPN P2S é uma solução útil para usar em vez de uma VPN site a site. É estabelecida uma ligação VPN P2S ao iniciá-la a partir do computador cliente.

> [!IMPORTANT]
> O modelo de implementação clássico suporta apenas clientes VPN do Windows e utiliza SSTP (Secure Socket Tunneling Protocol), um protocolo VPN baseado em SSL. Para dar suporte a clientes VPN não Windows, você deve criar sua VNet com o modelo de implantação do Gerenciador de recursos. O modelo de implementação Resource Manager suporta a VPN IKEv2, além de SSTP. Para obter mais informações, veja [Sobre ligações P2S](point-to-site-about.md).
>
>

![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

As conexões de autenticação de certificado ponto a site exigem os seguintes pré-requisitos:

* Um gateway de VPN Dinâmico.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Essa chave é considerada um certificado confiável e é usada para autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente VPN nativo que já está no sistema operacional com as informações necessárias para se conectar à VNet.

As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público local. A ligação VPN é criada através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição. 

Para obter mais informações sobre conexões ponto a site, consulte [FAQ de ponto a site](#point-to-site-faq).

### <a name="example-settings"></a>Definições de exemplo

Use os valores a seguir para criar um ambiente de teste ou consulte esses valores para entender melhor os exemplos neste artigo:

- **Criar configurações de rede virtual (clássica)**
   - **Nome**: insira *VNet1*.
   - **Espaço de endereço**: insira *192.168.0.0/16*. Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet, como apresentado no diagrama.
   - **Nome da sub-rede**: insira *front-end*.
   - **Intervalo de endereços da sub-rede**: insira *192.168.1.0/24*.
   - **Assinatura**: selecione uma assinatura na lista de assinaturas disponíveis.
   - **Grupo de recursos**: insira *TestRG*. Selecione **criar novo**se o grupo de recursos não existir.
   - **Local**: selecione **leste dos EUA** na lista.

  - **Configurações de conexão VPN**
    - **Tipo de conexão**: selecione **ponto a site**.
    - **Espaço de endereço do cliente**: insira *172.16.201.0/24*. Clientes VPN que se conectam à VNet usando essa conexão ponto a site recebem um endereço IP do pool especificado.

- **Configurações de sub-rede configuração do gateway**
   - **Nome**: preenchido com *GatewaySubnet*.
   - **Intervalo de endereços**: insira *192.168.200.0/24*. 

- **Definições de configuração do gateway**:
   - **Tamanho**: selecione a SKU de gateway que você deseja usar.
   - **Tipo de roteamento**: selecione **dinâmico**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: criar uma rede virtual

Se você ainda não tiver uma rede virtual (VNet), crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo:

1. No menu [portal do Azure](https://portal.azure.com) ou na **Home** Page do, selecione **criar um recurso**. O **New** é aberta a página.

2. Na **pesquisar no marketplace** , insira *rede virtual* e selecione **rede Virtual** da lista devolvida. O **rede Virtual** é aberta a página.

3. Na lista **selecionar um modelo de implantação** , selecione **clássico**e, em seguida, selecione **criar**. O **criar rede virtual** é aberta a página.

4. Na página **Criar rede virtual**, configure as definições da VNet. Nesta página, deve adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

   ![Página Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Selecione a **assinatura** que você deseja usar na lista suspensa.

6. Selecione um **grupo de recursos**existente. Ou crie um novo grupo de recursos selecionando **criar novo** e inserindo um nome. Se você estiver criando um novo grupo de recursos, nomeie o grupo de recursos de acordo com os valores de configuração planejados. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).

7. Selecione um **local** para sua VNet. Essa configuração determina a localização geográfica dos recursos que você implanta nesta VNet.

8. Selecione **criar** para criar a VNet. Na página **notificações** , você verá uma mensagem **implantação em andamento** .

8. Depois que a rede virtual tiver sido criada, a mensagem na página **notificações** será alterada para **implantação bem-sucedida**. Selecione **fixar no painel** se desejar localizar facilmente sua VNet no painel. 

10. Adicione um servidor DNS (opcional). Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS para a resolução de nomes. O endereço IP do servidor DNS especificado deve ser o endereço de um servidor DNS que possa resolver os nomes dos recursos na sua VNet.

    Para adicionar um servidor DNS, selecione **servidores DNS** na página VNet. Em seguida, digite o endereço IP do servidor DNS que você deseja usar e selecione **salvar**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Parte 2: criar uma sub-rede de gateway e um gateway de roteamento dinâmico

Nesta etapa, você cria uma sub-rede de gateway e um gateway de roteamento dinâmico. No portal do Azure para o modelo de implantação clássico, você cria a sub-rede de gateway e o gateway por meio das mesmas páginas de configuração. Use a sub-rede de gateway somente para os serviços de gateway. Nunca implemente nada diretamente na sub-rede de gateway (como VMs ou outros serviços).

1. Na portal do Azure, vá para a rede virtual para a qual você deseja criar um gateway.

2. Na página de sua rede virtual, selecione **visão geral**e, na seção **conexões VPN** , selecione **Gateway**.

   ![Selecione para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na página **Nova Ligação VPN**, selecione **Ponto a site**.

   ![Tipo de ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Para **espaço de endereço de cliente**, adicione o intervalo de endereços IP do qual os clientes VPN recebem um endereço IP ao se conectar. Use um intervalo de endereços IP privado que não se sobreponha ao local no qual você se conecta ou com a VNet à qual você se conecta. Você pode substituir o intervalo autopreenchido pelo intervalo de endereços IP privado que você deseja usar. Este exemplo mostra o intervalo autopreenchido. 

   ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecione **criar gateway imediatamente**e selecione configuração de **Gateway opcional** para abrir a página de **configuração do gateway** .

   ![Selecione a configuração de gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Na página **configuração do gateway** , selecione **sub-rede** para adicionar a sub-rede de gateway. É possível criar uma sub-rede de gateway tão pequena quanto/29. No entanto, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos/28 ou/27. Isso permitirá que endereços suficientes acomodem possíveis configurações adicionais que talvez você queira no futuro. Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. A associação de um grupo de segurança de rede a essa sub-rede pode fazer com que o gateway de VPN não funcione conforme o esperado. Selecione **OK** para salvar essa configuração.

   ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Selecione o **Tamanho** do gateway. O tamanho é o SKU de gateway para o gateway de rede virtual. No portal do Azure, o SKU padrão é **padrão**. Para obter mais informações sobre as SKUs de gateway, consulte [sobre as configurações de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Selecione **OK** quando terminar de configurar esta página.

   ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na página **nova conexão VPN** , selecione **OK** na parte inferior da página para começar a criar seu gateway de rede virtual. Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do SKU de gateway que você selecionar.
 
## <a name="generatecerts"></a>Criar certificados

O Azure usa certificados para autenticar clientes VPN para VPNs ponto a site. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é considerada *confiável*. Os certificados de cliente devem ser gerados a partir do certificado raiz confiável e, em seguida, instalados em cada computador cliente nos certificados-repositório de certificados do User\Personal\Certificates atual. O certificado é usado para autenticar o cliente quando ele se conecta à VNet. 

Se você usar certificados autoassinados, eles deverão ser criados usando parâmetros específicos. Você pode criar um certificado autoassinado usando as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md), ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante seguir as etapas nestas instruções ao usar certificados raiz autoassinados e gerar certificados de cliente a partir do certificado raiz autoassinado. Caso contrário, os certificados criados não serão compatíveis com conexões P2S e você receberá um erro de conexão.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Adquirir a chave pública (. cer) para o certificado raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Carregar o ficheiro .cer do certificado de raiz

Depois que o gateway tiver sido criado, carregue o arquivo. cer (que contém as informações de chave pública) para um certificado raiz confiável para o servidor do Azure. Não carregue a chave privada para o certificado raiz. Depois de carregar o certificado, o Azure o usa para autenticar clientes que instalaram um certificado de cliente gerado do certificado raiz confiável. Posteriormente, você poderá carregar arquivos de certificado raiz confiáveis adicionais (até 20), se necessário.  

1. Na seção **conexões VPN** da página de sua VNet, selecione o gráfico clientes para abrir a página **conexão VPN ponto a site** .

   ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na página **conexão VPN ponto a site** , selecione **gerenciar certificado** para abrir a página **certificados** .

   ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na página **certificados** , selecione **carregar** para abrir a página **carregar certificado** .

    ![Página Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Selecione o gráfico de pasta para procurar o arquivo. cer. Selecione o arquivo e, em seguida, selecione **OK**. O certificado carregado aparece na página **certificados** .

   ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Configurar o cliente

Para se conectar a uma VNet usando uma VPN ponto a site, cada cliente deve instalar um pacote para configurar o cliente VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para ligar à rede virtual.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais cliente com suporte, consulte as [perguntas frequentes sobre conexões ponto a site](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Gerar e instalar um pacote de configuração de cliente VPN

1. Na portal do Azure, na página **visão geral** da sua VNet, em **conexões VPN**, selecione o gráfico do cliente para abrir a página **conexão VPN ponto a site** .

2. Na página **conexão VPN ponto a site** , selecione o pacote de download que corresponde ao sistema operacional do cliente onde ele está instalado:

   * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.

   ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Depois que o pacote for gerado, baixe-o e instale-o no computador cliente. Se você vir um pop-up do SmartScreen, selecione **mais informações**e, em seguida, selecione **executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.

### <a name="install-a-client-certificate"></a>Instalar um certificado de cliente

Para criar uma conexão P2S de um computador cliente diferente daquele usado para gerar os certificados de cliente, instale um certificado de cliente. Ao instalar um certificado de cliente, você precisará da senha que foi criada quando o certificado do cliente foi exportado. Normalmente, você pode instalar o certificado simplesmente clicando duas vezes nele. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).


## <a name="connect-to-your-vnet"></a>Ligar à VNet

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente a partir do qual está a ligar.
>
>

1. Para se conectar à sua VNet, no computador cliente, acesse **conexões VPN** no portal do Azure e localize a conexão VPN que você criou. A conexão VPN tem o mesmo nome que a sua rede virtual. Selecione **Ligar**. Se uma mensagem pop-up sobre o certificado for exibida, selecione **continuar** para usar privilégios elevados.

2. Na página status da **conexão** , selecione **conectar** para iniciar a conexão. Se você vir a tela **Selecionar certificado** , verifique se o certificado de cliente exibido é o correto. Caso contrário, selecione o certificado correto na lista suspensa e, em seguida, selecione **OK**.

3. Se a conexão for realizada com sucesso, você verá uma notificação **conectada** .


### <a name="troubleshooting-p2s-connections"></a>Resolução de problemas com ligações P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN

1. Verifique se a conexão VPN está ativa. Abra um prompt de comando com privilégios elevados no computador cliente e execute **ipconfig/all**.
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

## <a name="add-or-remove-trusted-root-certificates"></a>Adicionar ou remover certificados raiz confiáveis

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando você remove um certificado raiz, os clientes que têm um certificado gerado dessa raiz não podem mais se autenticar e se conectar. Para que esses clientes se autentiquem e se conectem novamente, você deve instalar um novo certificado de cliente gerado de um certificado raiz que é confiável para o Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, consulte carregar o arquivo. cer do certificado raiz.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Na seção **conexões VPN** da página de sua VNet, selecione o gráfico clientes para abrir a página **conexão VPN ponto a site** .

   ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na página **conexão VPN ponto a site** , selecione **gerenciar certificado** para abrir a página **certificados** .

   ![Página Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na página **certificados** , selecione as reticências ao lado do certificado que você deseja remover e, em seguida, selecione **excluir**.

   ![Eliminar certificado de raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Se necessário, você pode revogar um certificado de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Esse método é diferente de remover um certificado raiz confiável. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como: Obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova seus espaços para que seja uma cadeia de caracteres contínua.
3. Vá para a rede virtual clássica. Selecione **conexão VPN ponto a site**e, em seguida, selecione **gerenciar certificado** para abrir a página **certificados** .
4. Selecione **lista de revogação** para abrir a página **lista de revogação** . 
5. Selecione **Adicionar certificado** para abrir a página **Adicionar certificado à lista de revogação** .
6. Em **impressão digital**, Cole a impressão digital do certificado como uma linha contínua de texto, sem espaços. Selecione **OK** para concluir.

Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentam se conectar usando esse certificado recebem uma mensagem dizendo que o certificado não é mais válido.

## <a name="point-to-site-faq"></a>Perguntas frequentes sobre ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes

- Depois que a conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). 

- Para saber mais sobre as máquinas virtuais do Linux e de rede, confira [visão geral da rede de VM do Linux e do Azure](../virtual-machines/linux/network-overview.md).

- Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
