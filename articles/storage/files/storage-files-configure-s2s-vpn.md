---
title: Configure uma VPN site-to-site (S2S) para utilização com ficheiros Azure | Microsoft Docs
description: Como configurar uma VPN site-to-site (S2S) para utilização com ficheiros Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629296"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Configure uma VPN site-to-site para utilização com ficheiros Azure
Pode utilizar uma ligação VPN Site-to-Site (S2S) para montar as suas partilhas de ficheiros Azure sobre a SMB a partir da sua rede no local, sem abrir a porta 445. Você pode configurar uma VPN site-to-site usando [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), que é um recurso Azure que oferece serviços VPN, e é implantado em um grupo de recursos ao lado de contas de armazenamento ou outros recursos Azure.

![Um gráfico de topologia que ilustra a topologia de um gateway Azure VPN que liga uma partilha de ficheiros Azure a um site no local usando uma VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Recomendamos vivamente que leia [a visão geral do Azure Files](storage-files-networking-overview.md) antes de continuar com este artigo para uma discussão completa das opções de networking disponíveis para ficheiros Azure.

O artigo detalha os passos para configurar uma VPN site-to-site para montar ações de ficheiros Azure diretamente no local. Se procura encaminhar o tráfego de sincronização para Azure File Sync sobre uma VPN site-to-site, consulte [configurar configurar as definições de proxy e firewall do Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- Uma partilha de ficheiros Azure que gostaria de montar no local. As ações de ficheiros Azure são implantadas dentro de contas de armazenamento, que são construções de gestão que representam um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes blob ou filas. Pode saber mais sobre como implementar ações de ficheiros Azure e contas de armazenamento na [Create a azure file share](storage-how-to-create-file-share.md).

- Um ponto final privado para a conta de armazenamento que contém a partilha de ficheiros Azure que pretende montar no local. Para saber mais sobre como criar um ponto final privado, consulte [os pontos finais da rede De Configuração Azure Files](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Um aparelho de rede ou servidor no seu datacenter no local que seja compatível com o Gateway Azure VPN. O Azure Files é agnóstico do aparelho de rede no local escolhido, mas o Azure VPN Gateway mantém uma [lista de dispositivos testados](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Diferentes aparelhos de rede oferecem diferentes funcionalidades, características de desempenho e funcionalidades de gestão, por isso considere-os ao selecionar um aparelho de rede.

    Se não tiver um aparelho de rede existente, o Windows Server contém uma função de servidor incorporada, encaminhamento e acesso remoto (RRAS), que pode ser utilizado como o aparelho de rede no local. Para saber mais sobre como configurar o encaminhamento e o acesso remoto no Windows Server, consulte [RAS Gateway](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Adicionar conta de armazenamento ao VNet
No portal Azure, navegue para a conta de armazenamento que contém a partilha de ficheiros Azure que gostaria de montar no local. Na tabela de conteúdos para a conta de armazenamento, selecione as firewalls e a entrada **de redes virtuais.** A menos que tenha adicionado uma rede virtual à sua conta de armazenamento quando a criou, o painel resultante deverá ter o acesso do botão **Desembaraçar a partir de** **todas as redes selecionadas.**

Para adicionar a sua conta de armazenamento à rede virtual desejada, **selecione Redes Selecionadas**. Na subposição de **redes Virtuais,** clique em **+ Adicionar a rede virtual existente** ou **+Adicionar uma nova rede virtual** dependendo do estado pretendido. A criação de uma nova rede virtual resultará na criação de um novo recurso Azure. O novo recurso VNet ou existente não precisa de estar no mesmo grupo de recursos ou subscrição que a conta de armazenamento, no entanto deve estar na mesma região que a conta de armazenamento e o grupo de recursos e subscrição em que implanta o seu VNet deve corresponder ao que irá implementar o seu Gateway VPN. 

![Screenshot do portal Azure dando a opção de adicionar uma rede virtual existente ou nova à conta de armazenamento](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Se adicionar a rede virtual existente, ser-lhe-á pedido que selecione uma ou mais sub-redes dessa rede virtual à qual a conta de armazenamento deve ser adicionada. Se selecionar uma nova rede virtual, criará uma sub-rede como parte da criação da rede virtual, podendo adicionar mais tarde através do recurso Azure resultante para a rede virtual.

Se não tiver adicionado uma conta de armazenamento à sua subscrição antes, o ponto final do serviço Microsoft.Storage terá de ser adicionado à rede virtual. Isto pode demorar algum tempo, e até que esta operação esteja concluída, não poderá aceder às ações de ficheiros Azure dentro dessa conta de armazenamento, incluindo através da ligação VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Implementar um Gateway Azure VPN
Na tabela de conteúdos para o portal Azure, selecione **Criar um novo recurso** e procurar por Gateway de rede *Virtual*. O seu gateway de rede virtual deve estar na mesma subscrição, região de Azure e grupo de recursos como a rede virtual que implementou no passo anterior (note que o grupo de recursos é automaticamente selecionado quando a rede virtual é escolhida). 

Para efeitos de implantação de um Gateway Azure VPN, deve povoar os seguintes campos:

- **Nome**: O nome do recurso Azure para o Gateway VPN. Este nome pode ser qualquer nome que ache útil para a sua gestão.
- **Região**: Região em que será implantada a VPN Gateway.
- **Tipo gateway**: Para efeitos de implantação de uma VPN site-to-site, deve selecionar **VPN**.
- **Tipo VPN**: Pode escolher quer *se baseie em rotas** quer **em políticas,** dependendo do seu dispositivo VPN. As VPNs baseadas em rotas suportam o IKEv2, enquanto as VPNs baseadas em políticas apenas suportam iKEv1. Para saber mais sobre os dois tipos de gateways VPN, consulte [sobre gateways VPN baseados em políticas e rotas](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: O SKU controla o número de túneis locais-locais permitidos e o desempenho desejado da VPN. Para selecionar o SKU apropriado para o seu caso de utilização, consulte a listagem [Gateway SKU.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) O SKU do Gateway VPN pode ser alterado mais tarde, se necessário.
- **Rede virtual**: A rede virtual que criou no passo anterior.
- **Endereço IP público**: O endereço IP do Gateway VPN que será exposto à internet. É provável que tenha de criar um novo endereço IP, no entanto também poderá utilizar um endereço IP existente não utilizado, se for apropriado. Se selecionar para **criar novo,** um novo recurso IP Azure será criado no mesmo grupo de recursos que o Gateway VPN e o  **nome de endereço IP público** será o nome do endereço IP recém-criado. Se selecionar **Utilizar o** endereço IP existente, deve selecionar o endereço IP existente não utilizado.
- **Ativar o modo ativo**: Selecione **Apenas Ativado** se estiver a criar uma configuração de gateway ativa ativa, caso contrário deixe **o Desativado** selecionado. Para saber mais sobre o modo ativo, consulte [as instalações cruzadas altamente disponíveis e a conectividade VNet-to-VNet](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Configure BGP ASN**: Selecione **Apenas Ativado** se a sua configuração necessitar especificamente desta definição. Para saber mais sobre este cenário, consulte [Sobre o BGP com o Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Selecione **Review + criar** para criar o Gateway VPN. Um Gateway VPN pode demorar até 45 minutos para criar e implantar totalmente.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Crie uma porta de entrada de rede local para o seu portal de entrada no local 
Um gateway de rede local é um recurso Azure que representa o seu aparelho de rede no local. Na tabela de conteúdos para o portal Azure, selecione **Criar um novo recurso** e procurar o gateway de rede *local.* O gateway de rede local é um recurso Azure que será implantado ao lado da sua conta de armazenamento, rede virtual e Gateway VPN, mas não precisa de estar no mesmo grupo de recursos ou subscrição que a conta de armazenamento. 

Para efeitos de implantação do recurso de gateway de rede local, deve povoar os seguintes campos:

- **Nome**: O nome do recurso Azure para a porta de entrada da rede local. Este nome pode ser qualquer nome que ache útil para a sua gestão.
- **Endereço IP**: O endereço IP público do seu gateway local no local.
- **Espaço de** endereço : Os intervalos de endereço para a rede que esta porta de entrada de rede local representa. Pode adicionar várias gamas de espaço de endereços, mas certifique-se de que as gamas especifica aqui não se sobrepõem a gamas de outras redes a que pretende ligar. 
- **Configurar as definições de BGP**: Apenas configurar as definições de BGP se a sua configuração necessitar desta definição. Para saber mais sobre este cenário, consulte [Sobre o BGP com o Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Assinatura**: A subscrição desejada. Isto não precisa de corresponder à subscrição utilizada para o Gateway VPN ou para a conta de armazenamento.
- **Grupo de recursos**: O grupo de recursos desejado. Isto não precisa de corresponder ao grupo de recursos utilizado para o Gateway VPN ou para a conta de armazenamento.
- **Localização**: A Região Azure o recurso de gateway de rede local deve ser criado. Isto deve coincidir com a região que selecionou para o Gateway VPN e a conta de armazenamento.

Selecione **Criar** para criar o recurso de gateway de rede local.  

## <a name="configure-on-premises-network-appliance"></a>Configurar aparelhos de rede no local
Os passos específicos para configurar o seu aparelho de rede no local dependem com base no aparelho de rede que a sua organização selecionou. Dependendo do dispositivo que a sua organização escolheu, a [lista de dispositivos testados](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) pode ter uma ligação às instruções do seu fornecedor de dispositivos para configurar com o Gateway Azure VPN.

## <a name="create-the-site-to-site-connection"></a>Criar a ligação Site-a-Local
Para completar a implantação de uma VPN S2S, deve criar uma ligação entre o seu aparelho de rede no local (representado pelo recurso de gateway de rede local) e o Gateway VPN. Para isso, navegue até ao Gateway VPN que criou acima. Na tabela de conteúdos para o Gateway VPN, selecione **Connections**, e clique em **Adicionar**. O painel de **ligação de adicionar** resultante requer os seguintes campos:

- **Nome**: O nome da ligação. Um Gateway VPN pode hospedar várias ligações, por isso escolha um nome útil para a sua gestão que irá distinguir esta ligação particular.
- **Tipo de ligação**: Uma vez que esta é uma ligação S2S, selecione **Site-to-site (IPSec)** na lista de espera.
- **Gateway de rede virtual**: Este campo é selecionado automaticamente para o Gateway VPN a que está a fazer a ligação e não pode ser alterado.
- **Gateway de rede local**: Esta é a porta de entrada de rede local que pretende ligar ao seu Gateway VPN. O painel de seleção resultante deve ter o nome da porta de entrada de rede local que criou acima.
- **Chave partilhada (PSK)**: Uma mistura de letras e números, usada para estabelecer encriptação para a ligação. A mesma chave partilhada deve ser utilizada tanto na rede virtual como nas portas de rede locais. Se o seu dispositivo gateway não fornecer um, pode fazer um aqui em cima e forneca-lo ao seu dispositivo.

Selecione **OK** para criar a ligação. Pode verificar se a ligação foi feita com sucesso através da página **'Ligações'.**

## <a name="mount-azure-file-share"></a>Partilha de ficheiros mount Azure 
O passo final na configuração de uma VPN S2S é verificar se funciona para ficheiros Azure. Pode fazê-lo montando o seu ficheiro Azure no local com o seu SISTEMA preferido. Consulte as instruções a montar por OS aqui:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Ver também
- [Visão geral da rede de ficheiros Azure](storage-files-networking-overview.md)
- [Configure uma VPN ponto-a-local (P2S) no Windows para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md)