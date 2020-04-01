---
title: Tutorial sobre a preparação do portal do Azure para implementar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implementar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474463"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Prepare-se para implementar portal de caixas de dados Azure

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
| 2. |**[Aprovisionar o Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprovisionar o Data Box Gateway em VMware](data-box-gateway-deploy-provision-vmware.md)**|Para Hyper-V, aprovisione e ligue a um dispositivo virtual Data Box Gateway num sistema anfitrião com Hyper-V no Windows Server 2016 ou Windows Server 2012 R2. <br><br><br> Para VMware, foretae e ligue-se a um dispositivo virtual Data Box Gateway num sistema de anfitriões que executa VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Ligar, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. Em seguida, pode aprovisionar partilhas SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicione partilhas, ligue-se a partilhas através de SMB ou NFS. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui, vai encontrar os pré-requisitos de configuração para o recurso do Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

* A subscrição do Microsoft Azure está ativada para um recurso Azure Stack Edge. Certifique-se de que utilizou uma subscrição apoiada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)cloud solution provider [(CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)ou o Patrocínio do [Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tem acesso ao proprietário ou colaborador ao nível do grupo de recursos para os recursos data Box Edge/Data Box Gateway, IoT Hub e Azure Storage.
    - Para criar qualquer recurso Data Box Edge/ Data Box Gateway, deve ter permissões como contribuinte (ou superior) ao nível do grupo de recursos. Também precisa de se `Microsoft.DataBoxEdge` certificar de que o fornecedor está registado. Para obter informações sobre como se registar, vá ao [Register fornecedor de recursos.](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Armazenamento Azure é, por defeito, um fornecedor de recursos registado.
- Tem acesso a administrador ou utilizador à Microsoft Graph API. Para mais informações, consulte a referência de [permissões do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo Data Box Gateway

Antes de implementar um dispositivo virtual, certifique-se de que:

- Tem acesso a um sistema de anfitriões que executa hiper-V no Windows Server 2012 R2 ou posteriormente ou VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser usado para fornecer um dispositivo.
- O sistema anfitrião tem capacidade para dedicar os recursos seguintes para aprovisionar o seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelos menos 8 GB de RAM.
  - Uma interface de rede.
  - Um disco de SO de 250 GB.
  - Um disco virtual de 2 TB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Data Box Gateway. Para mais informações, consulte os [requisitos](data-box-gateway-system-requirements.md)do sistema Data Box Gateway .

- Para as condições normais de funcionamento do seu Portal da Caixa de Dados, deve ter um:

    - Um mínimo de 10 Mbps de largura de banda de descarregamento para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20 Mbps dedicado a carregar e transferir largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Gateway para gerir os dispositivos virtuais, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso Data Box Gateway, dê os seguintes passos no portal Azure.

1. Utilize as suas credenciais Microsoft Azure para iniciar sessão:

    - O portal Azure neste [https://portal.azure.com](https://portal.azure.com)URL: .
    - Ou, o portal do Governo [https://portal.azure.us](https://portal.azure.us)Azure nesta URL: . Para mais detalhes, vá ao [Governo De Connect para azure usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ Crie um recurso**. Pesquisar por **Data Box Edge / Data Box Gateway**. Selecione Data Box Edge / Data Box Gateway. Selecione **Criar**.
3. Escolha a subscrição que pretende utilizar para o dispositivo Data Box Gateway. Selecione a região onde pretende implementar o recurso Data Box Gateway. Para obter uma lista de todas as regiões onde está disponível o recurso Azure Stack Edge, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. Na opção **Data Box Gateway,** selecione **Criar**.

    ![Pesquisar o serviço Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. No separador **Basics,** introduza ou selecione os seguintes **detalhes do Projeto**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isto é automaticamente povoado com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

5. Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |   
    |Região     |Para obter uma lista de todas as regiões onde está disponível o recurso Azure Stack Edge, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Para o Governo azure, todas as regiões governamentais listadas nas [regiões de Azure](https://azure.microsoft.com/global-infrastructure/regions/) estão disponíveis. <br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|
    
    ![Criar o recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selecione **Rever + criar**.
 
7. No **separador Review + criar,** reveja os detalhes de **Preços,** **Termos de utilização**e os detalhes para o seu recurso. Selecione **Criar**.

    ![Rever detalhes de recursos gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

A criação do recurso demora alguns minutos. Depois que o recurso é criado e implantado com sucesso, você é notificado. Selecione **Ir para o recurso**.

![Rever detalhes de recursos gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Transferir a imagem do dispositivo virtual

Depois de criar o recurso do Data Box Gateway, transfira a imagem do dispositivo virtual adequado para aprovisionar um dispositivo virtual no seu sistema anfitrião. As imagens do dispositivo virtual são específicas de um sistema operativo.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

Siga estes passos no [portal Azure](https://portal.azure.com/) para descarregar uma imagem de dispositivo virtual.

1. No recurso que criou e, em seguida, selecione **visão geral**. Se tiver um recurso De Gateway de Caixa de Dados Azure existente, selecione o recurso e vá para **a Visão Geral**. **Selecione configuração**do dispositivo .

    ![Novo recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. No azulejo de **imagem Descarregamento,** selecione a imagem do dispositivo virtual correspondente ao sistema operativo no servidor de hospedeiro utilizado para fornecer o VM. Os ficheiros de imagem são de aproximadamente 5,6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e versões posteriores](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMWare ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

    ![Descarregue imagem de dispositivo virtual Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois do recurso Data Box Gateway estar a funcionar, terá de obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Gateway ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou e, em seguida, selecione **a visão geral**. Na **configuração**do Dispositivo, vá ao **Configure e ative** o azulejo.

    ![Configurar e ativar azulejos](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecione **A tecla Generate** para criar uma chave de ativação. Selecione o ícone da cópia para copiar a tecla e guarde-a para posterior utilização.

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

OU

> [!div class="nextstepaction"]
> [Aprovisionar um Data Box Gateway em VMware](./data-box-gateway-deploy-provision-vmware.md)


