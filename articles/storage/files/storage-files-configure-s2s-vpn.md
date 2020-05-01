---
title: Configure uma VPN site-to-site (S2S) para utilização com ficheiros Azure / Microsoft Docs
description: Como configurar uma VPN site-to-site (S2S) para utilização com ficheiros Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061026"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Configure uma VPN site-to-site para utilização com ficheiros Azure
Pode utilizar uma ligação VPN site-to-site (S2S) para montar as suas ações de ficheiro Azure sobre SMB a partir da sua rede no local, sem abrir a porta 445. Pode configurar um VPN site-to-site utilizando o [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), que é um recurso Azure que oferece serviços VPN, e é implantado num grupo de recursos ao lado de contas de armazenamento ou outros recursos Azure.

![Um gráfico de topologia que ilustra a topologia de um gateway Azure VPN que liga uma partilha de ficheiros Azure a um site no local usando uma VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Recomendamos vivamente que leia a visão geral da [rede Azure Files](storage-files-networking-overview.md) antes de continuar com esta forma de se reler para uma discussão completa das opções de networking disponíveis para ficheiros Azure.

O artigo detalha os passos para configurar uma VPN site-to-site para montar as ações de ficheiro sinuosamente no local. Se procura desviar o tráfego de sincronização para o Azure File Sync através de uma VPN site-to-site, consulte [configurar as definições de proxy de Ficheiros Azure e de firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- Uma partilha de ficheiros Azure que gostaria de montar no local. As ações de ficheiros Azure são implantadas dentro de contas de armazenamento, que são construções de gestão que representam um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes de blob ou filas. Pode saber mais sobre como implementar ações de ficheiros Azure e contas de armazenamento em [Create a Azure file share](storage-how-to-create-file-share.md).

- Um ponto final privado para a conta de armazenamento que contém a partilha de ficheiros Azure que pretende montar no local. Para saber mais sobre como criar um ponto final privado, consulte [configurar pontos finais](storage-files-networking-endpoints.md?tabs=azure-portal)da rede Azure Files . 

- Um aparelho de rede ou servidor no seu centro de dados no local compatível com o Azure VPN Gateway. O Azure Files é agnóstico do aparelho de rede no local escolhido, mas o Azure VPN Gateway mantém uma [lista de dispositivos testados.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) Diferentes aparelhos de rede oferecem diferentes funcionalidades, características de desempenho e funcionalidades de gestão, por isso considere-as ao selecionar um aparelho de rede.

    Se não tiver um aparelho de rede existente, o Windows Server contém uma função de servidor incorporado, encaminhamento e acesso remoto (RRAS), que pode ser usado como o aparelho de rede no local. Para saber mais sobre como configurar o Encaminhamento e o Acesso Remoto no Servidor do Windows, consulte [RAS Gateway](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Adicionar conta de armazenamento à VNet
No portal Azure, navegue para a conta de armazenamento que contém a partilha de ficheiros Azure que deseja montar no local. Na tabela de conteúdos para a conta de armazenamento, selecione as Firewalls e a entrada de **redes virtuais.** A menos que tenha adicionado uma rede virtual à sua conta de armazenamento quando a criou, o painel resultante deve ter o **acesso permitir** a partir do botão de rádio para todas as **redes** selecionadas.

Para adicionar a sua conta de armazenamento à rede virtual desejada, selecione **redes Selecionadas**. Na subposição de **redes Virtuais,** clique em ou + Adicione a **rede virtual existente** ou **+Adicione uma nova rede virtual** dependendo do estado pretendido. A criação de uma nova rede virtual resultará na criação de um novo recurso Azure. O novo ou existente recurso VNet não precisa de estar no mesmo grupo de recursos ou subscrição que a conta de armazenamento, no entanto deve estar na mesma região que a conta de armazenamento e o grupo de recursos e subscrição em que implementa o seu VNet deve corresponder ao em que irá implantar o seu VpN Gateway. 

![Screenshot do portal Azure dando a opção de adicionar uma rede virtual existente ou nova à conta de armazenamento](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Se adicionar rede virtual existente, será-lhe pedido que selecione uma ou mais subredes dessa rede virtual a que a conta de armazenamento deve ser adicionada. Se selecionar uma nova rede virtual, criará uma subrede como parte da criação da rede virtual, podendo adicionar mais tarde através do recurso Azure resultante para a rede virtual.

Se ainda não adicionou uma conta de armazenamento à sua subscrição, o ponto final do serviço Microsoft.Storage terá de ser adicionado à rede virtual. Isto pode demorar algum tempo, e até que esta operação esteja concluída, não poderá aceder às ações de ficheiro Saque Azure dentro dessa conta de armazenamento, incluindo através da ligação VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Implementar um Gateway VPN Azure
Na tabela de conteúdos para o portal Azure, selecione **Criar um novo recurso** e procurar gateway de rede *Virtual*. O gateway da rede virtual deve estar na mesma subscrição, região azure e grupo de recursos como a rede virtual que implementou na fase anterior (note que o grupo de recursos é automaticamente selecionado quando a rede virtual é escolhida). 

Para efeitos de implantação de um Azure VPN Gateway, deve povoar os seguintes campos:

- **Nome**: O nome do recurso Azure para o Gateway VPN. Este nome pode ser qualquer nome que ache útil para a sua gestão.
- **Região**: A região em que será implantado o Gateway VPN.
- **Tipo**de gateway : Para efeitos de implantação de uma VPN site-to-site, deve selecionar **VPN**.
- **Tipo VPN:** Pode escolher quer *baseado em Rota** quer baseado em **políticas** dependendo do seu dispositivo VPN. As VPNs baseadas na rota suportam o IKEv2, enquanto as VPNs baseadas em políticas apenas suportam o IKEv1. Para saber mais sobre os dois tipos de gateways VPN, consulte [sobre gateways VPN baseados em políticas e baseados em rotas](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: O SKU controla o número de túneis permitidos no local e o desempenho desejado da VPN. Para selecionar o SKU apropriado para o seu caso de utilização, consulte a listagem [Gateway SKU.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) O SKU do Gateway VPN pode ser alterado mais tarde, se necessário.
- **Rede virtual**: A rede virtual que criou no passo anterior.
- **Endereço IP público**: O endereço IP do VPN Gateway que será exposto à internet. É provável que necessite de criar um novo endereço IP, no entanto, também pode utilizar um endereço IP não utilizado existente, se for apropriado. Se selecionar criar **um novo**recurso IP address Azure será criado no mesmo grupo de recursos que o VPN Gateway e o nome de endereço **IP público** será o nome do endereço IP recém-criado. Se selecionar Utilize o **uso existente,** deve selecionar o endereço IP existente não utilizado.
- **Ativar o modo ativo**: Só selecione **ativado** se estiver a criar uma configuração ativa de gateway, caso contrário deixe **o Desativado** selecionado. Para saber mais sobre o modo ativo-ativo, consulte [as instalações transversais altamente disponíveis e a conectividade VNet-to-VNet](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Configure BGP ASN**: Só selecione **Ativado** se a sua configuração necessitar especificamente desta definição. Para saber mais sobre este cenário, consulte [sobre bGP com Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Selecione **Review + crie** para criar o Gateway VPN. Um Gateway VPN pode demorar até 45 minutos a criar e a implantar completamente.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Crie uma porta de entrada de rede local para o seu gateway no local 
Um portal de rede local é um recurso Azure que representa o seu aparelho de rede no local. Na tabela de conteúdos para o portal Azure, selecione **Criar um novo recurso** e procurar o gateway da rede *local.* O portal de rede local é um recurso Azure que será implantado ao lado da sua conta de armazenamento, rede virtual e VPN Gateway, mas não precisa de estar no mesmo grupo de recursos ou subscrição que a conta de armazenamento. 

Para efeitos de implantação do recurso de gateway da rede local, deve povoar os seguintes campos:

- **Nome**: O nome do recurso Azure para o portal da rede local. Este nome pode ser qualquer nome que ache útil para a sua gestão.
- **Endereço IP**: O endereço IP público do seu portal local no local.
- **Espaço de endereços**: O endereço varia para a rede que este portal de rede local representa. Pode adicionar várias gamas de espaço de endereços, mas certifique-se de que as gamas que especifica aqui não se sobrepõem a gamas de outras redes às quais pretende ligar. 
- **Configurar as definições de BGP**: Apenas configure as definições de BGP se a sua configuração necessitar desta definição. Para saber mais sobre este cenário, consulte [sobre bGP com Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Subscrição**: A subscrição desejada. Isto não precisa de corresponder à subscrição utilizada para o Gateway VPN ou a conta de armazenamento.
- **Grupo de recursos**: O grupo de recursos desejado. Isto não precisa de corresponder ao grupo de recursos utilizado para o Gateway VPN ou a conta de armazenamento.
- **Localização**: Região Azure deve ser criado o recurso de gateway da rede local. Isto deve corresponder à região selecionada para o Gateway VPN e a conta de armazenamento.

Selecione **Criar** para criar o recurso de gateway da rede local.  

## <a name="configure-on-premises-network-appliance"></a>Configurar o aparelho de rede no local
As medidas específicas para configurar o seu aparelho de rede no local dependem com base no aparelho de rede selecionado pela sua organização. Dependendo do dispositivo escolhido pela sua organização, a [lista de dispositivos testados](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) pode ter uma ligação com as instruções do fornecedor do seu dispositivo para configurar com o Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Criar a ligação Site-a-Local
Para completar a implantação de um S2S VPN, deve criar uma ligação entre o seu aparelho de rede no local (representado pelo recurso gateway da rede local) e o Gateway VPN. Para isso, navegue para o Gateway VPN que criou acima. Na tabela de conteúdos para o Gateway VPN, selecione **Ligações**, e clique em **Adicionar**. O painel de **ligação Add** resultante requer os seguintes campos:

- **Nome**: O nome da ligação. Um VPN Gateway pode hospedar várias ligações, por isso escolha um nome útil para a sua gestão que distinga esta ligação em particular.
- Tipo de **ligação**: Uma vez que esta é uma ligação S2S, selecione **Site-to-site (IPSec)** na lista de drop-down.
- **Gateway de rede virtual**: Este campo é selecionado automaticamente para o Gateway VPN a que está a fazer a ligação e não pode ser alterado.
- **Gateway de rede local**: Este é o portal de rede local que pretende ligar ao seu Gateway VPN. O painel de seleção resultante deve ter o nome do portal de rede local que criou acima.
- **Chave partilhada (PSK)**: Uma mistura de letras e números, utilizada para estabelecer encriptação para a ligação. A mesma chave partilhada deve ser utilizada tanto na rede virtual como nos gateways da rede local. Se o seu dispositivo de porta de entrada não fornecer um, pode fazer um aqui e fornecê-lo ao seu dispositivo.

Selecione **OK** para criar a ligação. Pode verificar se a ligação foi feita com sucesso através da página **Connections.**

## <a name="mount-azure-file-share"></a>Partilha de ficheiros do Monte Azure 
O passo final na configuração de uma VPN S2S é verificar se funciona para ficheiros Azure. Pode fazê-lo montando a sua quota de ficheiroS Azure no local com o seu SISTEMA preferido. Consulte as instruções para montar pelo OS aqui:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Consulte também
- [Visão geral da rede Azure Files](storage-files-networking-overview.md)
- [Configure uma VPN ponto-a-local (P2S) no Windows para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md)