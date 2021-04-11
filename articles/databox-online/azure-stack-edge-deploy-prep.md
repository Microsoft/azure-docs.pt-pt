---
title: Tutorial para preparar portal Azure, ambiente do datacenter para implementar Azure Stack Edge Pro | Microsoft Docs
description: O primeiro tutorial sobre a implementação do Azure Stack Edge Pro envolve a preparação do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: alkohli
ms.openlocfilehash: 0e0d7808e449a6e4f3e341cd40acdc220e65c0ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068222"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Tutorial: Prepare-se para implementar Azure Stack Edge Pro  

Este é o primeiro tutorial na série de tutoriais de implementação que são necessários para implementar completamente O Azure Stack Edge Pro. Este tutorial descreve como preparar o portal Azure para implementar um recurso Azure Stack Edge. 

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Obter a chave de ativação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="get-started"></a>Introdução

Para implementar Azure Stack Edge Pro, consulte os seguintes tutoriais na sequência prescrita.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Prepare o portal Azure para o Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Crie e configuure o seu recurso Azure Stack Edge antes de instalar um dispositivo físico Azure Stack Box Edge. |
| 2. |**[Instalar Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|Desembale, rack e cabo o dispositivo físico Azure Stack Edge Pro.  |
| 3. |**[Conecte-se, instale e ative o Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. O dispositivo está pronto para configurar partilhas SMB ou NFS.  |
| 4. |**[Dados de transferência com Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
| 5. |**[Transforme dados com Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Configure os módulos de computação no dispositivo para transformar os dados à medida que se move para Azure. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso Azure Stack Edge, o seu dispositivo Azure Stack Edge Pro e a rede datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

* A subscrição do Microsoft Azure tem de estar ativada para um recurso do Azure Stack Edge. Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). As assinaturas pay-as-you-go não são suportadas.

* Tem acesso ao proprietário ou colaborador a nível de grupo de recursos para os recursos Azure Stack Edge / Data Box Gateway, IoT Hub e Azure Storage.

  * Deverá ser **proprietário** ao nível de subscrição para conceder acesso ao contribuinte. Para dar acesso ao colaborador a outra pessoa, no portal Azure, aceda ao controlo de acesso a **todas as** subscrições de  >  **serviços**  >  **(IAM)**  >  **+Add**  >  **Add fun assignment**. Para obter mais informações, consulte [Tutorial: Conceder a um utilizador acesso aos recursos da Azure utilizando o portal Azure](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Para criar qualquer recurso Azure Stack Edge / Data Box Gateway, deverá ter permissões como contribuinte (ou superior) a nível de grupo de recursos. Também é necessário certificar-se de que o `Microsoft.DataBoxEdge` fornecedor de recursos está registado. Para obter informações sobre como registar um fornecedor de recursos, consulte [o fornecedor de recursos Do Registo.](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)
  * Para criar qualquer recurso IoT Hub, certifique-se de que o fornecedor microsoft.devices está registado. Para obter informações sobre como realizar o registo, aceda a [Registar fornecedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Azure Storage é, por defeito, um fornecedor de recursos registado.
* Tem acesso administrativo ou utilizador à Azure Ative Directory Graph API. Para mais informações, consulte [a Azure Ative Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* Não está bloqueado por nenhuma política Azure criada pelo administrador do seu sistema. Para obter mais informações sobre políticas, consulte [Quickstart: Crie uma atribuição de política para identificar recursos não conformes](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Para o dispositivo Azure Stack Edge Pro

Antes de implementar um dispositivo físico, certifique-se de que:

* Reviu as informações de segurança que estavam incluídas no pacote de envio.
* Tem uma ranhura de 1U disponível num rack standard de 19" no seu datacenter para montagem do dispositivo.
* Você tem acesso a uma superfície de trabalho plana, estável e nivelada onde o dispositivo pode descansar com segurança.
* O local onde pretende configurar o dispositivo tem uma potência CA padrão de uma fonte independente ou uma unidade de distribuição de energia do rack (PDU) com uma alimentação de alimentação ininterrupta (UPS).
* Tem acesso a um dispositivo físico.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

* A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Azure Stack Edge Pro. Para obter mais informações, consulte [os requisitos do sistema Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

* Para condições normais de funcionamento do seu Azure Stack Edge Pro, tem:

  * Um mínimo de 10 Mbps descarrega largura de banda para garantir que o dispositivo permanece atualizado.
  * Um mínimo de 20 Mbps dedicado upload e download largura de banda para transferir ficheiros.

## <a name="create-new-resource-for-existing-device"></a>Criar novos recursos para dispositivos existentes

Se for um cliente Azure Stack Edge Pro existente, utilize o seguinte procedimento para criar um novo recurso se precisar de substituir ou reiniciar o dispositivo existente.

Se for um novo cliente, recomendamos que explore a utilização de dispositivos Azure Stack Edge Pro - GPU para as suas cargas de trabalho. Para mais informações, aceda ao [Que é Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-overview.md). Para obter informações sobre a encomenda de um Azure Stack Edge Pro com dispositivo GPU, vá para [criar um novo recurso para Azure Stack Edge Pro - GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Para criar um novo recurso Azure Stack Edge Pro para um dispositivo existente, dê os seguintes passos no portal Azure.

1. Use as suas credenciais Microsoft Azure para iniciar sação em:

    - O portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com) .
    - Ou, o portal do Governo Azure nesta URL: [https://portal.azure.us](https://portal.azure.us) . Para mais detalhes, [aceda ao Governo de Azure utilizando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).

1. Selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge**. Em seguida, selecione **Criar**.

1. Selecione a subscrição do dispositivo Azure Stack Edge Pro e do país para enviar o dispositivo para o **Navio para**.

   ![Selecione a subscrição e navio-a-país para o seu dispositivo](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Na lista de tipos de dispositivos que são apresentados, selecione **Azure Stack Edge Pro - FPGA**. Em seguida, escolha **Selecionar**. 

   O tipo de dispositivo **Azure Stack Edge Pro - FPGA** só é apresentado se tiver um dispositivo existente. Se precisar de encomendar um novo dispositivo, vá para [criar um novo recurso para o Azure Stack Edge Pro - GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Pesquisar serviço Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. No separador **Básico**:

   1. Insira ou selecione os **seguintes detalhes do Projeto.**
    
       |Definição  |Valor  |
       |---------|---------|
       |Subscrição    |Este valor é automaticamente povoado com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
       |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

   1. Introduza ou selecione os **seguintes detalhes da Instância**.

       |Definição  |Valor  |
       |---------|---------|
       |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem de 2 e 50 caracteres, incluindo letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
       |Region     |Para obter uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo de Azure, todas as regiões governamentais estão disponíveis, como mostra as regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

   1. Selecione **Rever + criar**.

    ![Detalhes do projeto e da instância](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. No **separador 'Rever +' criar,** rever os **Termos de utilização,** detalhes de **preços** e os detalhes do seu recurso. Em seguida, selecione **Criar**.

    ![Reveja detalhes de recursos e termos de privacidade do Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. A criação do recurso demora alguns minutos. Depois de o recurso ser criado e implementado com sucesso, é notificado. Selecione **Ir para recurso**.

   ![Vá ao recurso Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Após a encomenda ser feita, a Microsoft revê a encomenda e contacta-o (via e-mail) com detalhes de envio.

![Notificação para revisão da encomenda Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Azure Stack Edge estar a funcionar, terás de obter a chave de ativação. Esta chave é utilizada para ativar e ligar o seu dispositivo Azure Stack Edge Pro ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Aceda ao recurso que criou e selecione **Overview**. Verá uma notificação no sentido de que a sua encomenda está a ser processada.

    ![Selecione visão geral](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Depois de a encomenda ser processada e o dispositivo estar a caminho, a **Visão Geral** atualiza.. Aceite o nome padrão do **Cofre da Chave Azure** ou introduza um novo. **Selecione A tecla de ativação de geração**. Selecione o ícone de cópia para copiar a chave e guarde-a para posterior utilização.

    ![Obter a chave de ativação](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * A chave de ativação expira três dias após a sua geração.
> * Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge Pro tais como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar o Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Instalar Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)