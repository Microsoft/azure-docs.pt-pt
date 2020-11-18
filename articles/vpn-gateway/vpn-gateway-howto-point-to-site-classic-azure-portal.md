---
title: 'Ligue um computador a uma rede virtual utilizando P2S: autenticação de certificado: Clássico do portal Azure'
titleSuffix: Azure VPN Gateway
description: Crie uma ligação VPN Gateway de ponto a local clássica utilizando o portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657079"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configure uma ligação ponto-a-local utilizando a autenticação de certificado (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra-lhe como criar um VNet com uma ligação Ponto-a-Local. Cria este VNet com o modelo de implementação clássico utilizando o portal Azure. Esta configuração utiliza certificados para autenticar o cliente da ligação, seja autoassinado ou emitido pela AC. Também pode criar esta configuração com uma ferramenta ou modelo de implementação diferente utilizando opções descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Utilize uma porta VPN VPN ponto-a-local (P2S) para criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As ligações VPN ponto-a-local são úteis quando pretende ligar-se ao seu VNet a partir de uma localização remota. Quando tem apenas alguns clientes que precisam de se ligar a um VNet, um P2S VPN é uma solução útil para usar em vez de uma VPN site-to-site. É estabelecida uma ligação VPN P2S ao iniciá-la a partir do computador cliente.

> [!IMPORTANT]
> O modelo de implementação clássico suporta apenas clientes VPN do Windows e utiliza SSTP (Secure Socket Tunneling Protocol), um protocolo VPN baseado em SSL. Para suportar clientes VPN não-Windows, tem de criar o seu VNet com o modelo de implementação do Gestor de Recursos. O modelo de implementação Resource Manager suporta a VPN IKEv2, além de SSTP. Para obter mais informações, veja [Sobre ligações P2S](point-to-site-about.md).
>
>

![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Configurações e requisitos

### <a name="requirements"></a>Requirements

As ligações de autenticação de certificado ponto-a-local requerem os seguintes itens. Há passos neste artigo que o ajudarão a criá-los.

* Um gateway de VPN Dinâmico.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Esta chave é considerada um certificado de confiança e é utilizada para a autenticação.
* Um certificado de cliente gerado a partir do certificado de raiz e instalado em cada computador cliente que irá ligar. Este certificado é utilizado para autenticação de cliente.
* Um pacote de configuração de cliente VPN tem de estar gerado e instalado em todos os computadores cliente que estabelece ligação. O pacote de configuração do cliente configura o cliente nativo VPN que já está no sistema operativo com as informações necessárias para se ligar ao VNet.

As ligações ponto-a-local não requerem um dispositivo VPN ou um endereço IP virado para o público no local. A ligação VPN é criada através de SSTP (Secure Socket Tunneling Protocol). No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição.

Para obter mais informações, consulte [as ligações Ponto-a-Local](point-to-site-about.md) e as [FAQ](#faq).

### <a name="example-settings"></a>Definições de exemplo

Utilize os seguintes valores para criar um ambiente de teste ou consulte estes valores para entender melhor os exemplos deste artigo:

* **Grupo de Recursos:** TestRG
* **Nome da VNet:** VNet1
* **Espaço do endereço:** 192.168.0.0/16 <br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço do cliente:** 172.16.201.0/24 <br> Os clientes VPN que se ligam ao VNet utilizando esta ligação Ponto-a-Local recebem um endereço IP a partir da piscina especificada.
* **Tipo de ligação**: Selecione **Ponto a local**.
* **Intervalo de endereços GatewaySubnet (bloco CIDR):** 192.168.200.0/24

Antes de começar, verifique se tem uma assinatura Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Criar um gateway de VPN

1. Navegue para o VNet que criou.
1. Na página VNet, em Definições, selecione **Gateway**. Na página **Gateway,** pode ver o portal da sua rede virtual. Esta rede virtual ainda não tem uma porta de entrada. Clique na nota que diz **Clique aqui para adicionar uma ligação e um gateway**.
1. Na página **Configure uma ligação VPN e** página de gateway, selecione as seguintes definições:

   * Tipo de ligação: Ponto a Site
   * Espaço de endereço do cliente: Adicione a gama de endereços IP a partir da qual os clientes VPN recebem um endereço IP ao ligar. Utilize um intervalo de endereços IP privado que não se sobreponha ao local de localização a que se conecta, ou ao VNet a que se conecta.
1. Deixe a caixa de verificação para **Não configurar um portal neste momento** não selecionado. Vamos criar uma porta de entrada.
1. Na parte inferior da página, selecione **Seguinte: Gateway >**.
1. No separador **Gateway,** selecione os seguintes valores:

   * **Tamanho:** O tamanho é o gateway SKU para o seu gateway de rede virtual. No portal Azure, o SKU predefinido é **padrão**. Para obter mais informações sobre as VERS de gateway, consulte [as definições de gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Tipo de encaminhamento:** Tem de selecionar **Dynamic** para uma configuração ponto-a-local. O encaminhamento estático não funcionará.
   * **Sub-rede gateway:** Este campo já está preenchido automaticamente. Não pode mudar o nome. Se tentar alterar o nome utilizando o PowerShell ou qualquer outro meio, o gateway não funcionará corretamente.
   * **Intervalo de endereços (bloco CIDR):** Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Ao fazê-lo, permitirá endereços suficientes para acomodar possíveis configurações adicionais que possa desejar no futuro. Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) à sub-rede do gateway. Associar um grupo de segurança de rede a esta sub-rede pode fazer com que a sua porta de entrada VPN não funcione como esperado.
1. Selecione **Rever + criar** para validar as suas definições.
1. Assim que a validação passar, **selecione Criar**. Um gateway VPN pode demorar até 45 minutos para ser concluído, dependendo do gateway SKU que seleciona.

## <a name="create-certificates"></a><a name="generatecerts"></a>Criar certificados

O Azure utiliza certificados para autenticar clientes VPN para VPNs ponto-a-local. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada *de confiança.* Os certificados de cliente devem ser gerados a partir do certificado raiz fidedigno e depois instalados em cada computador cliente na loja Certificates-Current de certificados user\Personal\Certificates. O certificado é utilizado para autenticar o cliente quando este se liga ao VNet. 

Se utilizar certificados auto-assinados, devem ser criados utilizando parâmetros específicos. Pode criar um certificado auto-assinado utilizando as instruções [para PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md), ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante seguir os passos nestas instruções quando utiliza certificados de raiz auto-assinados e gera certificados de cliente a partir do certificado raiz auto-assinado. Caso contrário, os certificados que criar não serão compatíveis com ligações P2S e receberá um erro de ligação.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Adquirir a chave pública (.cer) para o certificado raiz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Gerar um certificado de cliente

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Carregar o ficheiro .cer do certificado de raiz

Após a criação do gateway, faça o upload do ficheiro .cer (que contém as informações de chave pública) para obter um certificado de raiz fidedigno para o servidor Azure. Não carre fique com a chave privada para o certificado de raiz. Depois de fazer o upload do certificado, o Azure utiliza-o para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado raiz fidedigno. Pode carregar mais tarde ficheiros adicionais de certificados de raiz fidedignos (até 20), se necessário.

1. Navegue para a rede virtual que criou.
1. Em **Definições**, selecione **ligações ponto-a-local**.
1. **Selecione o certificado de Gestão**.
1. Selecione **Carregar**.
1. No painel **de certificados Do upload,** selecione o ícone da pasta e navegue para o certificado que pretende carregar.
1. Selecione **Carregar**.
1. Depois de o certificado ter sido carregado com sucesso, pode vê-lo na página de certificado Manage. Poderá ter de selecionar **Refresh** para visualizar o certificado que acabou de carregar.

## <a name="configure-the-client"></a>Configurar o cliente

Para se ligar a um VNet utilizando uma VPN ponto-a-local, cada cliente deve instalar um pacote para configurar o cliente VPN do Windows nativo. O pacote de configuração configura o cliente VPN do Windows nativo com as definições necessárias para ligar à rede virtual.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente. Para a lista de sistemas operativos clientes suportados, consulte [as ligações Sobre o Ponto-a-Local](point-to-site-about.md) e as [FAQ](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Gerei e instale um pacote de configuração de cliente VPN

1. Navegue **nas definições de ligações ponto-a-local** para o seu VNet.
1. No topo da página, selecione o pacote de descarregamento que corresponde ao sistema operativo do cliente onde será instalado:

   * Para clientes de 64 bits, selecione **o cliente VPN (64-bit)**.
   * Para clientes de 32 bits, selecione **o cliente VPN (32-bit)**.

1. O Azure gera um pacote com as definições específicas que o cliente necessita. Sempre que fizer alterações no VNet ou gateway, tem de descarregar um novo pacote de configuração do cliente e instalá-los nos computadores clientes.
1. Depois de gerar o pacote, selecione **Download**.
1. Instale o pacote de configuração do cliente no computador cliente. Ao instalar, se vir um popup smartScreen a dizer que o Windows protegeu o seu PC, selecione **Mais informações** e, em seguida, selecione **Executar de qualquer maneira**. Também pode guardar o pacote para instalar noutros computadores cliente.

### <a name="install-a-client-certificate"></a>Instalar um certificado de cliente

Para este exercício, quando gerou o certificado de cliente, foi automaticamente instalado no seu computador. Para criar uma ligação P2S a partir de um computador cliente diferente daquele utilizado para gerar os certificados de cliente, tem de instalar o certificado de cliente gerado nesse computador.

Quando instala um certificado de cliente, precisa da senha criada quando o certificado do cliente foi exportado. Normalmente, pode instalar o certificado clicando duas vezes. Para obter mais informações, veja [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install)(Instalar um certificado de cliente exportado).

## <a name="connect-to-your-vnet"></a>Ligar à VNet

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente a partir do qual está a ligar.
>

1. No computador cliente, vá às definições de VPN.
1. Selecione a VPN que criou. Se utilizar as definições de exemplo, a ligação será rotulada **como Grupo TestRG VNet1**.
1. Selecione **Connect** (Ligar).
1. Na caixa de Rede Virtual Windows Azure, selecione **Connect**. Se aparecer uma mensagem pop-up sobre o certificado, **selecione Continue** a utilizar privilégios elevados e **Sim** para aceitar alterações de configuração.
1. Quando a sua ligação for bem sucedida, verá uma notificação **conectada.**

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN

1. Verifique se a sua ligação VPN está ativa. Abra uma solicitação de comando elevada no computador do cliente e execute **ipconfig/all**.
1. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser semelhantes a este exemplo:

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

## <a name="to-connect-to-a-virtual-machine"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Para adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que têm um certificado gerado a partir dessa raiz já não podem autenticar e ligar. Para que esses clientes autentem e se conectem novamente, tem de instalar um novo certificado de cliente gerado a partir de um certificado de raiz que é fidedigno pela Azure.

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado de raiz fidedigna

Pode adicionar até 20 certificados de raiz fidedignos .cer ficheiros ao Azure utilizando o mesmo processo que usou para adicionar o primeiro certificado de raiz fidedigno.

### <a name="remove-a-trusted-root-certificate"></a>Remover um certificado de raiz fidedigna

1. Na secção **de ligações ponto-a-local** da página para o seu VNet, selecione **'Gerir' o certificado**.
1. Selecione a elipse ao lado do certificado que pretende remover e, em seguida, selecione **Delete**.

## <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Se necessário, pode revogar um certificado de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Este método difere da remoção de um certificado de raiz fidedigno. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, consulte [Como: Recuperar a impressão digital de um certificado.](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)
1. Copie a informação para um editor de texto e remova os seus espaços para que seja uma cadeia contínua.
1. Navegue **para a ligação VPN ponto-a-local** e, em seguida, selecione **Obter o certificado**.
1. Selecione **lista de revogação** para abrir a página da **lista de revogação.**
1. Na **impressão digital,** cole a impressão digital do certificado como uma linha contínua de texto, sem espaços.
1. **Selecione + Adicionar à lista** para adicionar a impressão digital à lista de revogação do certificado (CRL).

Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentam ligar-se através deste certificado recebem uma mensagem a dizer que o certificado já não é válido.

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Próximos passos

* Depois da sua ligação estar concluída, pode adicionar máquinas virtuais às suas redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](../index.yml).

* Para saber mais sobre networking e máquinas virtuais Linux, consulte [a visão geral da rede Azure e Linux VM](../virtual-machines/network-overview.md).

* Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).