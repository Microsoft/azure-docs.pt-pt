---
title: Configurar uma VPN S2S (site a site) para uso com os arquivos do Azure | Microsoft Docs
description: Como configurar uma VPN S2S (site a site) para uso com os arquivos do Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7762366f68bee2cd8c44e81bb22366c504ff1a73
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484426"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Configurar uma VPN site a site para uso com os arquivos do Azure
Você pode usar uma conexão VPN S2S (site a site) para montar seus compartilhamentos de arquivos do Azure via SMB de sua rede local, sem abrir a porta 445. Você pode configurar uma VPN site a site usando o gateway de [VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md), que é um recurso do Azure que oferece serviços VPN e é implantado em um grupo de recursos juntamente com contas de armazenamento ou outros recursos do Azure.

![Um gráfico de topologia que ilustra a topologia de um gateway de VPN do Azure que conecta um compartilhamento de arquivos do Azure a um site local usando uma VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

É altamente recomendável que você leia a [visão geral de rede de arquivos do Azure](storage-files-networking-overview.md) antes de continuar com este artigo para obter uma discussão completa das opções de rede disponíveis para os arquivos do Azure.

O artigo fornece detalhes sobre as etapas para configurar uma VPN site a site para montar compartilhamentos de arquivos do Azure diretamente no local. Se você pretende rotear o tráfego de sincronização para Sincronização de Arquivos do Azure por meio de uma VPN site a site, consulte [definindo configurações de proxy e firewall de sincronização de arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- Um compartilhamento de arquivos do Azure que você deseja montar no local. Você pode usar um compartilhamento de arquivos [Standard](storage-how-to-create-file-share.md) ou [Premium do Azure](storage-how-to-create-premium-fileshare.md) com sua VPN site a site.

- Um dispositivo de rede ou servidor em seu datacenter local compatível com o gateway de VPN do Azure. Os arquivos do Azure são independentes do dispositivo de rede local escolhido, mas o gateway de VPN do Azure mantém uma [lista de dispositivos testados](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Dispositivos de rede diferentes oferecem recursos, características de desempenho e funcionalidades de gerenciamento diferentes, portanto, considere-os ao selecionar um dispositivo de rede.

    Se você não tiver um dispositivo de rede existente, o Windows Server conterá uma função de servidor interna, roteamento e acesso remoto (RRAS), que pode ser usada como o dispositivo de rede local. Para saber mais sobre como configurar o roteamento e o acesso remoto no Windows Server, consulte [Gateway de Ras](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Adicionar conta de armazenamento à VNet
Na portal do Azure, navegue até a conta de armazenamento que contém o compartilhamento de arquivos do Azure que você deseja montar no local. No Sumário da conta de armazenamento, selecione a entrada **firewalls e redes virtuais** . A menos que você tenha adicionado uma rede virtual à sua conta de armazenamento quando ela foi criada, o painel resultante deverá ter o botão de opção **permitir acesso de** **todas as redes** selecionadas.

Para adicionar sua conta de armazenamento à rede virtual desejada, selecione **redes selecionadas**. Sob o subtítulo **redes virtuais** , clique em **+ Adicionar rede virtual existente** ou **+ Adicionar nova rede virtual** dependendo do estado desejado. A criação de uma nova rede virtual resultará em um novo recurso do Azure que está sendo criado. O recurso de VNet novo ou existente não precisa estar no mesmo grupo de recursos ou assinatura que a conta de armazenamento, no entanto, ele deve estar na mesma região que a conta de armazenamento, e o grupo de recursos e a assinatura em que você implantar sua VNet devem corresponder ao que você irá  implante seu gateway de VPN no. 

![Captura de tela da portal do Azure fornecendo a opção de adicionar uma rede virtual nova ou existente à conta de armazenamento](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Se você adicionar uma rede virtual existente, será solicitado a selecionar uma ou mais sub-redes da rede virtual à qual a conta de armazenamento deve ser adicionada. Se você selecionar uma nova rede virtual, criará uma sub-rede como parte da criação da rede virtual e poderá adicionar mais tarde por meio do recurso do Azure resultante para a rede virtual.

Se você não tiver adicionado uma conta de armazenamento à sua assinatura antes, o ponto de extremidade do serviço Microsoft. Storage precisará ser adicionado à rede virtual. Isso pode levar algum tempo e até que a operação seja concluída, você não poderá acessar os compartilhamentos de arquivos do Azure dentro dessa conta de armazenamento, incluindo por meio da conexão VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Implantar um gateway de VPN do Azure
No Sumário da portal do Azure, selecione **criar um novo recurso** e pesquise *Gateway de rede virtual*. O gateway de rede virtual deve estar na mesma assinatura, região do Azure e grupo de recursos que a rede virtual implantada na etapa anterior (Observe que o grupo de recursos é selecionado automaticamente quando a rede virtual é escolhida). 

Para fins de implantação de um gateway de VPN do Azure, você deve preencher os seguintes campos:

- **Nome**: o nome do recurso do Azure para o gateway de VPN. Esse nome pode ser qualquer nome que você ache útil para o seu gerenciamento.
- **Região**: a região na qual o gateway de VPN será implantado.
- **Tipo de gateway**: para fins de implantação de uma VPN site a site, você deve selecionar **VPN**.
- **Tipo de VPN**: você pode escolher *baseado em rota** ou **baseado em política** dependendo do seu dispositivo VPN. As VPNs baseadas em rota dão suporte a IKEv2, enquanto as VPNs baseadas em políticas dão suporte apenas a IKEv1. Para saber mais sobre os dois tipos de gateways de VPN, consulte [sobre gateways de VPN baseados em políticas e rotas](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: o SKU controla o número de túneis de site a site permitidos e o desempenho desejado da VPN. Para selecionar o SKU apropriado para seu caso de uso, consulte a listagem de [SKU do gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) . A SKU do gateway de VPN pode ser alterada posteriormente, se necessário.
- **Rede virtual**: a rede virtual que você criou na etapa anterior.
- **Endereço IP público**: o endereço IP do gateway de VPN que será exposto à Internet. Provavelmente, você precisará criar um novo endereço IP, no entanto, você também poderá usar um endereço IP não utilizado existente se isso for apropriado. Se você optar por **criar um novo**, um novo endereço IP recurso do Azure será criado no mesmo grupo de recursos que o gateway de VPN e o **nome do endereço IP público** será o nome do endereço IP recém-criado. Se você selecionar **usar existente**, deverá selecionar o endereço IP não usado existente.
- **Habilitar modo ativo-ativo**: selecione somente **habilitado** se você estiver criando uma configuração de gateway ativo-ativo; caso contrário, deixe **desabilitado** selecionado. Para saber mais sobre o modo ativo-ativo, consulte [conectividade altamente disponível entre locais e vnet para vnet](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Configurar o BGP ASN**: selecione somente **habilitado** se sua configuração exigir especificamente essa configuração. Para saber mais sobre essa configuração, consulte [sobre o BGP com o gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Selecione **examinar + criar** para criar o gateway de VPN. Um gateway de VPN pode levar até 45 minutos para criar e implantar totalmente.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Criar um gateway de rede local para o seu gateway local 
Um gateway de rede local é um recurso do Azure que representa seu dispositivo de rede no local. No Sumário da portal do Azure, selecione **criar um novo recurso** e pesquise *Gateway de rede local*. O gateway de rede local é um recurso do Azure que será implantado junto com sua conta de armazenamento, rede virtual e gateway de VPN, mas não precisa estar no mesmo grupo de recursos ou assinatura que a conta de armazenamento. 

Para fins de implantação do recurso de gateway de rede local, você deve preencher os seguintes campos:

- **Nome**: o nome do recurso do Azure para o gateway de rede local. Esse nome pode ser qualquer nome que você ache útil para o seu gerenciamento.
- **Endereço IP**: o endereço IP público do seu gateway local.
- **Espaço de endereço**: os intervalos de endereços para a rede que esse gateway de rede local representa. Você pode adicionar vários intervalos de espaço de endereço, mas certifique-se de que os intervalos especificados aqui não se sobreponham a intervalos de outras redes às quais você deseja se conectar. 
- **Definir configurações de BGP**: Defina somente as configurações de BGP se sua configuração exigir essa configuração. Para saber mais sobre essa configuração, consulte [sobre o BGP com o gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Assinatura**: a assinatura desejada. Isso não precisa corresponder à assinatura usada para o gateway de VPN ou a conta de armazenamento.
- **Grupo de recursos**: o grupo de recursos desejado. Isso não precisa corresponder ao grupo de recursos usado para o gateway de VPN ou para a conta de armazenamento.
- **Local**: a região do Azure em que o recurso de gateway de rede local deve ser criado. Isso deve corresponder à região que você selecionou para o gateway de VPN e a conta de armazenamento.

Selecione **criar** para criar o recurso de gateway de rede local.  

## <a name="configure-on-premises-network-appliance"></a>Configurar dispositivo de rede local
As etapas específicas para configurar seu dispositivo de rede local dependem com base no dispositivo de rede selecionado pela sua organização. Dependendo do dispositivo que sua organização escolheu, a [lista de dispositivos testados](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) pode ter um link para as instruções do fornecedor do dispositivo para configurar com o gateway de VPN do Azure.

## <a name="create-private-endpoint-preview"></a>Criar ponto de extremidade privado (visualização)
A criação de um ponto de extremidade privado para sua conta de armazenamento dá à sua conta de armazenamento um endereço IP dentro do espaço de endereço IP de sua rede virtual. Quando você monta o compartilhamento de arquivos do Azure do local usando esse endereço IP privado, as regras de roteamento definidas de forma automática pela instalação de VPN rotearão sua solicitação de montagem para a conta de armazenamento por meio da VPN. 

Na folha da conta de armazenamento, selecione **conexões de ponto de extremidade privado** no sumário à esquerda e **+ ponto de extremidade privado** para criar um novo ponto de extremidade privado. O assistente resultante tem várias páginas a serem concluídas:

![Uma captura de tela da seção noções básicas da seção criar ponto de extremidade privado](media/storage-files-configure-s2s-vpn/create-private-endpoint-1.png)

Na guia **noções básicas** , selecione o grupo de recursos, o nome e a região desejados para seu ponto de extremidade particular. Isso pode ser o que você desejar, eles não precisam corresponder à conta de armazenamento de qualquer forma, embora seja necessário criar o ponto de extremidade privado na mesma região que a rede virtual na qual você deseja criar o ponto de extremidade privado.

Na guia **recurso** , selecione o botão de opção para **conectar-se a um recurso do Azure em meu diretório**. Em **tipo de recurso**, selecione **Microsoft. Storage/storageAccounts** para o tipo de recurso. O campo de **recurso** é a conta de armazenamento com o compartilhamento de arquivos do Azure ao qual você deseja se conectar. O subrecurso de destino é **arquivo**, pois isso é para os arquivos do Azure.

A guia **configuração** permite selecionar a rede virtual específica e a sub-rede à qual você deseja adicionar seu ponto de extremidade privado. Selecione a rede virtual que você criou acima. Você deve selecionar uma sub-rede distinta da sub-rede à qual você adicionou o ponto de extremidade de serviço acima.

A guia **configuração** também permite que você configure uma zona DNS privada. Isso não é necessário, mas permite que você use um caminho UNC amigável (como `\\mystorageaccount.privatelink.file.core.windows.net\myshare`) em vez de um caminho UNC com um endereço IP para montar o compartilhamento de arquivos do Azure. Isso também pode ser feito com seus próprios servidores DNS em sua rede virtual.

Clique em **examinar + criar** para criar o ponto de extremidade privado. Depois que o ponto de extremidade privado tiver sido criado, você verá dois novos recursos: um recurso de ponto de extremidade privado e uma interface de rede virtual emparelhada. O recurso de interface de rede virtual terá o IP privado dedicado da conta de armazenamento. 

## <a name="create-the-site-to-site-connection"></a>Criar a conexão site a site
Para concluir a implantação de uma VPN S2S, você deve criar uma conexão entre seu dispositivo de rede local (representado pelo recurso de gateway de rede local) e o gateway de VPN. Para fazer isso, navegue até o gateway de VPN que você criou acima. No Sumário do gateway de VPN, selecione **conexões**e clique em **Adicionar**. O painel **Adicionar conexão** resultante exige os seguintes campos:

- **Nome**: o nome da conexão. Um gateway de VPN pode hospedar várias conexões, portanto, escolha um nome útil para seu gerenciamento que distinguirá essa conexão específica.
- **Tipo de conexão**: como esta conexão S2S, selecione **site a site (IPSec)** na lista suspensa.
- **Gateway de rede virtual**: esse campo é selecionado automaticamente para o gateway de VPN ao qual você está fazendo a conexão e não pode ser alterado.
- **Gateway de rede local**: esse é o gateway de rede local que você deseja conectar ao seu gateway de VPN. O painel de seleção resultante deve ter o nome do gateway de rede local que você criou acima.
- **Chave compartilhada (PSK)** : uma mistura de letras e números, usada para estabelecer a criptografia para a conexão. A mesma chave compartilhada deve ser usada tanto na rede virtual quanto nos gateways de rede local. Se o seu dispositivo de gateway não fornecer um, você poderá fazer um aqui e fornecê-lo ao seu dispositivo.

Selecione **OK** para criar a conexão. Você pode verificar se a conexão foi feita com êxito por meio da página **conexões** .

## <a name="mount-azure-file-share"></a>Montar o compartilhamento de arquivos do Azure 
A etapa final da configuração de uma VPN S2S é verificar se ela funciona para os arquivos do Azure. Você pode fazer isso montando o compartilhamento de arquivos do Azure localmente com seu sistema operacional preferencial. Consulte as instruções para montar por sistema operacional aqui:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Consulte também
- [Visão geral da rede de arquivos do Azure](storage-files-networking-overview.md)
- [Configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md)
