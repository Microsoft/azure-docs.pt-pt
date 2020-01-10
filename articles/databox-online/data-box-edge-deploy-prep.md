---
title: Tutorial para preparar portal do Azure, ambiente de datacenter para implantar Azure Data Box Edge | Microsoft Docs
description: O primeiro tutorial sobre a implantação de Azure Data Box Edge envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d9778544fd9e20dc3244a37c644117d1cd505a96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438670"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: preparar para implantar Azure Data Box Edge  

Este é o primeiro tutorial da série de tutoriais de implantação que são necessários para implantar completamente Azure Data Box Edge. Este tutorial descreve como preparar o portal do Azure para implantar um recurso de Data Box Edge.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="get-started"></a>Começar

Para implantar Data Box Edge, consulte os tutoriais a seguir na sequência prescrita.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Edge](data-box-edge-deploy-prep.md)** |Criar e configurar o seu recurso do Data Box Edge antes de instalar um dispositivo físico Data Box Edge. |
| 2. |**[Instalar o Data Box Edge](data-box-edge-deploy-install.md)**|Descompactar, montar e instalar o dispositivo físico do Data Box Edge.  |
| 3. |**[Conectar, configurar e ativar Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. O dispositivo está pronto para configurar partilhas SMB ou NFS.  |
| 4. |**[Transferir dados com o Data Box Edge](data-box-edge-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
| 5. |**[Transformar dados com o Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configure os módulos de computação no dispositivo para transformar os dados conforme eles são movidos para o Azure. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A seguir, os pré-requisitos de configuração para o recurso Data Box Edge, o dispositivo Data Box Edge e a rede do datacenter.

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

- Sua assinatura do Microsoft Azure está habilitada para um recurso de Data Box Edge. Não há suporte para assinaturas pagas conforme o uso.
- Você tem acesso de proprietário ou colaborador em nível de grupo de recursos para os recursos de Data Box Edge/Gateway do Data Box, Hub IoT e armazenamento do Azure.

    - Para criar qualquer recurso de Data Box Edge/Gateway do Data Box, você deve ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisa certificar-se de que o provedor de `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como registrar-se, acesse [registrar provedor de recursos](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar qualquer recurso do Hub IoT, verifique se o provedor Microsoft. Devices está registrado. Para obter informações sobre como registrar-se, acesse [registrar provedor de recursos](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso de conta de armazenamento, novamente você precisa de colaborador ou acesso mais alto com escopo no nível do grupo de recursos. O armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de administrador ou de usuário para Azure Active Directory API do Graph. Para obter mais informações, consulte [Azure Active Directory API do Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-edge-device"></a>Para o dispositivo do Data Box Edge

Antes de implementar um dispositivo físico, certifique-se de que:

- Você analisou as informações de segurança que foram incluídas no pacote de remessa.
- Você tem um slot de 1U disponível em um rack padrão de 19 em seu datacenter para montar o dispositivo no rack.
- Você tem acesso a uma superfície de trabalho plana, estável e em nível onde o dispositivo pode ficar com segurança.
- O site em que você pretende configurar o dispositivo tem energia de AC padrão de uma fonte independente ou uma unidade de distribuição de energia (PDU) de rack com uma fonte de alimentação ininterrupta (UPS).
- Tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede em seu datacenter é configurada de acordo com os requisitos de rede para seu dispositivo Data Box Edge. Para obter mais informações, consulte [Data Box Edge requisitos do sistema](data-box-edge-system-requirements.md).

- Para condições de operação normal do seu Data Box Edge, você tem:

    - Um mínimo de 10 Mbps de largura de banda de download para garantir que o dispositivo permaneça atualizado.
    - Um mínimo de 20 Mbps carregamento dedicado e largura de banda de download para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Edge para gerir os dispositivos físicos, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso de Data Box Edge, execute as seguintes etapas na portal do Azure.

1. Use suas credenciais de Microsoft Azure para entrar no 
    
    - O portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou o portal do Azure governamental nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter mais detalhes, acesse [conectar-se ao Azure governamental usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ criar um recurso**. Pesquise por **Data Box Edge/gateway do data Box**. Selecione **Data Box Edge/gateway do data Box**. Selecione **Criar**.
3. Escolha a assinatura que você deseja usar para o dispositivo de Data Box Edge. Selecione a região onde pretende implementar o recurso Data Box Edge. Para esta versão, leste dos EUA, Ásia Oriental do Sul e Europa Ocidental estão disponíveis. 

    Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. A região armazena apenas os metadados para o gerenciamento de dispositivos. Os dados reais podem ser armazenados em qualquer conta de armazenamento. 
    
    Na opção **Data Box Edge** , selecione **criar**.

    ![Pesquisar o serviço Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na guia **noções básicas** , insira ou selecione os **detalhes do projeto**a seguir.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isso é preenchido automaticamente com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

4. Insira ou selecione os **detalhes da instância**a seguir.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Região     |Para esta versão, leste dos EUA, Ásia Oriental do Sul e Europa Ocidental estão disponíveis para implantar seu recurso. Se você estiver usando o Azure governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Detalhes do projeto e da instância](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selecione **Avançar: endereço de envio**.

    - Se você já tiver um dispositivo, selecione a caixa de combinação para **eu ter um dispositivo data Box Edge**.
    - Se esse for o novo dispositivo que você está solicitando, insira o nome do contato, a empresa, o endereço para enviar o dispositivo e as informações de contato.

    ![Endereço de envio para novo dispositivo](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecione **Avançar: revisar + criar**.

7. Na guia **revisar + criar** , examine os **detalhes de preços**, **termos de uso**e os detalhes do recurso. Selecione a caixa de combinação para **revisar os termos de privacidade**.

    ![Revisar Data Box Edge termos de privacidade e detalhes do recurso](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecione **Criar**.

A criação do recurso demora alguns minutos. Depois que o recurso for criado e implantado com êxito, você será notificado. Selecione **ir para o recurso**.

![Ir para o recurso de Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Depois que o pedido for feito, a Microsoft analisará o pedido e chegará a você (por email) com detalhes de remessa.

![Notificação para revisão da ordem de Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso de Data Box Edge estiver em execução, você precisará obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Edge ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que você criou. Selecione **visão geral** e, em seguida, selecione **configuração do dispositivo**.

    ![Selecionar configuração do dispositivo](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. No bloco **Ativar** , selecione **gerar chave** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar o Data Box Edge.

> [!div class="nextstepaction"]
> [Instalar Data Box Edge](./data-box-edge-deploy-install.md)



