---
title: Tutorial para preparar portal Azure, ambiente datacenter para implantar Azure Data Box Edge  Microsoft Docs
description: O primeiro tutorial sobre a implementação do Azure Data Box Edge envolve a preparação do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 179a6181efdc6c31d50d3b5b3f708fd9149dadd3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384726"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: Prepare-se para implantar borda de caixa de dados Azure  

Este é o primeiro tutorial da série de tutoriais de implantação que são necessários para implantar completamente o Azure Data Box Edge. Este tutorial descreve como preparar o portal Azure para implementar um recurso Data Box Edge.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="get-started"></a>Introdução

Para implementar data box edge, consulte os seguintes tutoriais na sequência prescrita.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Edge](data-box-edge-deploy-prep.md)** |Criar e configurar o seu recurso do Data Box Edge antes de instalar um dispositivo físico Data Box Edge. |
| 2. |**[Instalar o Data Box Edge](data-box-edge-deploy-install.md)**|Descompactar, montar e instalar o dispositivo físico do Data Box Edge.  |
| 3. |**[Ligar, configurar e ativar data box edge](data-box-edge-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. O dispositivo está pronto para configurar partilhas SMB ou NFS.  |
| 4. |**[Transferir dados com o Data Box Edge](data-box-edge-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
| 5. |**[Transformar dados com o Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configure módulos de computação no dispositivo para transformar os dados à medida que se move para O Azure. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso Data Box Edge, o seu dispositivo Data Box Edge e a rede datacenter.

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

- A subscrição do Microsoft Azure está ativada para um recurso Data Box Edge. As subscrições pay-as-you-go não são suportadas.
- Tem acesso ao proprietário ou colaborador ao nível do grupo de recursos para os recursos data Box Edge/Data Box Gateway, IoT Hub e Azure Storage.

    - Para criar qualquer recurso Data Box Edge/ Data Box Gateway, deve ter permissões como contribuinte (ou superior) ao nível do grupo de recursos. Também precisa de se certificar de que o fornecedor `Microsoft.DataBoxEdge` está registado. Para obter informações sobre como se registar, vá ao [Register fornecedor de recursos.](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Para criar qualquer recurso IoT Hub, certifique-se de que o fornecedor microsoft.Devices está registado. Para obter informações sobre como se registar, vá ao [Register fornecedor de recursos.](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Armazenamento Azure é, por defeito, um fornecedor de recursos registado.
- Tem acesso a administrador ou utilizador ao Azure Ative Directory Graph API. Para mais informações, consulte [o Azure Ative Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-edge-device"></a>Para o dispositivo do Data Box Edge

Antes de implementar um dispositivo físico, certifique-se de que:

- Reviu as informações de segurança que foram incluídas no pacote de envio.
- Tem uma ranhura de 1U disponível num rack padrão de 19" no seu datacenter para montagem do dispositivo.
- Tem acesso a uma superfície de trabalho plana, estável e nivelada onde o dispositivo pode descansar em segurança.
- O site onde pretende configurar o dispositivo tem uma potência CA padrão a partir de uma fonte independente ou de uma unidade de distribuição de energia de rack (PDU) com uma fonte de alimentação ininterrupta (UPS).
- Tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Data Box Edge. Para mais informações, consulte [os requisitos do sistema](data-box-edge-system-requirements.md)de borda de caixa de dados .

- Para as condições normais de funcionamento do seu Limite de Caixa de Dados, tem:

    - Um mínimo de 10 Mbps de largura de banda de descarregamento para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20 Mbps dedicado a carregar e transferir largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Edge para gerir os dispositivos físicos, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso Data Box Edge, dê os seguintes passos no portal Azure.

1. Use as suas credenciais Microsoft Azure para iniciar sessão 
    
    - O portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou, o portal do Governo Azure neste URL: [https://portal.azure.us](https://portal.azure.us). Para mais detalhes, vá ao [Governo De Connect para azure usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ Crie um recurso**. Pesquisar por **Data Box Edge / Data Box Gateway**. Selecione **Data Box Edge / Data Box Gateway**. Selecione **Criar**.
3. Escolha a subscrição que pretende utilizar para o dispositivo Data Box Edge. Selecione a região onde pretende implementar o recurso Data Box Edge. Para obter uma lista de todas as regiões onde está disponível o recurso Azure Stack Edge, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

    Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. A região armazena apenas os metadados para a gestão de dispositivos. Os dados reais podem ser armazenados em qualquer conta de armazenamento.
    
    Na opção **Data Box Edge,** selecione **Criar**.

    ![Pesquisar o serviço Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. No separador **Basics,** introduza ou selecione os seguintes **detalhes do Projeto**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isto é automaticamente povoado com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

4. Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões onde está disponível o recurso Azure Stack Edge, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo Azure, todas as regiões governamentais estão disponíveis, como mostra as [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Detalhes do projeto e da instância](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selecione **Seguinte: Endereço de envio**.

    - Se já tiver um dispositivo, selecione a caixa de combinação para **eu ter um dispositivo Data Box Edge**.
    - Se este é o novo dispositivo que está a encomendar, insira o nome de contacto, empresa, endereço para enviar o dispositivo e contacte informações.

    ![Endereço de envio para novo dispositivo](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecione **Seguinte: Rever + criar**.

7. No **separador Review + criar,** reveja os detalhes de **Preços,** **Termos de utilização**e os detalhes para o seu recurso. Selecione a caixa de combinação para **eu ter revisto os termos de privacidade**.

    ![Rever detalhes de recursos de Data Box Edge e termos de privacidade](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecione **Criar**.

A criação do recurso demora alguns minutos. Depois que o recurso é criado e implantado com sucesso, você é notificado. Selecione **Ir para o recurso**.

![Ir ao recurso Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Depois de a encomenda ser feita, a Microsoft analisa a encomenda e contacta-o (via e-mail) com detalhes de envio.

![Notificação para revisão da ordem Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Data Box Edge estar a funcionar, terá de obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Edge ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou. Selecione **a visão geral** e, em seguida, selecione a **configuração**do dispositivo .

    ![Selecione configuração do dispositivo](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. No azulejo **Activate,** **selecione A chave Generate** para criar uma chave de ativação. Selecione o ícone da cópia para copiar a tecla e guarde-a para posterior utilização.

    ![Obter a chave de ativação](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após a sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar data box edge.

> [!div class="nextstepaction"]
> [Instalar borda de caixa de dados](./data-box-edge-deploy-install.md)



