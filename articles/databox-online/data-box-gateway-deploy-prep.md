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
ms.openlocfilehash: fdb4b7c370d5edadf3e0e5bc0d1ae548da6bcaa9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438645"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: preparar para implantar Gateway do Azure Data Box


Este é o primeiro tutorial da série de tutoriais de implementação necessários para implementar completamente o Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implementar o recurso do Data Box Gateway. 

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Transferir a imagem do dispositivo virtual
> * Obter a chave de ativação

## <a name="get-started"></a>Começar

Para implementar o Data Box Gateway, consulte os seguintes tutoriais na sequência indicada.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Gateway](data-box-gateway-deploy-prep.md)** |Crie e configure o seu recurso do Data Box Gateway antes de aprovisionar um dispositivo virtual Data Box Gateway. |
| 2. |**[Aprovisionar o Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprovisionar o Data Box Gateway em VMware](data-box-gateway-deploy-provision-vmware.md)**|Para Hyper-V, aprovisione e ligue a um dispositivo virtual Data Box Gateway num sistema anfitrião com Hyper-V no Windows Server 2016 ou Windows Server 2012 R2. <br><br><br> Para o VMware, provisione e conecte-se a um Gateway do Data Box dispositivo virtual em um sistema de host executando VMware ESXi 6,0, 6,5 ou 6,7.<br></br> |
| 3. |**[Ligar, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. Em seguida, pode aprovisionar partilhas SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicione partilhas, ligue-se a partilhas através de SMB ou NFS. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui, vai encontrar os pré-requisitos de configuração para o recurso do Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

- Sua assinatura do Microsoft Azure deve ter suporte para Gateway do Data Box recurso. Também há suporte para assinaturas pagas conforme o uso.
- Você tem acesso de proprietário ou colaborador em nível de grupo de recursos para os recursos de Data Box Edge/Gateway do Data Box, Hub IoT e armazenamento do Azure.

    - Para criar qualquer recurso de Data Box Edge/Gateway do Data Box, você deve ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisa certificar-se de que o provedor de `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como registrar-se, acesse [registrar provedor de recursos](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso de conta de armazenamento, novamente você precisa de colaborador ou acesso mais alto com escopo no nível do grupo de recursos. O armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de administrador ou de usuário para Azure Active Directory API do Graph. Para obter mais informações, consulte [Azure Active Directory API do Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo Data Box Gateway

Antes de implementar um dispositivo virtual, certifique-se de que:

- Você tem acesso a um sistema de host executando o Hyper-V no Windows Server 2012 R2 ou posterior ou VMware (ESXi 6,0, 6,5 ou 6,7) que pode ser usado para provisionar um dispositivo.
- O sistema anfitrião tem capacidade para dedicar os recursos seguintes para aprovisionar o seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelos menos 8 GB de RAM.
  - Uma interface de rede.
  - Um disco de SO de 250 GB.
  - Um disco virtual de 2 TB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Data Box Gateway. Para obter mais informações, consulte os [requisitos de sistema do gateway do data Box](data-box-gateway-system-requirements.md).

- Para condições normais de operação do seu Gateway do Data Box, você deve ter um:

    - Um mínimo de 10 Mbps de largura de banda de download para garantir que o dispositivo permaneça atualizado.
    - Um mínimo de 20 Mbps carregamento dedicado e largura de banda de download para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Gateway para gerir os dispositivos virtuais, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso de Gateway do Data Box, execute as seguintes etapas na portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar no:

    - O portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou o portal do Azure governamental nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter mais detalhes, acesse [conectar-se ao Azure governamental usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ criar um recurso**. Pesquise por **Data Box Edge/gateway do data Box**. Selecione Data Box Edge/Gateway do Data Box. Selecione **Criar**.
3. Escolha a assinatura que você deseja usar para Gateway do Data Box dispositivo. Selecione a região em que você deseja implantar o recurso de Gateway do Data Box. Para esta versão, leste dos EUA, Ásia Oriental do Sul e Europa Ocidental estão disponíveis. Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. Na opção **gateway do data Box** , selecione **criar**.

    ![Pesquisar o serviço Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na guia **noções básicas** , insira ou selecione os **detalhes do projeto**a seguir.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isso é preenchido automaticamente com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

5. Insira ou selecione os **detalhes da instância**a seguir.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |   
    |Região     |Para esta versão, leste dos EUA, Ásia Oriental do Sul e Europa Ocidental estão disponíveis para implantar seu recurso. Para o Azure governamental, todas as regiões governamentais listadas nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) estão disponíveis. <br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|
    
    ![Criar o recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selecione **Rever + criar**.
 
7. Na guia **revisar + criar** , examine os **detalhes de preços**, **termos de uso**e os detalhes do recurso. Selecione **Criar**.

    ![Examinar Gateway do Data Box detalhes do recurso](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

A criação do recurso demora alguns minutos. Depois que o recurso for criado e implantado com êxito, você será notificado. Selecione **ir para o recurso**.

![Examinar Gateway do Data Box detalhes do recurso](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Transferir a imagem do dispositivo virtual

Depois de criar o recurso do Data Box Gateway, transfira a imagem do dispositivo virtual adequado para aprovisionar um dispositivo virtual no seu sistema anfitrião. As imagens de dispositivo virtual são específicas para um sistema operacional.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

Siga estas etapas na [portal do Azure](https://portal.azure.com/) para baixar uma imagem de dispositivo virtual.

1. No recurso que você criou e, em seguida, selecione **visão geral**. Se você tiver um recurso de Gateway do Azure Data Box existente, selecione o recurso e acesse **visão geral**. Selecione **configuração do dispositivo**.

    ![Novo recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. No bloco **baixar imagem** , selecione a imagem do dispositivo virtual correspondente ao sistema operacional no servidor host usado para provisionar a VM. Os arquivos de imagem são aproximadamente 5,6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e versões posteriores](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMware ESXi 6,0, 6,5 ou 6,7](https://aka.ms/dbe-vmdk).

    ![Baixar Gateway do Data Box imagem do dispositivo virtual](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso de Gateway do Data Box estiver em execução, você precisará obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Gateway ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que você criou e, em seguida, selecione **visão geral**. Na **configuração do dispositivo**, vá para o bloco **configurar e ativar** .

    ![Configurar e ativar o bloco](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecione **gerar chave** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após sua geração.
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


