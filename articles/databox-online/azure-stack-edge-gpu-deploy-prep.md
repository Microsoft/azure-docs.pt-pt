---
title: Tutorial para preparar portal Azure, ambiente do datacenter para implantar GPU de Azure Stack Edge Microsoft Docs
description: O primeiro tutorial sobre a implementação do GPU Azure Stack Edge envolve a preparação do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c02af8983489651d8b5b83d5d3107a534c51f067
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254683"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-with-gpu"></a>Tutorial: Prepare-se para implantar Azure Stack Edge com GPU 

Este é o primeiro tutorial na série de tutoriais de implementação que são necessários para implantar completamente Azure Stack Edge com GPU. Este tutorial descreve como preparar o portal Azure para implementar um recurso Azure Stack Edge.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para a implementação do Azure Stack Edge, tem de preparar primeiro o seu ambiente. Uma vez que o ambiente esteja pronto, siga as etapas necessárias e, se necessário, etapas e procedimentos opcionais para implantar totalmente o dispositivo. As instruções de colocação passo a passo indicam quando deve executar cada uma destas etapas necessárias e opcionais.

| Passo | Descrição |
| --- | --- |
| **Preparação** |Estes passos devem ser concluídos em preparação para a próxima implantação. |
| **[Lista de verificação de configuração de implementação](#deployment-configuration-checklist)** |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Estes validam o ambiente como estando preparado para a implementação. |
|  | |
|**Tutoriais de implantação** |Estes tutoriais são necessários para implementar o seu dispositivo Azure Stack Edge em produção. |
|**[1. Prepare o portal Azure para a borda da pilha de Azure](azure-stack-edge-gpu-deploy-prep.md)** |Crie e configuure o seu recurso Azure Stack Edge antes de instalar um dispositivo físico Azure Stack Box Edge. |
|**[2. Instalar borda de pilha azul](azure-stack-edge-gpu-deploy-install.md)**|Desembale, rack e cabo o dispositivo físico Azure Stack Edge.  |
|**[3. Ligue-se ao Aresta da Pilha Azure](azure-stack-edge-gpu-deploy-connect.md)** |Assim que o dispositivo estiver instalado, ligue-se ao dispositivo web UI local.  |
|**[4. Configurar as definições de rede para Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo a rede de cálculo e as definições de procuração web para o seu dispositivo.   |
|**[5. Configurar as definições do dispositivo para Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Atribua um nome do dispositivo e domínio DNS, configuure o servidor de atualização e o tempo do dispositivo. |
|**[6. Configurar definições de segurança para Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configure certificados para o seu dispositivo. Utilize certificados gerados pelo dispositivo ou traga os seus próprios certificados.   |
|**[7. Ativar a borda da pilha de Azure](azure-stack-edge-gpu-deploy-activate.md)** |Utilize a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar ações SMB ou NFS ou ligar via REST. |
|**[8. Cálculo de configuração](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure o papel de computação no seu dispositivo. Isto também criará um cluster Kubernetes. |
|**[9A. Dados de transferência com ações edge](azure-stack-edge-j-series-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
|**[9B. Transferir dados com contas de armazenamento Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Adicione contas de armazenamento e conecte-se ao armazenamento de bolhas através de REST APIs. |


Pode agora começar a recolher informações sobre a configuração do software para o seu dispositivo Azure Stack Edge.

## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação

Antes de implementar o seu dispositivo, tem de recolher informações para configurar o software no seu dispositivo Azure Stack Edge. Preparar algumas destas informações com antecedência ajuda a agilizar o processo de implantação do dispositivo no seu ambiente. Utilize a [lista de verificação de configuração de implementação Azure Stack Edge](azure-stack-edge-gpu-deploy-checklist.md) para anotar os detalhes de configuração à medida que implementa o seu dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso Azure Stack Edge, o seu dispositivo Azure Stack Edge e a rede de datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

- A sua subscrição microsoft Azure está ativada para um recurso Azure Stack Edge. Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). As assinaturas pay-as-you-go não são suportadas.
- Tem acesso ao proprietário ou colaborador a nível de grupo de recursos para os recursos Azure Stack Edge/Data Box Gateway, IoT Hub e Azure Storage.

    - Para criar qualquer recurso Azure Stack Edge / Data Box Gateway, deverá ter permissões como contribuinte (ou superior) a nível de grupo de recursos. Também tem de se certificar de que o `Microsoft.DataBoxEdge` fornecedor está registado. Para obter informações sobre como se registar, aceda ao [fornecedor de recursos Register](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar qualquer recurso IoT Hub, certifique-se de que o fornecedor microsoft.devices está registado. Para obter informações sobre como se registar, aceda ao [fornecedor de recursos Register](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Azure Storage é, por defeito, um fornecedor de recursos registado.
- Tem acesso administrativo ou utilizador à Azure Ative Directory Graph API. Para mais informações, consulte [a Azure Ative Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-azure-stack-edge-device"></a>Para o dispositivo Azure Stack Edge

Antes de implementar um dispositivo físico, certifique-se de que:

- Reviu as informações de segurança que estavam incluídas no pacote de envio.
- Tem uma ranhura de 1U disponível num rack standard de 19" no seu datacenter para montagem do dispositivo.
- Você tem acesso a uma superfície de trabalho plana, estável e nivelada onde o dispositivo pode descansar com segurança.
- O local onde pretende configurar o dispositivo tem uma potência CA padrão de uma fonte independente ou uma unidade de distribuição de energia do rack (PDU) com uma alimentação de alimentação ininterrupta (UPS).
- Tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Azure Stack Edge. Para obter mais informações, consulte [os requisitos do sistema Azure Stack Edge System](azure-stack-edge-system-requirements.md).

- Para condições normais de funcionamento do seu Azure Stack Edge, tem:

    - Um mínimo de 10-Mbps descarrega largura de banda para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20-Mbps dedicado upload e download largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso Azure Stack Edge existente para gerir o seu dispositivo físico, ignore este passo e vá para [obter a chave de ativação](#get-the-activation-key).

Para criar um recurso Azure Stack Edge, tome os seguintes passos no portal Azure.

1. Utilize as suas credenciais Microsoft Azure para iniciar súb9 no portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com) .

2. No painel esquerdo, selecione **+ Crie um recurso**. Procure e selecione **Azure Stack Edge / Data Box Gateway**. Selecione **Criar**. Se vir algum problema, vá a [questões de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

3. Escolha a subscrição que pretende utilizar para o dispositivo Azure Stack Edge. Selecione o país para onde pretende enviar este dispositivo físico. Selecione **dispositivos Show**.

    ![Criar um recurso 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selecione o tipo de dispositivo. No **Azure Stack Edge Commercial,** escolha **Azure Stack Edge com GPU** e selecione **Inscreva-se**. 

    ![Criar um recurso 2](media/azure-stack-edge-gpu-deploy-prep/create-resource-2.png)

5. É apresentado um formulário curto. Preencha o formulário e **selecione Enviar por isso.** A Microsoft ativará a sua subscrição.

    ![Criar um recurso 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Após a subscrição estar ativada, deverá poder prosseguir com a criação de recursos. Na lâmina **do tipo dispositivo Select,** escolha **Select**.

    ![Criar um recurso 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)


6. No **separador Básicos, insira** ou selecione os seguintes detalhes do **Projeto.**
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isto é automaticamente povoado com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo de Azure, todas as regiões governamentais estão disponíveis, como mostra as regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Criar um recurso 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


5. Selecione **Seguinte: Endereço de envio**.

    - Se já tiver um dispositivo, selecione a caixa de combinação para **eu ter um dispositivo Azure Stack Edge**.

        ![Criar um recurso 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Se este for o novo dispositivo que está a encomendar, insira o nome de contacto, a empresa, o endereço para enviar o dispositivo e as informações de contacto.

        ![Criar um recurso 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

6. Selecione **Seguinte: Rever + criar**.

7. No **separador 'Rever +' criar,** rever os **detalhes de Preços,** **Termos de utilização**e os detalhes do seu recurso. Selecione a caixa de combinação para **eu ter revisto os termos de privacidade**.

    ![Criar um recurso 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

8. Selecione **Criar**.

A criação do recurso demora alguns minutos. Depois de o recurso ser criado e implementado com sucesso, é notificado. Selecione **Ir para recurso**.

![Vá ao recurso Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Após a encomenda ser feita, a Microsoft revê a encomenda e contacta-o (via e-mail) com detalhes de envio.

![Notificação para revisão da encomenda Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

Se encontrar problemas durante o processo de encomenda, consulte [problemas de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Azure Stack Edge estar a funcionar, terás de obter a chave de ativação. Esta chave é utilizada para ativar e ligar o seu dispositivo Azure Stack Edge ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou. Selecione **Overview** e, em seguida, selecione **a configuração do dispositivo**.

    ![Selecione configuração do dispositivo](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. No **azulejo Ativar,** **selecione Gerar a tecla** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e guarde-a para posterior utilização.

    ![Obter a chave de ativação](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após a sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge tais como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar o Azure Stack Edge.

> [!div class="nextstepaction"]
> [Instalar borda de pilha de Azure](./azure-stack-edge-gpu-deploy-install.md)



