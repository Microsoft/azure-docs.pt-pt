---
title: Tutorial sobre a preparação do portal do Azure para implementar o Data Box Edge | Microsoft Docs
description: O primeiro tutorial sobre como implementar o Edge de caixa de dados do Azure envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 19c4fc96653f966ea5642149d944886e4b7f4483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401667"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: Preparar a implementação de borda de caixa de dados do Azure  


Este é o primeiro tutorial da série de tutoriais de implementação que são necessários para implementar completamente o Edge de caixa de dados do Azure. Este tutorial descreve como preparar o portal do Azure para implementar um recurso de borda de caixa de dados.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


### <a name="get-started"></a>Introdução

Para implementar o Edge de caixa de dados, consulte os seguintes tutoriais na seqüência prescrita.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Edge](data-box-edge-deploy-prep.md)** |Criar e configurar o seu recurso do Data Box Edge antes de instalar um dispositivo físico Data Box Edge. |
| 2. |**[Instalar o Data Box Edge](data-box-edge-deploy-install.md)**|Descompactar, montar e instalar o dispositivo físico do Data Box Edge.  |
| 3. |**[Ligar, configurar e ativar o limite da caixa de dados](data-box-edge-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. O dispositivo está pronto para configurar partilhas SMB ou NFS.  |
| 4. |**[Transferir dados com o Data Box Edge](data-box-edge-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
| 5. |**[Transformar dados com o Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configure os módulos do Edge no dispositivo para transformar os dados durante a deslocação destes para o Azure. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso de borda de caixa de dados, o seu dispositivo Edge de caixa de dados e a rede de centro de dados.

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, certifique-se de que:

- Subscrição do Microsoft Azure está ativada para um recurso de borda de caixa de dados. Subscrições pay as you go não são suportadas.
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-edge-device"></a>Para o dispositivo do Data Box Edge

Antes de implementar um dispositivo físico, certifique-se de que:

- Reviu as informações de segurança que foi incluídas no pacote de remessa.
- Tem uma 1 ranhura de U disponível num bastidor padrão 19" no seu datacenter para rack montar o dispositivo. 
- Tem acesso para uma superfície plana, estável e nível de trabalho em que o dispositivo pode rest com segurança.
- O site onde pretende configurar o dispositivo tem alternada padrão de uma origem de independente ou uma unidade de distribuição de energia (PDU) de rack com uma alimentação ininterrupta (UPS).
- Tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada pelos requisitos de rede para o seu dispositivo do Edge de caixa de dados. Para obter mais informações, consulte [requisitos de sistema de borda de caixa de dados](data-box-edge-system-requirements.md).

- Para condições de operação normais de extremidade da caixa de dados, terá de:

    - Um mínimo de 10 Mbps transferir largura de banda para garantir que o dispositivo se mantém atualizado.
    - Um mínimo de 20 Mbps dedicado carregar e transferir a largura de banda para transferência de ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Edge para gerir os dispositivos físicos, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso de borda de caixa de dados, siga os passos seguintes no portal do Azure.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão no 
    
    - O portal do Azure neste URL: [ https://portal.azure.com ](http://portal.azure.com).
    - Em alternativa, o portal do Azure Government neste URL: [https://portal.azure.us](https://portal.azure.us)

2. No painel esquerdo, selecione **+ criar um recurso**. Procure **dados de caixa Edge / dados caixa Gateway**. Selecione **dados de caixa Edge / dados caixa Gateway**. Selecione **Criar**.
3. Escolha a subscrição que pretende utilizar para o dispositivo de limite de caixa de dados. Selecione a região onde pretende implementar o recurso Data Box Edge. Nesta versão, E.U.A. leste, Sudeste asiático e Europa estão disponíveis. Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. Na **dados de caixa de borda** opção, selecione **criar**.

    ![Pesquisar o serviço Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Sobre o **Noções básicas** separador, introduza ou selecione as seguintes **detalhes do projeto**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isso é preenchido automaticamente com base na seleção da anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Introduza ou selecione as seguintes **detalhes de instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Name   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Região     |Nesta versão, E.U.A. leste, Sudeste asiático e Europa estão disponíveis para implementar o seu recurso. Se utilizar o Azure Government, todas as regiões do Governo estão disponíveis, como mostra a [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Detalhes do projeto e a instância](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selecione **seguinte: Endereço para remessa**.

    - Se já tiver um dispositivo, selecione a caixa de combinação **tenho um dispositivo de limite de caixa de dados**.
    - Se se tratar do novo dispositivo que estão a ordenação, introduza o nome de contato, empresa, o endereço para enviar o dispositivo e informações de contacto.

    ![Endereço de envio para o novo dispositivo](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecione **seguinte: Rever + criar**.

7. Na **rever + criar** separador, reveja o **os detalhes dos preços**, **termos de utilização**e os detalhes do seu recurso. Selecione a caixa de combinação **revi os termos de privacidade**.

    ![Reveja os detalhes do recurso de borda de caixa de dados e os termos de privacidade](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecione **Criar**.

A criação do recurso demora alguns minutos. Depois do recurso é criado e implementado com êxito, receberá uma notificação. Selecione **Ir para recurso**.

![Ir para o recurso de borda de caixa de dados](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Depois do pedido é feito, a Microsoft analisa a ordem e acede ao (via e-mail) com detalhes de envio.

![Notificação para revisão do pedido de borda de caixa de dados](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois do recurso de borda de caixa de dados está em execução, terá de obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Edge ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou. Selecione **descrição geral** e, em seguida, selecione **configuração do dispositivo**.

    ![Selecione a configuração do dispositivo](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Sobre o **Activate** mosaico, selecione **gerar chave** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e guarde-o para utilização posterior.

    ![Obter a chave de ativação](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após ser gerado.
> - Se a chave expirou, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para saber como instalar o Edge de caixa de dados.

> [!div class="nextstepaction"]
> [Instalar o Edge de caixa de dados](./data-box-edge-deploy-install.md)



