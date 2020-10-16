---
title: Azure Lab Services - faça upload de uma imagem personalizada para a Shared Image Gallery
description: Descreve como fazer o upload de uma imagem personalizada para a Galeria de Imagens Partilhadas. Os departamentos de TI universitários acharão as imagens importadoras especialmente benéficas.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712414"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Carregar uma imagem personalizada para a Shared Image Gallery

A Shared Image Gallery está disponível para você para importar suas próprias imagens personalizadas para criar laboratórios em Azure Lab Services. Os departamentos universitários de TI acharão as imagens importadoras especialmente benéficas pelas seguintes razões: 

* Não é preciso criar imagens manualmente usando o modelo VM de um laboratório.
* Pode fazer upload de imagens criadas utilizando outras ferramentas, tais como SCCM, Endpoint Manager, etc.

Este artigo descreve passos que podem ser tomadas para trazer uma imagem personalizada e usá-la nos Serviços Azure Lab. 

> [!IMPORTANT]
> Quando se move as suas imagens de um ambiente de laboratório físico para a Az Labs, precisa de as reestruturar adequadamente. Não reutilizámos simplesmente as imagens existentes dos laboratórios físicos. <br/>Para mais detalhes, consulte a [publicação de blogs Moving from a Physical Lab Services para Azure Lab Services.](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Traga imagem personalizada de um ambiente de laboratório físico

Os passos a seguir mostram como importar uma imagem personalizada que começa a partir de um ambiente físico de laboratório. Um VHD é então criado a partir deste ambiente e importado para a Shared Image Gallery em Azure para que possa ser usado dentro dos Serviços Azure Lab.

Existem muitas opções para criar um VHD a partir de um ambiente de laboratório físico. Os seguintes passos mostram como criar um VHD a partir de um VM Hiper-V do Windows:

1. Comece com um Hiper-V VM no seu ambiente físico de laboratório que foi criado a partir da sua imagem.
    1. O VM deve ser criado como um VM de Geração 1.
    1. O VM deve utilizar um tamanho fixo do disco. Também pode especificar o tamanho do disco nesta janela. O tamanho do disco não deve ser superior a 128 GB.<br/>    
    As imagens com o tamanho do disco > 128 GB não são suportadas pelos Serviços de Laboratório Azure. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Conecte o disco rígido virtual":::   
    1. Imagem do VM como normalmente faria.
1. [Ligue-se ao VM e prepare-o para o Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)
    1. [Set Windows configurations for Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure) (Definir configurações do Windows para o Azure)
    1. [Verifique os Serviços Windows que são o mínimo necessário para garantir a conectividade VM](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [Atualizar as definições de registo remoto de ambiente de trabalho](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [Configurar as regras do Windows Firewall](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Instalar atualizações do Windows
    1. [Instale o Agente VM Azure e a configuração adicional como mostrado aqui](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    Acima de etapas vai criar uma imagem especializada. Se criar uma imagem generalizada, também terá de executar o [SysPrep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep). <br/>
        Deverá criar uma imagem especializada se pretender manter o diretório do Utilizador (que pode conter ficheiros, informações de contas de utilizador, etc.) que é necessário pelo software incluído na imagem.
1. Uma **vez que o Hyper-V** cria um ficheiro **VHDX** por predefinição, é necessário convertê-lo num ficheiro VHD.
    1. Navegue para o disco de edição de ação **do gestor hiper-V.**  ->  **Action**  ->  **Edit Disk**
    1. Aqui, terá a opção de **converter** o disco de um VHDX para um VHD
    1. Ao tentar expandir o tamanho do disco, certifique-se de que não excede 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Conecte o disco rígido virtual" do portal Azure para escolher o tamanho do disco. Como mencionado anteriormente, o tamanho não deve ser > 128 GB.
1. Tira uma foto do disco gerido.
    Isto pode ser feito a partir de PowerShell, utilizando o portal Azure, ou de dentro do Storage Explorer, como descrito em [Criar uma imagem instantânea utilizando o portal ou PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).
1. Na Galeria de Imagens Partilhadas, crie uma definição de imagem e versão:
    1. [Criar uma definição de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition).
    1. Também precisa de especificar aqui se está a criar uma imagem especializada/generalizada.
1. Crie o laboratório nos Serviços de Laboratório Azure e selecione a imagem personalizada da Galeria de Imagens Partilhadas.

    Se expandiu o disco após a instalação do SISTEMA no VM Original, também terá de estender a unidade C no Windows para utilizar o espaço de disco não atribuído. Para isso, inicie sessão no modelo VM após a criação do laboratório e, em seguida, siga passos semelhantes ao mostrado em [Estender um volume básico](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume). Existem opções para o fazer através da UI, bem como para utilizar o PowerShell.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da Galeria de Imagens Partilhada](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [Como usar galeria de imagens partilhada](how-to-use-shared-image-gallery.md)
