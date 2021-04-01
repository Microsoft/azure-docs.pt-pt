---
title: Tutorial sobre a preparação do portal do Azure para implementar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implementar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101706100"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Prepare-se para implementar gateway de caixa de dados azul

Este é o primeiro tutorial da série de tutoriais de implementação necessários para implementar completamente o Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implementar o recurso do Data Box Gateway.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Transferir a imagem do dispositivo virtual
> * Obter a chave de ativação

## <a name="get-started"></a>Introdução

Para implementar o Data Box Gateway, consulte os seguintes tutoriais na sequência indicada.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Gateway](data-box-gateway-deploy-prep.md)** |Crie e configure o seu recurso do Data Box Gateway antes de aprovisionar um dispositivo virtual Data Box Gateway. |
| 2. |**[Aprovisionar o Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprovisionar o Data Box Gateway em VMware](data-box-gateway-deploy-provision-vmware.md)**|Para Hyper-V, aprovisione e ligue a um dispositivo virtual Data Box Gateway num sistema anfitrião com Hyper-V no Windows Server 2016 ou Windows Server 2012 R2. <br><br><br> Para vMware, provisão e ligação a um dispositivo virtual Data Box Gateway num sistema de anfitrião que executa VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Ligar, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. Em seguida, pode aprovisionar partilhas SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicione partilhas, ligue-se a partilhas através de SMB ou NFS. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui, vai encontrar os pré-requisitos de configuração para o recurso do Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

* A subscrição do Microsoft Azure tem de estar ativada para um recurso do Azure Stack Edge. Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tem acesso ao proprietário ou colaborador a nível de grupo de recursos para os recursos Azure Stack Edge / Data Box Gateway, IoT Hub e Azure Storage.
    - Para criar qualquer recurso Azure Stack Edge / Data Box Gateway, deverá ter permissões como contribuinte (ou superior) a nível de grupo de recursos. Também tem de se certificar de que o `Microsoft.DataBoxEdge` fornecedor está registado. Para obter informações sobre como realizar o registo, aceda a [Registar fornecedor de recursos](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Azure Storage é, por defeito, um fornecedor de recursos registado.
- Tem acesso administrativo ou utilizador à Microsoft Graph API. Para obter mais informações, consulte a [referência de permissões do Microsoft Graph](/graph/permissions-reference).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo Data Box Gateway

Antes de implementar um dispositivo virtual, certifique-se de que:

- Tem acesso a um sistema de anfitriões que executa o Hyper-V no Windows Server 2012 R2 ou mais tarde ou VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser utilizado para uma provisão de um dispositivo.
- O sistema anfitrião tem capacidade para dedicar os recursos seguintes para aprovisionar o seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelos menos 8 GB de RAM. Recomendamos vivamente pelo menos 16 GB de RAM.
  - Uma interface de rede.
  - Um disco de SO de 250 GB.
  - Um disco virtual de 2 TB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Data Box Gateway. Para obter mais informações, consulte os [requisitos](data-box-gateway-system-requirements.md)do sistema Data Box Gateway .

- Para condições normais de funcionamento do seu Portal da Caixa de Dados, deverá ter um:

    - Um mínimo de 10-Mbps descarrega largura de banda para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20-Mbps dedicado upload e download largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Gateway para gerir os dispositivos virtuais, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso Data Box Gateway, tome os seguintes passos no portal Azure.

1. Utilize as suas credenciais Microsoft Azure para iniciar seduca em qualquer um destes portais:

    - O portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com) .
    - O portal do Governo Azure nesta URL: [https://portal.azure.us](https://portal.azure.us) . Para mais informações, [aceda ao Governo de Azure utilizando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).
    
2. Selecione **+ Criar um recurso**.

    ![Gateway da caixa de dados Azure Criar um botão de recurso](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. Digite **Portal da Caixa de Dados** na caixa de pesquisa e prima Enter.

    ![Pesquisa rumo ao serviço Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Em seguida, selecione **Azure Data Box Gateway**.

    ![Selecione a Caixa de Dados Gateway SKU](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Selecione **Criar**.

    ![Clique em Criar para criar o recurso Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. No separador **Básico**:

    Insira ou selecione os **seguintes detalhes do Projeto.**
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Escolha a subscrição que pretende utilizar para o seu dispositivo Data Box Gateway. A subscrição está ligada à sua conta de faturação.|
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).|

   Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   |Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 caracteres contendo letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número. |
    |Region  |Selecione a região onde pretende implementar o seu recurso. Escolha um local próximo da região geográfica onde pretende implantar o seu dispositivo. <br> Para obter uma lista de todas as regiões onde estão disponíveis os recursos data base Gateway/Azure Stack Edge, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) <br> Para o Governo de Azure, estão disponíveis todas as regiões governamentais listadas nas [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)|

   Em seguida, selecione **Review + create** para rever o seu pedido.

   ![Entrada de detalhes de projeto e exemplo para uma encomenda de Gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. No **separador 'Rever +' criar,** rever os **detalhes de Preços,** **Termos de utilização** e os detalhes do seu recurso. Selecione **Criar**.

    ![Dados de recursos do Gateway de Caixa de Dados apresentados para revisão](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

A criação do recurso demora alguns minutos. Depois de o recurso ser criado e implementado com sucesso, é notificado. Selecione **Ir para recurso**.

![Uma encomenda completa do Portal da Caixa de Dados](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>Transferir a imagem do dispositivo virtual

Depois de criar o recurso do Data Box Gateway, transfira a imagem do dispositivo virtual adequado para aprovisionar um dispositivo virtual no seu sistema anfitrião. As imagens do dispositivo virtual são específicas de um sistema operativo.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

Siga estes passos no [portal Azure](https://portal.azure.com/) para descarregar uma imagem do dispositivo virtual.

1. No recurso que criou e, em seguida, selecione **Overview**. Se tiver um recurso Azure Data Box Gateway existente, selecione o recurso e vá ao **Overview**. Selecione **a configuração do dispositivo**.

    ![Novo recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. No azulejo de **imagem Download,** selecione a imagem do dispositivo virtual correspondente ao sistema operativo no servidor anfitrião utilizado para o fornecimento do VM. Os ficheiros de imagem são aproximadamente 5.6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e versões posteriores](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMware ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

    ![Baixar imagem do dispositivo virtual Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Data Box Gateway estar a funcionar, terá de obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Gateway ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou e, em seguida, **selecione Overview**. Na **configuração** do Dispositivo, vá ao **Configure e ative** o azulejo.

    ![Configurar e ativar azulejos](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. **Selecione A tecla Gerar** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e guarde-a para posterior utilização.

    ![Obter a chave de ativação](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após a sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Transferir a imagem do dispositivo virtual
> * Obter a chave de ativação

Avance para o próximo tutorial para saber como aprovisionar uma máquina virtual para o Data Box Gateway. Consoante o sistema operativo anfitrião, veja as instruções detalhadas em:

> [!div class="nextstepaction"]
> [Aprovisionar um Data Box Gateway em Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [Aprovisionar um Data Box Gateway em VMware](./data-box-gateway-deploy-provision-vmware.md)