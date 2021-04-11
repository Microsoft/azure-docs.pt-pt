---
title: Tutorial para preparar portal Azure, ambiente do datacenter para implementar Azure Stack Edge Pro GPU | Microsoft Docs
description: O primeiro tutorial sobre a implementação do Azure Stack Edge Pro GPU envolve a preparação do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: a58146c2c8121f3f0e3b564caafbb09396d39672
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568550"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Tutorial: Prepare-se para implementar Azure Stack Edge Pro com GPU 

Este tutorial é o primeiro da série de tutoriais de implementação que são necessários para implantar completamente O Azure Stack Edge Pro com GPU. Este tutorial descreve como preparar o portal Azure para implementar um recurso Azure Stack Edge.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para a implementação do Azure Stack Edge Pro, tem de preparar primeiro o seu ambiente. Uma vez que o ambiente esteja pronto, siga as etapas necessárias e, se necessário, etapas e procedimentos opcionais para implantar totalmente o dispositivo. As instruções de colocação passo a passo indicam quando deve executar cada uma destas etapas necessárias e opcionais.

| Passo | Description |
| --- | --- |
| **Preparação** |Estes passos devem ser concluídos em preparação para a próxima implantação. |
| **[Lista de verificação de configuração de implementação](#deployment-configuration-checklist)** |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Estes pré-requisitos validam que o ambiente está pronto para ser implantado. |
|  | |
|**Tutoriais de implantação** |Estes tutoriais são necessários para implementar o seu dispositivo Azure Stack Edge Pro em produção. |
|**[1. Prepare o portal Azure para o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)** |Crie e configuure o seu recurso Azure Stack Edge antes de instalar um dispositivo físico Azure Stack Box Edge. |
|**[2. Instale Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|Desembale, rack e cabo o dispositivo físico Azure Stack Edge Pro.  |
|**[3. Ligue-se ao Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |Assim que o dispositivo estiver instalado, ligue-se ao dispositivo web UI local.  |
|**[4. Configurar as definições de rede para Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo a rede de cálculo e as definições de procuração web para o seu dispositivo.   |
|**[5. Configurar as definições do dispositivo para OZure Stack Edge Pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Atribua um nome do dispositivo e domínio DNS, configuure o servidor de atualização e o tempo do dispositivo. |
|**[6. Configurar definições de segurança para Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configure certificados para o seu dispositivo. Utilize certificados gerados pelo dispositivo ou traga os seus próprios certificados.   |
|**[7. Ativar a Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |Utilize a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar ações SMB ou NFS ou ligar via REST. |
|**[8. Cálculo de configuração](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure o papel de computação no seu dispositivo. Um cluster Kubernetes também é criado. |
|**[9A. Dados de transferência com ações edge](./azure-stack-edge-gpu-deploy-add-shares.md)** |Adicione partilhas e ligue-se a partilhas através de SMB ou NFS. |
|**[9B. Transferir dados com contas de armazenamento Edge](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)** |Adicione contas de armazenamento e conecte-se ao armazenamento de bolhas através de REST APIs. |


Pode agora começar a recolher informações sobre a configuração do software para o seu dispositivo Azure Stack Edge Pro.

## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação

Antes de implementar o seu dispositivo, tem de recolher informações para configurar o software no seu dispositivo Azure Stack Edge Pro. Preparar algumas destas informações com antecedência ajuda a agilizar o processo de implantação do dispositivo no seu ambiente. Utilize a [lista de verificação de configuração Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-checklist.md) para anotar os detalhes de configuração à medida que implementa o seu dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso Azure Stack Edge, o seu dispositivo Azure Stack Edge Pro e a rede datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

Antes de começar, certifique-se de que:

- A subscrição do Microsoft Azure tem de estar ativada para um recurso do Azure Stack Edge. Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). As assinaturas pay-as-you-go não são suportadas. Para identificar o tipo de subscrição Azure que tem, veja [o que é uma oferta Azure?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer)
- Tem acesso ao proprietário ou colaborador a nível de grupo de recursos para os recursos Azure Stack Edge Pro/Data Box Gateway, IoT Hub e Azure Storage.

    - Para criar qualquer recurso Azure Stack Edge / Data Box Gateway, deverá ter permissões como contribuinte (ou superior) a nível de grupo de recursos. 
    - Também precisa de se certificar de que os `Microsoft.DataBoxEdge` fornecedores e `MicrosoftKeyVault` fornecedores de recursos estão registados. Para criar qualquer recurso IoT Hub, `Microsoft.Devices` o fornecedor deve ser registado. 
        - Para registar um fornecedor de recursos, no portal Azure, vá às **Subscrições de > Domiciliária > os fornecedores de recursos > de subscrição.** 
        - Procure o fornecedor de recursos específico, por `Microsoft.DataBoxEdge` exemplo, e registe o fornecedor de recursos. 
    - Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Azure Storage é, por defeito, um fornecedor de recursos registado.
- Tem acesso a administrador ou utilizador à Azure Ative Directory Graph API para gerar chave de ativação ou operações credenciais, como criação de ações que utiliza uma conta de armazenamento. Para mais informações, consulte [a Azure Ative Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Para o dispositivo Azure Stack Edge Pro

Antes de implementar um dispositivo físico, certifique-se de que:

- Reviu as informações de segurança que estavam incluídas no pacote de envio.
- Tem uma ranhura de 1U disponível num rack standard de 19" no seu datacenter para montagem do dispositivo.
- Você tem acesso a uma superfície de trabalho plana, estável e nivelada onde o dispositivo pode descansar com segurança.
- O local onde pretende configurar o dispositivo tem uma potência CA padrão de uma fonte independente ou uma unidade de distribuição de energia do rack (PDU) com uma alimentação de alimentação ininterrupta (UPS).
- Tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Azure Stack Edge Pro. Para obter mais informações, consulte [os requisitos do sistema Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

- Para condições normais de funcionamento do seu Azure Stack Edge Pro, tem:

    - Um mínimo de 10-Mbps descarrega largura de banda para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20-Mbps dedicado upload e download largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso Azure Stack Edge existente para gerir o seu dispositivo físico, ignore este passo e vá para [obter a chave de ativação](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar um recurso Azure Stack Edge, tome os seguintes passos no portal Azure.

1. Utilize as suas credenciais Microsoft Azure para iniciar súb9 no portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com) .

2. No painel esquerdo, selecione **+ Crie um recurso**. Procure e selecione **Azure Stack Edge / Data Box Gateway**. Selecione **Criar**. 

3. Escolha a subscrição que pretende utilizar para o dispositivo Azure Stack Edge Pro. Selecione o país para onde pretende enviar este dispositivo físico. Selecione **dispositivos Show**.

    ![Criar um recurso 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selecione o tipo de dispositivo. Em **Azure Stack Edge Pro,** escolha **Azure Stack Edge Pro com GPU** e, em seguida, escolha **Select**. Se vir algum problema ou não conseguir selecionar o tipo de dispositivo, aceda a [problemas de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

    ![Criar um recurso 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Com base na necessidade do seu negócio, pode selecionar Azure Stack Edge Pro com 1 ou 2 Unidades de Processamento Gráfico (GPUs) da Nvidia. 

    ![Criar um recurso 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. No **separador Básicos, insira** ou selecione os seguintes detalhes do **Projeto.**
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |A subscrição é automaticamente povoada com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |

7. Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem de 2 a 50 caracteres contendo letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Region     |Para obter uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo de Azure, todas as regiões governamentais estão disponíveis, como mostra as regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Criar um recurso 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. Selecione **Seguinte: Endereço de envio**.

    - Se já tem um dispositivo, selecione a caixa de combinação para **eu já ter um dispositivo**.

        ![Criar um recurso 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Se este é o novo dispositivo que está a encomendar, insira o nome de contacto, a empresa, o endereço para enviar o dispositivo e as informações de contacto.

        ![Criar um recurso 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Selecione **Seguinte: Tags**. Opcionalmente fornecer etiquetas para categorizar recursos e consolidar a faturação. Selecione **Seguinte: Rever + criar**.

10. No **separador 'Rever +' criar,** rever os **detalhes de Preços,** **Termos de utilização** e os detalhes do seu recurso. Selecione a caixa de combinação para **eu ter revisto os termos de privacidade**.

    ![Criar um recurso 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    É também notificado que durante a criação de recursos está ativado um Identidade de Serviço Gerido (MSI) que lhe permite autenticar os serviços na nuvem. Esta identidade existe enquanto o recurso existir.

11. Selecione **Criar**.

    A criação do recurso demora alguns minutos. Também é criado um MSI que permite que o dispositivo Azure Stack Edge comunique com o fornecedor de recursos em Azure.

    Depois de o recurso ser criado e implementado com sucesso, é notificado. Selecione **Ir para recurso**.

    ![Vá ao recurso Azure Stack Edge Pro](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Após a encomenda ser feita, a Microsoft revê a encomenda e contacta-o (via e-mail) com detalhes de envio.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> Se pretender criar várias encomendas ao mesmo tempo ou clonar uma encomenda existente, pode utilizar os [scripts em Amostras Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Para mais informações, consulte o ficheiro README.

Se encontrar problemas durante o processo de encomenda, consulte [problemas de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se necessário, prepare o seu ambiente para o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para criar um recurso Azure Stack Edge, execute os seguintes comandos em Azure CLI.

1. Crie um grupo de recursos utilizando o [grupo az criar](/cli/azure/group#az_group_create) comando ou utilizar um grupo de recursos existente:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Para criar um dispositivo, utilize o [dispositivo az databoxedge criar](/cli/azure/databoxedge/device#az_databoxedge_device_create) comando:

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. A região armazena apenas os metadados para a gestão do dispositivo. Os dados reais podem ser armazenados em qualquer conta de armazenamento.

   Para obter uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo de Azure, todas as regiões governamentais estão disponíveis, como mostra as regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)

1. Para criar uma encomenda, executar a [ordem az databoxedge criar](/cli/azure/databoxedge/order#az_databoxedge_order_create) comando:

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

A criação do recurso demora alguns minutos. Executar a [ordem de databoxedge az mostrar](/cli/azure/databoxedge/order#az_databoxedge_order_show) comando para ver a ordem:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Depois de estoirar um pedido, a Microsoft revê a encomenda e contacta-o por e-mail com detalhes de envio.

---

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Azure Stack Edge estar a funcionar, terás de obter a chave de ativação. Esta chave é utilizada para ativar e ligar o seu dispositivo Azure Stack Edge Pro ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso criado e **selecione Overview**.

2. No painel direito, insira um nome para o Cofre da Chave Azure ou aceite o nome predefinido. O nome do cofre pode estar entre 3 e 24 caracteres.

   É criado um cofre-chave para cada recurso Azure Stack Edge que é ativado com o seu dispositivo. O cofre permite armazenar e aceder a segredos, por exemplo, a Chave de Integridade do Canal (CIK) para o serviço está armazenada no cofre da chave. 

   Uma vez especificado um nome de cofre de chave, **selecione Gerar a tecla** para criar uma chave de ativação. 

   ![Obter a chave de ativação](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   Aguarde alguns minutos enquanto a chave do cofre e da chave de ativação são criadas. Selecione o ícone de cópia para copiar a chave e guarde-a para posterior utilização.<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - A chave de ativação expira três dias após a sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge Pro tais como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar o Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Instalar Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)